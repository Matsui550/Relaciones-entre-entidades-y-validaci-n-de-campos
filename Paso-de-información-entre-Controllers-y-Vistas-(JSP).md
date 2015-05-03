Un controlador puede hacer mas que enviarnos una simple vista, podemos utilizarlo para pasar información como cadenas de texto, números, objetos e incluso listas de objetos.

## El objeto Model
Cada controlador recibe como primer parametro un objeto de la clase ```Model```. Este objeto es sumamente util para enviar información al controlador mediante el método ```addAttribute```.

### Agregando elementos al model.
Como ejemplo. En el código del Controller agregamos diferentes elementos al model y le asignamos un nombre a cada elemento:
```java
Cliente cliente = new Cliente();
List<Cliente> todosClientes = new ArrayList<Cliente>();

model.addAttribute("usuario", "Nombre Usuario");
model.addAttribute("cliente", cliente);
model.addAttribute("clientes", todosClientes);
```

## Del lado de la vista.
Una vez que los elementos han sido agregados al objeto ```model``` podemos trabajar con ellos en el código JSP de nuestra vista. Podemos utilizar sentencias de control propias de JSP o simplemente desplegar sus valores.

```java
${usuario} // Escribe 'Nombre Usuario' sobre la página
${cliente.nombre} // Escribe el valor de la propiedad 'nombre' del objeto 'cliente'
```

También podemos iterar sobre la lista de clientes para acceder a cada uno de los objetos dentro de la lista.

```java
<c:forEach items="${clientes}" var="cliente">
  <h3>Nombre: ${cliente.nombre} </h3>
  <h4>Teléfono: ${cliente.telefono} </h4>                  
</c:forEach>
```

> **Nota** En la sección correspondiente a sentencias de control en JSP podras encontrar mas información sobre como funciona la sentencia ```forEach``` y como utilizarla para llenar tablas o listas de datos en las vistas.