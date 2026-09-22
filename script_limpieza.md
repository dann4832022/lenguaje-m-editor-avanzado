let
    // Paso 1: Fuente de datos original
    Origen = Csv.Document(File.Contents("C:\Users\dann4\Downloads\ventas_raw.csv"), [Delimiter=",", Columns=5, Encoding=65001, QuoteStyle=QuoteStyle.None]),
    #"Encabezados promovidos" = Table.PromoteHeaders(Origen, [PromoteAllScalars=true]),

    // Paso 2: Eliminar espacios en blanco al inicio y al final
    // de la columna nombre_producto usando Text.Trim
    LimpiarEspacios = Table.TransformColumns(#"Encabezados promovidos", {{"nombre_producto", Text.Trim, type text}}),

    // Paso 3: Estandarizar la columna categoria a Title Case (Mayúscula en cada palabra)
    // para unificar "computación", "COMPUTACIÓN" y "Computación"
    EstandarizarCategoria = Table.TransformColumns(LimpiarEspacios, {{"categoria", Text.Proper, type text}}),

    // Paso 4: Filtrar y eliminar registros de prueba
    // Excluir filas donde categoria sea exactamente "Prueba"
    // usando Table.SelectRows
    EliminarPruebas = Table.SelectRows(EstandarizarCategoria, each ([categoria] <> "Prueba")),

    // Paso 5: Definir tipos de datos correctos
    // id_venta: Int64.Type
    // nombre_producto y categoria: type text
    // precio: type number
    // fecha_venta: type date
    TiparColumnas = Table.TransformColumnTypes(EliminarPruebas, {
        {"id_venta", Int64.Type},
        {"nombre_producto", type text},
        {"categoria", type text},
        {"precio", type number},
        {"fecha_venta", type date}
    })

in
    TiparColumnas