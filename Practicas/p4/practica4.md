# Práctica 4: Asegurar la granja web
## Alejandro Gallardo Fernández

### Instalar un certificado SSL autofirmado para configurar el acceso por HTTPS
Para generar un certificado SSL autofirmado en Ubuntu Server solo debemos 
activar el módulo SSL de Apache, generar los certificados y especificarle 
la ruta a los certificados en la configuración. Ejecutaremos como root las 
siguientes órdenes:
* a2enmod ssl
* service apache2 restart
* mkdir /etc/apache2/ssl
* openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout 
/etc/apache2/sl/apache.key -out /etc/apache2/ssl/apache.crt

> foto

Nos va a pedir una serie de datos para conigurar el dominio.
Así que editamos el archivo de configuración del sitio default-ssl con la 
orden: *nano /etc/apache2/sites-available/default-ssl* y agregamos un par 
de lineas debajo de *'SSLENgine on'*

> foto

Activamos el sitio *default--ssl* y reiniciamos apache:

>foto

Ahora que hemos reiniciado Apache, accedemos al servidor web mediante el 
protocolo HTTPS y vemos, si estamos accediendo con un navegador web, que en 
la barra de dirección sale en rojo el https, porque se trata de un 
certificado autofirmado.
Usamos la herramienta curl para hacer peticiones por HTTPS:

> foto

### Configuración del cortafuegos
