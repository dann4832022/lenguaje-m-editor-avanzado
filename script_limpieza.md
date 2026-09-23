let
    // Paso 1: Fuente de datos original y encabezados promovidos
    Origen = Csv.Document(File.Contents("C:\Users\dann4\Downloads\ventas_raw.csv"), [Delimiter=",", Columns=5, Encoding=65001, QuoteStyle=QuoteStyle.None]),
    #"Encabezados promovidos" = Table.PromoteHeaders(Origen, [PromoteAllScalars=true]),

    // Paso 2: Eliminar espacios en blanco al inicio y al final de nombre_producto
    LimpiarEspacios = Table.TransformColumns(#"Encabezados promovidos", {{"nombre_producto", Text.Trim, type text}}),

    // Paso 3: Estandarizar la columna categoria a Title Case (Mayúscula la primera letra)
    // Esto convierte "PRUEBA", "prueba", "Computación", "COMPUTACIÓN" a un formato estándar.
    EstandarizarCategoria = Table.TransformColumns(LimpiarEspacios, {{"categoria", Text.Proper, type text}}),

    // Paso 4: Filtrar y eliminar registros de prueba
    // Al haber estandarizado en el Paso 3, "PRUEBA" y "prueba" pasaron a ser "Prueba",
    // por lo que este filtro los elimina a todos correctamente.
    EliminarPruebas = Table.SelectRows(EstandarizarCategoria, each ([categoria] <> "Prueba")),

    // Paso 5: Definir tipos de datos correctos para cada columna
    TiparColumnas = Table.TransformColumnTypes(EliminarPruebas, {
        {"id_venta", Int64.Type},
        {"nombre_producto", type text},
        {"categoria", type text},
        {"precio", type number},
        {"fecha_venta", type date}
    })
in
    TiparColumnas