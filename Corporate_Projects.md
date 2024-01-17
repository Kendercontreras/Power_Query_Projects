**Proyecto: Transformación, limpieza y consolidación de Datos**

Descripción del Proyecto:

El proyecto de Transformación, limpieza y consolidación de Datos, se centró en optimizar y unificar el proceso de manejo de datos en la generación de reportes para una empresa corporativa. Como analista de datos, mi objetivo era mejorar la eficiencia en la transformación de datos, permitiendo una toma de decisiones más informadas. 🚀

Objetivos y Alcance 
Los objetivos principales incluyeron la consolidación y limpieza de datos recibidos diariamente, provenientes de diferentes fuentes y formatos, para generar informes unificados. El alcance abarcó desde la extracción hasta la transformación de datos en Power BI, utilizando herramientas como Power Query y su editor avanzado. 📊

Desafíos y Soluciones 
El proyecto presentó desafíos significativos, como la gestión de grandes volúmenes de datos heterogéneos y la necesidad de mantener la coherencia en los informes. Abordé estos desafíos mediante la implementación de consultas eficientes, la eliminación de duplicados y la integración de información clave en un único repositorio. 💡

Herramientas Utilizadas 
Durante el desarrollo del proyecto, utilicé la herramienta Power Query en Power BI, para la limpieza y transformación de datos. 🛠️

Estructura del Proyecto 
La estructura del proyecto se diseñó para facilitar la comprensión y mantenimiento del flujo de datos. Se creó una consulta consolidada con todos los archivos diarios, hasta consolidar todo un año calendario. 🗂️

Resultados y Beneficios 
La consolidación de datos permitió generar informes unificados y coherentes, proporcionando una visión más clara y eficiente para la toma de decisiones. Los reportes resultantes facilitaron la identificación de patrones y tendencias, mejorando la capacidad de la empresa para realizar análisis estratégicos. 📈

Proceso de Desarrollo 
Desde la identificación de los requisitos hasta la implementación y validación, el proceso de desarrollo se llevó a cabo de manera estructurada, asegurando la calidad y consistencia de los datos a lo largo del proyecto. 🔄

Códigos M
Los códigos M desarrollados para el proyecto, se centran en la eficiencia y claridad en la manipulación de datos. A continuación, se detallan los códigos utilizados, que ilustran la lógica aplicada en la limpieza y transformación de datos. 🧑‍💻

Uno de los primeros desafíos presentados, fue al momento de extraer los archivos desde sus respectivas bases de datos, existía un error en los registros porque, dejaba todos los datos agrupados en la columna "NOMBRE_CLIENTE". Ejemplo: En los registros que debían haber sido:
"DOCUMENTO_CLIENTE_TIPO" = "RUT"
"DOCUMENTO_CLIENTE" = "11111111"
"DOCUMENTO_CLIENTE_DV" = "1"

Los deja como: 

"NOMBRE_CLIENTE" = "Nombre;RUT,11111111,1"
"DOCUMENTO_CLIENTE_TIPO" = ""
"DOCUMENTO_CLIENTE" = ""
"DOCUMENTO_CLIENTE_DV" = ""

Es por eso, que este tipo de datos se filtra y se transforman por separado, corrigiendo los errores de extracción, mediante el siguiente código:

``` let
    Origen = Folder.Files("E:\Reportes DEM\2024\Reportes_Unificados\ReporteDemCorporate\XLS Procesados\Enero"),
    #"Archivos ocultos filtrados1" = Table.SelectRows(Origen, each [Attributes]?[Hidden]? <> true),
    #"Invocar función personalizada1" = Table.AddColumn(#"Archivos ocultos filtrados1", "Transformar archivo (3)", each #"Transformar archivo (3)"([Content])),
    #"Columnas con nombre cambiado1" = Table.RenameColumns(#"Invocar función personalizada1", {"Name", "Source.Name"}),
    #"Otras columnas quitadas1" = Table.SelectColumns(#"Columnas con nombre cambiado1", {"Source.Name", "Transformar archivo (3)"}),
    #"Columna de tabla expandida1" = Table.ExpandTableColumn(#"Otras columnas quitadas1", "Transformar archivo (3)", Table.ColumnNames(#"Transformar archivo (3)"(#"Archivo de ejemplo (3)"))),
    #"Tipo cambiado" = Table.TransformColumnTypes(#"Columna de tabla expandida1",{{"ID_ORDEN_SISTEMA_ORIGEN", type text}, {"ID_ACCION_ORDEN", type text}, {"NOMBRE_CLIENTE", type text}, {"DOCUMENTO_CLIENTE_TIPO", type text}, {"DOCUMENTO_CLIENTE", type text}, {"DOCUMENTO_CLIENTE_DV", type text}, {"USUARIO_CREACION", type text}, {"SISTEMA_ORIGEN", type text}, {"CANAL", type text}, {"TIPO_DE_REQUERIMIENTO", type text}, {"ES_PORTABILIDAD", type text}, {"ESTADO", type text}, {"FECHA_HORA_DEL_ESTADO", type datetime}, {"CODIGO_SKU", type text}, {"DESCRIPTIVO_SKU", type text}, {"TIPO_DE_RECURSO", type text}, {"TIPO_DE_PLAN", type text}, {"ES_DEVOLUCION", type text}, {"SERIE", Int64.Type}, {"ID_ORDEN_SIS_ORIGEN_APP", type text}, {"COMENTARIO_FLAG_ERROR", type text}, {"NOD_REGION", type text}, {"NOD_COMUNA", type text}, {"NOMBRE_CALLE", type text}, {"NUMERO_CALLE", Int64.Type}}),
    #"Filtro" = Table.SelectRows(#"Tipo cambiado", each Text.Contains([NOMBRE_CLIENTE], ";")),
    #"Columnas quitadas" = Table.RemoveColumns(Filtro,{"Source.Name", "DOCUMENTO_CLIENTE_TIPO", "DOCUMENTO_CLIENTE", "DOCUMENTO_CLIENTE_DV", "USUARIO_CREACION", "SISTEMA_ORIGEN", "CANAL", "TIPO_DE_REQUERIMIENTO", "ES_PORTABILIDAD", "ESTADO", "FECHA_HORA_DEL_ESTADO", "CODIGO_SKU", "DESCRIPTIVO_SKU", "TIPO_DE_RECURSO", "TIPO_DE_PLAN", "ES_DEVOLUCION", "SERIE", "ID_ORDEN_SIS_ORIGEN_APP", "COMENTARIO_FLAG_ERROR", "NOD_REGION", "NOD_COMUNA", "NOMBRE_CALLE", "NUMERO_CALLE"}),
    #"Dividir columna por delimitador1" = Table.SplitColumn(#"Columnas quitadas", "NOMBRE_CLIENTE", Splitter.SplitTextByDelimiter(";", QuoteStyle.Csv), {"NOMBRE_CLIENTE", "DOCUMENTO_CLIENTE_TIPO", "DOCUMENTO_CLIENTE", "DOCUMENTO_CLIENTE_DV", "USUARIO_CREACION", "SISTEMA_ORIGEN", "CANAL", "TIPO_DE_REQUERIMIENTO", "ES_PORTABILIDAD", "ESTADO", "FECHA_HORA_DEL_ESTADO", "CODIGO_SKU", "DESCRIPTIVO_SKU", "TIPO_DE_RECURSO", "TIPO_DE_PLAN", "ES_DEVOLUCION", "SERIE", "ID_ORDEN_SIS_ORIGEN_APP", "COMENTARIO_FLAG_ERROR", "NOD_REGION", "NOD_COMUNA", "NOMBRE_CALLE", "NUMERO_CALLE"}),
    #"Tipo cambiado1" = Table.TransformColumnTypes(#"Dividir columna por delimitador1",{{"FECHA_HORA_DEL_ESTADO", type datetime}})
in
    #"Tipo cambiado1"
```

Al mismo tiempo, se realiza la Lectura y transformación de archivos originales, en los cuales se excluyen todos aquellos registros incluidos en la consulta anterior. Es decir, todos los registros que en la columna "NOMBRE_CLIENTE" no existiera el valor “;”. Posteriormente, se anexó la consulta resultante del código anterior, con los datos estructurados y limpios. A partir de ese punto, se realizaron las transformaciones correspondientes para realizar la limpieza de los datos de este reporte. Todo esto se llevó a cabo con el siguiente código:

```
let
    Origen = Folder.Files("E:\Reportes DEM\2024\Reportes_Unificados\ReporteDemCorporate\XLS Procesados\Enero"),
    #"Archivos ocultos filtrados1" = Table.SelectRows(Origen, each [Attributes]?[Hidden]? <> true),
    #"Invocar función personalizada1" = Table.AddColumn(#"Archivos ocultos filtrados1", "Transformar archivo (3)", each #"Transformar archivo (3)"([Content])),
    #"Columnas con nombre cambiado1" = Table.RenameColumns(#"Invocar función personalizada1", {"Name", "Source.Name"}),
    #"Otras columnas quitadas1" = Table.SelectColumns(#"Columnas con nombre cambiado1", {"Source.Name", "Transformar archivo (3)"}),
    #"Columna de tabla expandida1" = Table.ExpandTableColumn(#"Otras columnas quitadas1", "Transformar archivo (3)", Table.ColumnNames(#"Transformar archivo (3)"(#"Archivo de ejemplo (3)"))),
    #"Tipo cambiado" = Table.TransformColumnTypes(#"Columna de tabla expandida1",{{"ID_ORDEN_SISTEMA_ORIGEN", type text}, {"ID_ACCION_ORDEN", type text}, {"NOMBRE_CLIENTE", type text}, {"DOCUMENTO_CLIENTE_TIPO", type text}, {"DOCUMENTO_CLIENTE", type text}, {"DOCUMENTO_CLIENTE_DV", type text}, {"USUARIO_CREACION", type text}, {"SISTEMA_ORIGEN", type text}, {"CANAL", type text}, {"TIPO_DE_REQUERIMIENTO", type text}, {"ES_PORTABILIDAD", type text}, {"ESTADO", type text}, {"FECHA_HORA_DEL_ESTADO", type datetime}, {"CODIGO_SKU", type text}, {"DESCRIPTIVO_SKU", type text}, {"TIPO_DE_RECURSO", type text}, {"TIPO_DE_PLAN", type text}, {"ES_DEVOLUCION", type text}, {"SERIE", type text}, {"ID_ORDEN_SIS_ORIGEN_APP", type text}, {"COMENTARIO_FLAG_ERROR", type text}, {"NOD_REGION", type text}, {"NOD_COMUNA", type text}, {"NOMBRE_CALLE", type text}, {"NUMERO_CALLE", type text}}),
    #"Filas filtradas" = Table.SelectRows(#"Tipo cambiado", each not Text.Contains([NOMBRE_CLIENTE], ";")),
    #"Consulta anexada" = Table.Combine({#"Filas filtradas", Corporate_con_errores}),
    #"Valor reemplazado" = Table.ReplaceValue(#"Consulta anexada",".","",Replacer.ReplaceText,{"NUMERO_CALLE"}),
    #"Filtrando por Recambio" = Table.SelectRows(#"Valor reemplazado", each ([TIPO_DE_REQUERIMIENTO] = "RECAMBIO")),
    #"Duplicados quitados" = Table.Distinct(#"Filtrando por Recambio", {"ID_ORDEN_SISTEMA_ORIGEN"}),
    #"Columnas quitadas" = Table.RemoveColumns(#"Duplicados quitados",{"Source.Name", "ID_ACCION_ORDEN", "NOMBRE_CLIENTE", "DOCUMENTO_CLIENTE_TIPO", "DOCUMENTO_CLIENTE", "DOCUMENTO_CLIENTE_DV", "USUARIO_CREACION", "SISTEMA_ORIGEN", "CANAL", "ESTADO", "ES_PORTABILIDAD", "FECHA_HORA_DEL_ESTADO", "TIPO_DE_PLAN", "ES_DEVOLUCION", "ID_ORDEN_SIS_ORIGEN_APP", "COMENTARIO_FLAG_ERROR"}),
    #"Poner En Mayúsculas Cada Palabra" = Table.TransformColumns(#"Columnas quitadas",{{"NOD_REGION", Text.Proper, type text}, {"NOD_COMUNA", Text.Proper, type text}, {"NOMBRE_CALLE", Text.Proper, type text}}),
    #"Columnas quitadas1" = Table.RemoveColumns(#"Poner En Mayúsculas Cada Palabra",{"CODIGO_SKU", "DESCRIPTIVO_SKU", "TIPO_DE_RECURSO", "SERIE"})
in
    #"Columnas quitadas1"
```

Conclusión y Futuro
Este proyecto estableció una base de datos anual con datos limpios y estructurados, facilitando futuras visualizaciones para mejorar la toma de decisiones. 🎉
