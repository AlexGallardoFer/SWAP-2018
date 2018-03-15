# Práctica 2: Clonar la información de un sitio web

## Alejandro Gallardo Fernández

## Crear un tar con ficheros locales en un equipo remoto

La idea de esta función es por si tenemos que crear un tar.gz en nuestro 
equipo y pasarlo a otro equipo, pero con el problema de que en nuestro 
equipo no tenemos espacio. Para resolver el problema podemos usar *ssh* 
para crear el tar.gz directamente en el otro equipo.

Con el comando: *tar czf - <directorio> | ssg <otro_equipo> 'cat > 
~/tar.gz'* habremos creado en la máquina *<otro_equipo>* el tar.gz que 
creamos en la primera parte del comando.

Para lo que queremos hacer en esta asignatura, merece más la pena usar 
**rsync**.

## Instalar la herramienta **rsync**

Lo primero que he hecho ha sido instalar la herramienta **rsync** con el 
comando *sudo apt-get install rsync*.

Como recomendación para no hacer algo mal sin querer en un futuro, en vez 
de trabajar como root, nos damos permisos al usuario normal en */var/www/* 
con la orden: *sudo chown alexgallardo:alexgallardo -R /var/www*

Para comprobar el funcionamiento de **rsync** probamos a clonar una carpeta 
cualquiera de la máquina principal en la máquina secundaria, para eso, en 
la máquina secundaria usamos la orden: *rsync -avz -e ssh 
<ip_maquina1>:/var/ww/ /var/www/*. Nos pedirá la pass del usuario de la 
máquina principal y después podemos comprobar que se ha clonado 
correctamente el contenido de la máquina principal en la máquina secundaria 
con la orden: *ls -la /var/www*.

> Hay que tener en cuenta que los directorios y ficheros copiados en la 
máquina destino mantienen los permisos y dueño que en la máquina origen.

## Acceso sin contraseña para **ssh**

Mediante **ssh-keygen** podemos generar un par de claves pública-privada 
con la orden: *ssh-keygen -b 4096 -t rsa*, tenemos que usar este comando en 
la máquina secundaria.

Para copiar la clave en la máquina principal, para que podamos acceder por 
ssh a ella sin tener que poner una pass cada vez que nos queramos conectar, 
usamos la orden: *ssh-copy-id <maquina1>. A partir de aqui ya podremos 
conectarnos a dicho equipo sin contraseña.

## Programar tareas con **crontab**

Gracias a **cron** que es un administrador de procesos en segund plano, 
podemos "programar" que se ejecute una orden en la máquina secundaria para 
que esté todo el rato checkeando la máquina principal en busca de cambios, 
y si los hubiera, clonarlos a la máquina secundaria.

Para ello tenemos que editar el archivo */etc/crontab* y añadimos la 
línea: *\* \* \* \* \* alexgallardo rsync -avz -e ssh 
<ip_maquina1>:/var/www/ /var/www/*, donde los \* significan:

1. Significa el *minuto* exacto de una hora. <0-59>
2. Significa la *hora* exacta de un día. <0-23>
3. Significa el *día* exacto de un mes. <1-31>
4. Significa el *mes* exacto de un año. <1-12>
5. Significa el *día de la semana*. <1=lunes-7=domingo>

Si en vez de poner números ponemos \* en su lugar, estamo marcando *todo*, 
por ejemplo, si ponemos \* en los *días* del mes, se ejecutará **todos** 
los días del mes que esté especificado (o todos en caso de un \* en los 
*meses*).
