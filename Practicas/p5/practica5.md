# Práctica 5: Replicación de bases de datos MySQL

## Alejandro Gallardo Fernández

### Crear una BD e insertar datos
Para realizar esta práctica tenemos que crearnos una BD en MySQL y luego 
insertarle algunos datos. De esta forma tendremos datos con los que 
podremos hacer copias de seguridad. Usaremos la interfaz de línea de 
comandos de MySQL:

![img](https://github.com/Taunerify/SWAP-2018/blob/master/Practicas/p5/img/img1.png)

Ahora que tenemos datos insertados en nuestra BD (llamados "datos"), vamos 
a ver cómo entrar y hacer una consulta.

![img](https://github.com/Taunerify/SWAP-2018/blob/master/Practicas/p5/img/img2.png)


### Replicar una BD MySQL con *mysqldump*
EL propio MySQL ofrece una herramienta para clonar las BD que tenemos en 
las máquinas, la herramienta es: ***mysqldump***. Esta herramienta tiene 
una cantidad considerable de opciones, con la opción *mysqldump --help* 
podemos ver muchas de ellas.

Lo primero que tendríamos que hacer es ejecutar las siguientes órdenes en 
la máquina 1 (servidor BD principal):

![img](https://github.com/Taunerify/SWAP-2018/blob/master/Practicas/p5/img/img3.png)

Luego ejecutamos (también en la máquina 1) el siguiente comando: *mysqldump 
ejemplodb -u root -p > /tmp/ejemplodb.sql*.
Y como habíamos bloqueado las tablas, las tenemos que desbloquear, o sea, 
quitar el "LOCK".

![img](https://github.com/Taunerify/SWAP-2018/blob/master/Practicas/p5/img/img4.png)

Ahora ya podemos irnos a la máquina 2, la máquina esclava, para copiar el 
archivo .SQL con todos los datos guardados desde la máquina 1. Usamos la 
orden: *scp IPM1:/tmp/ejemplodb.sql /tmp/* y habremos copiado desde la 
máquina 1 a la máquina 2 los datos que hay almacenados en la BD.

Una vez que ya tenemos la copia de seguridad en el esclavo ya podemos 
importar la BD completa en el MySQL. Para ello creamos primero la BD:

![img](https://github.com/Taunerify/SWAP-2018/blob/master/Practicas/p5/img/img5.png)

Luego restauramos los datos contenidos en la BD con la orden: *mysqldump -u 
root -p ejemplodb < /tmp/ejemplodb.sql*.


### Replicación de BD mediante una configuración *maestro-esclavo*
Lo que acabamos de hacer funciona perfectamente, pero es algo que habría 
que estar realizando a mano. Sin embargo, MySQL tiene la opción de 
configurar un demonio para hacer la replicación de las BD sobre un esclavo 
a partir de los datos que al macena el maestro.
Como configuramos un demonio, se trata de un proceso automático que ya es 
mucho más cómodo, aunque hay que configurar tanto el servidor principal 
como el secundario.

Lo primero que tenemos que hacer es hacer la configuración del maestro. 
Para ello editamos el archivo de configuración de mysql para realizar las 
siguientes modificaciones.

- Comentamos el parámetro bind-address que sirve para que que escuche a un 
servidor.
> #bind-address 192.168.150.128
- Le indicamos dónde almacenar el log de errores.
> log_error = /var/log/mysql/error.log
-Establecemos el identificador del servidor.
> server-id = 1
- Guardamos el documento y reiniciamos el servicio.
> /etc/init.d/mysql restart


Ahora ya podemos pasar a la configuración del mysql del esclavo (hay que 
editar su archivo de configuración).

![img](https://github.com/Taunerify/SWAP-2018/blob/master/Practicas/p5/img/img6.png)

La configuración es igual que la del maestro con la diferencia de que el 
*server-id* será 2. Reiniciamos el servicio.

Entramos en mysql y ejecutamos las siguientes órdenes:

![img](https://github.com/Taunerify/SWAP-2018/blob/master/Practicas/p5/img/img7.png)

Volvemos a la máquina esclava, entramos en mysql y le damos los datos del 
maestro. Entramos en el entorno de mysql y ejecutaremos la siguiente 
sentencia:

![img](https://github.com/Taunerify/SWAP-2018/blob/master/Practicas/p5/img/img8.png)

Arrancamos el esclavo con la orden: *mysql> START SLAVE;* y ya 
está todo listo para que los demonios 
funcionen correctamente y repliquen automáticamente los datos que se 
cambien en el maestro.

A partir de aqui ya podemos hacer pruebas en el maestro y se deberían de 
replicar en el esclavo automáticamente.

Por último colvemos al maestro y volvemos a activar las tablas para que 
puedan meterse nuevos datos en el maestro: *mysql> UNLOCK TABLES;*
