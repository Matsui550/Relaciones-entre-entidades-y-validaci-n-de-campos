A menudo es necesario recurrir a tablas para mostrar datos dentro de la aplicación, uno de los requerimientos clave es la capacidad de realizar busquedas, filtrado y ordenamientos a traves de los campos de una tabla. Para entregar dicha funcionalidad se ha incluido [DataTables](http://www.datatables.net/) dentro de los recursos frontend de la aplicación.

Para utilizar DataTables:

### Paso 1: Contar con una tabla con datos en la aplicación.
Dentro de el archivo jsp debe existir una tabla con datos, a la cual le agregaremos el campo de busqueda mediante [DataTables](http://www.datatables.net/). Una tabla sencilla de ejemplo:

```html
<table id="tclientes" class="table table-bordered table-hover table-striped table-responsive">
  <thead>
    <tr>
      <th>Empresa</th>
      <th>RFC</th>
      <th>Nombre de contacto</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Some data</td>
      <td>Some data</td>
      <td>Some data</td>
    </tr>
    <tr>
      <td>Some data</td>
      <td>Some data</td>
      <td>Some data</td>
    </tr>
    <tr>
      <td>Some data</td>
      <td>Some data</td>
      <td>Some data</td>
    </tr>
  </tbody>
</table>
```

### Paso 2: Incluir los archivos javascript y css necesarios
Para poder utilizar [DataTables](http://www.datatables.net/), debemos incluir los siguientes archivos dentro del jsp en el cual se encuentra la tabla:

```html
<!-- Datatables css -->
<link href="${pageContext.request.contextPath}/resources/css/libs/dataTables.bootstrap.css" rel="stylesheet" type="text/css">

<!-- Datatables js -->
<script src="${pageContext.request.contextPath}/resources/js/libs/jquery.dataTables.min.js"></script>
<script src="${pageContext.request.contextPath}/resources/js/libs/dataTables.bootstrap.min.js"></script>
```
*NOTA: Los archivos javascript deben incluirse en el orden indicado, todo archivo js perteneciente a bootstrap debe incluirse antes de los archivos señalados.*

Se recomienda incluir el archivo css dentro de la etiqueta `<head>` y los archivos javascript justo antes de cerrar la etiqueta `<body>`.

### Paso 3: Por ultimo.
Una vez que tenemos una tabla en nuestro documento y que hemos incluido los archivos necesarios bastara con incluir el siguiente script justo antes de cerrar la etiqueta body, y despues de los archivos js requeridos.

```js
<script >
  $(document).ready(function () {
    $('#tclientes').DataTable();
  });
</script>
```

*Notese que el elemento '#tclientes' es el mismo id asignado a la tabla*

### Resultado:

Whoala:
![DataTables sample](http://i.imgur.com/VUykicO.png?1)

**NOTA:** <br/>
*En mi caso ha sido necesario comentarizar la inclusión de los siguientes archivos (Por el momento y solo en caso de que los tengas, comentariza):*

```html
<script src="${pageContext.request.contextPath}/resources/js/libs/morris.min.js"></script>
<script src="${pageContext.request.contextPath}/resources/js/libs/morris-data.js"></script>
```

*En caso de ser requerido, puedes consultar la vista 'clientes.jsp' correspondiente al modulo de ventas para ver el ejemplo completo de la implementación de DataTables.*