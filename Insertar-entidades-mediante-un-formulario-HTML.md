Para hacer la inserción se requiere:

 * Una vista JSP con el formulario HTML escrito con ayuda de Spring form ```<form:form>```
 * Un Controller que atienda peticiones ```GET``` y envie la vista con el formulario limpio.
 * Un Controller que atienda peticiones ```POST``` y reciba los datos del formulario, los valide e inserte a la base de datos o en su defecto informe al usuario sobre los errores de validación

A continuación realizaremos paso a paso el formulario para la entidad ```Cliente```
## Controller para peticiones GET
```java
@RequestMapping(value = "ventas/nvocliente", method = RequestMethod.GET)
public String nvoCliente(Model model)
{
    Cliente cliente = new Cliente();
    cliente.setStatus(true);
       
    model.addAttribute("cliente", cliente);
    return "Ventas/nvoCliente";
}
```

 * Crearemos un objeto de la clase ```Cliente``` y lo enviaremos a la vista, este objeto sera el que se manipulará desde el formulario HTML.
 * Por default, todos los clientes al registrarse tienen ```status = true```, esta propiedad no será editable en el formulario, por lo cual la igualamos a ```true``` desde ahora.

## Controller para peticiones POST
Cuando el usuario envia el formulario, lo enviara a la ruta ```ventas/nvocliente``` pero esta vez la petición sera de tipo ```POST``` y se atendera con el siguiente controller:
```java
@RequestMapping(value = "ventas/nvocliente", method = RequestMethod.POST)
public String regNvoCliente(Model model, @Valid Cliente cliente, BindingResult bindingResult)
{
    if(bindingResult.hasErrors())
    {
        System.out.println("Invalid with: " + bindingResult.getErrorCount() + " errors");
        System.out.println("Error: " + bindingResult.getFieldError().getField());
        return "Ventas/nvoCliente";
    }
    else
    {
        daoCliente.create(cliente);
        return "Ventas/clienteInsertado";
    }
}
```

 * Ademas del parametro tradicional ```model```, este controller recibe un ```cliente``` y un ```bindingResult```.
 * La anotación ```@Valid``` sobre ```cliente```, esta indicando al controlador que valide los valores del objeto cliente basado en las anotaciones de la entidad ```Cliente``` tales como ```@NotNull```, ```@Email```, ```@Size```, ```@Pattern``` etc.
 * En caso de encontrar algún error durante la validación, dicho error/errores será colocado en el objeto ```bindingResult``` el cual se encargara de llevar las alertas de error a la vista JSP para informar al usuario.
 * Si la validación de campos no genera ningún error, insertamos el objeto ```cliente``` en la base de datos mediante el objeto ```daoCliente```. En seguida analizaremos el objeto ```daoCliente``` y sus funciones.
 * Si la validación genera errores, direccionamos al usuario a la vista con el formulario, donde se le mostrarán los errores de validación para que sean corregidos.
 * Si la validación no genera errores, direccionamos al usuario a cualquier otra vista, en este caso lo direccionamos a una vista donde se le mostrará un mensaje indicandole que el ```cliente``` ha sido registrado exitosamente.

## El formulario HTML/Spring
¿Recuerdas que durante la petición ```GET``` que solicita la vista con el formulario también enviamos un objeto de la clase ```Cliente``` a dicha vista? Ahora usaremos este objeto para indicar al formulario que debe editar sus propiedades.

Mas alla de una etiqueta clasica ```<form>``` de HTML, utilizaremos una etiqueta de Spring/JSP mucho mas poderosa que la propia de HTML. Para poder utilizar esta etiqueta **debemos colocar** al inicio del archivo JSP lo siguiente: ```<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>```

1. Abrimos la etiqueta ```<form:form>``` y asignamos algunos atributos: ```<form:form id="fnvo-cliente" commandName="cliente" action="nvocliente" method="POST">```
   * El atributo ```commandName``` indica que en este formulario estaremos editando el objeto ```cliente``` que hemos recibido desde el controlador a través del ```model```.
   * El atributo ```action``` indica a que ruta se enviará el formulario, en este caso sera a la ruta ```ventas/nvocliente```, dado que la vista actual ya se encuentra en la dirección ```ventas/*``` solo indicamos la última parte de la URL.
   * El atributo ```method``` indica que este formulario se enviará mediante una petición de tipo ```POST```

2. A continuación escribimos las etiquetas ```<label>``` e ```<input>``` para cada uno de los campos del formulario.
```java
<br/><label for="fnvoc-empresa">Empresa</label>
<form:input path="empresa" name="empresa" id="fnoc-empresa" 
            type="text" class="form-control"/>
<form:errors path="empresa" element="div" class="alert-danger pad-10" />
```
   * La etiqueta ```<label>``` es HTML puro y no tiene mayor complicación
   * La etiqueta ```<form:input>``` pertenece a Spring/JSP y utiliza el atributo ```path``` para indicar con que propiedad del objeto ```cliente``` se relacionará este ```<input>```.
   * La etiqueta ```<form:errors>``` solo se activará cuando se haya enviado el formulario para validación al controlador y si el objeto genera errores de validación. El atributo ```path``` indica a que campo del objeto ```cliente``` pertenecen los errores que se mostrarán en esta etiqueta. El atributo ```element="div"``` indica que los mensajes de error se colocarán dentro de un elemento ```<div>```. Finalmente el atributo ```class``` indica las clases css que se asignarán al ```<div>``` de los mensajes de error.
   * Cuando hay algún error de validación el formulario mostrará los mensajes definidos en las anotaciones de la entidad.

![Rendering form errors](http://i.imgur.com/YV7LnUE.png)

Escribimos las etiquetas necesarias para cada uno de los campos del formulario. Relacionando con las propiedades correspondientes mediante el atributo ```path``` como se ha indicado.