Vamos a construir tres entidades, relacionarlas y asignar validaciones para varios campos.

##Consideremos el siguiente ejemplo:

![ER Sample](http://i.imgur.com/uvLW2xm.png)

 * Hay tres entidades, ```CLIENTE``` ```ORDEN_VENTA``` y ```FACTURA```.
 * Se relacionan: ```CLIENTE``` tiene muchas ```ORDEN_VENTA``` a su vez que ```ORDEN_VENTA``` tiene una (y solo una) ```FACTURA```.

##Construyendo la entidad 'CLIENTE'

Como se ha mostrado en ejemplos anteriores configuramos una entidad y sus campos mediante anotaciones ```@Entity```, ```@Table```, ```@Column```, ```@Id``` etc.

En esta ocación, también vamos a poner restricciones a ciertos campos de nuestra entidad, por ejemplo, el campo de teléfono solo aceptara digitos y espacios, el email debera ser un email apropiado, el nombre de contacto solo admitirá letras, puntos y espacios y debe tener una longitud minima de 2 caracteres y máxima de 100, entre otras. **Para dichas validaciones utilizaremos las anotaciones:** ```@Pattern```, ```@Size``` y ```@Email```.

### La anotación @Size
Util para validar el tamaño de cadenas de texto, por ejemplo para validar que una cadena tenga entre 2 y 100 caracteres:

```java
@Size(min=2, max=100, message = "Debe tener entre 2 y 100 caracteres")
@Column(name = "NOMBRE")
private String nombreContacto;
```

 * El parametro ```min``` indica la longitud minima aceptable.
 * El parametro ```max``` indica la longitud máxima aceptable.
 * El parametro ```message``` indica el mensaje de error que ocasionalmente se enviara al usuario cuando el campo no cumpla con el tamaño minimo o máximo (Posteriormente veremos como aprovechar este mensaje).

### La anotación @Pattern
Util para validar que una cadena de texto utilizando expresiones regulares. En el paquete ```com.sapito.db.util``` existen las clases ```RExp``` y ```RExpErrors``` las cuales contienen algunas
de las expresiones regulares mas comunes asi como mensajes de error para cuando no se cumplan dichas expresiones respectivamente.

Ejemplo:
```java
@Pattern(regexp = "^[A-Z]+$", message = "Solo se admiten letras mayúsculas")
@Column(name = "COLUMNA")
private String campoX
```
 * En este ejemplo solo se admiten letras mayúsculas y se valida mediante la expresión regular "^[A-Z]+$"

Para facilitar este tipo de tareas se recomienda utilizar las clases ```RExp``` y ```RExpErrors```. Si hay alguna expresión que utilices continuamente, puedes solicitar que se agregue a la clase.

Ejemplo con las clases auxiliares:
```java
@Pattern(regexp = RExp.letrasAcentuadasPuntos, message = RExpErrors.letrasAcentuadasPuntos)
@Column(name = "NOMBRE")
private String nombreContacto;
```
 * De esta manera el campo ```nombreContacto``` solo admitira letras(Mayúsculas o minusculas), letras acentuadas y puntos.
 * Cada expresión esta documentada en el código de la clase ```RExp```

### La anotación @Email
Hibernate provee una manera facil para validar emails, mediante la anotación ```@Email```.

Ejemplo:
```java
@NotNull
@Email(message = "Ingrese una dirección de email valida")
@Column(name = "EMAIL")
private String email;
```
 * La anotacion ```@NotNull``` indica que ese campo no puede quedar vacio.

### Combinando anotaciones para crear la entidad Cliente.
Las anotacines pueden utilizarce indistintamente sobre un mismo campo. Con lo cual nuestra entidad ```Cliente``` quedaria de la siguiente forma:

```java
package com.sapito.db.entities;

import com.sapito.db.util.RExp;
import com.sapito.db.util.RExpErrors;
import java.io.Serializable;
import java.util.Collection;
import javax.persistence.CascadeType;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.OneToMany;
import javax.persistence.Table;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Pattern;
import javax.validation.constraints.Size;
import org.hibernate.validator.constraints.Email;

@Entity
@Table(name = "CLIENTE")
public class Cliente implements Serializable
{
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE)
    @NotNull
    @Column(name = "ID")
    private Long id;
    
    @NotNull
    @Size(min=1, max=500, message = "El nombre de la empresa debe tener entre 1 y 500 caracteres")
    @Column(name = "EMPRESA")
    private String empresa;
    
    @Column(name = "SUCURSAL")
    private String sucursal;
    
    @NotNull
    @Size(min=12, max=13, message = "El RFC debe tener 12 o 13 caracteres")
    @Pattern(regexp = RExp.letrasBasicasDigitos, message = RExpErrors.letrasBasicasDigitos)
    @Column(name = "RFC")
    private String rfc;
    
    @NotNull
    @Size(min=2, max=100, message = "Debe tener entre 2 y 100 caracteres")
    @Pattern(regexp = RExp.letrasAcentuadasPuntos, message = RExpErrors.letrasAcentuadasPuntos)
    @Column(name = "NOMBRE")
    private String nombreContacto;
    
    @NotNull
    @Size(min=2, max=100, message = "Debe tener entre 2 y 100 caracteres")
    @Pattern(regexp = RExp.letrasAcentuadasPuntos, message = RExpErrors.letrasAcentuadasPuntos)
    @Column(name = "APELLIDO_PATERNO")
    private String apellidoPaternoContacto;
    
    @NotNull
    @Size(min=2, max=100, message = "Debe tener entre 2 y 100 caracteres")
    @Pattern(regexp = RExp.letrasAcentuadasPuntos, message = RExpErrors.letrasAcentuadasPuntos)
    @Column(name = "APELLIDO_MATERNO")
    private String apellidoMaternoContacto;
    
    @NotNull
    @Size(min=2, max=100, message = "Debe tener entre 2 y 100 caracteres")
    @Pattern(regexp = RExp.letrasAcentuadasPuntos, message = RExpErrors.letrasAcentuadasPuntos)
    @Column(name = "PAIS")
    private String pais;
    
    @Column(name = "ESTADO")
    private String estado;
    
    @Column(name = "MUNICIPIO")
    private String municipio;
    
    @Column(name = "COLONIA")
    private String colonia;
    
    @Column(name = "CP")
    private int cp;
    
    @Column(name = "CALLE")
    private String calle;

    @Pattern(regexp = RExp.letrasBasicasDigitos, message = RExpErrors.letrasBasicasDigitos)
    @Column(name = "NUMERO_EXTERIOR")
    private String numeroE;
    
    @Column(name = "NUMERO_INTERIOR")
    private String numeroI;
    
    @Pattern(regexp = RExp.digitosEspacios, message = RExpErrors.digitosEspacios)
    @Column(name = "TELEFONO1")
    private String telefono1;
    
    @Pattern(regexp = RExp.digitosEspacios, message = RExpErrors.digitosEspacios)
    @Column(name = "EXTENSION1")
    private String extension1;
    
    @Pattern(regexp = RExp.digitosEspacios, message = RExpErrors.digitosEspacios)
    @Column(name = "TELEFONO2")
    private String telefono2;
    
    @Pattern(regexp = RExp.digitosEspacios, message = RExpErrors.digitosEspacios)
    @Column(name = "EXTENSION2")
    private String extension2;
    
    @NotNull
    @Email(message = "Ingrese una dirección de email valida")
    @Column(name = "EMAIL")
    private String email;
    
    @NotNull
    @Column(name = "STATUS")
    private boolean status;

    // ... Setters & Getters
}