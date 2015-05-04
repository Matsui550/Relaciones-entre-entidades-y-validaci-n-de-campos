Vamos a construir tres entidades, relacionarlas y asignar validaciones para varios campos.

##Consideremos el siguiente ejemplo:

![ER Sample](http://i.imgur.com/uvLW2xm.png)

 * Hay tres entidades, ```CLIENTE``` ```ORDEN_VENTA``` y ```FACTURA```.
 * Se relacionan: ```CLIENTE``` tiene muchas ```ORDEN_VENTA``` a su vez que ```ORDEN_VENTA``` tiene una (y solo una) ```FACTURA```.

##Construyendo la entidad 'CLIENTE'

Como se ha mostrado en ejemplos anteriores configuramos una entidad y sus campos mediante anotaciones ```@Entity```, ```@Table```, ```@Column```, ```@Id``` etc.

En esta ocación, también vamos a poner restricciones a ciertos campos de nuestra entidad, por ejemplo, el campo de teléfono solo aceptará digitos y espacios, el email deberá ser un email apropiado, el nombre de contacto solo admitirá letras, puntos y espacios y debe tener una longitud minima de 2 caracteres y máxima de 100, entre otras. **Para dichas validaciones utilizaremos las anotaciones:** ```@Pattern```, ```@Size``` y ```@Email```.

### La anotación @Size
Útil para validar el tamaño de cadenas de texto, por ejemplo para validar que una cadena tenga entre 2 y 100 caracteres:

```java
@Size(min=2, max=100, message = "Debe tener entre 2 y 100 caracteres")
@Column(name = "NOMBRE")
private String nombreContacto;
```

 * El parametro ```min``` indica la longitud minima aceptable.
 * El parametro ```max``` indica la longitud máxima aceptable.
 * El parametro ```message``` indica el mensaje de error que ocasionalmente se enviará al usuario cuando el campo no cumpla con el tamaño minimo o máximo (Posteriormente veremos como aprovechar este mensaje).

### La anotación @Pattern
Util para validar que una cadena de texto utilizando expresiones regulares. En el paquete ```com.sapito.db.util``` existen las clases ```RExp``` y ```RExpErrors``` las cuales contienen algunas
de **las expresiones regulares mas comunes** asi como **mensajes de error** para cuando no se cumplan dichas expresiones respectivamente.

Ejemplo:
```java
@Pattern(regexp = "^[A-Z]+$", message = "Solo se admiten letras mayúsculas")
@Column(name = "COLUMNA")
private String campoX
```
 * En este ejemplo solo se admiten letras mayúsculas y se valida mediante la expresión regular "^[A-Z]+$"

Para **facilitar este tipo de tareas** se recomienda utilizar las clases ```RExp``` y ```RExpErrors```. Si hay alguna expresión que utilices continuamente, puedes solicitar que se agregue a la clase.

Ejemplo con las clases auxiliares:
```java
@Pattern(regexp = RExp.letrasAcentuadasPuntos, message = RExpErrors.letrasAcentuadasPuntos)
@Column(name = "NOMBRE")
private String nombreContacto;
```
 * De esta manera el campo ```nombreContacto``` solo admitirá letras(Mayúsculas o minusculas), letras acentuadas y puntos.
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
Las anotacines pueden utilizarce indistintamente sobre un mismo campo. Con lo cual nuestra entidad ```Cliente``` quedaría de la siguiente forma:

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
```

### La entidad Orden_Venta.
Utilizando anotaciones la entidad quedará de la siguiente forma:
```java
package com.sapito.db.entities;

import java.io.Serializable;
import java.util.Date;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.JoinColumn;
import javax.persistence.ManyToOne;
import javax.persistence.OneToOne;
import javax.persistence.Table;
import javax.validation.constraints.NotNull;

/**
 *
 * @author giovanni
 */
@Entity
@Table(name = "ORDEN_VENTA")
public class OrdenVenta implements Serializable
{
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE)
    @Column(name = "ID")
    private Long id;

    @Column(name = "FECHA_PEDIDO")
    private Date fechaPedido;
    
    @NotNull
    @Column(name = "FECHA_ENTREGA")
    private Date fechaEntrega;
    
    @NotNull
    @Column(name = "MONTO")
    private double monto;
    
    @Column(name = "FACTuRADA")
    private boolean facturada;
    
    @Column(name = "DEPOSITO")
    private boolean deposito;
 
    // ... Setters & Getters
}
```

### La entidad Factura
De la siguiente forma:
```java
package com.sapito.db.entities;

import java.io.Serializable;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.OneToOne;
import javax.persistence.Table;
import javax.validation.constraints.NotNull;

/**
 *
 * @author giovanni
 */
@Entity
@Table(name = "FACTURA")
public class Factura implements Serializable
{
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE)
    @Column(name = "ID")
    private Long id;
    
    @Column(name = "SUBTOTAL")
    private double subTotal;
    
    @Column(name = "TOTAL")
    private double total;
    
    @Column(name = "IVA")
    private double IVA;

    // Setters & Getters
}
```

## Relaciones entre entidades.
Ahora vamos a crear relaciones entre dichas entidades.

### Cliente tiene muchas OrdenVenta
Mediante la anotación ```@OneToMany``` en la entidad Cliente definimos su relación (Un ```Cliente``` tiene muchas ```OrdenVenta```) con la entidad ```OrdenVenta```:

```java
@OneToMany(cascade = CascadeType.ALL, mappedBy = "cliente")
private Collection<OrdenVenta> ordenesVenta;
```

 * El parametro ```mappedBy``` indica que en la entidad ```OrdenVenta``` hay un campo llamado ```cliente``` que almacena una referencia al cliente que posee la ```OrdenVenta```

Definimos la relación también en la entidad ```OrdenVenta```:
```java
@JoinColumn(name = "ID_CLIENTE")
@ManyToOne
private Cliente cliente;
```
 * ```@JoinColumn(name = "ID_CLIENTE")``` Indica que la entidad utilizara una columna llamada ID_CLIENTE para almacenar una referencia al cliente al que pertenece la ```OrdenVenta```
 * ```@ManyToOne``` Indica que muchas ```OrdenVenta``` pertenecen a un ```Cliente```.

De esta forma queda definida la relación entre ```Cliente``` y ```OrdenVenta```

### OrdenVenta tiene una Factura
Definimos la relación en ```OrdenVenta```:
```java
@OneToOne
@JoinColumn(name = "ORDEN_VENTA_ID")
private Factura factura;
```
 * ```@JoinColumn``` Funciona de forma analoga a la del campo ```cliente```
 * ```@OneToOne``` Indica que una ```Factura``` se asocia con una (Y solo una) ```OrdenVenta```

De esta manera las relaciones entre las tres tablas ya estan definidas. Ahora debes hacer **Clean and Build** y ejecutar la aplicación para que hibernate se encargue de crear las tablas y sus relaciones en la base de datos.

> **NOTA** Una vez que las entidades de tu modulo esten terminadas, debes notificar a tu lider para que este a su vez notifique a todos los demas lideres. En cuanto las entidades de todos los equipos esten listas, se inhabilitará el parametro que indica a hibernate que cree las tablas cada vez que se inicia la aplicación y se podra trabajar en el resto de requerimientos.

## El código completo:
Para propositos de consulta. Dejo el código completo de las tres clases/entidades del ejemplo:

La clase ```Cliente```
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

/**
 *
 * @author giovanni
 */
@Entity
@Table(name = "CLIENTE")
public class Cliente implements Serializable
{
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE)
    @Column(name = "ID")
    private Long id;
    
    @NotNull
    @Size(min=1, max=500, message = "El nombre de la instancia debe tener entre 1 y 500 caracteres")
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
    

/** *** *** *** *** *** *** **** *** *** *** *** *** *** */
/** *** *** *** *** ***  RELACIONES  *** *** *** *** *** */
    
    @OneToMany(cascade = CascadeType.ALL, mappedBy = "cliente")
    private Collection<OrdenVenta> ordenesVenta;
    
    
    
/** *** *** *** *** *** *** **** *** *** *** *** *** *** */
/** *** *** *** *** *** *** **** *** *** *** *** *** *** */
    

    public Long getId()
    {
        return id;
    }

    public void setId(Long id)
    {
        this.id = id;
    }

    public String getEmpresa()
    {
        return empresa;
    }

    public void setEmpresa(String empresa)
    {
        this.empresa = empresa;
    }

    public String getSucursal()
    {
        return sucursal;
    }

    public void setSucursal(String sucursal)
    {
        this.sucursal = sucursal;
    }

    public String getRfc()
    {
        return rfc;
    }

    public void setRfc(String rfc)
    {
        this.rfc = rfc;
    }

    public String getNombreContacto()
    {
        return nombreContacto;
    }

    public void setNombreContacto(String nombreContacto)
    {
        this.nombreContacto = nombreContacto;
    }

    public String getApellidoPaternoContacto()
    {
        return apellidoPaternoContacto;
    }

    public void setApellidoPaternoContacto(String apellidoPaternoContacto)
    {
        this.apellidoPaternoContacto = apellidoPaternoContacto;
    }

    public String getApellidoMaternoContacto()
    {
        return apellidoMaternoContacto;
    }

    public void setApellidoMaternoContacto(String apellidoMaternoContacto)
    {
        this.apellidoMaternoContacto = apellidoMaternoContacto;
    }

    public String getPais()
    {
        return pais;
    }

    public void setPais(String pais)
    {
        this.pais = pais;
    }

    public String getEstado()
    {
        return estado;
    }

    public void setEstado(String estado)
    {
        this.estado = estado;
    }

    public String getMunicipio()
    {
        return municipio;
    }

    public void setMunicipio(String municipio)
    {
        this.municipio = municipio;
    }

    public String getColonia()
    {
        return colonia;
    }

    public void setColonia(String colonia)
    {
        this.colonia = colonia;
    }

    public int getCp()
    {
        return cp;
    }

    public void setCp(int cp)
    {
        this.cp = cp;
    }

    public String getCalle()
    {
        return calle;
    }

    public void setCalle(String calle)
    {
        this.calle = calle;
    }

    public String getNumeroE()
    {
        return numeroE;
    }

    public void setNumeroE(String numeroE)
    {
        this.numeroE = numeroE;
    }

    public String getNumeroI()
    {
        return numeroI;
    }

    public void setNumeroI(String numeroI)
    {
        this.numeroI = numeroI;
    }

    public String getTelefono1()
    {
        return telefono1;
    }

    public void setTelefono1(String telefono1)
    {
        this.telefono1 = telefono1;
    }

    public String getExtension1()
    {
        return extension1;
    }

    public void setExtension1(String extension1)
    {
        this.extension1 = extension1;
    }

    public String getTelefono2()
    {
        return telefono2;
    }

    public void setTelefono2(String telefono2)
    {
        this.telefono2 = telefono2;
    }

    public String getExtension2()
    {
        return extension2;
    }

    public void setExtension2(String extension2)
    {
        this.extension2 = extension2;
    }

    public String getEmail()
    {
        return email;
    }

    public void setEmail(String email)
    {
        this.email = email;
    }

    public boolean isStatus()
    {
        return status;
    }

    public void setStatus(boolean status)
    {
        this.status = status;
    }

    public Collection<OrdenVenta> getOrdenesVenta()
    {
        return ordenesVenta;
    }

    public void setOrdenesVenta(Collection<OrdenVenta> ordenesVenta)
    {
        this.ordenesVenta = ordenesVenta;
    }
    
}
```

La clase ```OrdenVenta```
```java
package com.sapito.db.entities;

import java.io.Serializable;
import java.util.Date;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.JoinColumn;
import javax.persistence.ManyToOne;
import javax.persistence.OneToOne;
import javax.persistence.Table;
import javax.validation.constraints.NotNull;

/**
 *
 * @author giovanni
 */
@Entity
@Table(name = "ORDEN_VENTA")
public class OrdenVenta implements Serializable
{
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE)
    @Column(name = "ID")
    private Long id;

    @Column(name = "FECHA_PEDIDO")
    private Date fechaPedido;
    
    @NotNull
    @Column(name = "FECHA_ENTREGA")
    private Date fechaEntrega;
    
    @NotNull
    @Column(name = "MONTO")
    private double monto;
    
    @Column(name = "FACTuRADA")
    private boolean facturada;
    
    @Column(name = "DEPOSITO")
    private boolean deposito;
    
/* *** *** *** *** *** *** *** *** *** *** *** ***/
/* *** *** *** ***  RELACIONES *** *** *** *** ***/
    
    @JoinColumn(name = "ID_CLIENTE")
    @ManyToOne
    private Cliente cliente;
    
    @OneToOne
    @JoinColumn(name = "ORDEN_VENTA_ID")
    private Factura factura;
    
/* *** *** *** *** *** *** *** *** *** *** *** ***/
/* *** *** *** *** *** *** *** *** *** *** *** ***/

    
    public Long getId()
    {
        return id;
    }

    public void setId(Long id)
    {
        this.id = id;
    }

    public Date getFechaPedido()
    {
        return fechaPedido;
    }

    public void setFechaPedido(Date fechaPedido)
    {
        this.fechaPedido = fechaPedido;
    }

    public Date getFechaEntrega()
    {
        return fechaEntrega;
    }

    public void setFechaEntrega(Date fechaEntrega)
    {
        this.fechaEntrega = fechaEntrega;
    }

    public double getMonto()
    {
        return monto;
    }

    public void setMonto(double monto)
    {
        this.monto = monto;
    }

    public boolean isFacturada()
    {
        return facturada;
    }

    public void setFacturada(boolean facturada)
    {
        this.facturada = facturada;
    }

    public boolean isDeposito()
    {
        return deposito;
    }

    public void setDeposito(boolean deposito)
    {
        this.deposito = deposito;
    }

    public Cliente getCliente()
    {
        return cliente;
    }

    public void setCliente(Cliente cliente)
    {
        this.cliente = cliente;
    }

    public Factura getFactura()
    {
        return factura;
    }

    public void setFactura(Factura factura)
    {
        this.factura = factura;
    }
    
}
```

La clase ```Factura```
```java
/*
 * To change this license header, choose License Headers in Project Properties.
 * To change this template file, choose Tools | Templates
 * and open the template in the editor.
 */
package com.sapito.db.entities;

import java.io.Serializable;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.OneToOne;
import javax.persistence.Table;
import javax.validation.constraints.NotNull;

/**
 *
 * @author giovanni
 */
@Entity
@Table(name = "FACTURA")
public class Factura implements Serializable
{
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE)
    @Column(name = "ID")
    private Long id;
    
    @Column(name = "SUBTOTAL")
    private double subTotal;
    
    @Column(name = "TOTAL")
    private double total;
    
    @Column(name = "IVA")
    private double IVA;
    
    
/* *** *** *** *** *** *** *** *** *** *** *** ***/
/* *** *** *** ***  RELACIONES *** *** *** *** ***/
    
    @OneToOne(mappedBy = "factura")
    private OrdenVenta ordenVenta;
    
/* *** *** *** *** *** *** *** *** *** *** *** ***/
/* *** *** *** *** *** *** *** *** *** *** *** ***/
    

    public Long getId()
    {
        return id;
    }

    public void setId(Long id)
    {
        this.id = id;
    }

    public double getSubTotal()
    {
        return subTotal;
    }

    public void setSubTotal(double subTotal)
    {
        this.subTotal = subTotal;
    }

    public double getTotal()
    {
        return total;
    }

    public void setTotal(double total)
    {
        this.total = total;
    }

    public double getIVA()
    {
        return IVA;
    }

    public void setIVA(double IVA)
    {
        this.IVA = IVA;
    }


    public OrdenVenta getOrdenVenta()
    {
        return ordenVenta;
    }

    public void setOrdenVenta(OrdenVenta ordenVenta)
    {
        this.ordenVenta = ordenVenta;
    }
    
    
    
}
```