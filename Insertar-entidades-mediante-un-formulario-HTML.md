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

2. A continuación escribimos las etiquetas ```<label>``` e ```<input>``` para los campos del formulario.
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

3. Escribimos las etiquetas necesarias para cada uno de los campos restantes del formulario, relacionandolos con las propiedades correspondientes mediante el atributo ```path``` como se ha indicado.

## El objeto daoCliente y la clase GenericDao
Para manipular objetos de la base de datos (Entidades) utilizaremos una clase generica, ```GenericDao```, esta clase se encargara de solicitar a hibernate que inserte, actualice, consulte y elimine objetos de la base de datos. La clase cuenta con los siguiente metodos:

 * ```create```: Inserta un nuevo objeto a la base de datos
 * ```edit```: Actualize la informacióñ de un objeto en la base de datos
 * ```findAll```: Consulta todos los objetos de cierta clase de la base de datos
 * ```find```: Busca por ID en cierta tabla de la base de datos
 * ```findBySpecificField```: Busca objetos por un campo especifico.
 * entre otros.

A cada instancia del objeto ```GenericDao``` debemos informarle que entidades vamos a manipular, por ejemplo, para manipular objetos de la clase ```Cliente``` utilizaremos un objeto de la clase ```GenericDao``` al que le informaremos que manipulara objetos ```cliente```. Para manipular objetos de la clase ```OrdenVenta``` necesitamos **otra** instancia de la clase ```GenericDao``` a la cual le informaremos que manipulará objetos de la clase ```OrdenVenta```. Podemos definir el objeto ```daoClientes``` como una instancia de ```GenericDao``` e informarle que manipulara objetos de la clase ```Cliente``` de la siguiente forma:

```java
private GenericDao<Cliente> daoCliente;
    
@Autowired
public void setDaoCliente(GenericDao<Cliente> daoCliente)
{
    this.daoCliente = daoCliente;
    daoCliente.setClass(Cliente.class); // Asignamos la clase que manipulará
}
```
 * La anotación ```@Autowired``` indica que Spring sera quien se encargue de instanciar al objeto, con lo cual nosotros podemos utilizarlo libremente sin preocuparnos por inicializarlo.
 * El código anterior se repite por cada instancia que tengamos y necesitemos manipular.
 * La variable y el metodo deben ser de tipo *global* a la clase, no se admiten **daos** locales.
 * El sufijo y prefijo ```dao``` en los nombres de objetos y clases significa **Data Access Object** *(Objeto de acceso a datos)*

## Código completo.
Para cuestiones de consulta y aclaración, el código del ejemplo anterior es el siguiente:

Formulario HTML/JSP:
```html
<!-- En la parte superior del archivo .jsp: -->
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>

<%@page contentTy ...
<!-- ... -->

<!-- Formulario de nuevo cliente -->
<form:form id="fnvo-cliente" commandName="cliente" action="nvocliente" method="POST">

    <div class="panel panel-success">
      <div class="panel-heading">
        <h4>Datos de la empresa</h4>
      </div>
      <div class="panel-body">
        <div class="row">
          <div class="col-md-6">
            <br/><label for="fnvoc-empresa">Empresa</label>
            <form:input path="empresa" name="empresa" id="fnoc-empresa" 
                        type="text" class="form-control"/>
            <form:errors path="empresa" element="div" class="alert-danger pad-10" />
          </div>

          <div class="col-md-6">
            <br/><label for="fnvoc-suc">Sucursal</label>
            <form:input path="sucursal" name="sucursal" id="fnvoc-suc" 
                        type="text" class="form-control"/>
            <form:errors path="sucursal" element="div" class="alert-danger pad-10" />
          </div>
        </div>
        <div class="row">
          <div class="col-md-6">
            <br/><label for="fnvoc-rfc">RFC</label>
            <form:input path="rfc" name="rfc" id="fnvoc-rfc" 
                        type="text" class="form-control"/>
            <form:errors path="rfc" element="div" class="alert-danger pad-10" />
          </div>

          <div class="col-md-3">
            <br/><label for="fnvoc-pais">Pais</label>
            <form:input path="pais" name="pais" id="fnvoc-pais" 
                        type="text" class="form-control" />
            <form:errors path="pais" element="div" class="alert-danger pad-10" />
          </div>

          <div class="col-md-3">
            <br/><label for="fnvoc-edo">Estado</label>
            <form:input path="estado" name="estado" id="fnvoc-edo"
                        type="text" class="form-control" />
            <form:errors path="estado" element="div" class="alert-danger pad-10" />
          </div>
        </div>
        <div class="row">
          <div class="col-md-3">
            <br/><label for="fnvoc-municipio">Municipio o ciudad</label>
            <form:input path="municipio" name="municipio" id="fnvoc-mun"
                        type="text" class="form-control" />
            <form:errors path="municipio" element="div" class="alert-danger pad-10" />
          </div>
          <div class="col-md-3">
            <br/><label for="fnvoc-cp">Código Postal</label>
            <form:input path="cp" name="cp" id="fnvoc-cp" 
                        type="text" class="form-control" />
            <form:errors path="cp" element="div" class="alert-danger pad-10" />
          </div>
          <div class="col-md-6">
            <br/><label for="fnvoc-col">Colonia</label>
            <form:input path="colonia" name="colonia" id="fnvoc-col" 
                        type="text" class="form-control" />
            <form:errors path="colonia" element="div" class="alert-danger pad-10" />
          </div>
        </div>
        <div class="row">
          <div class="col-md-6">
            <br/><label for="fnvoc-calle">Calle</label>
            <form:input path="calle" name="calle" id="fnvoc-calle" 
                        type="text" class="form-control" />
            <form:errors path="calle" element="div" class="alert-danger pad-10" />
          </div>
          <div class="col-md-3">
            <br/><label for="fnvoc-nume">Número exterior</label>
            <form:input path="numeroE" name="numeroE" id="fnvoc-nume"
                        type="text" class="form-control" />
            <form:errors path="numeroE" element="div" class="alert-danger pad-10" />
          </div>
          <div class="col-md-3">
            <br/><label for="fnvoc-numi">Número interior</label>
            <form:input path="numeroI" name="numeroI" id="fnvoc-numi"
                        type="text" class="form-control" />
            <form:errors path="numeroI" element="div" class="alert-danger pad-10" />
          </div>
        </div>
      </div>
    </div>

    <br/><br/>
    <div class="panel panel-success">
      <div class="panel-heading">
        <h4>Datos del contacto</h4>
      </div>
      <div class="panel-body">
        <div class="row">
          <div class="col-md-4">
            <br/><label for="fnvoc-nombre">Nombre (s)</label>
            <form:input path="nombreContacto" name="nombreContacto" 
                        type="text" class="form-control"/>
            <form:errors path="nombreContacto" element="div" class="alert-danger pad-10" />
          </div>

          <div class="col-md-4">
            <br/><label for="fnvoc-apaterno">Apellido paterno</label>
            <form:input path="apellidoPaternoContacto" name="apellidoPaternoContacto"
                        type="text" class="form-control"/>
            <form:errors path="apellidoPaternoContacto" element="div" class="alert-danger pad-10" />
          </div>

          <div class="col-md-4">
            <br/><label for="fnvoc-amaterno">Apellido materno</label>
            <form:input path="apellidoMaternoContacto" name="apellidoMaternoContacto" 
                        type="text" class="form-control"/>
            <form:errors path="apellidoMaternoContacto" element="div" class="alert-danger pad-10" />
          </div>
        </div>
        <div class="row">
          <div class="col-md-4">
            <br/><label for="fnvoc-email">Email</label>
            <form:input path="email" name="email" id="fnvoc-email" 
                        type="email" class="form-control" />
            <form:errors path="email" element="div" class="alert-danger pad-10" />
          </div>

          <div class="col-md-2">
            <br/><label for="fnvoc-tel1">Teléfono</label>
            <form:input path="telefono1" name="telefono1" 
                        type="text" class="form-control"/>
            <form:errors path="telefono1" element="div" class="alert-danger pad-10" />
          </div>
          <div class="col-md-2">
            <br/><label>Extensión</label>
            <form:input path="extension1" name="extension1"
                        type="text" class="form-control" />
            <form:errors path="extension1" element="div" class="alert-danger pad-10" />
          </div>
          <div class="col-md-2">
            <br/><label for="fnvoc-tel2">Teléfono 2</label>
            <form:input path="telefono2" name="telefono2" 
                        type="text" class="form-control"/>
            <form:errors path="telefono2" element="div" class="alert-danger pad-10" />
          </div>
          <div class="col-md-2">
            <br/><label>Extensión 2</label>
            <form:input path="extension2" name="extension2"
                        type="text" class="form-control" />
            <form:errors path="extension2" element="div" class="alert-danger pad-10" />
          </div>

        </div>
      </div>
    </div>

    <div class="row text-right">
      <div class="col-md-12">
        <br/><br/>
        <button type="submit" class="btn btn-success right">Guardar</button>
        &nbsp;&nbsp;
        <button class="btn btn-primary right" type="reset">
          Limpiar
        </button>
        <br/><br/>
      </div>
    </div>
</form:form>

<!-- ... -->
```

Controler métodos ```GET``` y ```POST``` asi como la instancia de ```daoClientes```:
```java
private GenericDao<Cliente> daoCliente;
    
@Autowired
public void setDaoCliente(GenericDao<Cliente> daoCliente)
{
    this.daoCliente = daoCliente;
    daoCliente.setClass(Cliente.class);
}

@RequestMapping(value = "ventas/nvocliente", method = RequestMethod.GET)
public String nvoCliente(Model model)
{
    Cliente cliente = new Cliente();
    cliente.setStatus(true);

    model.addAttribute("cliente", cliente);
    return "Ventas/nvoCliente";
}

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
        return "Ventas/clientes";
    }
}
```

