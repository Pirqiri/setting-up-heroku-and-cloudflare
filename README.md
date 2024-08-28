# Configuración de Heroku y Cloudflare para un dominio nic.cl

Fuente: [Setting up Heroku and Cloudflare (the right way)](https://gist.github.com/mrispoli24/043684c7af2c5b5ac3b71cd46ac60972)

Este documento cubre la configuración de tu dominio.cl utilizando Heroku y Cloudflare con un certificado SSL. 

## Paso 1: Obtener los DNS generados en la aplicación (Heroku)

* Ingresa a tu aplicación en Heroku.
* Ve a la pestaña **Settings**.
* Desplázate hacia abajo hasta la sección **SSL Certificates** y haz clic en **Configure SSL** para crear un certificado.
* Elige la opción **Automatic Certificate Management (ACM)**.
* Haz clic en el botón **Next**.
* Desplázate hacia abajo hasta la sección **Domains**.
* Haz clic en **Add Domain**.
* Ingresa el nombre de tu dominio `<NOMBRE_DOMINIO>.cl`.
* Presiona el botón **Next** y se desplegará un resumen donde verás el DNS generado para tu dominio (algo como `*****tkb.herokudns.com`).
* Ahora, repite el paso anterior para crear tu dominio incluyendo el `www` (`www.<NOMBRE_DOMINIO>.cl`).
* Tu dominio está ahora configurado en Heroku.
* No cierres esta pestaña, utilizaremos esta información en el paso 4.

## Paso 2: Obtener los DNS de Cloudflare

* Ingresa a [Cloudflare](https://dash.cloudflare.com/sign-up) y crea una cuenta.
* Una vez dentro, ingresa tu dominio en **Introduzca un dominio existente**.
* Ingresa a la configuración de tu dominio, selecciona la opción **Free** y haz clic en **Continuar**.
* Ahora ingresa a **Sitios Web**. Para tu nuevo dominio dirá **Actualización pendiente del servidor de nombres**.
* Selecciona tu dominio, se mostrará la información general.
* En la sección 3, **Actualice los servidores de nombres**, verás los **servidores de nombres Cloudflare asignados** (DNS).
* No cierres esta vista, necesitarás copiar estos DNS en tu cuenta de Nic.cl.

## Paso 3: Configurar Nic.cl para que utilice los DNS de Cloudflare

* Inicia sesión en Nic.cl e ingresa al dominio que quieres configurar dentro de la tabla **Mis Dominios**.
* Busca la sección **Configuración Técnica** y asegúrate de tener seleccionada la opción **Servidores DNS**.
* Ve a la pestaña de Cloudflare (Paso 2) y copia el primer servidor DNS.
* En el campo **Nombre de Servidor**, pega el primer servidor copiado desde Cloudflare.
* Haz clic en el botón **Agregar servidor de nombre**.
* Se mostrará un nuevo campo para agregar un segundo servidor de DNS.
* En el nuevo campo **Nombre de Servidor**, pega el segundo servidor copiado desde Cloudflare.
* Desplázate hasta el final de la vista y haz clic en **Actualizar datos de dominio**.
* La asociación del DNS de Cloudflare con tu dominio `.cl` tomará un tiempo (entre 30 minutos y un par de horas).

## Paso Opcional: Verificar la Propagación de DNS

* Ingresa a [DNS Checker](https://dnschecker.org/#NS/) y escribe el nombre de tu dominio en el campo **DNS Check**.
* Inicia la búsqueda.
* Aquí podrás ver un listado de servidores DNS que conocen tu dominio.

## Paso 4: Configurar los CNAME de tu dominio en Cloudflare

* Ingresa a tu sitio en Cloudflare.
* En el menú izquierdo, selecciona la opción **DNS**.
* En la sección **DNS management for `<NOMBRE_DE_TU_SITIO>`**, haz clic en el botón **Agregar registro**.
* Agrega el primer registro:
  * **Tipo:** `CNAME`
  * **Nombre (Obligatorio):** `<TU_DOMINIO>.CL`
  * **Dirección IPv4 (Obligatorio):** El DNS generado por Heroku para `<TU_DOMINIO>.CL` (obtenido en el paso 1).
* Haz clic en **Guardar**.
* Repite el paso anterior para agregar otro registro:
  * **Tipo:** `CNAME`
  * **Nombre (Obligatorio):** `www`
  * **Dirección IPv4 (Obligatorio):** El DNS generado por Heroku para `www.<TU_DOMINIO>.CL` (obtenido en el paso 1).
* Haz clic en **Guardar**.
* Si todo está correcto, en la columna **Estado de proxy** deberás ver una nube de color naranja.

## Paso 5: Generar el certificado SSL en Cloudflare

* Dentro de Cloudflare, ve a la sección **SSL/TLS**.
* En la sección **Encriptación SSL/TLS**, haz clic en el botón **Configurar**.
* Dentro de **SSL/TLS personalizado**, selecciona **Completo (estricto)** y haz clic en **Guardar**.
* Ahora, en la sección **Servidor de origen**, crea un certificado de origen seleccionando el botón **Crear certificado**.
* Mantén seleccionada la opción **Generar la clave privada y CSR con Cloudflare**.
* En la sección **Enumere los nombres de servidores**, deberías tener `www.<TU_DOMINIO>.CL` y `<TU_DOMINIO>.CL`.
* En la sección **Elija cuánto tiempo debe pasar para que expire el certificado**, mantén la opción **15 años**.
* Selecciona el botón **Crear**.
* Cloudflare desplegará la clave pública y privada del certificado creado.
* No cierres esta pestaña, necesitaremos esta información en el siguiente paso.

## Paso 6: Configurar el certificado SSL generado por Cloudflare en Heroku

* Ve a la pestaña de Heroku (sección **Settings**).
* En la sección **SSL Certificates**, selecciona **Configure SSL**.
* Selecciona **Remove SSL**, esto eliminará el certificado generado inicialmente.
* Ingresa el nombre de tu dominio en la confirmación y confirma.
* Vuelve a ingresar seleccionando el botón **Configure SSL**.
* Esta vez, selecciona la opción **Manual Certificate** y haz clic en **Next**.
* Desde Cloudflare, copia la clave pública y pégala en Heroku.
* Desde Cloudflare, copia la clave privada y pégala en Heroku.
* Listo, ya tienes configurado el certificado y la conexión está encriptada entre los actores (Dominio, Cloudflare (DNS) y Heroku (máquina que tiene tu app)).
