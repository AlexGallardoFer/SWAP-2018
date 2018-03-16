# Práctica 3: Balanceo de carga

## Alejandro Gallardo Fernández

## Breve resumen sobre qué consiste la práctica
En esta práctica lo que vamos a hacer es crear una tercera máquina en la que instalaremos dos balanceadores de 
carga distintos, para que repartan la carga entre las dos máquinas que creamos en la práctica 1: **nginx** y 
**haproxy**. Mediremos el rendimiento de los dos balanceadores por separado 
sometiéndoles a una alta carga.

## Instalar **nginx** en ubuntu server
Se recomienda el uso de esta orden para instalar **nginx**: *sudo apt-get update && sudo apt-get dist-upgrade && 
sudo apt-get autoremove*. Después la orden: *sudo apt-get install nginx*. Y por último, la orden: *sudo 
systemctl start nginx*. 
