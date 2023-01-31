# Práctica 1 - Configuración de máquina virtual en el IaaS

## Configuración Previa :gear:

Una vez realizado las tareas previas:
- Disponer de Github Education.
- Unirse al Github Classroom de la asignatura.
- Haber aceptado [la asignación de esta misma tarea.](https://classroom.github.com/a/JBCmrK5V)
- Haber consultado los materiales de referencia de [Introducción a Markdown](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax) y [Github Pages](https://docs.github.com/en/pages).

Estamos listos para comenzar la práctica.

## Configuración de la Máquina Virtual :desktop_computer:

Una vez instalado la VPN de la ULL (a través del programa [Global Protect](https://www.paloaltonetworks.es/resources/datasheets/globalprotect-datasheet) para Windows, o a través de los comandos proporcionados por la [guía de instalacion](https://docs.google.com/document/d/1xhSRVqo6y5HYtQQtBemLEwDG6a_yjGlzrxjwuYxIQAk/edit) en Ubuntu), y asignada nuestra máquina virtual, iniciamos sesión por primera vez.

![First Login](/docs/assets/img01.png)

<sub>*Como se indica en el [guión](https://ull-esit-inf-dsi-2223.github.io/prct01-iaas/) de la práctica, tras el primer login es mandatorio cambiar la contraseña del nuevo usuario debido a motivos de seguridad.</sub>

Para conocer la dirección ip de la mv, basta con utilizar el commando ```ifconfig -a```. Una vez obtenida la ip, podremos hacer una conexión por SSH desde cualquier ordenador a la MV.

<sub>*Siempre y cuando tengamos activa la VPN de la institución</sub>

```
ssh usuario@10.X.XXX.XXX
```
Introducimos ```yes``` para aceptar la conexión:
```
The authenticity of host '10.X.XXX.XXX (10.X.XXX.XXX)' can't be established.
ED25519 key fingerprint is SHA256:XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.X.XXX.XXX' (ED25519) to the list of known hosts.
```
Una vez introducida la nueva contraseña del ```usuario``` se realizaría la conexión.
```
  Memory usage: 7%                 IPv4 address for ens3: 10.X.XXX.XXX
  Swap usage:   0%


0 updates can be applied immediately.


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
New release '22.04.1 LTS' available.
Run 'do-release-upgrade' to upgrade to it.


Last login: Tue Jan 31 16:12:05 2023
```
Para cambiar el nombre del Host, modificamos el fichero ```/etc/hostname``` situado en la raíz.
```
usuario@ubuntu:~$ cat /etc/hostname
ubuntu
usuario@ubuntu:~$ sudo vi /etc/hostname
usuario@ubuntu:~$ cat /etc/hostname
iaas-dsi
```
Asimismo se debe modificar el fichero ```/etc/hosts```:
```
usuario@ubuntu:~$ cat /etc/hosts
127.0.0.1 localhost
127.0.1.1 ubuntu
usuario@ubuntu:~$ sudo vi /etc/hosts
usuario@ubuntu:~$ cat /etc/hosts
127.0.0.1 localhost
127.0.1.1 iaas-dsi
```
Y reinciamos la mv:
```
usuario@ubuntu:~$ sudo reboot
Connection to 10.X.XXX.XXX closed by remote host.
Connection to 10.X.XXX.XXX closed.
```
Una vez reiniciada, volvemos a iniciar sesión en la mv y buscamos todas las actualizaciones disponibles para la mv.
```
sudo apt update
```
Una vez encontradas, procedemos a actualizar todo el software.
```
sudo apt upgrade
```
Volvemos a reinciar la mv:
```
usuario@ubuntu:~$ sudo reboot
Connection to 10.X.XXX.XXX closed by remote host.
Connection to 10.X.XXX.XXX closed.
```
Aprovechamos para añadir permanentemente la dirección ip de nuestra mv en el fichero ```/etc/hosts``` así no tenemos que volver a recordar la ip.
```
iluzio@Peridot:~$ cat /etc/hosts
127.0.0.1       localhost
127.0.1.1       Peridot
iluzio@Peridot:~$ sudo vi /etc/hosts
iluzio@Peridot:~$ cat /etc/hosts
127.0.0.1       localhost
127.0.1.1       Peridot
10.X.XXX.XX     iaas-dsi
```

### Generación de Claves Pública-Privada

Ahora toca configurar, en la máquina local, la infraestructura de clave pública-privada:
```
iluzio@Peridot:~$ ssh-keygen
```
Y ejecutamos el siguiente comando para copiar las claves públicas desde la máquina local a la máquina virtual:
```
iluzio@Peridot:~$ ssh-copy-id usuario@iaas-dsi
The authenticity of host 'iaas-dsi (10.X.XXX.XXX)' can't be established.
ED25519 key fingerprint is SHA256:CU+KbWWGTNmCxHhCeCYkmplWZSdz1bEdcNSd12eWEV8.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 2 key(s) remain to be installed -- if you are prompted now it is to install the new keys
usuario@iaas-dsi's password: 

Number of key(s) added: 2

Now try logging into the machine, with:   "ssh 'usuario@iaas-dsi'"
and check to make sure that only the key(s) you wanted were added.
```
Con esto podemos inciar sesión directamente, sin requerir contraseña, en la máquina virtual con el siguiente comando:
```
ssh usuario@iaas-dsi
```
Para no tener que escribir ```usuario``` en el comando, creamos y modificamos el siguiente fichero:
```
iluzio@Peridot:~$ touch /home/iluzio/.ssh/config
iluzio@Peridot:~$ vi /home/iluzio/.ssh/config
iluzio@Peridot:~$ cat /home/iluzio/.ssh/config
Host iaas-dsi
  HostName iaas-dsi
  User usuario
iluzio@Peridot:~$ ssh iaas-dsi
Welcome to Ubuntu 20.04.5 LTS (GNU/Linux 5.4.0-137-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue Jan 31 18:05:17 UTC 2023

  System load:  0.0                Processes:             139
  Usage of /:   37.9% of 19.52GB   Users logged in:       0
  Memory usage: 6%                 IPv4 address for ens3: 10.6.130.123
  Swap usage:   0%


 * Introducing Expanded Security Maintenance for Applications.
   Receive updates to over 25,000 software packages with your
   Ubuntu Pro subscription. Free for personal use.

     https://ubuntu.com/pro

0 updates can be applied immediately.

New release '22.04.1 LTS' available.
Run 'do-release-upgrade' to upgrade to it.


Last login: Tue Jan 31 17:54:33 2023 from 10.20.51.154
```
Por último, generamos las claves pública-privada en la máquina virtual, siguiendo los mismos pasos seguidos con la local.
```
usuario@iaas-dsi:~$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/usuario/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/usuario/.ssh/id_rsa
Your public key has been saved in /home/usuario/.ssh/id_rsa.pub
The key fingerprint is:
usuario@iaas-dsi:~$ cat /home/usuario/.ssh/id_rsa.pub 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDzqvqv9LWWJmdsTs80xB1jos5tXY4QiH0HUWy8F7xzektM93FW/wH8OfBn6cHNjZZAemeXWqxpo7Pkz8dJU4l50BKFWX35TjXQsrna7NzX9WDnJSKNq+XajBBwSmI8v7tHRLb0sV5rdBXfHPa7fjzG5ye6Gzq6eXoNLLuF0O81YEOZrBYu5XRAunG52EiXD22oeyo0vp5fwTfB4dCTAl+jpFdujf4PZ+2TwhWuz+VY2XBg3ei1lNOjGT3/Ke23Psw2NMg+VQGlicc2PovwbSwopDgQunxjhaH4nwhhyr531gcGghEYe5ni80w5YveftrpsW8+DQ2k6mK/mBQvF7iKS/j3k2xHlTngQSOZSa/Ce9u7FVrofCltFwGiXE1I2ue6CBJWNYmbsDiOIiDKrrju2eXLWmtGqiTwOLKw73Yk4BM8C8WQRj0hHQ9vLaXcG+Mfccz3NajLQejzIPj405JWjAP0DTdKnLKxgReGLGxr8hosA4s9/HFPF3OElvOEcrG8= usuario@iaas-dsi
```

## Instalación de Git y Node.js en la Máquina Virtual del IaaS

Instalamos git en la máquina virtual:
```
usuario@iaas-dsi:~$ sudo apt install git
[sudo] password for usuario: 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
git is already the newest version (1:2.25.1-1ubuntu3.8).
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```
No nos podemos olvidar de realizar la configuración inicial de Git:
```
usuario@iaas-dsi:~$ git config --global user.name "Luis Chinea Rangel"
usuario@iaas-dsi:~$ git config --global user.email "alu0101118116@ull.edu.es"
usuario@iaas-dsi:~$ git config --list
user.name=Luis Chinea Rangel
user.email=alu0101118116@ull.edu.es
```
Configuramos el prompt de la terminal para que en todo momento nos muestre la rama actual en la que nos encontramos cuando accedamos a algún directorio que resulta estar asociado a algún repositorio git. Para ello se hace uso del script [git prompt.](https://github.com/git/git/blob/master/contrib/completion/git-prompt.sh)
```
usuario@iaas-dsi:~$ wget https://raw.githubusercontent.com/git/git/master/contrib/completion/git-prompt.sh
--2023-01-31 18:13:17--  https://raw.githubusercontent.com/git/git/master/contrib/completion/git-prompt.sh
usuario@iaas-dsi:~$ mv git-prompt.sh .git-prompt.sh
usuario@iaas-dsi:~$ vi .bashrc
usuario@iaas-dsi:~$ tail .bashrc
...
source ~/.git-prompt.sh
PS1='\[\033]0;\u@\h:\w\007\]\[\033[0;34m\][\[\033[0;31m\]\w\[\033[0;32m\]($(git branch 2>/dev/null | sed -n "s/\* \(.*\)/\1/p"))\[\033[0;34m\]]$'
usuario@iaas-dsi:~$ exec bash -l
[~()]$
```
Para poder comprobar el funcionamiento del script, tenemos que añadir la clave pública de la mv a nuestra configuración de claves SSH y GPG de nuestra respectiva cuenta de Github. Para ello, copiamos primero la clave:
```
[~()]$cat ~/.ssh/id_rsa.pub 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDzqvqv9LWWJmdsTs80xB1jos5tXY4QiH0HUWy8F7xzektM93FW/wH8OfBn6cHNjZZAemeXWqxpo7Pkz8dJU4l50BKFWX35TjXQsrna7NzX9WDnJSKNq+XajBBwSmI8v7tHRLb0sV5rdBXfHPa7fjzG5ye6Gzq6eXoNLLuF0O81YEOZrBYu5XRAunG52EiXD22oeyo0vp5fwTfB4dCTAl+jpFdujf4PZ+2TwhWuz+VY2XBg3ei1lNOjGT3/Ke23Psw2NMg+VQGlicc2PovwbSwopDgQunxjhaH4nwhhyr531gcGghEYe5ni80w5YveftrpsW8+DQ2k6mK/mBQvF7iKS/j3k2xHlTngQSOZSa/Ce9u7FVrofCltFwGiXE1I2ue6CBJWNYmbsDiOIiDKrrju2eXLWmtGqiTwOLKw73Yk4BM8C8WQRj0hHQ9vLaXcG+Mfccz3NajLQejzIPj405JWjAP0DTdKnLKxgReGLGxr8hosA4s9/HFPF3OElvOEcrG8= usuario@iaas-dsi
```
Y la configuramos en nuestra cuenta:
![Configuración de Claves SSH y GPG en Github](/docs/assets/img02.png)
Con esto ya podemos clonar repositorios a nuestra mv:
```
[~()]$git clone git@github.com:ULL-ESIT-INF-DSI-2223/ull-esit-inf-dsi-22-23-prct01-iaas-iluzioDev.git
Cloning into 'ull-esit-inf-dsi-22-23-prct01-iaas-iluzioDev'...
warning: You appear to have cloned an empty repository.
```