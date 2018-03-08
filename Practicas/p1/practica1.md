# Práctica 1 
## Alejandro Gallardo Fernández

## Instalación de **LAMP SERVER** y **SSH SERVER**
En esta práctica he creado dos máquinas virtuales en las que he instalado el servidor **LAMP** y el 
servidor **SSH**.

En mi caso, lo primero que hice fue una instalación básica de Ubuntu Server, sin especificar nada en especial, y 
una vez instalada, usé la orden *"sudo tasksel"* para abrir el menú que salía en el guión para instalar el 
servidor **LAMP** y el servidor de **SSH**.

Una vez instalados, comprobé la versión de Apache con la orden *"apache2 -v"* y luego comprobé que Apache estaba 
activo con la orden *"ps aux | grep apache"*.


## Instalación de **CURL**
El siguiente paso de la práctica es instalar **cURL**, una herramienta en línea de comandos para transferir 
archivos con sintaxis URL que soporta diferentes protocolos. Para instalar **cURL** en las máquinas usé la orden 
*"sudo apt-get install curl"*.

Con **cURL** instalado, creo un archivo .html simple para poder comprobar que funciona: *hola.html*.
Cuando hago la orden *"curl http://<ip_de_mi_maquina>/hola.html"* me sale el archivo y puedo comprobar que todo 
funciona bien. 

Luego, he comprobado que funciona el curl en las dos máquinas y que puedo conectarme entre ellas con ssh y hacer 
ping.
