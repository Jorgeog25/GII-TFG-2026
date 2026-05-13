# Descripción del funcionamiento del sistema

## Caso de Uso - Recibir Solicitud

El proyecto se ha desarrollado utilizando sistemas de Microsoft, aprovechando su compatibilidad con Exchange Online. En este flujo, el actor principal es Exchange Online, que activa el evento inicial del sistema cuando se recibe un correo electrónico. A partir de ese momento, el proceso se ejecuta automáticamente y el sistema genera la respuesta correspondiente según el caso detectado.

El funcionamiento es el siguiente: cuando se recibe un correo dirigido al buzón compartido **VARIACIONES_PLANTA_EXTERIOR**, se dispara el trigger del flujo. La conexión entre **Power Automate** y **Exchange Online** se realiza mediante el conector **Office 365 Outlook**, que comprueba el buzón cada minuto.

El correo recibido se procesa como un JSON que contiene distintos atributos. Uno de los más importantes es `subject`, que se utiliza para determinar la acción que debe realizarse con el correo.

![Trigger](./DescripcionSol/Trigger.png)

El sistema contempla cuatro casuísticas principales mediante un bloque **Switch**:

## 1. Caso `TE:FORMRESUELTO`

Asunto definido internamente. Un técnico envía este correo al buzón para actualizar el estado del formulario.

## 2. Caso `Documentacion Desacuerdo con la Solucion`

Correo enviado por un cliente. El sistema mueve el mensaje a la carpeta de documentación de desacuerdo, facilitando al técnico la gestión de dicha documentación.

## 3. Caso `Documentacion Acuerdo`

Correo enviado también por un cliente. El sistema mueve el mensaje a una carpeta específica para que el técnico pueda tratarlo posteriormente.

## 4. Caso por defecto

Corresponde a cualquier asunto diferente de los anteriores.

## Detalle del caso por defecto

Cuando el asunto no coincide con ninguno de los casos anteriores, el correo es analizado por un agente configurado mediante un prompt. Este agente devuelve un JSON con la intención del mensaje y todos los números de más de cuatro cifras mencionados en el correo.

El agente está desarrollado en **Power Apps**, herramienta de Microsoft, y se conecta al flujo mediante **Microsoft Dataverse**.

### Ejemplo de JSON devuelto

```json
{
  "intencion": "consulta",
  "numeros": [
    "8809795",
    "23052900002",
    "48821"
  ]
}
```

A continuación, se almacena el primer dato en el dataset de **Power BI**. La conexión se realiza mediante el conector de Power BI, que accede al dataset semántico. Inicialmente, se guarda en la tabla `TotalEmail` un identificador único, junto con la intención detectada y la fecha actual.

Después, se analiza la intención obtenida, lo que puede dar lugar a dos situaciones.

# 1. Intenciones que no requieren consulta en base de datos

Si la intención es:

- `separata`
- `asesoramiento`
- `canalizacion`
- `peligro` (sin número)
- `ajena`

El sistema responde automáticamente con un correo informando de que el buzón no está destinado a atender ese tipo de solicitudes y redirige al remitente al canal correspondiente.

## Caso Ajena

![Caso Ajena](./DescripcionSol/IntencionAjena.png)

## Caso Separata

![Caso Separata](./DescripcionSol/IntencionSeparata.png)

# 2. Intenciones que requieren consulta en base de datos

Si la intención es:

- `reclamación`
- `consulta`
- `peligro` (con número)

El sistema consulta las bases de datos disponibles y procesa la información obtenida.

Este proceso se realiza mediante condiciones anidadas, ya que se consultan varias tablas y cada una contiene datos con estructuras diferentes.

# Consulta sobre Actuaciones

La primera consulta se realiza sobre la tabla de **Actuaciones**.

```sql
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

Esta consulta devuelve información como la siguiente:

![Respuesta Actuaciones](./DescripcionSol/RespuestaACT.png)

## Traducción de provincia

Por ejemplo, la provincia se almacena en base de datos mediante un código numérico. Posteriormente, dicho código se traduce al nombre correspondiente mediante una tabla de equivalencias.

```text
coalesce(
  variables('provincias')?[string(outputs('Run_a_query_against_a_dataset_ACT')?['body/firstTableRows']?[0]?['[cod_prov]'])],
  'Desconocida'
)
```

## Cálculo del estado del proyecto

Las distintas fases del proyecto (`FaF`, `FaLF`, `FNmF`, `FeF`, `FvF`, `FPmF`, `FrF`, etc.) se utilizan para calcular el estado actual del expediente.

```text
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

Una vez procesados los datos, el sistema construye automáticamente el correo de respuesta en función del estado obtenido.

# Consulta sobre WEPES

Si la consulta anterior no devuelve resultados, el sistema consulta la base de datos de **WEPES**.

```sql
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

# Consulta sobre PETTER

Si la consulta sobre WEPES también devuelve resultados vacíos, se realiza una última consulta sobre **PETTER**.

```sql
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

# Registro de información procesada

Tras finalizar la consulta y antes de comprobar el siguiente número, el sistema almacena en la tabla `Numero`:

- El número consultado.
- La empresa colaboradora traducida.
- La provincia.
- El técnico responsable.
- El número de veces que se ha consultado dicho identificador.

# Caso sin resultados

Si ninguna de las consultas devuelve resultados, el sistema informa en la respuesta de que el número indicado no existe en las bases de datos y solicita que se revise si es correcto.

# Ejemplo de respuesta

![Respuesta Numeros](./DescripcionSol/RespuestaNumeros.png)

## Caso de uso - Recibir formulario

El actor principal de este caso de uso es **Microsoft Forms**, ya que es el sistema encargado de registrar el envío del formulario y desencadenar automáticamente el flujo de procesamiento.

En caso de que el cliente necesite adjuntar documentación, reclamar por exceso sobre la fecha prevista o reclamar sobre la solución propuesta, deberá rellenar un formulario desarrollado en **Microsoft Forms**.

Una vez completado y enviado el formulario, se activa automáticamente el siguiente flujo de **Power Automate**, encargado de analizar la información recibida y ejecutar las acciones correspondientes.

# Activación del flujo

El flujo comienza mediante el trigger:

- `When a new response is submitted`

Este trigger pertenece al conector de **Microsoft Forms** y se encarga de detectar automáticamente cuándo un usuario envía una nueva respuesta en el formulario configurado.

Internamente, el trigger mantiene una conexión permanente entre Microsoft Forms y Power Automate. Cada vez que un usuario pulsa el botón **Enviar** en el formulario, Microsoft Forms genera un evento que es recibido por Power Automate, iniciando así la ejecución automática del flujo.

El trigger devuelve información básica relacionada con el envío realizado, principalmente:

- Identificador del formulario.
- Identificador único de la respuesta generada.
- Fecha de envío.
- Usuario que realizó la respuesta, si aplica.

Un ejemplo simplificado de los datos recibidos por el trigger sería el siguiente:

```json
{
  "formId": "f3b2f2aa-92f0-4f13-81ab-xxxxxxxx",
  "responseId": "125",
  "responder": "cliente@correo.com",
  "submitDate": "2025-01-18T10:25:43Z"
}
```

El dato más importante recibido es `responseId`, ya que permite recuperar posteriormente toda la información rellenada por el cliente dentro del formulario.

# Obtención de datos del formulario

Una vez activado el trigger, el flujo ejecuta la acción:

- `Get response details`

Esta acción utiliza el identificador `responseId` generado anteriormente para consultar directamente la respuesta almacenada en Microsoft Forms.

En este punto, el sistema recupera todos los campos rellenados por el usuario, incluyendo:

- Datos identificativos.
- Número de expediente o actuación.
- Tipo de solicitud.
- Comentarios escritos por el cliente.
- Respuesta seleccionada en preguntas de opción.
- Archivos adjuntos, en caso de existir.

La información se recibe estructurada en formato JSON, permitiendo posteriormente trabajar cada campo de manera independiente dentro de Power Automate.

Un ejemplo simplificado de la respuesta obtenida sería:

```json
{
  "responseId": "125",
  "nombre": "Juan Pérez",
  "correo": "cliente@correo.com",
  "telefono": "600123123",
  "numero_expediente": "23052900002",
  "tipo_solicitud": "Desacuerdo con solucion",
  "comentarios": "No estoy conforme con la solucion propuesta.",
  "hay_documentacion": true,
}
```

Gracias a esta estructura JSON, Power Automate puede acceder individualmente a cada dato utilizando expresiones dinámicas, condiciones y variables internas del flujo.

# Comprobación de documentación

Posteriormente, el flujo realiza una comprobación denominada:

- `Hay Documentacion`

Esta condición permite verificar si el cliente ha adjuntado documentación adicional.

Después, el sistema evalúa si el cliente acepta la solución propuesta mediante la condición:

- `Es aceptación de acuerdo`

## Caso aceptación de acuerdo

Si el cliente acepta la solución propuesta, el sistema ejecuta automáticamente las siguientes acciones:

1. Envío de un correo electrónico desde el buzón compartido mediante:

   - `Send an email from a shared mailbox (V2)`

2. Actualización de variables internas del flujo para registrar el estado del expediente y el resultado del proceso.

El correo enviado confirma la recepción de la aceptación y deja constancia del acuerdo alcanzado.

## Caso desacuerdo con documentación

Si el cliente indica desacuerdo con la solución propuesta y además adjunta documentación, el flujo accede a la condición:

- `Es desacuerdo con documentacion`

En este caso, el sistema realiza automáticamente las siguientes acciones:

1. Envío de un correo desde el buzón compartido notificando la recepción de la reclamación y de la documentación aportada.

2. Actualización de las variables internas necesarias para identificar el estado del proceso y facilitar posteriormente el trabajo del técnico responsable.

Una vez finalizadas las validaciones y ejecutadas las acciones correspondientes, el flujo procede a almacenar toda la información procesada en la base de datos de **Power BI**.

La conexión entre **Power Automate** y **Power BI** se realiza mediante el conector oficial de Power BI, que permite insertar información directamente sobre un dataset semántico previamente configurado.

El almacenamiento de la información se realiza mediante la acción:

- `Add a row into a table`

Esta acción inserta automáticamente un nuevo registro en la tabla `Formulario` del dataset de Power BI.

La tabla está configurada con un identificador autogenerado, funcionando de manera similar a una base de datos SQL con campos autoincrementales. Por ello, no es necesario obtener manualmente el último identificador almacenado, ya que Power BI genera automáticamente un ID único para cada nuevo registro insertado.

Los datos almacenados corresponden a toda la información obtenida y procesada desde Microsoft Forms. Únicamente se insertan aquellos campos que han sido rellenados por el usuario durante el formulario. Los campos no informados se almacenan con valor `null`, permitiendo mantener una estructura homogénea dentro de la tabla `Formulario` del dataset de Power BI.
