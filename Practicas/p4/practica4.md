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

Nos va a pedir una serie de datos para conigurar el dominio.

![img](https://github.com/Taunerify/SWAP-2018/blob/master/Practicas/p4/img/install_ssl.png)

Editamos el archivo de configuración del sitio default-ssl con la 
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

Al acabar todo esto habremos "terminado" con M1, ahora hay que copiarles 
los dos archivos que creamos al principio a la M2 y al balanceador.

Tenemos que copiarlos tal cual, no sirve hacer las órdenes de nuevo en esas 
máquinas porque crearían archivos diferentes y no queremos eso.

Además de copiarlos, en el balanceador hay que configurar algunas cosillas 
en los archivos de configuración del programa que hace el balanceo para que 
todo funcione.

>foto

### Configuración del cortafuegos iptables
Vamos a ver cómo utilizar la herramienta para establecer ciertas reglas y 
filtrar algunos tipos de tráfico o controlar el acceso a ciertas páginas.

Hay un montón de órdenes para configurar **iptables**, y están en el guión 
de la práctica.

Lo más común es **crear un script** que se ejecute en el arranque del 
sistema. Vamos a ver cómo realizar una configuración básica para un 
servidor web:

> foto

Hecho esto en la M1, ya solo queda probar que funcione haciendo peticiones 
HTTP y HTTPS tanto a la M1 como al balanceador y ver que funciona.

> foto

Todo funciona correctamente :octocat:.
