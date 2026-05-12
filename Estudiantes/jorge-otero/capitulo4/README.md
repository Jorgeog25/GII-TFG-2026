# Descripción de la solución

El proyecto se ha desarrollado con sistemas de Microsoft gracias a su compatibilidad con Exchange Online. 

El funcionamiento es el siguiente. Al recibir un correo dirigido al buzon compartido VARIACIONES_PLANTA_EXTERIOR. Se dispara el trigger. Esta conexion entre Power Automate y Excahn ge Online es mediante el conector Office 365 Outlook. Se comprueba cada minuto. Este correo se procesa de la siguiente manera. Un json con los siguientes datos.
![](./DescripcionSol/Trigger.png)

Uno de los atributos es subject. Que se tliza para determinar el acto que se va a hacer con ese correo. 

Hay 4 casuisticsas trabajadas por un Switch 
1. El asunto es **TE:FORMRESUELTO** determinado internamiente.Un técnico enviara este correo al buzón que actualizará el estado del formulario. 

2. El asunto es **Documentacion Desacuerdo con la Solucion** enviado por un cliente para que el sistema mueva ese correo a la carpeta de Documentacion Desacuerdo. Que facilita al técnico trabjar esa documentacion.

3. El asunto es **Documentacion Acuerdo** enviado tambien por un cliente. Se movera el correo a una carpeta para que el técnico lo trabaje. 

4. Un asunto diferente a los mencionados. Es el caso default.

El detalle del caso 4 es el siguiente:

Este correo pasa a ser analizado por un agente con prompt introducido para que conteste con un JSON con la intencion y todos los números de más de 4 cifras mencionados en el correo. Este agente está en Power Apps (herammienta de Micorsoft) que se conecta a traves de Microsoft Dataverse. 

**Ejemplo de JSON**
``` 
{
  "intencion": "consulta",
  "numeros": [
    "8809795",
    "23052900002",
    "48821"
  ]
}
```

Aqui se guarda el primer dato en el dataset de PowerBi. La conexion se crea con Power Bi connector que accede al Dataset semántico de Power Bi. Inicialmente se almacena en la tabla TotalEmai. Un ID unívoco junto con la intencion y la fecha actual. 

A continucación, se pasa a analizar la intención. Que da lugar a dos posibles situaciones.

1. La intencion es: separata, asesoramiento, canalizacion, peligro (sin número) o ajena. Todas estas situacione se responden con correos que informan que el buaon no se dedica a responder a esos casos y se desvia al lugar correcto. 

Caso Ajena
![](./DescripcionSol/IntencionAjena.png)

Caso Separata
![](./DescripcionSol/IntencionSeparata.png)

2. En caso de que la intencion sea: reclamación, consulta o peligro (con número), se procede a consultar en base de datos y a procesar los datos recibidos.

Este paso, se hace a traves de if anidados porque se tratan de varias tablas. Además, los datosd e cada una son diferentes. 

La primera que se consulta es la de Actuaciones. 
```
EVALUATE
SELECTCOLUMNS(
    FILTER(
        sgipe_circuitos,
        sgipe_circuitos[NUMERO] = "@{items('Recorrer_Numeros')}"
    ),
    "cod_prov", sgipe_circuitos[cod_prov],
    "FaF", sgipe_circuitos[FaF],
    "FaLF", sgipe_circuitos[FaLF],
    "FNmF", sgipe_circuitos[FNmF],
    "FeF", sgipe_circuitos[FeF],
    "FvF", sgipe_circuitos[FvF],
    "FPmF", sgipe_circuitos[FPmF],
    "FPMotivo", sgipe_circuitos[FPMotivo],
    "FrF", sgipe_circuitos[FrF],
    "GEF", sgipe_circuitos[GEF],
    "FiF", sgipe_circuitos[FiF],
    "AccEst", sgipe_circuitos[AccEst],
    "EECC", sgipe_circuitos[ContLC]
)
```

Un ejemplo de como se trabja un dato en este caso sería el de provincia. En base de datos está guardado con un número. Entonces se comprueba el valor almacenado con el valor correspondiente 

```
coalesce(
  variables('provincias')?[string(outputs('Run_a_query_against_a_dataset_ACT')?['body/firstTableRows']?[0]?['[cod_prov]'])],
  'Desconocida'
)
```
Las fases (todas las Fs...) se usan para calcular el estado del proyecto, tiempos etc. 

```
if(
  or(
    not(empty(outputs('Run_a_query_against_a_dataset_ACT')?['body/firstTableRows']?[0]?['[FaF]'])),
    not(empty(outputs('Run_a_query_against_a_dataset_ACT')?['body/firstTableRows']?[0]?['[FaLF]']))
  ),
  'Anulada',

  if(
    not(empty(outputs('Run_a_query_against_a_dataset_ACT')?['body/firstTableRows']?[0]?['[FNmF]'])),
    'Fase de Terminado',

    if(
      not(empty(outputs('Run_a_query_against_a_dataset_ACT')?['body/firstTableRows']?[0]?['[FPmF]'])),

      if(
        equals(outputs('Run_a_query_against_a_dataset_ACT')?['body/firstTableRows']?[0]?['[FPMotivo]'], 'por Permiso Oficial'),
        'Fase Pendiente de Gestión de Permisos',

        if(
          equals(outputs('Run_a_query_against_a_dataset_ACT')?['body/firstTableRows']?[0]?['[FPMotivo]'], 'por Causa Cliente'),
          'Fase Pendiente de aceptación de propuesta por parte del peticionario',
          'Fase de Construcción'
        )
      ),

      if(
        not(empty(outputs('Run_a_query_against_a_dataset_ACT')?['body/firstTableRows']?[0]?['[FeF]'])),
        'Fase de Construcción',

        if(
          and(
            empty(outputs('Run_a_query_against_a_dataset_ACT')?['body/firstTableRows']?[0]?['[FvF]']),
            empty(outputs('Run_a_query_against_a_dataset_ACT')?['body/firstTableRows']?[0]?['[FNmF]']),
            empty(outputs('Run_a_query_against_a_dataset_ACT')?['body/firstTableRows']?[0]?['[FPmF]']),
            not(empty(outputs('Run_a_query_against_a_dataset_ACT')?['body/firstTableRows']?[0]?['[FrF]']))
          ),
          'Fase de validación',

          'Fase de redacción de proyecto'
        )
      )
    )
  )
)
```
El siguiente paso es ir formando el correo en funcion de los resultados obtenidos. En funcion del estado se dice una cosa u otra. Dando un correo diferente dependiendo de lo obtenido.

En caso de que la respuesta de la consulta este vacia, se pasa a consultar para Wepes

```
EVALUATE
SELECTCOLUMNS(
    FILTER(
        WEPES_var1_expediente,
        WEPES_var1_expediente[id] = @{items('Recorrer_Numeros')}
    ),
    "Numero",  WEPES_var1_expediente[id],
    "Estado",  WEPES_var1_expediente[Estado_logico],
    "Ultima_mod", WEPES_var1_expediente[ultima_modificacion.fecha],
    "Provincia", WEPES_var1_expediente[nom_prov],
    "tecnico", WEPES_var1_expediente[TECNICO],
    "Fecha_cambio",
        COALESCE(
            WEPES_var1_expediente[InicioEstadoLogico02],
            WEPES_var1_expediente[InicioEstadoLogico01]
        )
)
```

Por último si esta consulta devuelve vacia, se comprueba para Petter

```
EVALUATE
SELECTCOLUMNS(
    ADDCOLUMNS(
        FILTER(
            'pett Incidencia',
            'pett Incidencia'[codigo] = "PET@{items('Recorrer_Numeros')}"
        ),
        "EECC",
        VAR IncActualId = 'pett Incidencia'[id]
        RETURN
            MAXX(
                TOPN(
                    1,
                    FILTER(
                        'pett Incidencia_Eecc',
                        'pett Incidencia_Eecc'[id_incidencia] = IncActualId &&
                        ISBLANK('pett Incidencia_Eecc'[fecha_baja])
                    ),
                    'pett Incidencia'[fecha_creacion],
                    DESC
                ),
                RELATED('pett Tm_Eecc'[valor])
            )
    ),
    "codigo", [codigo],
    "codigo_tipo", [codigo_tipo],
    "codigo_impacto", [codigo_impacto],
    "codigo_prioridad", [codigo_prioridad],
    "codigo_estado", [codigo_estado],
    "codigo_peticionario_tipo", [codigo_peticionario_tipo],
    "descripcion", [descripcion],
    "fecha_resolucion_estimada", [fecha_resolucion_estimada],
    "fecha_creacion", [fecha_creacion],
    "fecha_cierre", [fecha_cierre],
    "EECC", [EECC]
)
```
Tras finalizar la consulta y antes de comprobar el sigueinte numero, se guardara en la tabla Numero el número, la empresa colaboradora ya traducida, la provincia, el técnico responsable y se hara un calculo de cuantas veces se ha consultado. 

En caso de que esta consulta tambien este vacia. Se dirá que este numero no existe en las bases de datos y que se revise si es correcto. 

Un ejemplo de la respuesta que daría ante una consulta sería

![](./DescripcionSol/RespuestaCompleta.png)






