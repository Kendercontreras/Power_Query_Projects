**Proyecto de Transformación de Datos en Power BI utilizando Power Query**

Descripción del Proyecto

En conjunto con las transformaciones descritas en este reporitorio: [Corporate_Projects](https://github.com/Kendercontreras/Power_Query_Projects/blob/master/Corporate_Projects.md), [Data_Transformation_Origen](https://github.com/Kendercontreras/Power_Query_Projects/blob/master/Data_Transformation_origen) y [Series_Projects](https://github.com/Kendercontreras/Power_Query_Projects/blob/master/Series_Projects.md), se realiza esta transformación final, la cual se combinará junto con las consultas resultantes de los 3 archivos antes mencionados, para consolidar toda la información necesaria para la empresa, con la cual podrán crear las visualizaciones correspondientes.

Este proyecto tiene como objetivo realizar la transformación de datos en Power BI utilizando el editor avanzado de Power Query. La tarea principal es limpiar, combinar y estructurar datos provenientes de archivos almacenados en una carpeta específica. Estos datos son esenciales para la generación de informes y análisis en Power BI.

Desafíos Presentados

Diversidad de Fuentes:
Desafío: Los datos provienen de múltiples archivos en una carpeta, cada uno con su propio formato y estructura.
Solución: Se utilizó la función Folder.Files para cargar todos los archivos y se aplicaron transformaciones personalizadas para manejar las diferencias en la estructura de los archivos.

Unión de Tablas:
Desafío: La necesidad de combinar datos de varias tablas, cada una proveniente de diferentes fuentes.
Solución: Se implementaron operaciones de unión (join) con las funciones Table.NestedJoin y Table.ExpandTableColumn, permitiendo la consolidación de información proveniente de diversas fuentes.

Limpieza y Estandarización de Datos:
Desafío: Datos inconsistentes y valores nulos que afectan la calidad de la información.
Solución: Se realizaron múltiples transformaciones, como la eliminación de duplicados, cambio de tipos de datos, reemplazo de valores nulos y sustitución de texto para mejorar la calidad y consistencia de los datos.

Manejo de Errores y Excepciones:
Desafío: Posibilidad de errores durante la transformación de datos.
Solución: Se implementó un manejo adecuado de errores utilizando funciones como Table.ReplaceValue para reemplazar valores nulos y garantizar una ejecución fluida del flujo de trabajo.

Cómo se Solucionaron los Desafíos

Estrategias de Unión:
Se definieron claves de unión sólidas y se utilizaron funciones de unión que se adaptaran a las necesidades específicas de cada combinación de tablas.

Transformaciones Graduales:
Se adoptó un enfoque de transformaciones graduales, asegurándose de abordar cada desafío por separado y verificar los resultados en cada etapa.

Manejo Eficiente de Datos:
Se optimizó el rendimiento del flujo de trabajo, utilizando funciones como Table.Buffer para almacenar en memoria temporal las tablas más grandes y mejorando así la eficiencia del proceso.

A continuación, se presenta el código detallado: 

```
let
    Origen = Folder.Files("E:\Reportes DEM\2024\Reportes Cristian - originales\XLS Procesados\Enero"),
    #"Archivos ocultos filtrados1" = Table.SelectRows(Origen, each [Attributes]?[Hidden]? <> true),
    #"Invocar función personalizada1" = Table.AddColumn(#"Archivos ocultos filtrados1", "Transformar archivo", each #"Transformar archivo"([Content])),
    #"Columnas con nombre cambiado1" = Table.RenameColumns(#"Invocar función personalizada1", {"Name", "Source.Name"}),
    #"Otras columnas quitadas1" = Table.SelectColumns(#"Columnas con nombre cambiado1", {"Source.Name", "Transformar archivo"}),
    #"Columna de tabla expandida1" = Table.ExpandTableColumn(#"Otras columnas quitadas1", "Transformar archivo", Table.ColumnNames(#"Transformar archivo"(#"Archivo de ejemplo"))),
    #"Tipo cambiado" = Table.TransformColumnTypes(#"Columna de tabla expandida1",{{"ID_ORDEN", type text}, {"ID_ORDEN_DE_DESPACHO", type text}, {"ID_DE_ACCION_DE_ORDEN", type text}, {"NOMBRE_DEL_CLIENTE", type text}, {"DOCUMENTO_CLIENTE_TIPO", type text}, {"DOCUMENTO_CLIENTE", type text}, {"DOCUMENTO_CLIENTE_DV", type text}, {"USUARIO_CREACION", type text}, {"TIPO_DE_REQUERIMIENTO", type text}, {"ESTADO", type text}, {"FECHA_HORA_DEL_ESTADO", type datetime}, {"COMENTARIO_FLAG_ERROR", type text}, {"ID_SALESFORCE", type text}, {"CANTIDAD_IMEI", type text}, {"CANTIDAD_SIM", type text}, {"SISTEMA_DE_ORIGEN", type text}, {"CANAL", type text}, {"ES_PORTABILIDAD", type text}, {"TIPO_DE_PLAN", type text}, {"FECHA_ESTADO_AGENDADO", type text}, {"FECHA_ALTA", type text}, {"MONTH_FINALIZATION", type text}, {"SERIE", type text}, {"TIPO_DE_RECURSO", type text}, {"DESCRIPTIVO_SKU", type text}, {"MSISDN", type text}, {"CHR_NOMBRE_TIPO_CLTE", type text}, {"CHR_SUBTIPO_CLIENTE", type text}, {"NUMERO_DE_GUIA_DE_DESPACHO", type text}, {"ID_ASIGNACION_OTR", type text}, {"TIPO_CONFIRMACION", type text}, {"CODIGO_CLIENTE", type text}, {"OBSERVACION_FINAL", type text}, {"COMENTARIO_FINAL", type text}, {"ESTADO_ANTERIOR", type text}, {"OBSERVACION_ESTADO_ANTERIOR", type text}, {"COMENTARIO_ESTADO_ANTERIOR", type text}}),
    #"Combinando Residenciales" = Table.NestedJoin(#"Tipo cambiado", {"ID_ORDEN_DE_DESPACHO"}, Residenciales, {"ID_ORDEN_DE_DESPACHO"}, "Residenciales", JoinKind.LeftOuter),
    #"Se expandió Residenciales" = Table.ExpandTableColumn(#"Combinando Residenciales", "Residenciales", {"CHR_SUBTIPO_CLIENTE"}, {"Residenciales.CHR_SUBTIPO_CLIENTE"}),
    #"Filtrando para eliminar Residenciales" = Table.SelectRows(#"Se expandió Residenciales", each [Residenciales.CHR_SUBTIPO_CLIENTE] <> "Residencial"),
    #"Columnas iniciales quitadas" = Table.RemoveColumns(#"Filtrando para eliminar Residenciales",{"Source.Name", "ID_ORDEN", "ID_SALESFORCE", "COMENTARIO_FLAG_ERROR", "CANAL", "ES_PORTABILIDAD", "TIPO_DE_PLAN", "FECHA_ALTA", "MONTH_FINALIZATION", "CHR_SUBTIPO_CLIENTE", "NUMERO_DE_GUIA_DE_DESPACHO", "ID_ASIGNACION_OTR", "TIPO_CONFIRMACION", "DOCUMENTO_CLIENTE_TIPO", "DESCRIPTIVO_SKU", "TIPO_DE_RECURSO", "Residenciales.CHR_SUBTIPO_CLIENTE"}),
    #"Filas ordenadas" = Table.Buffer(Table.Sort(#"Columnas iniciales quitadas",{{"FECHA_HORA_DEL_ESTADO", Order.Descending}})),
    #"Duplicados quitados" = Table.Distinct(#"Filas ordenadas", {"ID_ORDEN_DE_DESPACHO"}),
    #"Filtrando por Recambio / Cambiar" = Table.SelectRows(#"Duplicados quitados", each ([TIPO_DE_REQUERIMIENTO] = "Cambiar" or [TIPO_DE_REQUERIMIENTO] = "Recambio")),
    #"Agregar columna RUT" = Table.AddColumn(#"Filtrando por Recambio / Cambiar", "RUT", each [DOCUMENTO_CLIENTE]&"-"&[DOCUMENTO_CLIENTE_DV]),
    #"Extrayendo fecha sin "",00000""" = Table.AddColumn(#"Agregar columna RUT", "FECHA_AGENDAMIENTO", each Text.BeforeDelimiter([FECHA_ESTADO_AGENDADO], ","), type text),
    #"Extrayendo hora del agendamiento" = Table.AddColumn(#"Extrayendo fecha sin "",00000""", "HORA_AGENDAMIENTO", each Text.AfterDelimiter([FECHA_AGENDAMIENTO], " "), type text),
    #"Agregando columna observacion" = Table.AddColumn(#"Extrayendo hora del agendamiento", "OBSERVACION", each if [ESTADO] = "Confirmado" then [OBSERVACION_ESTADO_ANTERIOR] else [OBSERVACION_FINAL]),
    #"Agregando columna Comentario" = Table.AddColumn(#"Agregando columna observacion", "COMENTARIO", each if [ESTADO] = "Confirmado" then [COMENTARIO_ESTADO_ANTERIOR] else [COMENTARIO_FINAL]),
    #"Poner En Mayusculas Cada Palabra" = Table.TransformColumns(#"Agregando columna Comentario",{{"COMENTARIO", Text.Proper, type text}}),
    #"Tipo cambiado a RUT, Fecha y hora" = Table.TransformColumnTypes(#"Poner En Mayusculas Cada Palabra",{{"RUT", type text}, {"FECHA_AGENDAMIENTO", type datetime}, {"HORA_AGENDAMIENTO", type time}, {"OBSERVACION", type text}, {"COMENTARIO", type text}}),
    #"Fecha extraida" = Table.TransformColumns(#"Tipo cambiado a RUT, Fecha y hora",{{"FECHA_AGENDAMIENTO", DateTime.Date, type date}}),
    #"Combinando Origen_Believe" = Table.NestedJoin(#"Fecha extraida", {"ID_DE_ACCION_DE_ORDEN"}, Reporte_Origen_Believe, {"ORDER_ACTION_KEY"}, "Reporte_Origen_Believe", JoinKind.LeftOuter),
    #"Se expandio Origen_Believe" = Table.ExpandTableColumn(#"Combinando Origen_Believe", "Reporte_Origen_Believe", {"ORDER_ACTION_STATUS_DESC", "ORDER_ACTION_REASON_DESC", "ORDER_ACTION_STEP_TYPE_NAME", "AGENT_USER_INITIAL", "NOD_NOMBRE_OBSERVACION", "NOD_REGION", "NOD_COMUNA", "NOMBRE_CALLE", "NUMERO_CALLE"}, {"ORDER_ACTION_STATUS_DESC", "ORDER_ACTION_REASON_DESC", "ORDER_ACTION_STEP_TYPE_NAME", "AGENT_USER_INITIAL", "NOD_NOMBRE_OBSERVACION", "ORIGEN.REGION", "ORIGEN.COMUNA", "ORIGEN.NOMBRE_CALLE", "ORIGEN.NUMERO_CALLE"}),
    #"Agregando Observación Final" = Table.AddColumn(#"Se expandio Origen_Believe", "OBSERVACION FINAL", each if [OBSERVACION] = "" then [NOD_NOMBRE_OBSERVACION] else [OBSERVACION]),
    #"Agregando Usuario Creacion" = Table.AddColumn(#"Agregando Observación Final", "USUARIO_CREADOR", each if [SISTEMA_DE_ORIGEN] = "DEM" then [USUARIO_CREACION] else [AGENT_USER_INITIAL], type text),
    #"Remplazando por Sin Informacion" = Table.ReplaceValue(#"Agregando Usuario Creacion",null,"Sin Información",Replacer.ReplaceValue,{"USUARIO_CREADOR"}),
    #"Combinando PCRC" = Table.NestedJoin(#"Remplazando por Sin Informacion", {"USUARIO_CREADOR"}, PCRC, {"CITRIX"}, "PCRC", JoinKind.LeftOuter),
    #"Se expandio PCRC" = Table.ExpandTableColumn(#"Combinando PCRC", "PCRC", {"PCRC"}, {"PCRC"}),
    #"Consultas combinadas" = Table.NestedJoin(#"Se expandio PCRC", {"ID_ORDEN_DE_DESPACHO"}, Reporte_Corporate, {"ID_ORDEN_SISTEMA_ORIGEN"}, "Reporte_Corporate", JoinKind.LeftOuter),
    #"Se expandio Reporte_Corporate" = Table.ExpandTableColumn(#"Consultas combinadas", "Reporte_Corporate", {"NOD_REGION", "NOD_COMUNA", "NOMBRE_CALLE", "NUMERO_CALLE"}, {"Corporate.NOD_REGION", "Corporate.NOD_COMUNA", "Corporate.NOMBRE_CALLE", "Corporate.NUMERO_CALLE"}),
    #"Agregando Region" = Table.AddColumn(#"Se expandio Reporte_Corporate", "REGION", each if [SISTEMA_DE_ORIGEN] = "OMS" then [ORIGEN.REGION] else [Corporate.NOD_REGION]),
    #"Agregando Comuna" = Table.AddColumn(#"Agregando Region", "COMUNA", each if [SISTEMA_DE_ORIGEN] = "OMS" then [ORIGEN.COMUNA] else [Corporate.NOD_COMUNA]),
    #"Agregando Calle" = Table.AddColumn(#"Agregando Comuna", "CALLE", each if [SISTEMA_DE_ORIGEN] = "OMS" then [ORIGEN.NOMBRE_CALLE] else [Corporate.NOMBRE_CALLE]),
    #"Agregando Numero Calle" = Table.AddColumn(#"Agregando Calle", "NUMERO_CALLE", each if [SISTEMA_DE_ORIGEN] = "OMS" then [ORIGEN.NUMERO_CALLE] else [Corporate.NUMERO_CALLE]),
    #"Sustituyendo DirecciÃ³n" = Table.ReplaceValue(#"Agregando Numero Calle","DirecciÃ³n","Dirección",Replacer.ReplaceText,{"OBSERVACION FINAL"}),
    #"Sustituyendo ErrÃ³nea" = Table.ReplaceValue(#"Sustituyendo DirecciÃ³n","ErrÃ³nea","Errónea",Replacer.ReplaceText,{"OBSERVACION FINAL"}),
    #"Sustituyendo Direccion Erronea" = Table.ReplaceValue(#"Sustituyendo ErrÃ³nea","Direccion Erronea","Dirección Errónea",Replacer.ReplaceText,{"OBSERVACION FINAL"}),
    #"Sustituyendo Sistemico" = Table.ReplaceValue(#"Sustituyendo Direccion Erronea","Sistemico","Sistémico",Replacer.ReplaceText,{"OBSERVACION FINAL"}),
    #"Sustituyendo Direcci¿n" = Table.ReplaceValue(#"Sustituyendo Sistemico","Direcci¿n","Dirección",Replacer.ReplaceText,{"OBSERVACION FINAL"}),
    #"Eliminar espacios al final" = Table.TransformColumns(#"Sustituyendo Direcci¿n", {{"COMENTARIO", Text.TrimEnd}}),
    #"Sustituyendo Comentarios" = Table.ReplaceValue(#"Eliminar espacios al final","A Solicitud", "Solicitado Por",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo A Petición De" = Table.ReplaceValue(#"Sustituyendo Comentarios","A Petición De", "Solicitado Por",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo De Movistar" = Table.ReplaceValue(#"Sustituyendo A Petición De","De Movistar", "Movistar",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo De Tch" = Table.ReplaceValue(#"Sustituyendo De Movistar","De Tch", "Movistar",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Reagendamiento" = Table.ReplaceValue(#"Sustituyendo De Tch","Reagendamiento", "Reagendar",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Solicitado Por Segmento Para Reagendar" = Table.ReplaceValue(#"Sustituyendo Reagendamiento","Solicitado Por Segmento Para Reagendar", "Solicitado Por Movistar",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Se Solicita Anulación Para Reagendar" = Table.ReplaceValue(#"Sustituyendo Solicitado Por Segmento Para Reagendar","Se Solicita Anulación Para Reagendar", "Solicitado Por Movistar",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Sla" = Table.ReplaceValue(#"Sustituyendo Se Solicita Anulación Para Reagendar","Sla", "SLA",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Bodega Mal Agendada - Ai23" = Table.ReplaceValue(#"Sustituyendo Sla","Bodega Mal Agendada - Ai23", "Bodega AI23",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Bodega Mal Agendada - Am47" = Table.ReplaceValue(#"Sustituyendo Bodega Mal Agendada - Ai23","Bodega Mal Agendada - Am47", "Bodega AM47",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Cancelación Orden Pap" = Table.ReplaceValue(#"Sustituyendo Bodega Mal Agendada - Am47","Cancelación Orden Pap", "Fue Ingresado Para Hacer Pruebas",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Prueba" = Table.ReplaceValue(#"Sustituyendo Cancelación Orden Pap","Prueba", "Fue Ingresado Para Hacer Pruebas",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Carrión" = Table.ReplaceValue(#"Sustituyendo Prueba","Carrión", "Fue Ingresado Para Hacer Pruebas",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Carrion" = Table.ReplaceValue(#"Sustituyendo Carrión","Carrion", "Fue Ingresado Para Hacer Pruebas",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Test" = Table.ReplaceValue(#"Sustituyendo Carrion","Test", "Fue Ingresado Para Hacer Pruebas",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Producto No Solicitado Solicitado Por Movistar" = Table.ReplaceValue(#"Sustituyendo Test","Producto No Solicitado Solicitado Por Movistar", "Producto No Solicitado",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Sin Respuesta Ti" = Table.ReplaceValue(#"Sustituyendo Producto No Solicitado Solicitado Por Movistar","Sin Respuesta Ti", "Sin Respuesta Ticket TI",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Cierre Por Error De Respuesta En Servicio De Reserva" = Table.ReplaceValue(#"Sustituyendo Sin Respuesta Ti","Cierre Por Error De Respuesta En Servicio De Reserva", "Error De Sistema No Permite Avanzar La Orden",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Orden Duplicada" = Table.ReplaceValue(#"Sustituyendo Cierre Por Error De Respuesta En Servicio De Reserva","Orden Duplicada", "Error De Sistema No Permite Avanzar La Orden",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Cerrado Por Proceso Automatico De Anulacion De Ordenes Masivo" = Table.ReplaceValue(#"Sustituyendo Orden Duplicada","Cerrado Por Proceso Automatico De Anulacion De Ordenes Masivo", "Error De Sistema No Permite Avanzar La Orden",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Error Sistema" = Table.ReplaceValue(#"Sustituyendo Cerrado Por Proceso Automatico De Anulacion De Ordenes Masivo","Error Sistema", "Error De Sistema No Permite Avanzar La Orden",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo No Descargan Del Pool" = Table.ReplaceValue(#"Sustituyendo Error Sistema","No Descargan Del Pool", "Error De Sistema No Permite Avanzar La Orden",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Cliente Retira En Sucursal Solicitado Por Movistar" = Table.ReplaceValue(#"Sustituyendo No Descargan Del Pool","Cliente Retira En Sucursal Solicitado Por Movistar", "Cliente Desiste De Agendamiento",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Sin Stock" = Table.ReplaceValue(#"Sustituyendo Cliente Retira En Sucursal Solicitado Por Movistar","Sin Stock", "Quiebre De Stock",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Algun" = Table.ReplaceValue(#"Sustituyendo Sin Stock","Algun", "Algún",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Cliente Retira En Sucursal Solicitado Por Tch" = Table.ReplaceValue(#"Sustituyendo Algun","Cliente Retira En Sucursal Solicitado Por Tch", "Cliente Desiste De Agendamiento",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Serie Masiva No Validada" = Table.ReplaceValue(#"Sustituyendo Cliente Retira En Sucursal Solicitado Por Tch","Serie Masiva No Validada", "Serie No Validada",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Solicitado Por Movistar - Bodega Am47" = Table.ReplaceValue(#"Sustituyendo Serie Masiva No Validada","Solicitado Por Movistar - Bodega Am47", "Bodega AM47",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Solicitado Por Tch" = Table.ReplaceValue(#"Sustituyendo Solicitado Por Movistar - Bodega Am47","Solicitado Por Tch", "Solicitado Por Movistar",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Cliente Desiste Solicitado Por Movistar" = Table.ReplaceValue(#"Sustituyendo Solicitado Por Tch","Cliente Desiste Solicitado Por Movistar", "Cliente Desiste De Agendamiento",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Serie" = Table.ReplaceValue(#"Sustituyendo Cliente Desiste Solicitado Por Movistar","Serie", "Serie Masiva",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Serie Masiva No Validada2" = Table.ReplaceValue(#"Sustituyendo Serie","Serie Masiva No Validada", "Serie No Validada",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Sustituyendo Valida Serie Masiva Masiva" = Table.ReplaceValue(#"Sustituyendo Serie Masiva No Validada2","Valida Serie Masiva Masiva", "Valida Serie Masiva",Replacer.ReplaceText,{"COMENTARIO"}),
    #"Texto insertado antes del delimitador" = Table.AddColumn(#"Sustituyendo Valida Serie Masiva Masiva", "COMENTARIO1", each Text.BeforeDelimiter([COMENTARIO], "."), type text),
    #"Combinando Series" = Table.NestedJoin(#"Texto insertado antes del delimitador", {"SERIE"}, Reporte_Series, {"SERIE"}, "Reporte_Series", JoinKind.LeftOuter),
    #"Expandiendo Series" = Table.ExpandTableColumn(#"Combinando Series", "Reporte_Series", {"ORDER_KEY", "ORDER_ACTION_KEY", "ORDER_ACTION_STATUS_DESC", "ORDER_ACTION_STEP_TYPE_NAME"}, {"Series_ORDER_KEY", "Series_ORDER_ACTION_KEY", "Series_ORDER_ACTION_STATUS_DESC", "Series_ORDER_ACTION_STEP_TYPE_NAME"}),
    #"Unificando Order Key" = Table.AddColumn(#"Expandiendo Series", "ORDER_KEY", each if [SISTEMA_DE_ORIGEN] = "OMS" then [ID_ORDEN_DE_DESPACHO] else [Series_ORDER_KEY]),
    #"Unificando Order action Key" = Table.AddColumn(#"Unificando Order Key", "ORDER_ACTION_KEY", each if [SISTEMA_DE_ORIGEN] = "OMS" then [ID_DE_ACCION_DE_ORDEN] else [Series_ORDER_ACTION_KEY]),
    #"Unificando Order Action Status Desc" = Table.AddColumn(#"Unificando Order action Key", "ORDER_ACTION_STATUS_DESC_N", each if [SISTEMA_DE_ORIGEN] = "OMS" then [ORDER_ACTION_STATUS_DESC] else [Series_ORDER_ACTION_STATUS_DESC]),
    #"Unificando Order Action Step Type Name" = Table.AddColumn( #"Unificando Order Action Status Desc", "ORDER_ACTION_STEP_TYPE_NAME_N", each if [SISTEMA_DE_ORIGEN] = "OMS" then [ORDER_ACTION_STEP_TYPE_NAME] else [Series_ORDER_ACTION_STEP_TYPE_NAME]),
    #"Columnas quitadas postransformación" = Table.RemoveColumns(#"Unificando Order Action Step Type Name",{"DOCUMENTO_CLIENTE", "DOCUMENTO_CLIENTE_DV", "USUARIO_CREACION", "FECHA_ESTADO_AGENDADO", "MSISDN", "CODIGO_CLIENTE", "OBSERVACION_FINAL", "COMENTARIO_FINAL", "OBSERVACION_ESTADO_ANTERIOR", "COMENTARIO_ESTADO_ANTERIOR", "OBSERVACION", "COMENTARIO", "AGENT_USER_INITIAL", "NOD_NOMBRE_OBSERVACION", "ORIGEN.REGION", "ORIGEN.COMUNA", "ORIGEN.NOMBRE_CALLE", "ORIGEN.NUMERO_CALLE", "Corporate.NOD_REGION", "Corporate.NOD_COMUNA", "Corporate.NOMBRE_CALLE", "Corporate.NUMERO_CALLE", "ORDER_ACTION_STATUS_DESC", "ORDER_ACTION_STEP_TYPE_NAME", "Series_ORDER_KEY", "Series_ORDER_ACTION_KEY", "Series_ORDER_ACTION_STATUS_DESC", "Series_ORDER_ACTION_STEP_TYPE_NAME"}),
    #"Combinando Tickets_Incidencia" = Table.NestedJoin(#"Columnas quitadas postransformación", {"ID_ORDEN_DE_DESPACHO"}, Tickets_Incidencia, {"ID orden"}, "TICKETS_INCIDENCIA", JoinKind.LeftOuter),
    #"Agregando Tickets_Incidencia" = Table.ExpandTableColumn(#"Combinando Tickets_Incidencia", "TICKETS_INCIDENCIA", {"Ticket"}, {"TICKETS_INCIDENCIA"}),
    #"Remplazando null por Sin ticket Informado" = Table.ReplaceValue(#"Agregando Tickets_Incidencia",null,"Sin Ticket Informado",Replacer.ReplaceValue,{"TICKETS_INCIDENCIA"}),
    #"Combinando Ordenes Anuladas" = Table.NestedJoin(#"Remplazando null por Sin ticket Informado", {"ID_ORDEN_DE_DESPACHO"}, #"Órdenes Anuladas", {"ID de orden DEM"}, "Órdenes Anuladas", JoinKind.LeftOuter),
    #"Se expandió Ordenes Anuladas" = Table.ExpandTableColumn(#"Combinando Ordenes Anuladas", "Órdenes Anuladas", {"Motivo de anulación"}, {"MOTIVO DE ANULACION"}),
    #"Combinando Reporte Diario" = Table.NestedJoin(#"Se expandió Ordenes Anuladas", {"ID_ORDEN_DE_DESPACHO"}, Reporte_Diario, {"id_orden_de_despacho"}, "Reporte_Diario", JoinKind.LeftOuter),
    #"Agregando Razón del estado y Mensaje de Error" = Table.ExpandTableColumn(#"Combinando Reporte Diario", "Reporte_Diario", {"razon_del_estado", "mensaje_de_error"}, {"RAZON DEL ESTADO", "MENSAJE DE ERROR"}),
    #"Consultas combinadas1" = Table.NestedJoin(#"Agregando Razón del estado y Mensaje de Error", {"RAZON DEL ESTADO"}, Detalle_Errores, {"Razón del estado"}, "Detalle_Errores", JoinKind.LeftOuter),
    #"Se expandió Detalle_Errores" = Table.ExpandTableColumn(#"Consultas combinadas1", "Detalle_Errores", {"Razón del estado_1"}, {"GRUPO_ERRORES"})
in
    #"Se expandió Detalle_Errores"
```

Este proyecto demuestra cómo enfrentar desafíos comunes en la limpieza y transformación de datos en Power BI, utilizando eficientemente las capacidades del editor avanzado de Power Query.
