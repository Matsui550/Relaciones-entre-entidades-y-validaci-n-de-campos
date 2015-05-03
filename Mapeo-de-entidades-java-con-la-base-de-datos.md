Vamos a integrar Hibernate al proyecto y a las clases java.

## Paso 1 | Asegurate de tener la versión mas actual del proyecto.

Las versiones recientes del proyecto (hosteado en Github). tienen soporte para Hibernate. Asegurate de hacer un **pull** para contar con la versión mas reciente. Posteriormente haz clic derecho sobre el proyecto y elige la opción 'Built with dependencies', espera a que el proceso termine, luego haz clic derecho sobre el proyecto y elige 'Clean and Build'.

## Paso 2 | Creando una entidad basica.

Cada una de nuestras tablas la vamos a representar como una clase java. A continuación crearemos una entidad muy basica llamada ```Cliente``` que tiene los campos ```ID``` ```NOMBRE``` ```APELLIDO_PATERNO``` y ```APELLIDO_MATERNO```. El código de la clase:

```java
package com.sapito.db.entities;

import java.io.Serializable;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Table;
import javax.validation.constraints.NotNull;

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
    @NotNull
    @Column(name = "ID")
    private long id;
    
    @Column(name = "NOMBRE")
    private String nombre;
    
    @Column(name = "APELLIDO_PATERNO")
    private String apellidoPaterno;
    
    @Column(name = "APELLIDO_MATERNO")
    private String apellidoMaterno;

    /**
     * @return the id
     */
    public long getId()
    {
        return id;
    }

    /**
     * @param id the id to set
     */
    public void setId(long id)
    {
        this.id = id;
    }

    /**
     * @return the nombre
     */
    public String getNombre()
    {
        return nombre;
    }

    /**
     * @param nombre the nombre to set
     */
    public void setNombre(String nombre)
    {
        this.nombre = nombre;
    }

    /**
     * @return the apellidoPaterno
     */
    public String getApellidoPaterno()
    {
        return apellidoPaterno;
    }

    /**
     * @param apellidoPaterno the apellidoPaterno to set
     */
    public void setApellidoPaterno(String apellidoPaterno)
    {
        this.apellidoPaterno = apellidoPaterno;
    }

    /**
     * @return the apellidoMaterno
     */
    public String getApellidoMaterno()
    {
        return apellidoMaterno;
    }

    /**
     * @param apellidoMaterno the apellidoMaterno to set
     */
    public void setApellidoMaterno(String apellidoMaterno)
    {
        this.apellidoMaterno = apellidoMaterno;
    }
    
}
```

**Algunos puntos a destacar sobre esta clase:**

 * Es una clase simple que implementa la interfaz ```Serializable```
 * Utiliza @Anotaciones para indicar ciertos elementos de persistencia.
 * La anotación ```@Entity``` indica que esta clase representa una tabla de la base de datos.
 * La anotación ```@Table``` nos permite indicar con que tabla se relacionara directamente esta entidad.
 * Cada propiedad de la clase representa una columna de la tabla.
 * Para la propiedad ```id``` indicamos las siguientes anotaciones
   * ```@Id``` Significa que esta propiedad es la **Primary Key** de la tabla
   * ```@GeneratedValue``` Indica que el Id es autogenerado y de tipo secuencial.
   * ```@NotNull``` Indica que este campo no puede estar vacio en la tabla
   * ```@column``` indica el nombre de la columna/campo con la que se relaciona esta propiedad
 * Para el resto de propiedades solo hemos indicado cual es la columna con la que se asociaran.

> **NOTA** Cabe destacar que las tablas, relaciones y secuencias no necesitan estar creadas en la bse de datos, solo se requiere que glassfish este conectado a una base de datos limpia y **Hibernate se encargara** de crear toda la estructura de la base de datos (Tablas, Secuencias, Relaciones) por nosotros.

> **NOTA** Asegurate de crear las clases Entidad de tu modulo en el paquete ```com.sapito.db.entities```

## Paso 3.
Finalmente haz un 'Clean and Build' del proyecto y ejecutalo. Una vez que la aplicación haya sido desplegada exitosamente las tablas indicadas habrán sido creadas en la base de datos. Puedes confirmarlo en el servidor de base de datos.

--- ---

**NOTA** En la clase ```com.sapito.db.config.PersistenceConfig``` en la linea ```85``` tenemos la siguiente sentencia: 
```java
setProperty("hibernate.hbm2ddl.auto", "create");
```
Esta sentencia indica que cada vez que la aplicación se ejecute Hibernate debe crear la base de datos (Tablas, Secuencias y relaciones) en el servidor PostgreSQL. En cuanto todos los modulos tengan creadas todas sus entidades deberas ejecutar la aplicación para que cuentes con la base de datos completa. Posteriormente una persona a cargo se encargara de deshabilitar esta sentencia. Deberas hacer un **pull** y se procedera a trabajar con la versión final de la base de datos.