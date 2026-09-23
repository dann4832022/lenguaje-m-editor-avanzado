let
    // Paso 1: Fuente de datos original usando Table.FromRows (sin modificar el origen)
    Origen = Table.FromRows(
        {
            {1, "  Laptop Pro  ", "computación", 1200.50, "2024-01-15"},
            {2, "Mouse Gamer", "COMPUTACIÓN", 35.00, "2024-01-16"},
            {3, " Teclado  ", "prueba", 50.00, "2024-01-17"},
            {4, "Monitor 24", "Computación ", 200.00, "2024-01-18"},
            {5, " Auriculares ", "PRUEBA", 45.00, "2024-01-19"},
            {6, "Silla Ergonomica", "oficina", 150.00, "2024-01-20"},
            {7, "Escritorio", "OFICINA", 300.00, "2024-01-21"}
        },
        {"id_venta", "nombre_producto", "categoria", "precio", "fecha_venta"}
    ),

    // Paso 2: Eliminar espacios en blanco al inicio y al final de la columna nombre_producto
    LimpiarEspacios = Table.TransformColumns(Origen, {{"nombre_producto", Text.Trim, type text}}),

    // Paso 3: Estandarizar la columna categoria a Title Case
    // Convierte "computación", "COMPUTACIÓN", "prueba", "PRUEBA", "oficina" a formato estándar
    EstandarizarCategoria = Table.TransformColumns(LimpiarEspacios, {{"categoria", Text.Proper, type text}}),

    // Paso 4: Filtrar y eliminar registros de prueba
    // Al estandarizar en el Paso 3, tanto "PRUEBA" como "prueba" pasaron a ser "Prueba",
    // permitiendo borrarlas a todas con este único filtro.
    EliminarPruebas = Table.SelectRows(EstandarizarCategoria, each ([categoria] <> "Prueba")),

    // Paso 5: Definir tipos de datos correctos
    TiparColumnas = Table.TransformColumnTypes(EliminarPruebas, {
        {"id_venta", Int64.Type},
        {"nombre_producto", type text},
        {"categoria", type text},
        {"precio", type number},
        {"fecha_venta", type date}
    })
in
    TiparColumnas