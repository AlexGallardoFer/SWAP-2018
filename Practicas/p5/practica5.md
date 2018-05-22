# Práctica 5: Replicación de bases de datos MySQL

## Alejandro Gallardo Fernández

### Crear una BD e insertar datos
Para realizar esta práctica tenemos que crearnos una BD en MySQL y luego 
insertarle algunos datos. De esta forma tendremos datos con los que 
podremos hacer copias de seguridad. Usaremos la interfaz de línea de 
comandos de MySQL:

> foto

Ahora que tenemos datos insertados en nuestra BD (llamados "datos"), vamos 
a ver cómo entrar y hacer una consulta.

> foto


### Replicar una BD MySQL con *mysqldump*
EL propio MySQL ofrece una herramienta para clonar las BD que tenemos en 
las máquinas, la herramienta es: ***mysqldump***. Esta herramienta tiene 
una cantidad considerable de opciones, con la opción *mysqldump --help* 
podemos ver muchas de ellas.

Lo primero que tendríamos que hacer es ejecutar las siguientes órdenes en 
la máquina 1 (servidor BD principal):

> foto

Luego ejecutamos (también en la máquina 1) el siguiente comando: *mysqldump 
ejemplodb -u root -p > /tmp/ejemplodb.sql*.
Y como habíamos bloqueado las tablas, las tenemos que desbloquear, o sea, 
quitar el "LOCK".

> foto

Ahora ya podemos irnos a la máquina 2, la máquina esclava, para copiar el 
archivo .SQL con todos los datos guardados desde la máquina 1. Usamos la 
orden: *scp IPM1:/tmp/ejemplodb.sql /tmp/* y habremos copiado desde la 
máquina 1 a la máquina 2 los datos que hay almacenados en la BD.

Una vez que ya tenemos la copia de seguridad en el esclavo ya podemos 
importar la BD completa en el MySQL. Para ello creamos primero la BD:

> foto

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
