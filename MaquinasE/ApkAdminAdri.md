### ApkAdmin-DockerLabs🇪🇸

#### 1. En primer lugar debemos de encender el laboratorio.
```bash
 sudo bash ./auto_deploy.sh apkadmin.tar 
```

<img width="911" height="666" alt="image" src="https://github.com/user-attachments/assets/34647090-13c1-4c8b-8636-bf1e81a1d487" />


#### 1.2 COmprobaremos mediante un ping que está la máquina en funcionamiento.
```bash
ping -c3 172.17.0.2
```

<img width="911" height="186" alt="image" src="https://github.com/user-attachments/assets/c4ba14d5-4090-4211-8716-1dbe93e4d6c2" />



#### 2. AHora realizaremos un escaneo de puertos para ver sus protocolos en uso.
```bash
nmap -p- -sCV 172.17.0.2
```

<img width="906" height="383" alt="image" src="https://github.com/user-attachments/assets/b33200b9-3b30-4db5-8fab-adb48ceefcee" />


#### 3. Sabiendo que los únicos puertos abiertos son el SSH en el 22 y el http en el 80 iremos a investigar en la web.

#### 3.1 Nos encontramos con un minijuego el cual vamos a seguirlo.

<img width="1008" height="956" alt="image" src="https://github.com/user-attachments/assets/0da9c1fd-2fec-4ed8-9c89-04f474a8de83" />


#### 3.2 Debeos en primer lugar que instalar las dependencias para poder intalar el archivo del reto.
```bash
sudo apt install adb                                    
sudo apt install google-android-platform-tools-installer
```

<img width="908" height="366" alt="image" src="https://github.com/user-attachments/assets/8f39fbeb-c524-487d-8d99-f0fb090ff548" />


#### 3.3 Debemos de configurar nuestro android para que se pueda utilizar la `depuración por USB`. Con el comando `adb devices` podremos corrobar que funciona.

<img width="926" height="166" alt="image" src="https://github.com/user-attachments/assets/6a7c45f0-1a85-4351-930f-49d36df8762e" />

#### 3.4 Procedemos a instalar la apk en el android.
```bash
adb install -r AdminBypassCTF.apk
```

<img width="918" height="2048" alt="WhatsApp Image 2026-09-03 at 12 49 31" src="https://github.com/user-attachments/assets/dce11b85-96ff-407a-b8b3-ff2c06f63b18" />


#### 4. Procedemos a instalar jadx y apktool para inspeccionar el archivo.
```bash
sudo apt update && sudo apt install apktool jadx -y
```

<img width="922" height="368" alt="image" src="https://github.com/user-attachments/assets/b92ae217-9277-4dad-8008-a7c939b25400" />


#### 4.2 Abriremos el archivo con jadx y buscaremos en la carpeta de AndroidManifest.xml.


<img width="1261" height="778" alt="image" src="https://github.com/user-attachments/assets/d7b7ef01-55d8-4494-b843-ab4c396c3d13" />


#### 4.3 Observando el código en la línea 55 podemos observar que el nombre tiene que ver con AdminActivity.

<img width="758" height="232" alt="image" src="https://github.com/user-attachments/assets/c3ec12d5-9cf1-46e9-9aff-9cc606e3118e" />


#### 4.4 Iremos hacia AdminActivity y veremos el nombre de usuario y contraseña.
```bash
#user: pingu
# passwd: chocolate
```
<img width="771" height="449" alt="image" src="https://github.com/user-attachments/assets/01cff36f-2315-4dd5-afed-4e019d4a6187" />


#### 5. Entraremos mediante ssh a la máquina.
```bash
ssh pingu@172.17.0..2
# passwd: chocolate
```

<img width="907" height="359" alt="image" src="https://github.com/user-attachments/assets/9648a06a-cf09-43ee-be15-72c857bcfa1f" />


#### 6. 





