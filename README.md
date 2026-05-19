# ThustAdri86
Thust-DockerLabs🇪🇸 

**1.1El primer paso es descargar el archivo trust.zip y lo vamos a descomprimir en la terminal**
```bash
 unzip trust.zip 
```
**1.2 Vamos a crear un script de bash el cual nos abra desde docker el autodeploy.sh para poder asi iniciar esta máquina**
```bash
nano auto_deploy.sh
```
**Dentro añadiremos este contenido**

```bash
#!/bin/bash

# Colores
greenColour="\e[0;32m\033[1m"
endColour="\033[0m\e[0m"
redColour="\e[0;31m\033[1m"
blueColour="\e[0;34m\033[1m"
yellowColour="\e[0;33m\033[1m"
purpleColour="\e[0;35m\033[1m"
turquoiseColour="\e[0;36m\033[1m"
grayColour="\e[0;37m\033[1m"

function ctrl_c(){
    echo -e "\n\n${redColour}[!] Saliendo...${endColour}\n"
    proc_red=$(docker network ls | grep dockerlabs_net | awk '{print $1}')
    if [ ! -z "$proc_red" ]; then
        docker network rm dockerlabs_net > /dev/null 2>&1
    fi
    proc_container=$(docker ps -a | grep trust | awk '{print $1}')
    if [ ! -z "$proc_container" ]; then
        docker rm -f $proc_container > /dev/null 2>&1
    fi
    exit 1
}

# Ctrl+C
trap ctrl_c INT

if [ "$(id -u)" -ne 0 ]; then
    echo -e "\n${redColour}[!] Este script debe ser ejecutado como root (sudo).${endColour}\n"
    exit 1
fi

if [ -z "$1" ]; then
    echo -e "\n${redColour}[!] Uso: $0 <archivo.tar>${endColour}\n"
    exit 1
fi

clear
echo -e "${blueColour}"
echo "    _               _               _          _     "
echo "   | |             | |             | |        | |    "
echo " _ | | ___   _ ____| |  _ ____ ____| |  _ ____| |__  "
echo "/ || |/ _ \ / / ___) | / _  _  _  _) | / _  _  _)  _ \ "
echo "| (_| | |_| | |    | |/ / | | | |  | |/ / | | | | |_) )"
echo " \____|\___/|_|_|    |___/|_|_|_|  |___/|_|_|_|_|____/ "
echo -e "${endColour}"

echo -e "${yellowColour}[+] Comprobando si existen contenedores previos...${endColour}"
docker rm -f $(docker ps -aq --filter name=trust) > /dev/null 2>&1
docker network rm dockerlabs_net > /dev/null 2>&1

echo -e "${yellowColour}[+] Creando red interna dockerlabs_net...${endColour}"
docker network create --subnet=172.18.0.0/16 dockerlabs_net > /dev/null 2>&1

echo -e "${yellowColour}[+] Cargando la imagen de la máquina...${endColour}"
docker load -i $1

echo -e "${yellowColour}[+] Desplegando el contenedor vulnerable...${endColour}"
docker run -d --name trust --net dockerlabs_net --ip 172.18.0.2 trust > /dev/null 2>&1

echo -e "\n${greenColour}Máquina desplegada, su dirección IP es --> 172.18.0.2${endColour}"
echo -e "${redColour}Presiona Ctrl+C cuando termines con la máquina para eliminarla${endColour}\n"

while true; do
    sleep 1
done
```
**1.3.Por último para preparar el docker lo iniciaremos**

```bash
chmod +x auto_deploy.sh
sudo ./auto_deploy.sh trust.tar
```
*Deberá de quedar algo asi*
<img width="975" height="506" alt="image" src="https://github.com/user-attachments/assets/537075fe-4f38-4011-addb-270c8658f3d4" />

**2. Para poder comprobar la comunicación haremos un ping desde otra terminal**
```bash
ping -c3 172.18.0.2
```
<img width="960" height="437" alt="image" src="https://github.com/user-attachments/assets/7172ba55-381c-41e2-a614-b1998909dc25" />

**3.Ahora en este punto realizaremos un reconocimiento con la herramienta nmap, la información relevante que nos aporta en nuestra circunstancia son los puertos 22 y 80.**

*Este es el comando principal de reconocimiento. Es como enviar un dron a escanear un edificio para ver qué puertas y ventanas tiene abiertas. Cada "pieza" (parámetro) del comando hace algo específico:
    sudo nmap: Lanza la herramienta Nmap con privilegios de administrador (necesario para gestionar los paquetes de red a bajo nivel).
    -sS (SYN Scan): Hace un escaneo sigiloso y rápido. Envía un saludo inicial a la máquina pero no llega a completar la conexión total, lo que permite descubrir puertos abiertos muy rápido.
    -p-: Le dice a Nmap que escanee todos los puertos posibles (desde el puerto 1 hasta el 65535). Si no pones el guion final, solo escanea los 1000 puertos más comunes.
    -sC: Ejecuta una serie de scripts automáticos por defecto que tiene Nmap para intentar encontrar datos básicos de seguridad o fallos comunes en esos puertos.
    -sV: Intenta averiguar la versión exacta del software que se está ejecutando en cada puerto abierto (por ejemplo, si usa una versión vieja de Apache o SSH que tenga fallos conocidos).
    -Pn: Le dice a Nmap: "No verifiques si la máquina está encendida antes de escanear, asume que está viva". Esto evita que las protecciones del sistema bloqueen el escaneo.
    -172.18.0.2: Es la dirección IP de la máquina víctima (el contenedor de DockerLabs) a la que estás apuntando el ataque.
    -oN scan_nmap.txt: Guarda todo el resultado del escaneo en un archivo de texto normal llamado scan_nmap.txt dentro de tu carpeta, para que no pierdas la información si se cierra la terminal.*

```bash
sudo nmap -sS -p- -sC -sV -Pn 172.18.0.2 -oN scan_nmap.txt
```

<img width="966" height="442" alt="image" src="https://github.com/user-attachments/assets/e9dfd62e-9f5e-4cbe-a335-f07c50a127bf" />


**4. Ahora pasaremos a la fase de enumeración web, en esta parte haremos fuzzing/rastreo de directorios para poder ver si el diccionario encuentra el archivo /secret.php.**
```bash
gobuster dir -u http://172.18.0.2 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php -r
```
*1. gobuster dir
    Para qué sirve: Lanza la herramienta Gobuster en su modo dir (Directory/File enumeration). Esto le indica al programa que su objetivo es buscar carpetas o archivos ocultos dentro de un servidor web.

2. -u http://172.18.0.2

    Para qué sirve: El parámetro -u significa URL (la dirección de la víctima). Aquí le estás diciendo a Gobuster exactamente contra qué página web tiene que lanzar el ataque de búsqueda. En este caso, el servidor web alojado en la IP de tu contenedor Docker.

3. -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

    Para qué sirve: El parámetro -w significa Wordlist (Diccionario). Como Gobuster no es adivino, necesita un archivo de texto con miles de palabras comunes para ir probándolas. Le estamos pasando la ruta de uno de los mejores diccionarios por defecto de Kali Linux. Contiene más de 200.000 palabras organizadas de las más comunes a las menos comunes en la historia de internet.

4. -x php

    Para qué sirve: El parámetro -x significa Extensions (Extensiones de archivo). Le estás diciendo a Gobuster que no solo busque carpetas llamadas "secreto", sino que a cada palabra del diccionario le añada la terminación .php (por ejemplo: secreto.php). Esto es crucial porque la máquina Trust está hecha en el lenguaje de programación web PHP.

5. -r

    Para qué sirve: El parámetro -r significa Follow Redirects (Seguir redirecciones). Si el servidor web intenta redirigirte automáticamente de una página a otra (por ejemplo, si vas a http://172.18.0.2/secreto y la web te manda a http://172.18.0.2/secreto/index.php), este parámetro le dice a Gobuster que siga el camino automáticamente y te muestre el resultado real de la página final.*

<img width="962" height="500" alt="image" src="https://github.com/user-attachments/assets/b79e14d3-c5a3-4c0e-974a-732e257c71fa" />

**Ahora entramos al enlace que hemos descubierto trás el fuzzing**

<img width="961" height="542" alt="image" src="https://github.com/user-attachments/assets/adbccb74-95c8-4e6b-a80c-a5f178cb5a27" />


**5. Ahora vamos a explorar mediante la herramienta de hydra en la web que nos proporciona la entrada por fuerza bruta y ver la contraseña**
```bash
sudo gunzip /usr/share/wordlists/rockyou.txt.gz
hydra -l mario -P /usr/share/wordlists/rockyou.txt 172.18.0.2 -t 64 ssh
```

*Como sabemos que el puerto 22 (SSH) está abierto (es el que sirve para iniciar sesión en la terminal del servidor) y sabemos que existe el usuario mario, vamos a hacer un ataque de fuerza bruta por diccionario. Hydra va a probar miles de contraseñas por segundo para mario hasta encontrar la correcta.

1. hydra
Lanza el programa Hydra, que es una de las herramientas de fuerza bruta para contraseñas de red más rápidas y famosas que existen en el hacking ético.

2. -l mario
La -l minúscula significa Login (nombre de usuario único). Aquí le dejas claro a Hydra que solo nos interesa probar contraseñas para el usuario específico que descubrimos en la web: mario.

3. -P /usr/share/wordlists/rockyou.txt
La -P mayúscula significa Password list (Diccionario de contraseñas). Le indicamos la ruta del archivo rockyou.txt. Este es el diccionario más famoso del mundo del hacking; contiene más de 14 millones de contraseñas reales que se filtraron en un hackeo masivo de una web en 2009.

4. 172.18.0.2
Es la dirección IP del objetivo. Le dice a Hydra a qué máquina de tu red interna tiene que dirigir los intentos de inicio de sesión.

5. -t 64

El parámetro -t significa Tasks (Hilos o tareas en paralelo). Por defecto, Hydra va probando contraseñas de una en una de forma más lenta. Al ponerle 64, le estás ordenando que realice 64 intentos de conexión de forma simultánea (a la vez). Esto hace que el ataque vaya a una velocidad brutal y encuentre la contraseña en pocos segundos.

6. ssh
Especifica el protocolo o servicio que queremos atacar. Como vimos en el escaneo de Nmap que el puerto de inicio de sesión remoto SSH estaba abierto, le indicamos a Hydra que use las reglas del protocolo SSH para intentar colarse.*

<img width="963" height="371" alt="image" src="https://github.com/user-attachments/assets/97da8583-1ce6-4427-ac4b-326a94f42d66" />

**6. Entraremos mediante el protocolo ssh a la sesión a través de remoto.**
```bash
ssh mario@172.18.0.2
```
*Password: chocolate*
<img width="964" height="513" alt="image" src="https://github.com/user-attachments/assets/0d6520e8-7179-4092-99d8-e97555034f05" />

**7. Procederemos a escalar privilegios ahora**
*En primer lugar haremos un sudo -l para poder ver todos los binarios que tienen permisos.*
<img width="895" height="163" alt="image" src="https://github.com/user-attachments/assets/9fbf89c6-403b-4ac7-9bd5-448bb4c2e8f5" />

**Por ultimo introduciremos el comando de vim para poder llegar a escalar al usuario de root**

*
1. sudo vim
Abre el editor de texto clásico de Linux llamado Vim, pero al llevar sudo por delante, lo estás abriendo con los superpoderes del administrador (root). Todo lo que pase dentro de ese editor se ejecutará con el máximo privilegio del sistema.

2. -c
Este parámetro en Vim significa "Command" (Comando). Le dice al editor: "En cuanto te abras, antes de que el usuario pueda escribir nada, ejecuta automáticamente la instrucción que te voy a dar entre comillas".

3. ':!/bin/bash'
Esta es la instrucción interna que se ejecuta dentro de Vim, y se divide en tres símbolos clave:
    : Abre la línea de comandos interna de Vim (abajo a la izquierda del editor).
    ! En Vim, el signo de exclamación significa "sal momentáneamente de este editor de texto y ejecuta un comando directamente en el sistema operativo subyacente".
    /bin/bash Es la ruta de la terminal nativa de Linux (la Shell). Le estás pidiendo que te abra una nueva línea de comandos.*
```bash
vim -c ':!/bin/bash'
```
<img width="989" height="1045" alt="image" src="https://github.com/user-attachments/assets/30715684-0d0e-497e-a326-ccdae5f08457" />





