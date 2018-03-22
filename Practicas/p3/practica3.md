# Práctica 3: Balanceo de carga

## Alejandro Gallardo Fernández

## Breve resumen sobre qué consiste la práctica
En esta práctica lo que vamos a hacer es crear una tercera máquina en la que instalaremos dos balanceadores de 
carga distintos, para que repartan la carga entre las dos máquinas que creamos en la práctica 1: **nginx** y 
**haproxy**. Mediremos el rendimiento de los dos balanceadores por separado 
sometiéndoles a una alta carga.

## Instalar **nginx** en ubuntu server
Se recomienda el uso de esta orden para instalar **nginx**: *sudo apt-get update && sudo apt-get dist-upgrade && 
sudo apt-get autoremove*.
Después la orden: *sudo apt-get install nginx*.
![img](https://github.com/Taunerify/SWAP-2018/blob/master/Practicas/p3/img/install%20nginx.png)
Y por último, la orden: *sudo systemctl start nginx*.
![img](https://github.com/Taunerify/SWAP-2018/blob/master/Practicas/p3/img/start%20nginx.png) 

Una vez instalado, podemos proceder a su configuración como balanceador de carga.

## Balanceo de carga usando nginx
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
