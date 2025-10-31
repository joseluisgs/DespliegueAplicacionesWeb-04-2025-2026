# Proxy Inverso con Nginx, Balanceo de Carga y SSL con Docker Compose
- [Proxy Inverso con Nginx, Balanceo de Carga y SSL con Docker Compose](#proxy-inverso-con-nginx-balanceo-de-carga-y-ssl-con-docker-compose)
  - [¿Qué es un proxy inverso?](#qué-es-un-proxy-inverso)
  - [Ventajas del uso de un proxy inverso](#ventajas-del-uso-de-un-proxy-inverso)
  - [Configuración de un proxy inverso con Nginx](#configuración-de-un-proxy-inverso-con-nginx)
  - [Ejemplo básico de configuración](#ejemplo-básico-de-configuración)
  - [Proxy Inverso como API Gateway](#proxy-inverso-como-api-gateway)
  - [Balanceo de carga con Nginx (`upstream`)](#balanceo-de-carga-con-nginx-upstream)
    - [Algoritmos de balanceo](#algoritmos-de-balanceo)
  - [Alta disponibilidad y escalabilidad con Docker Compose (`--scale`)](#alta-disponibilidad-y-escalabilidad-con-docker-compose---scale)
  - [Service Discovery en Docker Compose](#service-discovery-en-docker-compose)
  - [Redes internas en Docker Compose](#redes-internas-en-docker-compose)
  - [Arquitectura del sistema](#arquitectura-del-sistema)
  - [Estructura de archivos del proyecto](#estructura-de-archivos-del-proyecto)
  - [Ficheros completos del proyecto](#ficheros-completos-del-proyecto)
    - [`docker-compose.yml`](#docker-composeyml)
    - [`proxy/nginx.conf`](#proxynginxconf)
    - [`proxy/sites-enabled/default.conf`](#proxysites-enableddefaultconf)
    - [`web/index.html`](#webindexhtml)
    - [`apache/Dockerfile`](#apachedockerfile)
    - [`apache/sites-available/000-default.conf`](#apachesites-available000-defaultconf)
    - [`apache/website/index.html`](#apachewebsiteindexhtml)
    - [`make-certs.sh`](#make-certssh)
    - [`proxy/sites-enabled/ssl.conf`](#proxysites-enabledsslconf)
- [Laboratorio: Proxy Inverso con Nginx, Balanceo y SSL](#laboratorio-proxy-inverso-con-nginx-balanceo-y-ssl)
  - [Objetivos](#objetivos)
  - [Pasos](#pasos)
  - [Ejercicios](#ejercicios)
  - [Comprobaciones](#comprobaciones)
  - [Verificación y pruebas](#verificación-y-pruebas)
  - [Ejercicios propuestos](#ejercicios-propuestos)
  - [Conclusiones](#conclusiones)

---

## ¿Qué es un proxy inverso?

Un **proxy inverso** es un servidor que recibe solicitudes de clientes y las reenvía a uno o más servidores de origen o backend.  
El cliente nunca se comunica directamente con los servidores backend, sino que lo hace siempre a través del proxy.

Un proxy inverso se puede utilizar para:

- Balancear la carga entre varios servidores de origen.  
- Proporcionar alta disponibilidad y escalabilidad.  
- Proteger los servidores de origen de los ataques de denegación de servicio.  
- Cachear contenido estático para mejorar el rendimiento.  
- Enmascarar la dirección IP del servidor o del cliente.  
- Gestionar certificados SSL de forma centralizada.

---

## Ventajas del uso de un proxy inverso

- **Balanceo de carga:** distribuye las peticiones entre varios servidores backend.  
- **Alta disponibilidad:** si una instancia falla, otras continúan atendiendo.  
- **Seguridad:** oculta los servidores internos del acceso directo.  
- **Caché y compresión:** mejora el rendimiento y reduce la carga.  
- **Centralización de SSL:** el proxy gestiona los certificados.  
- **Escalabilidad:** permite añadir o quitar instancias sin afectar al cliente.  

---

## Configuración de un proxy inverso con Nginx

En un entorno Dockerizado, no necesitamos instalar Nginx manualmente, ya que se utiliza como contenedor.  
La idea es tener un contenedor **proxy** que reciba las peticiones externas y las distribuya a uno o varios contenedores backend.

---

## Ejemplo básico de configuración

```nginx
events {}

http {
    server {
        listen 80;
        server_name dominio.com;

        location / {
            proxy_pass http://nginx_server;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }

        location /one {
            proxy_pass http://nginx_server;
        }

        location /two {
            proxy_pass http://apache_server;
        }
    }
}
```

## Proxy Inverso como API Gateway
Un proxy inverso puede actuar como un **API Gateway**, dirigiendo las peticiones a diferentes servicios según la ruta solicitada. De esta manera , se centraliza el acceso a múltiples APIs siendo uno de los enfoques más comunes en arquitecturas de microservicios.

```nginx
http {
    server {
        listen 80;

        location /api {
            proxy_pass http://api_server;
        }

        location /auth {
            proxy_pass http://auth_server;
        }
    }
}
```

---

## Balanceo de carga con Nginx (`upstream`)

Nginx permite agrupar varios servidores backend mediante la directiva `upstream`.
Así, el proxy puede distribuir las peticiones de manera automática.

```nginx
http {
    upstream web_backend {
        server web_backend1:80;
        server web_backend2:80;
        server web_backend3:80;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://web_backend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }
    }
}
```

---

### Algoritmos de balanceo

Nginx permite distintos algoritmos de balanceo:

* **round-robin (por defecto):** envía las peticiones de manera alternada.
* **least_conn:** envía la petición al servidor con menos conexiones activas.
* **ip_hash:** siempre envía al mismo servidor en función de la IP del cliente.
* **hash $variable:** se puede usar cualquier variable para el reparto.

---

## Alta disponibilidad y escalabilidad con Docker Compose (`--scale`)

Docker Compose permite ejecutar múltiples instancias del mismo servicio de forma sencilla:

```bash
docker compose up --scale web_backend=3 -d
```

Esto lanzará tres contenedores (`web_backend_1`, `web_backend_2`, `web_backend_3`) que recibirán las peticiones balanceadas por el proxy.

---

## Service Discovery en Docker Compose

Docker Compose ofrece un sistema de **descubrimiento automático de servicios (Service Discovery)**.
Cada contenedor puede resolver el nombre de otro servicio dentro de la misma red usando su nombre como hostname.

Por ejemplo:

```nginx
proxy_pass http://web_backend:80;
```

Docker resolverá automáticamente ese nombre al conjunto de contenedores correspondientes.

---

## Redes internas en Docker Compose

Para garantizar la seguridad del entorno:

1. Solo el proxy debe exponer puertos al exterior.
2. Los backends deben comunicarse exclusivamente a través de la red interna.

```yaml
networks:
  internal_net:
    driver: bridge
```

De esta forma, las peticiones externas llegan solo al proxy, y los contenedores internos no son accesibles directamente.

---

## Arquitectura del sistema

1. El cliente se conecta al **proxy Nginx**.
2. Nginx reenvía la petición al backend correspondiente (`web_backend`, `apache_server`, etc.).
3. Los backends devuelven la respuesta al proxy.
4. El proxy entrega la respuesta al cliente.

---

## Estructura de archivos del proyecto

```
nginx-proxy-lb/
├── docker-compose.yml
├── proxy/
│   ├── nginx.conf
│   ├── sites-enabled/
│   │   ├── default.conf
│   │   └── ssl.conf
│   └── certs/
├── web/
│   └── index.html
├── apache/
│   ├── Dockerfile
│   ├── sites-available/
│   │   └── 000-default.conf
│   └── website/
│       └── index.html
├── make-certs.sh
└── README-LAB.md
```

---

## Ficheros completos del proyecto

### `docker-compose.yml`

```yaml
version: '3.8'

services:

  web_backend:
    image: nginx:stable-alpine
    volumes:
      - ./web:/usr/share/nginx/html:ro
    networks:
      - internal_net

  apache_server:
    build: ./apache
    networks:
      - internal_net

  proxy:
    image: nginx:stable-alpine
    container_name: proxy_server
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./proxy/nginx.conf:/etc/nginx/nginx.conf:ro
      - ./proxy/sites-enabled:/etc/nginx/conf.d:ro
      - ./proxy/certs:/etc/nginx/certs:ro
    depends_on:
      - web_backend
      - apache_server
    networks:
      - internal_net

networks:
  internal_net:
    driver: bridge
```

---

### `proxy/nginx.conf`

```nginx
user  nginx;
worker_processes  auto;
error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

events {
    worker_connections 1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;
    access_log  /var/log/nginx/access.log  main;
    resolver 127.0.0.11 valid=10s;

    server {
        listen 80;
        server_name _;

        set $backend_host "web_backend:80";

        location /apellidos {
            proxy_pass http://apache_server;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }

        location /nombre {
            proxy_pass http://web_backend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }

        location / {
            proxy_pass http://$backend_host;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }
    }
}
```

---

### `proxy/sites-enabled/default.conf`

```nginx
server {
    listen 80;
    server_name dominio.local;

    location = / {
        return 302 /welcome;
    }

    location /welcome {
        root /usr/share/nginx/html;
        index index.html;
    }

    location /one {
        proxy_pass http://nginx_server;
    }

    location /two {
        proxy_pass http://apache_server;
    }

    location /nombre {
        proxy_pass http://web_backend;
    }

    location /apellidos {
        auth_basic "Área privada";
        auth_basic_user_file /etc/nginx/.htpasswd;
        proxy_pass http://apache_server;
    }
}
```

---

### `web/index.html`

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Servidor Web Backend</title>
    <style>
        body { font-family: Arial, sans-serif; text-align: center; margin-top: 5em; }
        h1 { color: #3366cc; }
    </style>
</head>
<body>
    <h1>¡Hola desde el servidor backend!</h1>
    <p>Esta página se sirve desde una de las instancias balanceadas.</p>
</body>
</html>
```

---

### `apache/Dockerfile`

```dockerfile
FROM httpd:2.4-alpine
COPY ./website/ /usr/local/apache2/htdocs/
COPY ./sites-available/000-default.conf /usr/local/apache2/conf/extra/httpd-vhosts.conf
```

---

### `apache/sites-available/000-default.conf`

```apache
<VirtualHost *:80>
    DocumentRoot "/usr/local/apache2/htdocs"
    <Directory "/usr/local/apache2/htdocs">
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
    </Directory>
</VirtualHost>
```

---

### `apache/website/index.html`

```html
<!DOCTYPE html>
<html lang="es">
<head><meta charset="UTF-8"><title>Zona Privada Apache</title></head>
<body>
  <h1>Zona Privada - Apellidos</h1>
  <p>Esta es la zona privada del servidor Apache protegida por usuario y contraseña.</p>
</body>
</html>
```

---

### `make-certs.sh`

```bash
#!/usr/bin/env bash
set -e

mkdir -p proxy/certs
cd proxy/certs

DOMAIN="dominio.local"
DAYS=365

openssl genrsa -out ${DOMAIN}.key 2048
openssl req -new -key ${DOMAIN}.key -out ${DOMAIN}.csr -subj "/C=ES/ST=Madrid/CN=${DOMAIN}/O=DAW"
openssl x509 -req -in ${DOMAIN}.csr -signkey ${DOMAIN}.key -out ${DOMAIN}.crt -days ${DAYS}
cat ${DOMAIN}.key ${DOMAIN}.crt > ${DOMAIN}.pem

echo "Certificados generados en proxy/certs: ${DOMAIN}.key ${DOMAIN}.crt ${DOMAIN}.pem"
```

---

### `proxy/sites-enabled/ssl.conf`

```nginx
server {
    listen 443 ssl;
    server_name dominio.local;

    ssl_certificate /etc/nginx/certs/dominio.local.crt;
    ssl_certificate_key /etc/nginx/certs/dominio.local.key;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;

    location / {
        proxy_pass http://web_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-Proto https;
    }
}

server {
    listen 80;
    server_name dominio.local;
    return 301 https://$host$request_uri;
}
```

# Laboratorio: Proxy Inverso con Nginx, Balanceo y SSL

## Objetivos

- Comprender el funcionamiento de un proxy inverso.
- Configurar Nginx como balanceador de carga.
- Escalar backends con Docker Compose (`--scale`).
- Implementar Service Discovery en Docker.
- Configurar SSL con certificados autofirmados.

## Pasos

1. Generar certificados:
   ```bash
   ./make-certs.sh
````

2. Iniciar el entorno:

   ```bash
   docker compose up --scale web_backend=3 -d
   ```
3. Comprobar contenedores:

   ```bash
   docker ps
   ```
4. Acceder en el navegador:

   * [http://localhost](http://localhost)
   * [https://localhost](https://localhost)
5. Escalar y observar:

   ```bash
   docker compose up --scale web_backend=5 -d
   ```
6. Ver logs:

   ```bash
   docker logs proxy_server
   ```

## Ejercicios

1. Cambiar el algoritmo de balanceo a `least_conn`.
2. Añadir autenticación básica a `/apellidos`.
3. Mostrar el `hostname` del contenedor en la página web.
4. Simular la caída de una instancia y observar el balanceo.
5. Activar HTTPS y probar la conexión segura.

## Comprobaciones

* `curl http://localhost`
* `curl -k https://localhost`
* `docker exec proxy_server nginx -s reload`

---

## Verificación y pruebas

```bash
docker compose up --scale web_backend=3 -d
docker ps
curl http://localhost/
curl -k https://localhost/
docker logs proxy_server
````

---

## Ejercicios propuestos

1. Configurar el proxy con algoritmo `ip_hash`.
2. Implementar healthchecks para backends.
3. Analizar los logs de Nginx para comprobar la distribución de carga.
4. Configurar HTTPS con certificados reales (Let's Encrypt).
5. Implementar una caché en el proxy para recursos estáticos.

---

## Conclusiones

* Nginx puede actuar como proxy inverso y balanceador de carga.
* Docker Compose permite escalar servicios fácilmente.
* Service Discovery simplifica la conexión entre contenedores.
* Las redes internas protegen los backends del acceso externo.
* La terminación SSL en el proxy centraliza la seguridad y simplifica la gestión de certificados.


