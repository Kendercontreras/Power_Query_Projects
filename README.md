# Power_Query_Projects
Transformaciones de datos realizadas con Power Query de Power BI
Proyecto: Consolidación de Datos para Reportes DEM Corporate
Descripción del Proyecto
El proyecto de Consolidación de Datos para Reportes DEM Corporate se centró en optimizar y unificar el proceso de manejo de datos en la generación de reportes para una empresa corporativa. Como analista de datos semi-senior, mi objetivo era mejorar la eficiencia en la transformación de datos, permitiendo una toma de decisiones más informada.

Objetivos y Alcance
Los objetivos principales incluyeron la consolidación y limpieza de datos mensuales, provenientes de diferentes fuentes y formatos, para generar informes unificados. El alcance abarcó desde la extracción hasta la transformación de datos en Power BI, utilizando herramientas como Power Query y su editor avanzado.

Desafíos y Soluciones
El proyecto presentó desafíos significativos, como la gestión de grandes volúmenes de datos heterogéneos y la necesidad de mantener la coherencia en los informes. Abordé estos desafíos mediante la implementación de consultas eficientes, la eliminación de duplicados y la integración de información clave en un único repositorio.

Herramientas Utilizadas
Durante el desarrollo del proyecto, utilicé la herramienta Power Query en Power BI, para la limpieza y transformación de datos.

Estructura del Proyecto
La estructura del proyecto se diseñó para facilitar la comprensión y mantenimiento del flujo de datos. Se crearon consultas individuales para cada mes, seguidas de consultas de consolidación para unificar los datos a lo largo del año.

Resultados y Beneficios
La consolidación de datos permitió generar informes unificados y coherentes, proporcionando una visión más clara y eficiente para la toma de decisiones. Los reportes resultantes facilitaron la identificación de patrones y tendencias, mejorando la capacidad de la empresa para realizar análisis estratégicos.

Proceso de Desarrollo
Desde la identificación de los requisitos hasta la implementación y validación, el proceso de desarrollo se llevó a cabo de manera estructurada, asegurando la calidad y consistencia de los datos a lo largo del proyecto.

Códigos M:
Los códigos M desarrollados para el proyecto, se centran en la eficiencia y claridad en la manipulación de datos. A continuación, se detallan los códigos utilizados, que ilustran la lógica aplicada en la limpieza y transformación de datos.

Lectura y transformación de archivos originales. El ejemplo se muestra para un solo mes pero se realizó para todos los meses del año:

```
let
    Origen = Folder.Files("E:\Reportes DEM\2024\Reportes_Unificados\ControlSeriesReporteDemCorporate\XLS Procesados\Enero"),
    #"Archivos ocultos filtrados1" = Table.SelectRows(Origen, each [Attributes]?[Hidden]? <> true),
    #"Invocar función personalizada1" = Table.AddColumn(#"Archivos ocultos filtrados1", "Transformar archivo", each #"Transformar archivo"([Content])),
    #"Columnas con nombre cambiado1" = Table.RenameColumns(#"Invocar función personalizada1", {"Name", "Source.Name"}),
    #"Otras columnas quitadas1" = Table.SelectColumns(#"Columnas con nombre cambiado1", {"Source.Name", "Transformar archivo"}),
    #"Columna de tabla expandida1" = Table.ExpandTableColumn(#"Otras columnas quitadas1", "Transformar archivo", Table.ColumnNames(#"Transformar archivo"(#"Archivo de ejemplo"))),
    #"Tipo cambiado" = Table.TransformColumnTypes(#"Columna de tabla expandida1",{{"Source.Name", type text}, {"ID_ORDEN", Int64.Type}, {"ID_ORDEN_SISTEMA_ORIGEN", type text}, {"ID_ACCION_ORDEN", type text}, {"NOMBRE_CLIENTE", type text}, {"DOCUMENTO_CLIENTE_TIPO", type text}, {"DOCUMENTO_CLIENTE", Int64.Type}, {"DOCUMENTO_CLIENTE_DV", type text}, {"USUARIO_CREACION", type text}, {"SISTEMA_ORIGEN", type text}, {"FECHA_CREACION", Int64.Type}, {"FECHA_ULTIMA_MODIFICACION", Int64.Type}, {"COMENTARIO_FLAG_ERROR", type text}, {"NOD_NOMBRE_ESTADO", type text}, {"ID_RASTREO", Int64.Type}, {"ORDER_KEY", type text}, {"ORDER_ACTION_KEY", type text}, {"ID_GUIA_DESPACHO", Int64.Type}, {"ESTADO_CONFIRMACION", Int64.Type}, {"CUSTOMER_KEY", Int64.Type}, {"SUBSCRIBER_KEY", Int64.Type}, {"MSISDN", Int64.Type}, {"SERIE", type text}, {"CHR_TIPO_RECURSO", type text}, {"NOD_ID_ALMACEN_ORIGEN", type text}, {"NOD_ID_MATERIAL", type text}, {"DESCRIP_MATERIAL", type text}, {"PMP", Int64.Type}, {"ORDER_STATUS_DESC", type text}, {"ORDER_ACTION_STATUS_DESC", type text}, {"ORDER_ITEM_TYPE_DESC", type text}, {"ORDER_ACTION_REASON_DESC", type text}, {"ORDER_ACTION_START_DATE", Int64.Type}, {"ORDER_ACTION_STEP_TYPE_NAME", type text}, {"ORDER_ACTION_CREATOR", type text}, {"ORDER_ACTION_LAST_UPDATOR", type text}, {"SALES_CHANNEL_NAME_INITIAL", type text}, {"CUSTOMER_TYPE_DESC", type text}, {"CUSTOMER_SUB_TYPE_DESC", type text}, {"PORTABILITY_TYPE", type any}, {"DELIVERY_METHOD_DESC", type text}, {"PAYMENT_CATEGORY_DESC", type text}, {"SAP_PRIMER_MOVIMIENTO", type text}, {"SAP_PRIMER_ORDEN", type text}, {"SAP_ULTIMO_MOVIMIENTO", type text}, {"SAP_ULTIMO_ORDEN", type text}, {"ESTADO_SAP", type text}, {"MARCA", type text}}),
    #"Filtrando por NOMBRE_CLIENTE sin ;" = Table.SelectRows(#"Tipo cambiado", each not Text.Contains([NOMBRE_CLIENTE], ";")),
    #"Anexando ""Enero con ;""" = Table.Combine({#"Filtrando por NOMBRE_CLIENTE sin ;", #"Enero con ;"}),
    #"Columnas quitadas1" = Table.RemoveColumns(#"Anexando ""Enero con ;""",{"Source.Name", "ID_ORDEN", "DOCUMENTO_CLIENTE_TIPO", "DOCUMENTO_CLIENTE", "DOCUMENTO_CLIENTE_DV", "USUARIO_CREACION", "SISTEMA_ORIGEN", "FECHA_CREACION", "CUSTOMER_KEY", "NOD_ID_ALMACEN_ORIGEN"}),
    #"Filtrando por tipo de operación" = Table.SelectRows(#"Columnas quitadas1", each ([ORDER_ITEM_TYPE_DESC] <> "BULK PROVIDE SMB SUBSCRIPTION")),
    #"Reemplazando tipo de operación en blanco" = Table.ReplaceValue(#"Filtrando por tipo de operación","","ORDEN NO CREADA EN BELIEVE",Replacer.ReplaceValue,{"ORDER_ITEM_TYPE_DESC"}),
    #"Columnas quitadas" = Table.RemoveColumns(#"Reemplazando tipo de operación en blanco",{"NOMBRE_CLIENTE", "FECHA_ULTIMA_MODIFICACION", "COMENTARIO_FLAG_ERROR", "NOD_NOMBRE_ESTADO", "ID_RASTREO", "ID_GUIA_DESPACHO", "ESTADO_CONFIRMACION", "SUBSCRIBER_KEY", "MSISDN", "CHR_TIPO_RECURSO", "NOD_ID_MATERIAL", "DESCRIP_MATERIAL", "PMP", "ORDER_ACTION_REASON_DESC", "ORDER_ACTION_START_DATE", "ORDER_ACTION_CREATOR", "ORDER_ACTION_LAST_UPDATOR", "SALES_CHANNEL_NAME_INITIAL", "CUSTOMER_TYPE_DESC", "CUSTOMER_SUB_TYPE_DESC", "PORTABILITY_TYPE", "DELIVERY_METHOD_DESC", "PAYMENT_CATEGORY_DESC", "SAP_PRIMER_MOVIMIENTO", "SAP_PRIMER_ORDEN", "SAP_ULTIMO_MOVIMIENTO", "SAP_ULTIMO_ORDEN", "ESTADO_SAP", "MARCA"}),
    #"Filtrando por tipo de operación ""Cambiar""" = Table.SelectRows(#"Columnas quitadas", each ([ORDER_ITEM_TYPE_DESC] = "CAMBIAR"))
in
    #"Filtrando por tipo de operación ""Cambiar"""
```

Al momento de extraer los archivos desde sus respectivas bases de datos, existía un error en los registros porque, dejaba todos los datos agrupados en la columna "NOMBRE_CLIENTE". Ejemplo:

```Si tengo los campos "NOMBRE_CLIENTE", "DOCUMENTO_CLIENTE_TIPO", "DOCUMENTO_CLIENTE" y "DOCUMENTO_CLIENTE_DV", junto con los valores: "Nombre", "RUT", "11111111" y "1", en vez de separar los datos como "NOMBRE_CLIENTE" = "Nombre"
"DOCUMENTO_CLIENTE_TIPO" = "RUT"
"DOCUMENTO_CLIENTE" = "11111111"
"DOCUMENTO_CLIENTE_DV" = "1"

Los deja como: 

"NOMBRE_CLIENTE" = "Nombre;RUT,11111111,1"
"DOCUMENTO_CLIENTE_TIPO" = ""
"DOCUMENTO_CLIENTE" = ""
"DOCUMENTO_CLIENTE_DV" = ""
```
Es por eso, que este tipo de datos se filtra y se transforman por separado, corrigiendo los errores de extracción, mediante el siguiente código, el cual se ejecutó para todos los meses del año: 

```
let
    Origen = Folder.Files("E:\Reportes DEM\2024\Reportes_Unificados\ControlSeriesReporteDemCorporate\XLS Procesados\Enero"),
    #"Archivos ocultos filtrados1" = Table.SelectRows(Origen, each [Attributes]?[Hidden]? <> true),
    #"Invocar función personalizada1" = Table.AddColumn(#"Archivos ocultos filtrados1", "Transformar archivo", each #"Transformar archivo"([Content])),
    #"Columnas con nombre cambiado1" = Table.RenameColumns(#"Invocar función personalizada1", {"Name", "Source.Name"}),
    #"Otras columnas quitadas1" = Table.SelectColumns(#"Columnas con nombre cambiado1", {"Source.Name", "Transformar archivo"}),
    #"Columna de tabla expandida1" = Table.ExpandTableColumn(#"Otras columnas quitadas1", "Transformar archivo", Table.ColumnNames(#"Transformar archivo"(#"Archivo de ejemplo"))),
    #"Tipo cambiado" = Table.TransformColumnTypes(#"Columna de tabla expandida1",{{"Source.Name", type text}, {"ID_ORDEN", Int64.Type}, {"ID_ORDEN_SISTEMA_ORIGEN", type text}, {"ID_ACCION_ORDEN", type text}, {"NOMBRE_CLIENTE", type text}, {"DOCUMENTO_CLIENTE_TIPO", type text}, {"DOCUMENTO_CLIENTE", Int64.Type}, {"DOCUMENTO_CLIENTE_DV", type text}, {"USUARIO_CREACION", type text}, {"SISTEMA_ORIGEN", type text}, {"FECHA_CREACION", Int64.Type}, {"FECHA_ULTIMA_MODIFICACION", Int64.Type}, {"COMENTARIO_FLAG_ERROR", type text}, {"NOD_NOMBRE_ESTADO", type text}, {"ID_RASTREO", Int64.Type}, {"ORDER_KEY", type text}, {"ORDER_ACTION_KEY", type text}, {"ID_GUIA_DESPACHO", Int64.Type}, {"ESTADO_CONFIRMACION", Int64.Type}, {"CUSTOMER_KEY", Int64.Type}, {"SUBSCRIBER_KEY", Int64.Type}, {"MSISDN", Int64.Type}, {"SERIE", type text}, {"CHR_TIPO_RECURSO", type text}, {"NOD_ID_ALMACEN_ORIGEN", type text}, {"NOD_ID_MATERIAL", type text}, {"DESCRIP_MATERIAL", type text}, {"PMP", Int64.Type}, {"ORDER_STATUS_DESC", type text}, {"ORDER_ACTION_STATUS_DESC", type text}, {"ORDER_ITEM_TYPE_DESC", type text}, {"ORDER_ACTION_REASON_DESC", type text}, {"ORDER_ACTION_START_DATE", Int64.Type}, {"ORDER_ACTION_STEP_TYPE_NAME", type text}, {"ORDER_ACTION_CREATOR", type text}, {"ORDER_ACTION_LAST_UPDATOR", type text}, {"SALES_CHANNEL_NAME_INITIAL", type text}, {"CUSTOMER_TYPE_DESC", type text}, {"CUSTOMER_SUB_TYPE_DESC", type text}, {"PORTABILITY_TYPE", type any}, {"DELIVERY_METHOD_DESC", type text}, {"PAYMENT_CATEGORY_DESC", type text}, {"SAP_PRIMER_MOVIMIENTO", type text}, {"SAP_PRIMER_ORDEN", type text}, {"SAP_ULTIMO_MOVIMIENTO", type text}, {"SAP_ULTIMO_ORDEN", type text}, {"ESTADO_SAP", type text}, {"MARCA", type text}}),
    #"Filtrando por nombre con ;" = Table.SelectRows(#"Tipo cambiado", each Text.Contains([NOMBRE_CLIENTE], ";")),
    #"Columnas quitadas" = Table.RemoveColumns(#"Filtrando por nombre con ;",{"DOCUMENTO_CLIENTE_TIPO", "DOCUMENTO_CLIENTE", "DOCUMENTO_CLIENTE_DV", "USUARIO_CREACION", "SISTEMA_ORIGEN", "FECHA_CREACION", "FECHA_ULTIMA_MODIFICACION", "COMENTARIO_FLAG_ERROR", "NOD_NOMBRE_ESTADO", "ID_RASTREO", "ORDER_KEY", "ORDER_ACTION_KEY", "ID_GUIA_DESPACHO", "ESTADO_CONFIRMACION", "CUSTOMER_KEY", "SUBSCRIBER_KEY", "MSISDN", "SERIE", "CHR_TIPO_RECURSO", "NOD_ID_ALMACEN_ORIGEN", "NOD_ID_MATERIAL", "DESCRIP_MATERIAL", "PMP", "ORDER_STATUS_DESC", "ORDER_ACTION_STATUS_DESC", "ORDER_ITEM_TYPE_DESC", "ORDER_ACTION_REASON_DESC", "ORDER_ACTION_START_DATE", "ORDER_ACTION_STEP_TYPE_NAME", "ORDER_ACTION_CREATOR", "ORDER_ACTION_LAST_UPDATOR", "SALES_CHANNEL_NAME_INITIAL", "CUSTOMER_TYPE_DESC", "CUSTOMER_SUB_TYPE_DESC", "PORTABILITY_TYPE", "DELIVERY_METHOD_DESC", "PAYMENT_CATEGORY_DESC", "SAP_PRIMER_MOVIMIENTO", "SAP_PRIMER_ORDEN", "SAP_ULTIMO_MOVIMIENTO", "SAP_ULTIMO_ORDEN", "ESTADO_SAP", "MARCA"}),
    #"Dividir columna por delimitador" = Table.SplitColumn(#"Columnas quitadas", "NOMBRE_CLIENTE", Splitter.SplitTextByDelimiter(";", QuoteStyle.Csv), {"NOMBRE_CLIENTE", "DOCUMENTO_CLIENTE_TIPO", "DOCUMENTO_CLIENTE", "DOCUMENTO_CLIENTE_DV", "USUARIO_CREACION", "SISTEMA_ORIGEN", "FECHA_CREACION", "FECHA_ULTIMA_MODIFICACION", "COMENTARIO_FLAG_ERROR", "NOD_NOMBRE_ESTADO", "ID_RASTREO", "ORDER_KEY", "ORDER_ACTION_KEY", "ID_GUIA_DESPACHO", "ESTADO_CONFIRMACION", "CUSTOMER_KEY", "SUBSCRIBER_KEY", "MSISDN", "SERIE", "CHR_TIPO_RECURSO", "NOD_ID_ALMACEN_ORIGEN", "NOD_ID_MATERIAL", "DESCRIP_MATERIAL", "PMP", "ORDER_STATUS_DESC", "ORDER_ACTION_STATUS_DESC", "ORDER_ITEM_TYPE_DESC", "ORDER_ACTION_REASON_DESC", "ORDER_ACTION_START_DATE", "ORDER_ACTION_STEP_TYPE_NAME", "ORDER_ACTION_CREATOR", "ORDER_ACTION_LAST_UPDATOR", "SALES_CHANNEL_NAME_INITIAL", "CUSTOMER_TYPE_DESC", "CUSTOMER_SUB_TYPE_DESC", "PORTABILITY_TYPE", "DELIVERY_METHOD_DESC", "PAYMENT_CATEGORY_DESC", "SAP_PRIMER_MOVIMIENTO", "SAP_PRIMER_ORDEN", "SAP_ULTIMO_MOVIMIENTO", "SAP_ULTIMO_ORDEN", "ESTADO_SAP", "MARCA"})
in
    #"Dividir columna por delimitador"
```

Así mismo, se encontraron registros sin información, que demostraban que el registro estaba en su face inicial, por lo que, se apartaron del grupo de los registros que iban en fases más avanzadas, para transformarlos por separado:

```
let
    Origen = Folder.Files("E:\Reportes DEM\2024\Reportes_Unificados\ControlSeriesReporteDemCorporate\XLS Procesados\Enero"),
    #"Archivos ocultos filtrados1" = Table.SelectRows(Origen, each [Attributes]?[Hidden]? <> true),
    #"Invocar función personalizada1" = Table.AddColumn(#"Archivos ocultos filtrados1", "Transformar archivo", each #"Transformar archivo"([Content])),
    #"Columnas con nombre cambiado1" = Table.RenameColumns(#"Invocar función personalizada1", {"Name", "Source.Name"}),
    #"Otras columnas quitadas1" = Table.SelectColumns(#"Columnas con nombre cambiado1", {"Source.Name", "Transformar archivo"}),
    #"Columna de tabla expandida1" = Table.ExpandTableColumn(#"Otras columnas quitadas1", "Transformar archivo", Table.ColumnNames(#"Transformar archivo"(#"Archivo de ejemplo"))),
    #"Tipo cambiado" = Table.TransformColumnTypes(#"Columna de tabla expandida1",{{"Source.Name", type text}, {"ID_ORDEN", Int64.Type}, {"ID_ORDEN_SISTEMA_ORIGEN", type text}, {"ID_ACCION_ORDEN", type text}, {"NOMBRE_CLIENTE", type text}, {"DOCUMENTO_CLIENTE_TIPO", type text}, {"DOCUMENTO_CLIENTE", Int64.Type}, {"DOCUMENTO_CLIENTE_DV", type text}, {"USUARIO_CREACION", type text}, {"SISTEMA_ORIGEN", type text}, {"FECHA_CREACION", Int64.Type}, {"FECHA_ULTIMA_MODIFICACION", Int64.Type}, {"COMENTARIO_FLAG_ERROR", type text}, {"NOD_NOMBRE_ESTADO", type text}, {"ID_RASTREO", Int64.Type}, {"ORDER_KEY", type text}, {"ORDER_ACTION_KEY", type text}, {"ID_GUIA_DESPACHO", Int64.Type}, {"ESTADO_CONFIRMACION", Int64.Type}, {"CUSTOMER_KEY", Int64.Type}, {"SUBSCRIBER_KEY", Int64.Type}, {"MSISDN", Int64.Type}, {"SERIE", type text}, {"CHR_TIPO_RECURSO", type text}, {"NOD_ID_ALMACEN_ORIGEN", type text}, {"NOD_ID_MATERIAL", type text}, {"DESCRIP_MATERIAL", type text}, {"PMP", Int64.Type}, {"ORDER_STATUS_DESC", type text}, {"ORDER_ACTION_STATUS_DESC", type text}, {"ORDER_ITEM_TYPE_DESC", type text}, {"ORDER_ACTION_REASON_DESC", type text}, {"ORDER_ACTION_START_DATE", Int64.Type}, {"ORDER_ACTION_STEP_TYPE_NAME", type text}, {"ORDER_ACTION_CREATOR", type text}, {"ORDER_ACTION_LAST_UPDATOR", type text}, {"SALES_CHANNEL_NAME_INITIAL", type text}, {"CUSTOMER_TYPE_DESC", type text}, {"CUSTOMER_SUB_TYPE_DESC", type text}, {"PORTABILITY_TYPE", type any}, {"DELIVERY_METHOD_DESC", type text}, {"PAYMENT_CATEGORY_DESC", type text}, {"SAP_PRIMER_MOVIMIENTO", type text}, {"SAP_PRIMER_ORDEN", type text}, {"SAP_ULTIMO_MOVIMIENTO", type text}, {"SAP_ULTIMO_ORDEN", type text}, {"ESTADO_SAP", type text}, {"MARCA", type text}}),
    #"Filtrando por tipo de operación" = Table.SelectRows(#"Tipo cambiado", each ([ORDER_ITEM_TYPE_DESC] <> "BULK PROVIDE SMB SUBSCRIPTION")),
    #"Reemplazando tipo de operación en blanco" = Table.ReplaceValue(#"Filtrando por tipo de operación","","ORDEN NO CREADA EN BELIEVE",Replacer.ReplaceValue,{"ORDER_ITEM_TYPE_DESC"}),
    #"Columnas quitadas" = Table.RemoveColumns(#"Reemplazando tipo de operación en blanco",{"Source.Name", "ID_ORDEN", "NOMBRE_CLIENTE", "DOCUMENTO_CLIENTE_TIPO", "DOCUMENTO_CLIENTE", "DOCUMENTO_CLIENTE_DV", "USUARIO_CREACION", "SISTEMA_ORIGEN", "FECHA_CREACION", "FECHA_ULTIMA_MODIFICACION", "COMENTARIO_FLAG_ERROR", "NOD_NOMBRE_ESTADO", "ID_RASTREO", "ID_GUIA_DESPACHO", "ESTADO_CONFIRMACION", "CUSTOMER_KEY", "SUBSCRIBER_KEY", "MSISDN", "SERIE", "CHR_TIPO_RECURSO", "NOD_ID_ALMACEN_ORIGEN", "NOD_ID_MATERIAL", "DESCRIP_MATERIAL", "PMP", "ORDER_ACTION_REASON_DESC", "ORDER_ACTION_START_DATE", "ORDER_ACTION_CREATOR", "ORDER_ACTION_LAST_UPDATOR", "SALES_CHANNEL_NAME_INITIAL", "CUSTOMER_TYPE_DESC", "CUSTOMER_SUB_TYPE_DESC", "PORTABILITY_TYPE", "DELIVERY_METHOD_DESC", "PAYMENT_CATEGORY_DESC", "SAP_PRIMER_MOVIMIENTO", "SAP_PRIMER_ORDEN", "SAP_ULTIMO_MOVIMIENTO", "SAP_ULTIMO_ORDEN", "ESTADO_SAP", "MARCA"}),
    #"Filtrando por ""Orden no creada en Believe""" = Table.SelectRows(#"Columnas quitadas", each ([ORDER_ITEM_TYPE_DESC] = "ORDEN NO CREADA EN BELIEVE")),
    #"Duplicados quitados" = Table.Distinct(#"Filtrando por ""Orden no creada en Believe""", {"ID_ORDEN_SISTEMA_ORIGEN"}),
    #"Valor reemplazado" = Table.ReplaceValue(#"Duplicados quitados","","ORDEN NO CREADA EN BELIEVE",Replacer.ReplaceValue,{"ORDER_ACTION_STATUS_DESC"})
in
    #"Valor reemplazado"
```

Esto se realizó para todos los meses del año y luego, se consolidaron en una sola consulta para todo el año: 

```
let
    Origen = Table.Combine({Diciembre_Sin_orden, Enero_Sin_orden, Febrero_Sin_orden, Marzo_Sin_orden, Abril_Sin_orden, Mayo_Sin_orden, Junio_Sin_orden, Julio_Sin_orden, Agosto_Sin_orden, Septiembre_Sin_orden, Octubre_Sin_orden, Noviembre_Sin_orden}),
    #"Duplicados quitados" = Table.Distinct(Origen, {"ID_ORDEN_SISTEMA_ORIGEN"}),
    #"Consultas combinadas" = Table.NestedJoin(#"Duplicados quitados", {"ID_ORDEN_SISTEMA_ORIGEN"}, Canceladas_Consolidado, {"ID_ORDEN_SISTEMA_ORIGEN"}, "Canceladas_Consolidado", JoinKind.LeftOuter),
    #"Se expandió Canceladas_Consolidado" = Table.ExpandTableColumn(#"Consultas combinadas", "Canceladas_Consolidado", {"ORDER_STATUS_DESC"}, {"Canceladas_Consolidado.ORDER_STATUS_DESC"}),
    #"Filas filtradas" = Table.SelectRows(#"Se expandió Canceladas_Consolidado", each ([Canceladas_Consolidado.ORDER_STATUS_DESC] = null)),
    #"Columnas quitadas" = Table.RemoveColumns(#"Filas filtradas",{"Canceladas_Consolidado.ORDER_STATUS_DESC"}),
    #"Consultas combinadas1" = Table.NestedJoin(#"Columnas quitadas", {"ID_ORDEN_SISTEMA_ORIGEN"}, Completadas_Consolidado, {"ID_ORDEN_SISTEMA_ORIGEN"}, "Completadas_Consolidado", JoinKind.LeftOuter),
    #"Se expandió Completadas_Consolidado" = Table.ExpandTableColumn(#"Consultas combinadas1", "Completadas_Consolidado", {"ORDER_STATUS_DESC"}, {"Completadas_Consolidado.ORDER_STATUS_DESC"}),
    #"Filas filtradas1" = Table.SelectRows(#"Se expandió Completadas_Consolidado", each ([Completadas_Consolidado.ORDER_STATUS_DESC] = null)),
    #"Columnas quitadas1" = Table.RemoveColumns(#"Filas filtradas1",{"Completadas_Consolidado.ORDER_STATUS_DESC"}),
    #"Consultas combinadas2" = Table.NestedJoin(#"Columnas quitadas1", {"ID_ORDEN_SISTEMA_ORIGEN"}, #"Consolidado Año", {"ID_ORDEN_SISTEMA_ORIGEN"}, "Consolidado Año", JoinKind.LeftOuter),
    #"Se expandió Consolidado Año" = Table.ExpandTableColumn(#"Consultas combinadas2", "Consolidado Año", {"ORDER_STATUS_DESC"}, {"Consolidado Año.ORDER_STATUS_DESC"}),
    #"Filas filtradas2" = Table.SelectRows(#"Se expandió Consolidado Año", each ([Consolidado Año.ORDER_STATUS_DESC] = null)),
    #"Columnas quitadas2" = Table.RemoveColumns(#"Filas filtradas2",{"Consolidado Año.ORDER_STATUS_DESC"})
in
    #"Columnas quitadas2"
```

Paralelo a estas transformaciones, se referenciaron las consultas leídas en el primer bloque de código, y se filtraron por su estado, denotado en el campo ORDER_ACTION_STATUS_DESC. Se comenzó por los registros que tuvieran en el campo "ORDER_ACTION_STATUS_DESC", el valor "TERMINADO". Este valor, denota que los registros ya no sufrirán modificaciones con el pasar de los días, puesto que ya alcanzaron un estado final, por lo que, se transformaron por separado, eliminando los duplicados, considerando la columna"ORDER_ACTION_KEY", lo cual ayudó a simplificar la consulta y a eliminar redundancias en la información: 

```
let
    Origen = Enero_Original,
    #"Filtrando por TERMINADO" = Table.SelectRows(Origen, each ([ORDER_ACTION_STATUS_DESC] = "TERMINADO")),
    #"Duplicados quitados" = Table.Distinct(#"Filtrando por TERMINADO", {"ORDER_ACTION_KEY"})
in
    #"Duplicados quitados"
```

Esto se realizó para cada uno de los meses y luego se consolidó en una consulta anual: 

```
let
    Origen = Table.Combine({Completadas_Enero, Completadas_Febrero, Completadas_Marzo, Completadas_Abril, Completadas_Mayo, Completadas_Junio, Completadas_Julio, Completadas_Agosto, Completadas_Septiembre, Completadas_Octubre, Completadas_Diciembre, Completadas_Noviembre}),
    #"Duplicados quitados" = Table.Distinct(Origen, {"ORDER_ACTION_KEY"})
in
    #"Duplicados quitados"
```

Lo mismo se realizó con los registros que tuvieran en el campo "ORDER_ACTION_STATUS_DESC", el valor "CANCELADO", los cuales, al igual que los anteriores, no sufrirán modificaciones con el pasar de los días, puesto que ya alcanzaron un estado final, por lo que, se transformaron por separado, eliminando los duplicados, considerando la columna"ORDER_ACTION_KEY", lo cual ayudó a simplificar la consulta y a eliminar redundancias en la información:

```
let
Origen = Enero_Original,
    #"Filtrando por TERMINADO" = Table.SelectRows(Origen, each ([ORDER_ACTION_STATUS_DESC] = "CANCELADO")),
    #"Duplicados quitados" = Table.Distinct(#"Filtrando por TERMINADO", {"ORDER_ACTION_KEY"})
in
    #"Duplicados quitados"
```

Esto se realizó para cada uno de los meses y luego se consolidó en una consulta anual: 

```
let
    Origen = Table.Combine({Canceladas_Enero, Canceladas_Febrero, Canceladas_Marzo, Canceladas_Abril, Canceladas_Mayo, Canceladas_Junio, Canceladas_Julio, Canceladas_Agosto, Canceladas_Septiembre, Canceladas_Octubre, Canceladas_Noviembre, Canceladas_Diciembre}),
    #"Duplicados quitados" = Table.Distinct(Origen, {"ORDER_ACTION_KEY"})
in
    #"Duplicados quitados"
```

Al mismo tiempo, se generaron consultas para aquellos registros donde "ORDER_ACTION_STATUS_DESC", fueran diferentes a "TERMINADO" o "CANCELADO", lo cual denotaba que dichos registros, si tendrían modificaciones en días futuros, debido a que no habían alcanzado un estado final, por lo que, se transformaron de una forma diferente, agregando un identificador de actividad en una nueva columna llamada "Valor_Estado", el cual almacenaba un valor numérico, que era mayor si el estado era más reciente y uno menor, si el estado era más antiguó.

Una vez se definió esto, se ordenaron los registros por la columna "Valor_Estado", de mayor a menor, guardando dicho orden en memoria con un "Table.Buffer" y eliminando los duplicados presentes en la columna "ORDER_ACTION_KEY". Con esto, se eliminaron los registros que tenían un "Valor_Estado" más antiguo, siempre que ya tuviera uno más reciente. Así, se simplificó la consulta y se eliminaron las redundancias en la información.

```
let
    Origen = Enero_Original,
    #"Filtrando por estado de la orden" = Table.SelectRows(Origen, each [ORDER_ACTION_STATUS_DESC] <> "CANCELADO" and [ORDER_ACTION_STATUS_DESC] <> "TERMINADO"),
    #"Agregando indicador de actividad" = Table.AddColumn(#"Filtrando por estado de la orden", "Valor_Estado", each if [ORDER_ACTION_STEP_TYPE_NAME] = "" then 1 else if [ORDER_ACTION_STEP_TYPE_NAME] = "HANDLE SAP LOGISTICS" then 2 else if [ORDER_ACTION_STEP_TYPE_NAME] = "ACTIVATE RESOURCES" then 3 else if [ORDER_ACTION_STEP_TYPE_NAME] = "REQUEST ORDER FULFILLMENT" then 4 else if [ORDER_ACTION_STEP_TYPE_NAME] = "HANDLE FULFILLMENT RESPONSE" then 5 else if [ORDER_ACTION_STEP_TYPE_NAME] = "RELEASE RESOURCES" then 6 else if [ORDER_ACTION_STEP_TYPE_NAME] = "NOTIFY BILLING" then 7 else if [ORDER_ACTION_STEP_TYPE_NAME] = "NOTIFY ESB" then 8 else if [ORDER_ACTION_STEP_TYPE_NAME] = "FINALIZE ORDER" then 9 else if [ORDER_ACTION_STEP_TYPE_NAME] = "CLOSE ORDER ACTION" then 10 else "ACTIVIDAD NO REGISTRADA"),
    #"Ordenando de mayor a menor el indicador de actividad" = Table.Buffer(Table.Sort(#"Agregando indicador de actividad",{{"Valor_Estado", Order.Descending}})),
    #"Duplicados quitados" = Table.Distinct(#"Ordenando de mayor a menor el indicador de actividad", {"ORDER_ACTION_KEY"}),
    #"Eliminando indicador de actividad" = Table.RemoveColumns(#"Duplicados quitados",{"Valor_Estado"})
in
    #"Eliminando indicador de actividad"
```

Esto se realizó para cada uno de los meses del año y se consolidó luego en un archivo anual:

```
let
    Origen = Table.Combine({Enero, Febrero, Marzo, Abril, Mayo, Junio, Julio, Agosto, Septiembre, Octubre, Noviembre, Diciembre}),
    #"Agregando indicador de actividad" = Table.AddColumn(Origen, "Valor_Estado", each if [ORDER_ACTION_STEP_TYPE_NAME] = "" then 1 else if [ORDER_ACTION_STEP_TYPE_NAME] = "HANDLE SAP LOGISTICS" then 2 else if [ORDER_ACTION_STEP_TYPE_NAME] = "ACTIVATE RESOURCES" then 3 else if [ORDER_ACTION_STEP_TYPE_NAME] = "REQUEST ORDER FULFILLMENT" then 4 else if [ORDER_ACTION_STEP_TYPE_NAME] = "HANDLE FULFILLMENT RESPONSE" then 5 else if [ORDER_ACTION_STEP_TYPE_NAME] = "RELEASE RESOURCES" then 6 else if [ORDER_ACTION_STEP_TYPE_NAME] = "NOTIFY BILLING" then 7 else if [ORDER_ACTION_STEP_TYPE_NAME] = "NOTIFY ESB" then 8 else if [ORDER_ACTION_STEP_TYPE_NAME] = "FINALIZE ORDER" then 9 else if [ORDER_ACTION_STEP_TYPE_NAME] = "CLOSE ORDER ACTION" then 10 else "ACTIVIDAD NO REGISTRADA"),
    #"Ordenando de mayor a menor el indicador de actividad" = Table.Buffer(Table.Sort(#"Agregando indicador de actividad",{{"Valor_Estado", Order.Descending}})),
    #"Duplicados quitados" = Table.Distinct(#"Ordenando de mayor a menor el indicador de actividad", {"ORDER_ACTION_KEY"}),
    #"Eliminando indicador de actividad" = Table.RemoveColumns(#"Duplicados quitados",{"Valor_Estado"}),
    #"Consultas combinadas" = Table.NestedJoin(#"Eliminando indicador de actividad", {"ORDER_ACTION_KEY"}, Canceladas_Consolidado, {"ORDER_ACTION_KEY"}, "Canceladas_Consolidado", JoinKind.LeftOuter),
    #"Se expandió Canceladas_Consolidado" = Table.ExpandTableColumn(#"Consultas combinadas", "Canceladas_Consolidado", {"ORDER_STATUS_DESC"}, {"Canceladas_Consolidado.ORDER_STATUS_DESC"}),
    #"Filas filtradas" = Table.SelectRows(#"Se expandió Canceladas_Consolidado", each ([Canceladas_Consolidado.ORDER_STATUS_DESC] = null)),
    #"Columnas quitadas" = Table.RemoveColumns(#"Filas filtradas",{"Canceladas_Consolidado.ORDER_STATUS_DESC"}),
    #"Consultas combinadas1" = Table.NestedJoin(#"Columnas quitadas", {"ORDER_ACTION_KEY"}, Completadas_Consolidado, {"ORDER_ACTION_KEY"}, "Completadas_Consolidado", JoinKind.LeftOuter),
    #"Se expandió Completadas_Consolidado" = Table.ExpandTableColumn(#"Consultas combinadas1", "Completadas_Consolidado", {"ORDER_STATUS_DESC"}, {"Completadas_Consolidado.ORDER_STATUS_DESC"}),
    #"Filas filtradas1" = Table.SelectRows(#"Se expandió Completadas_Consolidado", each ([Completadas_Consolidado.ORDER_STATUS_DESC] = null)),
    #"Columnas quitadas1" = Table.RemoveColumns(#"Filas filtradas1",{"Completadas_Consolidado.ORDER_STATUS_DESC"})
in
    #"Columnas quitadas1"
```

Paralelo a todas estas transformaciones, se realizó una consulta nueva a los mismos orígenes, que mantenía solo las columnas que se mantenían fijas durante todo el ciclo de vida de los registros. Estas columnas habían sido eliminadas ya de las transformaciones anteriores, las cuales mantuvieron solo los campos que si variaban con el pasar de los días. Esto permitió eliminar las redundancias y aprovechar de mejor forma los recursos del sistema:

```
let
    Origen = Folder.Files("E:\Reportes DEM\2024\Reportes_Unificados\ControlSeriesReporteDemCorporate\XLS Procesados\Enero"),
    #"Archivos ocultos filtrados1" = Table.SelectRows(Origen, each [Attributes]?[Hidden]? <> true),
    #"Invocar función personalizada1" = Table.AddColumn(#"Archivos ocultos filtrados1", "Transformar archivo", each #"Transformar archivo"([Content])),
    #"Columnas con nombre cambiado1" = Table.RenameColumns(#"Invocar función personalizada1", {"Name", "Source.Name"}),
    #"Otras columnas quitadas1" = Table.SelectColumns(#"Columnas con nombre cambiado1", {"Source.Name", "Transformar archivo"}),
    #"Columna de tabla expandida1" = Table.ExpandTableColumn(#"Otras columnas quitadas1", "Transformar archivo", Table.ColumnNames(#"Transformar archivo"(#"Archivo de ejemplo"))),
    #"Tipo cambiado" = Table.TransformColumnTypes(#"Columna de tabla expandida1",{{"Source.Name", type text}, {"ID_ORDEN", Int64.Type}, {"ID_ORDEN_SISTEMA_ORIGEN", type text}, {"ID_ACCION_ORDEN", type text}, {"NOMBRE_CLIENTE", type text}, {"DOCUMENTO_CLIENTE_TIPO", type text}, {"DOCUMENTO_CLIENTE", Int64.Type}, {"DOCUMENTO_CLIENTE_DV", type text}, {"USUARIO_CREACION", type text}, {"SISTEMA_ORIGEN", type text}, {"FECHA_CREACION", Int64.Type}, {"FECHA_ULTIMA_MODIFICACION", Int64.Type}, {"COMENTARIO_FLAG_ERROR", type text}, {"NOD_NOMBRE_ESTADO", type text}, {"ID_RASTREO", Int64.Type}, {"ORDER_KEY", type text}, {"ORDER_ACTION_KEY", type text}, {"ID_GUIA_DESPACHO", Int64.Type}, {"ESTADO_CONFIRMACION", Int64.Type}, {"CUSTOMER_KEY", Int64.Type}, {"SUBSCRIBER_KEY", Int64.Type}, {"MSISDN", Int64.Type}, {"SERIE", Int64.Type}, {"CHR_TIPO_RECURSO", type text}, {"NOD_ID_ALMACEN_ORIGEN", type text}, {"NOD_ID_MATERIAL", type text}, {"DESCRIP_MATERIAL", type text}, {"PMP", Int64.Type}, {"ORDER_STATUS_DESC", type text}, {"ORDER_ACTION_STATUS_DESC", type text}, {"ORDER_ITEM_TYPE_DESC", type text}, {"ORDER_ACTION_REASON_DESC", type text}, {"ORDER_ACTION_START_DATE", Int64.Type}, {"ORDER_ACTION_STEP_TYPE_NAME", type text}, {"ORDER_ACTION_CREATOR", type text}, {"ORDER_ACTION_LAST_UPDATOR", type text}, {"SALES_CHANNEL_NAME_INITIAL", type text}, {"CUSTOMER_TYPE_DESC", type text}, {"CUSTOMER_SUB_TYPE_DESC", type text}, {"PORTABILITY_TYPE", type any}, {"DELIVERY_METHOD_DESC", type text}, {"PAYMENT_CATEGORY_DESC", type text}, {"SAP_PRIMER_MOVIMIENTO", type text}, {"SAP_PRIMER_ORDEN", type text}, {"SAP_ULTIMO_MOVIMIENTO", type text}, {"SAP_ULTIMO_ORDEN", type text}, {"ESTADO_SAP", type text}, {"MARCA", type text}}),
    #"Anexando Enero con ;" = Table.Combine({#"Tipo cambiado", #"Enero con ;"}),
    #"Columnas quitadas" = Table.RemoveColumns(#"Anexando Enero con ;",{"Source.Name", "ID_ORDEN", "ID_ACCION_ORDEN", "DOCUMENTO_CLIENTE_TIPO", "SISTEMA_ORIGEN", "FECHA_ULTIMA_MODIFICACION", "COMENTARIO_FLAG_ERROR", "NOD_NOMBRE_ESTADO", "ID_RASTREO", "ORDER_KEY", "ORDER_ACTION_KEY", "ID_GUIA_DESPACHO", "ESTADO_CONFIRMACION", "SUBSCRIBER_KEY", "MSISDN", "SERIE", "CHR_TIPO_RECURSO", "NOD_ID_MATERIAL", "DESCRIP_MATERIAL", "PMP", "ORDER_STATUS_DESC", "ORDER_ACTION_STATUS_DESC", "ORDER_ACTION_REASON_DESC", "ORDER_ACTION_START_DATE", "ORDER_ACTION_STEP_TYPE_NAME", "ORDER_ACTION_CREATOR", "ORDER_ACTION_LAST_UPDATOR", "SALES_CHANNEL_NAME_INITIAL", "CUSTOMER_TYPE_DESC", "CUSTOMER_SUB_TYPE_DESC", "PORTABILITY_TYPE", "DELIVERY_METHOD_DESC", "PAYMENT_CATEGORY_DESC", "SAP_PRIMER_MOVIMIENTO", "SAP_PRIMER_ORDEN", "SAP_ULTIMO_MOVIMIENTO", "SAP_ULTIMO_ORDEN", "ESTADO_SAP", "MARCA", "NOMBRE_CLIENTE", "ORDER_ITEM_TYPE_DESC"}),
    #"Duplicados quitados" = Table.Distinct(#"Columnas quitadas", {"ID_ORDEN_SISTEMA_ORIGEN"})
in
    #"Duplicados quitados"
```

Esta acción se realiza para todos los meses del año y se consolida en una sola consulta anual, con el siguiente código:

```
let
    Origen = Table.Combine({Enero_Dimensión, Febrero_Dimensión, Marzo_Dimensión, Abril_Dimensión, Mayo_Dimensión, Junio_Dimensión, Julio_Dimensión, Agosto_Dimensión, Septiembre_Dimensión, Octubre_Dimensión, Noviembre_Dimensión, Diciembre_Dimensión}),
    #"Duplicados quitados" = Table.Distinct(Origen, {"ID_ORDEN_SISTEMA_ORIGEN"}),
    #"Tipo cambiado" = Table.TransformColumnTypes(#"Duplicados quitados",{{"DOCUMENTO_CLIENTE", type text}}),
    #"Agregando RUT" = Table.AddColumn(#"Tipo cambiado", "RUT", each [DOCUMENTO_CLIENTE] &"-"& [DOCUMENTO_CLIENTE_DV]),
    #"Primeros caracteres insertados" = Table.AddColumn(#"Agregando RUT", "Primeros caracteres", each Text.Start(Text.From([FECHA_CREACION], "es-CL"), 4), type text),
    #"Últimos caracteres insertados" = Table.AddColumn(#"Primeros caracteres insertados", "Últimos caracteres", each Text.End(Text.From([FECHA_CREACION], "es-CL"), 4), type text),
    #"Primeros caracteres insertados1" = Table.AddColumn(#"Últimos caracteres insertados", "Primeros caracteres.1", each Text.Start([Últimos caracteres], 2), type text),
    #"Últimos caracteres insertados1" = Table.AddColumn(#"Primeros caracteres insertados1", "Últimos caracteres.1", each Text.End([Últimos caracteres], 2), type text),
    #"Personalizada agregada" = Table.AddColumn(#"Últimos caracteres insertados1", "Fecha_Creacion_Orden", each [Últimos caracteres.1] &"-"& [Primeros caracteres.1] &"-"&[Primeros caracteres]),
    #"Tipo cambiado1" = Table.TransformColumnTypes(#"Personalizada agregada",{{"Fecha_Creacion_Orden", type date}, {"RUT", type text}}),
    #"Columnas quitadas" = Table.RemoveColumns(#"Tipo cambiado1",{"DOCUMENTO_CLIENTE", "DOCUMENTO_CLIENTE_DV", "FECHA_CREACION", "Primeros caracteres", "Últimos caracteres", "Primeros caracteres.1", "Últimos caracteres.1"})
in
    #"Columnas quitadas"
```

Por último, se creó una tabla de hechos final, la cual consolidó todos las consultas anteriores ya transformadas:

```
let
    Origen = Table.Combine({#"Consolidado Año", Canceladas_Consolidado}),
    #"Anexando completadas" = Table.Combine({Origen, Completadas_Consolidado}),
    #"Consulta anexada" = Table.Combine({#"Anexando completadas", Consolidado_Sin_orden}),
    #"Consultas combinadas" = Table.NestedJoin(#"Consulta anexada", {"ID_ORDEN_SISTEMA_ORIGEN"}, Consolidado_Dimensión, {"ID_ORDEN_SISTEMA_ORIGEN"}, "Consolidado_Dimensión", JoinKind.LeftOuter),
    #"Se expandió Consolidado_Dimensión" = Table.ExpandTableColumn(#"Consultas combinadas", "Consolidado_Dimensión", {"USUARIO_CREACION", "CUSTOMER_KEY", "NOD_ID_ALMACEN_ORIGEN", "RUT", "Fecha_Creacion_Orden"}, {"USUARIO_CREACION", "CUSTOMER_KEY", "ID_ALMACEN_ORIGEN", "RUT", "FECHA_CREACION"})
in
    #"Se expandió Consolidado_Dimensión"
```

Con este proyecto se alcanza a tener una base de datos anual final, con todos los datos limpios y con una estructura establecida, que permitirá en los siguientes proyectos, realizar visualizaciones de datos que mejoraran la toma de decisiones empresariales y mi capacidad para abordar desafíos complejos en el ámbito de la analítica de datos, proporcionando soluciones efectivas.
