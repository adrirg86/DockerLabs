<img width="983" height="1041" alt="image" src="https://github.com/user-attachments/assets/0320d8a3-a2c3-4929-8ff3-d3b4099db755" /># Adopting-DockerLabs🇪🇸


## 1 Encender laboratorio.

#### 1.0 Primero vamos a desempaquetar el archivo comprimido y lanzar el laboratorio.
```bash
 unzip adopting.zip 
bash ./auto_deploy.sh adopting.tar 
```

<img width="914" height="536" alt="image" src="https://github.com/user-attachments/assets/70c27a2c-9fcf-4f97-8ac5-442d6d1caae7" />


#### 1.2 Realizamos un ping para comprobar el estado de la máquina.
```bash
ping -c3 172.17.0.2
```

<img width="910" height="236" alt="image" src="https://github.com/user-attachments/assets/b9981c21-3eca-48ee-bfe2-1b2c6d4d65cd" />


## 2. Reconocimiento.

#### 2.0 Realizamos un escaneo de puertos mediante nmap.
```bash
nmap -p- -sCV 172.17.0.2
```

<img width="908" height="979" alt="image" src="https://github.com/user-attachments/assets/7dc92d37-762c-46e9-b8d5-76d608698737" />

`vemos los puertos 22(ssh) y 2300(GET) abiertos.`


#### 2.1 Vamos a ver que recibimos con el método GET y POST.
```bash
curl -v GET http://172.17.0.2:2300
 curl -v POST http://172.17.0.2:2300
```
`El parámetro -v (o --verbose) en comandos de consola como curl o git activa el modo detallado (verbose).`

<img width="911" height="810" alt="image" src="https://github.com/user-attachments/assets/0b11e018-5f18-4b65-8725-5c0c3334a003" />


<img width="905" height="838" alt="image" src="https://github.com/user-attachments/assets/af8973b3-961d-4a6f-a3d6-811d6385b95d" />


#### 2.2 Realizamos un ffuf para ver los directorios ocultos.
```bash
ffuf -u http://172.17.0.2:2300/FUZZ/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -e .php -t 50
```

<img width="911" height="779" alt="image" src="https://github.com/user-attachments/assets/5b87b8ad-d6fd-4ee3-823a-78be9fb62818" />


#### 2.3 Veremos un formulario de crear cuenta y crearemos una.

<img width="1013" height="973" alt="image" src="https://github.com/user-attachments/assets/ea4f69bc-3b7a-4dcb-962a-30bb7fe9ecb2" />


#### 2.4 Vamos a realizar otro gobuster pero ahora con el directorio medio de dirbuster, filtrando por algunas extensiones y que excluya el length 806 porque sino daria todas el `ok 200` y no nos interesa.
```bash
gobuster dir -u http://172.17.0.2:2300/ -w /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-lowercase-2.3-medium.txt -x php,txt,py,bak,sql --exclude-length=806
```

<img width="996" height="418" alt="image" src="https://github.com/user-attachments/assets/b5eb4bc3-d338-4e27-8217-051a8adc03f3" />


### Explotación

#### 3.0 Mediante BurpSuite vamos a ver la paqueteria POST y GET, mandaremos al repetear el paquete POST que devuelve tus creedenciales.

<img width="983" height="1041" alt="image" src="https://github.com/user-attachments/assets/974192fa-da19-47df-99a3-805e306c2b82" />


#### 3.1 Ahora manipularemos al servidor con el rol para poder escalar a administrador, iremos a configuración y modificamos el math del proxy.

<img width="986" height="1061" alt="image" src="https://github.com/user-attachments/assets/e08ab4d7-b6a5-4e32-814b-6df96fae820e" />

#### 3.2 Veremos que al escalar a administrador nos ha aparecido una pestaña nueva.

<img width="931" height="736" alt="image" src="https://github.com/user-attachments/assets/d6f90325-7fb0-42ee-a2e4-3763b48a7786" />


#### 3.3 El backend rechaza nuestra consulta porque al estar manuipulando las respuestas existen incoherencias.

<img width="937" height="734" alt="image" src="https://github.com/user-attachments/assets/ddaa36a0-28c4-4f2c-a079-463843f07629" />


#### 3.4 
