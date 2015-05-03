Para que nuestra aplicación (Desplegada en Glassfish 4.1) tenga acceso a nuestra base de datos debemos configurar un pool de conexiones en el servidor de la siguiente manera.

##Prerequisitos

 * Servidor de base de datos PostgreSQL instalado y funcional
 * Servidor Glassfish 4.1 instalado y funcional
 * Netbeans IDE (Version con soporte para JavaEE)

> **NOTA:** Si la base de datos y Glassfish se ejecutan en maquinas distintas, deberas habilitar las conexiones remotas en los archivos de configuración de PostgreSQL.

## Paso 1 | Proporcionar el Driver JDBC para PostgreSQL a Glassfish Server.
Con el servidor en estado 'shutdown' descargamos el Driver desde [este enlace](https://jdbc.postgresql.org/download/postgresql-9.4-1201.jdbc4.jar).

Debes colorcar el driver en la dirección de librerias de Glassfish, generalmente esta se encuentra en el home del usuario del sistema (windows o Linux) por ejemplo: ```/home/giovanni/glassfish-4.1/glassfish/lib``` en Linux o ```C:\\Users\Giovanni\glassfish-4.1/glassfish/lib``` en Windows. Asegurate de poner el Driver descargado en la ruta indicada para poder conectar a la base d datos.

## Paso 2 | Arrancar el servidor y entrar a la consola de administración.
Puedes arrancar el servidor desde Netbeans (En la pestaña de servicios, en la sección 'Servidores' mediante un clic derecho y la opción 'start'):

![Arrancar Glassfish](http://i.imgur.com/zJNDNfK.png)

En la consola de salida de Netbeans podremos ver el proceso de arranque, una vez que el servidor este funcionando podremos entrar a la consola de administración desde un navegador web en la dirección [http://localhost:4848](http://localhost:4848)

## Paso 3 | Crear el Pool de conexiones.

Nos dirigimos a la sección **Resources > JDBC > JDBC Connection Pools** y hacemos clic sobre el boton 'nuevo' e indicamos los siguientes valores para el nuevo Pool:

 * Pool Name: sapito
 * Resource Type: javax.sql.ConnectionPoolDatasource
 * Database Driver Vendor: PostgreSQL
 * Introspect: Enabled

![Imagen con valores](http://i.imgur.com/JxxMCYp.png)

Y hacemos clic sobre el boton 'next'.

En la parte inferior de la página actual eliminamos todas las propiedades y solamente dejamos las siguientes con los valores apropiados:

 * User : El usuario para conectar a la base de datos
 * Password : El password asignado para dicho usuario
 * ServerName : La dirección del Servidor PostgreSQL
 * DatabaseName : El nombre de una base de datos limpia creada en el servidor especialmente para el proyecto
 * PortNumber : El puerto para conectar a PostgreSQL, generalmente el 5432

![Valores del pool](http://i.imgur.com/kzDCTF1.png)

Hacemos clic en el boton 'Finish'. Ahora podremos ver nuestro pool listado en una tabla de Pools, para validar que este funcionando hacemos clic sobre el nombre del Pool y en la página resultante presionamos el boton 'Ping'. si el ping es exitoso nuestro servidor Glassfish ya tiene conexión con la base de datos. De lo contrario revisa los pasos de la configuración o asegurate de que el servidor este activo y aceptando conexiones entrantes.

![Ping Succeeded](http://i.imgur.com/033xKhR.png)

## Paso 4 | Configurar el JDBC Resource

En la misma consola de administración nos dirigimos a la sección **Resources > JDBC > JDBC Resources** y hacemos clic en 'New'. Ingresamos los siguientes valores:

 * JNDI Name: jdbc/sapito
 * Pool name: sapito
 * Status: enabled

![JDBC Resource](http://i.imgur.com/qy3xsRG.png)

y hacemos clic sobre el boton 'ok'. Con esto ya tenemos nuestro servidor configurado para acceder a la base de datos.

