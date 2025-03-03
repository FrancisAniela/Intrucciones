# ServerParkingInstaller 🚗

## Tabla de Contenidos 📑

- [Instrucciones de Instalación](#instrucciones-de-instalación-)
    - [Requisitos Previos](#requisitos-previos-)
    - [Scripts de Instalación](#scripts-de-instalación-)
- [Descripción de los Scripts](#descripción-de-los-scripts-)
    - [Clonar Repositorio](#clonar-repositorio-)
- [Finalización](#finalización-)
- [Uso de Docker para Ambientes de Pruebas](#uso-de-docker-para-ambientes-de-pruebas-)
    - [Ambiente de pruebas](#ambiente-de-pruebas)

Este repositorio contiene scripts para la instalación de servidores de estacionamientos. 

## Instrucciones de Instalación 🛠️

### Requisitos Previos ✅

- Sistema operativo Debian 11.
- Asegúrate de tener `bash` y `sed` instalados en tu sistema.
- Descarga el repositorio en tu servidor, desde el archivo .zip.
- Descomprime el archivo.

### Scripts de Instalación 📜

1. **`server.sh`**: Este es el script principal que orquesta la ejecución de los demás scripts. Asegúrate de dar permisos de ejecución a todos los archivos de la carpeta ServerParkingInstaller.

```sh
sudo chmod +x ServerParkingInstaller/*
```

2. Navega al directorio 

```sh
cd ServerParkingInstaller
```

3. **Ejecuta el script principal**:

```sh
sudo ./server.sh
```

El script `server.sh` realiza las siguientes acciones:

- Elimina caracteres de retorno de carro de los scripts `config_front.sh`, `config_backend.sh`, y `config_systemctl.sh`.
- Fuente los siguientes scripts en orden:
    - `install_basic.sh`
    - `config_db.sh`
    - `active_ssh.sh`
    - `clone_repo.sh`
    - `func_basic.sh`
    - `config_systemctl.sh`
    - `config_backend.sh`
    - `config_front.sh`
- Ejecuta las siguientes funciones:
    - `install_basic`
    - `add_port_apache` para varios puertos.
    - `config_db`
    - `active_ssh`
    - `clone_repo`
    - `config_service`
    - `config_lite`
    - `config_cc`
    - `config_replication`
    - `config_ws`
- Reinicia el servicio `apache2`.

## Descripción de los Scripts 📂

- **`install_basic.sh`**: Contiene la función `install_basic` que instala las dependencias básicas.
- **`config_db.sh`**: Contiene la configuración de la base de datos. La información cargada en la base de datos es suficiente para un funcionamiento básico.
- **`active_ssh.sh`**: Activa las configuraciones relacionadas con SSH (Para accesos externos).
- **`clone_repo.sh`**: Clona los repositorios necesarios. 
- **`func_basic.sh`**: Contiene funciones básicas utilizadas por otros scripts.
- **`config_systemctl.sh`**: Configura los servicios de systemctl.
- **`config_backend.sh`**: Configura el backend del sistema.
- **`config_front.sh`**: Configura el frontend del sistema (Control center y Lite).

### Clonar Repositorio 🔑

Durante el proceso de instalación se requiere permiso de acceso al repositorio `CDS-SERVIDOR-SW-COMPILED`, asegúrate de tener este acceso y agregar la clave SSH, generada durante la instalación a tu cuenta de GitHub. Para ello sigue estos pasos:

1. La clave ssh se genera de forma automática al ejecutar el script server.sh, solo debes copiarla directamente de la terminal. (Al crearse la clave ssh preferiblemente mantén la ubicación por defecto y agrega una frase de seguridad).

2. Agrega la clave SSH generada a tu cuenta de GitHub. Puedes encontrar instrucciones detalladas en [GitHub Docs](https://docs.github.com/es/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account).

Esto es necesario para que el script `clone_repo.sh` funcione correctamente y pueda clonar los repositorios necesarios para la instalación.

## Finalización 🎉

Una vez que el script principal `server.sh` haya terminado de ejecutarse, tu entorno de servidor de estacionamientos debería estar completamente configurado y listo para usarse.

Asegúrate de verificar y ajustar cualquier configuración específica según tus necesidades antes de poner el sistema en producción.

## Uso de Docker para Ambientes de Pruebas 🐳

Para ejecutar el contenedor a partir de la imagen del Dockerfile y configurar el ambiente de pruebas, sigue estos pasos (Recuerda instalar docker en tu ambiente de pruebas):

Para más información sobre cómo instalar Docker en Windows o Linux, consulta la [documentación oficial de Docker](https://docs.docker.com/get-docker/).

1. Construye la imagen de Docker a partir del Dockerfile, para navega hasta el directorio donde está el archivo **DockerFile** y ejecuta en la terminal:

```sh
docker build -t debian-systemd .
```

2. Ejecuta el contenedor con la siguiente instrucción:

```sh
docker run -it --privileged --name debian-systemd-container --mount type=bind,source=/sys/fs/cgroup,target=/sys/fs/cgroup -p 8080:8080 -p 3306:3306 -p 8078:8078 -p 8081:8081 -p 8083:8083 -p 8090:8090 -p 83:83 -p 81:81 -p 8085:8085 debian-systemd
```

3. Descarga el comprimido .zip, descomprime.

4. Obtén el ID del contenedor
```sh
docker ps -a
```

**Ejemplo de salida:** 

```sh
CONTAINER ID   IMAGE            COMMAND                  CREATED          STATUS          PORTS                                                                 NAMES
10b766347814   debian-systemd   "/bin/bash"              10 minutes ago   Up 10 minutes   0.0.0.0:81->81/tcp, 0.0.0.0:83->83/tcp, 0.0.0.0:3306->3306/tcp, ...   debian-systemd-container
```

5. Copia el contenido del repositorio en el contenedor:

```sh
docker cp  ServerParkingInstaller CONTAINER_ID:/var/
```

**Ejemplo:**
```sh
docker cp  ServerParkingInstaller 10b766347814:/var/
```

6. Accede al contenedor. (Las credenciales de acceso al contenedor puedes ubicarlas en el archivo dockerfile)
```sh
docker exec -it debian-systemd-container /bin/bash
```

7. Una vez dentro del contenedor navega hasta la carpeta */var*:

```sh
cd /var
```

8. Asegúrate de dar permisos de ejecución a los scripts, para ello:

```sh
sudo chmod +x  ServerParkingInstaller/*
```

9. Ejecuta el script principal `server.sh`:

```sh
cd ServerParkingInstaller
sudo ./server.sh
```

Esto configurará tu entorno de servidor de estacionamientos dentro del contenedor Docker para pruebas.

### Acceso a sitios en el ambiente pruebas

1. **Control Center**: Abre un navegador y coloca la ruta http://localhost:81/home

- Credenciales:
-**user**:admin
-**password**:admin

2. **SoftCloudLite**: Abre un navegador y coloca la ruta http://localhost:8085/home
- Credenciales:
-**user**:admin
-**password**:admin
