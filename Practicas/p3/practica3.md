# Práctica 3: Balanceo de carga

## Alejandro Gallardo Fernández

## Breve resumen sobre qué consiste la práctica
En esta práctica lo que vamos a hacer es crear una tercera máquina en la que instalaremos dos balanceadores de 
carga distintos, para que repartan la carga entre las dos máquinas que creamos en la práctica 1: **nginx** y 
**haproxy**. Mediremos el rendimiento de los dos balanceadores por separado 
sometiéndoles a una alta carga.

## Instalar *nginx* en ubuntu server
Se recomienda el uso de esta orden para instalar **nginx**: *sudo apt-get update && sudo apt-get dist-upgrade && 
sudo apt-get autoremove*.
Después la orden: *sudo apt-get install nginx*.

![img](https://github.com/Taunerify/SWAP-2018/blob/master/Practicas/p3/img/install%20nginx.png)

Y por último, la orden: *sudo systemctl start nginx*.

![img](https://github.com/Taunerify/SWAP-2018/blob/master/Practicas/p3/img/start%20nginx.png) 

Una vez instalado, podemos proceder a su configuración como balanceador de carga.

## Balanceo de carga usando *nginx*
La configuración básica de nginx no nos sirve como está porque corresponde con la 
funcionalidad de un servidor web, así que tenemos que modificar el fichero 
*/etc/nginx/conf.d/default.conf*. El fichero debería quedar con este contenido:

![img](https://github.com/Taunerify/SWAP-2018/blob/master/Practicas/p3/img/configuracion%20nginx.png)

Una vez configurado, lanzamos el servicio **nginx** con la siguiente orden: *sudo systemctl start nginx*

> Cosas a tener en cuenta a la hora de comprobar que que el balanceador funcione:
> 1. Tenemos que evitar que el *rsync* que tenemos progamado en el crontab se haga, así que lo comentamos por 
ahora.
> 2. Cambiamos los index.html de las primeras dos máquinas para reconocer a cual nos está llevando el 
balanceador.

Después de lanzar el servicio **nginx** (y, si no nos ha dado fallos) probamos a hacer peticiones a la IP de 
esta máquina y deberíamos de ver como va alternando entre la máquina 1 y la máquina 2.


Efectivamente, funciona!

![img](https://github.com/Taunerify/SWAP-2018/blob/master/Practicas/p3/img/m1.png)

![img](https://github.com/Taunerify/SWAP-2018/blob/master/Practicas/p3/img/m2.png)

Otra opción que tenemos es cambiar el *"peso"* de las máquinas. Si una 
máquina es más potente que otra, le ponemos más peso, y el balanceador 
mandará más peticiones a esa máquina, por ejemplo si a la máquina 1 le 
ponemos un peso de 1 y a la máquina 2 un peso de 2, la máquina 2 recibirá 
el doble de carga que la máquina 1. Para cambiar el peso (por defecto es 1 
para todas las máquinas) hay que añadir un par de cosas:

![img](https://github.com/Taunerify/SWAP-2018/blob/master/Practicas/p3/img/pesos.png)

Aunque esta configuración es muy útil a nosotros nos interesa que las 
peticiones que vengan de la misma IP se dirijan a la misma máquina servidora 
final para evitar problemas. Para solucionar eso, hacemos un balanceo por IP, 
para ello usaremos la directiva *ip_hash*:

![img](https://github.com/Taunerify/SWAP-2018/blob/master/Practicas/p3/img/ip_hash.png)

Pero la desventaja que hay en el balanceo *ip_hash* es que todos los usuarios 
de un proxy serán dirigidos al mismo backend y supone que el balanceo no sea 
equilibrado. Para evitar esto se pueden usar *cookies* para balancear.

A partir de la versión 1.2 de **nginx** se pueden usar ya conexiones con 
*keepalive* entre el **nginx** y los servidores finales. Tenemos que volver a 
modificar nuestro *upstream*, añadiendo la directiva *keepalive* y un tiempo de 
mantenimiento de la conexión en segundos:

![img](https://github.com/Taunerify/SWAP-2018/blob/master/Practicas/p3/img/keepalive.png)

Y hasta aquí la parte de **nginx**, pero antes de seguir, paramos el servicio 
con la orden: *sudo systemctl stop nginx*. Ahora toca empezar la parte de **haproxy**.

## Instalar *haproxy*
Lo primero que tenemos que hacer es instalar **haproxy**: *sudo apt-get install 
haproxy*.

![img](https://github.com/Taunerify/SWAP-2018/blob/master/Practicas/p3/img/install%20haproxy.png)

Una vez instalado, modificamos el archivo */etc/haproxy/haproxy.cfg* porque 
como viene por defecto no nos sirve. Por ahora lo dejaremos como en la 
siguiente imagen:

![img](https://github.com/Taunerify/SWAP-2018/blob/master/Practicas/p3/img/configuracion%20haproxy.png)

## Comprobar el funcionamiento del balanceador

Una vez que ya tenemos la configuración en el fichero, lanzamos **haproxy** con 
el siguiente comando: *sudo /usr/sbin/haproxy -f /etc/haproxy/haproxy.cfg*.

Si no sale error, ya estaría hecho y podríamos comprobarlo como lo hicimos con 
**nginx**, usando **cURL** con la IP de la máquina balanceadora para ver como 
nos manda a una máquina o a otra.

![img](https://github.com/Taunerify/SWAP-2018/blob/master/Practicas/p3/img/install%20haproxy.png)

Funciona perfectamente.

## Someter a una alta carga al servidor balanceado
Vamos a usar una herramienta llamada **Apache Benchmark** para comprobar el 
rendimiento de nuestra granga web recién configurada.

Lo suyo es que ejecutemos el benchmark en otra máquina diferente a las que 
forman parte de la granja web (servidores web o balanceador) para que así no 
consuman recursos de la misma máquina y habría un menor rendimiento. La orden 
que hay que utilizar es la siguiente: *ab -n 1000 -c 10 http:192.168.150.130/index.html*.

Primero vamos a someter al balanceador **nginx** la carga:

![img](https://github.com/Taunerify/SWAP-2018/blob/master/Practicas/p3/img/cargaNginx1.png)
![img](https://github.com/Taunerify/SWAP-2018/blob/master/Practicas/p3/img/cargaNginx2.png)

Ahora vamos a someter al balanceador **haproxy**:

![img](https://github.com/Taunerify/SWAP-2018/blob/master/Practicas/p3/img/cargaHaproxy1.png)
![img](https://github.com/Taunerify/SWAP-2018/blob/master/Practicas/p3/img/cargaHaproxy2.png)


Como podemos ver, el balanceador de carga **haproxy** es más rápido que **nginx**, mirando el tiempo total que 
ha tardado en hacer el test.
