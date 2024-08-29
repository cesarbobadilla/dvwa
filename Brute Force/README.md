About

Damn Vulnerable Web Application (DVWA) is a PHP/MySQL web application that is damn vulnerable. Its main goals are to be an aid for security professionals to test their skills and tools in a legal environment, help web developers better understand the processes of securing web applications and aid teachers/students to teach/learn web application security in a class room environment

Project Home: https://github.com/digininja/DVWA

Si usa https://labs.play-with-docker.com/ se recomienda usar 

docker login -u username -p password

En la carpeta SSH, usamos el archivo Dockerfile, luego 

docker build -t alpine-ssh .

docker run --rm --publish=2222:22 alpine-ssh

Desde otro contenedor 

ssh root@192.168.0.6 -p 2222

Ponemos el password: computer

En la carpeta HYDRA, usamos el archivo Dockerfile, luego

docker build -t kali-hydra .

docker run -it kali-hydra /bin/bash 

