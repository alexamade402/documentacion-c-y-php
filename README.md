# 📄 Documentación Completa: Cómo Crear Reportes en C# y PHP

 Esta documentación te enseñará paso a paso cómo crear reportes en los lenguajes **C#** y **PHP**:
 **NOTA** presta atencion y lee para que puedas entender todo.

**Base de datos sql sever:**
 vamos a primero a crear nuetra base de datos en sql server.

primero entraremos a sql sever 
![imagensql](image-2.png)

ya que entremos en sql sever vamos a darle a **new query** para crear nuetra base de datos.
![imagennewquery](image-4.png)
 
 en esa hoja que nos abre escribires 
 **create database (y el nombre de la base de datos que en este caso es ) BD_FacturacionPruebas;**
  
  ya que tenemos nuetra base de datos creada crearemos nuetra tabla Factura.
**te preguntas como la creo** asi de esta manera 

CREATE TABLE Factura (
    ID INT PRIMARY KEY IDENTITY(1,1),
    DESCRIPCION VARCHAR(150),
    CATEGORIA VARCHAR(50),
    CANTIDAD INT,
    PRECIO_UNITARIO DECIMAL(10,2),
    ITEBIS DECIMAL(10,2),
    DESCUENTO DECIMAL(10,2),
    TOTAL_GENERAL AS ((CANTIDAD * PRECIO_UNITARIO + ITEBIS) - DESCUENTO) PERSISTED
);
GO

ahora le daremos a.
![imagenejecuta](image-5.png)

asi nuestra tabla y base de datos estaran creadas corectamente.

**creacion de reporte en php**
vamos ahora con php que es mas complejo que c# 

vamos a ir a visual studio code
 y vamos a nuestro folder y crearemos una carperta con el nombre que quieras ponerle en este caso vamos a ponerle **reportedefacturacion**.

 ya que tenemos nuestra carpeta abierta en visual studio code vamos a empezar a trabajar.

vamos a crear ahora un new fil **QUE ES ESO** hay vamos a crear como una clase por asi decirlo a ese new fil le pondremos de nombre **delete.php**

en **delete.php** vamos a escribir el siguiente codigo **Este código PHP sirve para eliminar una factura de la base de datos utilizando el ID que se le pasa por la URL.**

<?php
require_once("../conectarsql.php");

// Verificar si el parámetro 'id' está presente
if (isset($_GET['id'])) {
    $idFactura = $_GET['id'];

    // Consulta SQL para eliminar la factura
    $sql = "DELETE FROM Facturas WHERE ID = ?";
    $params = array($idFactura);

    // Preparar y ejecutar la consulta
    $stmt = sqlsrv_query($conn, $sql, $params);

    // Verificar si la eliminación fue exitosa
    if ($stmt) {
        echo "Factura eliminada con éxito.";
        // Redirigir a la página de listado después de 2 segundos
        header("Location: index.php");
    } else {
        echo "Error al eliminar la factura: " . print_r(sqlsrv_errors(), true);
    }
} else {
    echo "ID no proporcionado.";
}

// Cerrar la conexión
sqlsrv_close($conn);

?>

ahora aremos en mismo paso de crear otro new fil a ese le pondremos **index.php** que este sirve para  representa un formulario web de facturación que permite al usuario registrar una factura y enviarla al archivo insert.php mediante el método POST. Está diseñado con Bootstrap 5 para que se vea moderno y responsivo.

<?php
include("../conectarsql.php");

// Obtener los datos del formulario
$Descripcion = $_POST['Descripcion'];
$Categoria = $_POST['Categoria'];
$Cantidad = $_POST['Cantidad'];
$Precio_Unitario = $_POST['Precio_Unitario'];
$ITEBIS = $_POST['ITEBIS'];
$Descuento = $_POST['Descuento'];
$Total_General = $_POST['Total_General'];

    // Insertar los datos en la base de datos
$sql = "INSERT INTO Facturas (Descripcion, Categoria, Cantidad, Precio_Unitario, ITEBIS, Descuento, Total_General) 
            VALUES (?, ?, ?, ?, ?, ?, ?)";

    // Preparar la consulta
$params = array($Descripcion, $Categoria, $Cantidad, $Precio_Unitario, $ITEBIS, $Descuento, $Total_General);
    
    // Ejecutar la consulta
$stmt = sqlsrv_query($conn, $sql, $params);

    // Verificar si la inserción fue exitosa
if ($stmt) {
    echo "Factura agregada exitosamente.";
    header("Location: reporte.php");
} else {
    echo "Error al agregar la factura.";
    print_r(sqlsrv_errors());
}3
?>

ahora vamos a crear otro new fil donde a ese le pondremos de nombre **update.php** Este código PHP sirve para consultar y actualizar una factura específica desde la base de datos SQL Server.

<?php
require_once("../conectarsql.php");

// Verificar si el parámetro 'id' está presente
if (isset($_GET['id'])) {
    // Obtener los datos de la factura desde los parámetros
    
    $sql = "SELECT * FROM Facturas WHERE ID=?";
    $stmt = sqlsrv_query($conn, $sql, array($_GET['id']));
    $row = sqlsrv_fetch_array($stmt);
        
    // $descripcion = $_GET['descripcion'];
    // $categoria = $_GET['categoria'];
    // $cantidad = $_GET['cantidad'];
    // $precio_unitario = $_GET['precio_unitario'];
    // $itebis = $_GET['itebis'];
    // $descuento = $_GET['descuento'];
    // $total_general = $_GET['total_general'];

    // Mostrar el formulario con los valores actuales
    ?>
    <html lang="es">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Actualizar Factura</title>
        <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/css/bootstrap.min.css" rel="stylesheet">
    </head>
    <body>
        <div class="container mt-5">
            <h2>Actualizar Factura</h2>
            <form action="update.php" method="post">
                <input type="hidden" name="idFactura" value="<?php echo $row["ID"]; ?>">

                <div class="mb-3">
                    <label for="descripcion" class="form-label">Descripción</label>
                    <input type="text" class="form-control" id="descripcion" name="descripcion" value="<?php echo htmlspecialchars($row["Descripcion"]); ?>" required>
                </div>

                <div class="mb-3">
                    <label for="categoria" class="form-label">Categoría</label>
                    <input type="text" class="form-control" id="categoria" name="categoria" value="<?php echo htmlspecialchars($row["Categoria"]); ?>" required>
                </div>

                <div class="mb-3">
                    <label for="cantidad" class="form-label">Cantidad</label>
                    <input type="number" class="form-control" id="cantidad" name="cantidad" value="<?php echo htmlspecialchars($row["Cantidad"]); ?>" required>
                </div>

                <div class="mb-3">
                    <label for="precio_unitario" class="form-label">Precio Unitario</label>
                    <input type="number" step="0.01" class="form-control" id="precio_unitario" name="precio_unitario" value="<?php echo htmlspecialchars($row["Precio_Unitario"]); ?>" required>
                </div>

                <div class="mb-3">
                    <label for="itebis" class="form-label">ITEBIS</label>
                    <input type="number" step="0.01" class="form-control" id="itebis" name="itebis" value="<?php echo htmlspecialchars($row["ITEBIS"]); ?>" required>
                </div>

                <div class="mb-3">
                    <label for="descuento" class="form-label">Descuento</label>
                    <input type="number" step="0.01" class="form-control" id="descuento" name="descuento" value="<?php echo htmlspecialchars($row["Descuento"]); ?>" required>
                </div>

                <div class="mb-3">
                    <label for="total_general" class="form-label">Total General</label>
                    <input type="number" step="0.01" class="form-control" id="total_general" name="total_general" value="<?php echo htmlspecialchars($row["Total_General"]); ?>" required>
                </div>

                <button type="submit" class="btn btn-primary">Actualizar</button>
            </form>
        </div>
    </body>
    </html>

    <?php
} elseif ($_SERVER['REQUEST_METHOD'] === 'POST') {
    // Si se ha enviado el formulario, actualizamos la factura
    $idFactura = $_POST['idFactura'];
    $descripcion = $_POST['descripcion'];
    $categoria = $_POST['categoria'];
    $cantidad = $_POST['cantidad'];
    $precio_unitario = $_POST['precio_unitario'];
    $itebis = $_POST['itebis'];
    $descuento = $_POST['descuento'];
    $total_general = $_POST['total_general'];

    // Consulta SQL para actualizar la factura
    $sql = "UPDATE Facturas SET Descripcion = ?, Categoria = ?, Cantidad = ?, Precio_Unitario = ?, ITEBIS = ?, Descuento = ?, Total_General = ? WHERE ID = ?";
    $params = array($descripcion, $categoria, $cantidad, $precio_unitario, $itebis, $descuento, $total_general, $idFactura);

    // Ejecutar la consulta
    $stmt = sqlsrv_query($conn, $sql, $params);

    if ($stmt) {
        echo "Factura actualizada con éxito.";
        header("refresh:2; url=index.php");
    } else {
        echo "Error al actualizar la factura: " . print_r(sqlsrv_errors(), true);
    }
}

// Cerrar la conexión
sqlsrv_close($conn);
?>



ya que tenemos todos nuestro new fil creados con nuestros codigos vamos a encender el xammp para que todo funcione pd**como lo hago** asi vas a ir a tu buscadros de tu computadora y vas a buscar xammp debe de aparecerte asi. 

![imagenxammp](image-6.png)

pd**y como yo veo en fromulario creado**

y vamos vas a ir a google y vas poner lo siguiente http://localhost/fromulario%20reporte/factura/index.php

y asi se va a ver nuestro formulario

inicio del formulario 
![imagenfromulario](image-7.png)
reporte de factura 
![imagenfromulario](image-8.png)
actualizar reporte 
![imagenfromulario](image-9.png)

y asi terminamos nuestro reporte en php.

**reporte en c#:**

para crear nuestro reporte en c# vamos a ir a visual studio code donde hay vamos a crear nuetro proyecto.

PASO 1: Crear el proyecto
Abre Visual Studio.

Haz clic en Crear nuevo proyecto.

Selecciona "Aplicación de Windows Forms (.NET Framework)" (elige C#).

Asigna un nombre al proyecto, por ejemplo: FacturaReporteApp.

Haz clic en Crear.

**Diseño del Formulario para Reporte de Factura**
El formulario debe contener todos los campos de la base de datos: ID, Descripción, Categoría, Cantidad, Precio Unitario, ITBIS, Descuento y Total General.

Se colocan los siguientes controles:

TextBox para ID (txtID), Descripción (txtDescripcion), Precio Unitario (txtPrecioUnitario), ITBIS (txtITBIS), Descuento (txtDescuento) y Total General (txtTotalGeneral).

ComboBox para seleccionar la Categoría (cbCategoria).

NumericUpDown para ingresar la Cantidad (nudCantidad).

Un botón btnAgregarFactura para agregar los datos a la tabla.

Un DataGridView (dgvFactura) donde se muestran los datos ingresados.

Un botón btnImprimir para generar e imprimir el reporte.

Este formulario permite ingresar datos de la factura, verlos en una tabla y luego imprimir el reporte.

a si se deberia de ver nuestro formulario 
![imagenreporte](image-10.png)

ahora vamos a crear una clase 

Pasos para crear una clase en C#
1. Abrir el proyecto en Visual Studio
Abre Visual Studio y crea un nuevo proyecto o abre uno existente.

2. Agregar un nuevo archivo de clase
Haz clic derecho sobre la carpeta del proyecto.

Selecciona Agregar → Clase...

Escribe el nombre de la clase (por ejemplo, Persona.cs) y haz clic en Agregar.

 PASOS PARA USAR REPORTVIEWER EN C# (Windows Forms)
 1. Instalar el ReportViewer
Opción A: Si usas Visual Studio 2019 o superior:
Abre tu proyecto en Visual Studio.

Ve a Herramientas > Administrador de paquetes NuGet > Consola del Administrador.

Escribe este comando:

bash
Copiar
Editar
Install-Package Microsoft.Reporting.WinForms
Esto instala el paquete necesario.

2. Agregar el control ReportViewer al formulario
Haz clic derecho en el cuadro de herramientas y elige "Elegir elementos...".

Busca ReportViewer y marca la casilla.

Arrástralo desde la caja de herramientas al formulario.

 3. Crear el archivo RDLC (reporte)
Haz clic derecho en tu proyecto > Agregar > Nuevo elemento.

Selecciona "Informe" o "Report" (.rdlc).

Dale un nombre, por ejemplo: ReporteVentas.rdlc.

En el diseñador, puedes agregar:

Tablas

Gráficos

Campos de texto

Usa el menú Report Data para insertar los campos.

 4. Agregar la fuente de datos (DataSet)
Haz clic derecho en el proyecto > Agregar > Nuevo elemento.

Elige DataSet (.xsd), por ejemplo DatosVentas.xsd.

Agrega una tabla con las columnas necesarias (puedes arrastrar desde el Explorador de servidores si usas SQL).

y listo asi tienes tu reporte de c# completado. 

