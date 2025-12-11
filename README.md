# Guía de Instalación de Portainer y Creacion de Stacks

Esta guía explica paso a paso cómo instalar y configurar **Portainer CE** mediante Docker y cómo crear Stacks.

## Requisitos previos

* Sistema operativo: Windows / Linux / macOS
* Docker y Docker Compose instalados
* Acceso a terminal

## 1. Instalar Docker

Asegúrate de tener Docker instalado.

* **Windows 10/11:** Docker Desktop
* **Linux:**

```bash
sudo apt update
sudo apt install docker.io -y
```

## 2. Crear volumen para Portainer

```bash
docker volume create portainer_data
```

## 3. Desplegar Portainer

Lanza el contenedor de Portainer CE:

```bash
docker run -d \
  -p 9443:9443 \
  -p 8000:8000 \
  --name portainer \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer-ce:latest
```
<img width="1067" height="383" alt="portainer" src="https://github.com/user-attachments/assets/9dbbf441-5551-4483-bb96-8ce303cf862d" />

## 4. Acceder al panel web

* Abre en tu navegador:

```
https://localhost:9443
```

* Crea el usuario administrador la primera vez.

## 5. Crear un Stack en Portainer

<img width="1858" height="522" alt="portainer1" src="https://github.com/user-attachments/assets/400741b8-7981-46c8-9825-4d49708dcbe7" />

<img width="1863" height="782" alt="portainer2" src="https://github.com/user-attachments/assets/cf66c29d-9f2e-47bb-bb48-2355c5ec0afd" />

<img width="1561" height="399" alt="portainer3" src="https://github.com/user-attachments/assets/27d67d2f-db38-40ba-ae83-1c62f2167a6a" />


1. Accede a **Stacks** > **Add stack**
2. Introduce un nombre para tu Stack.
3. Pega tu archivo `docker-compose.yml` o selecciónalo desde tu repositorio.
4. Configura variables de entorno si es necesario.
5. Haz clic en **Deploy the stack**.
   
<img width="1555" height="737" alt="portainer4" src="https://github.com/user-attachments/assets/8f89524c-6629-4afa-b1fc-d4fdb0da2e58" />

Ejemplo `docker-compose.yml` para desplegar 2 servicios web en los puertos 8080 y 8081:

```yaml
version: "3.8"

services:
  #########################
  #     HOMER
  #########################
  homer:
    image: b4bz/homer
    container_name: homer
    ports:
      - 8081:8080
    volumes:
      - homer_data:/www/assets
    restart: unless-stopped

  #########################
  #     NEXTCLOUD + DB
  #########################
  nextcloud_db:
    image: mariadb:10.6
    container_name: nextcloud_db
    command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW
    environment:
      MYSQL_ROOT_PASSWORD: nextcloudroot
      MYSQL_DATABASE: nextcloud
      MYSQL_USER: nextcloud
      MYSQL_PASSWORD: nextcloud
    volumes:
      - nextcloud_db_data:/var/lib/mysql
    restart: unless-stopped

  nextcloud:
    image: lscr.io/linuxserver/nextcloud:latest
    container_name: nextcloud_app
    depends_on:
      - nextcloud_db
    environment:
      PUID: 1000
      PGID: 1000
      TZ: Europe/Madrid
    ports:
      - 8080:80
    volumes:
      - nextcloud_data:/config
      - nextcloud_files:/data
    restart: unless-stopped

#########################
#       VOLUMES
#########################
volumes:
  homer_data:
  nextcloud_db_data:
  nextcloud_data:
  nextcloud_files:

```
Una vez activos los contenedores podemos ver su informacion y estado:

<img width="1554" height="908" alt="portainer5" src="https://github.com/user-attachments/assets/8dceed7f-520d-4b36-8304-1b66c14c8f6d" />

## 6. Acceso a las aplicaciones webs.

Tan solo nos queda acceder a los respectivos puertos para utilizar nuestras aplicaciones:

```bash
http://localhost:8080/
```
<img width="1326" height="977" alt="portainer6" src="https://github.com/user-attachments/assets/4be03e91-554d-44c1-90ec-a61b6b1d2b09" />

```bash
http://localhost:8081/
```
<img width="1285" height="935" alt="portainer7" src="https://github.com/user-attachments/assets/eff93924-04d3-45b4-aa57-ac251f0b1338" />



