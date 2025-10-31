## 100 Preguntas Tipo Test sobre Despliegue y Servidores

- [100 Preguntas Tipo Test sobre Despliegue y Servidores](#100-preguntas-tipo-test-sobre-despliegue-y-servidores)
  - [I. Fundamentos de Servidores Web y Protocolo HTTP](#i-fundamentos-de-servidores-web-y-protocolo-http)
  - [II. Configuración y Administración de Apache](#ii-configuración-y-administración-de-apache)
  - [III. Configuración y Administración de Nginx](#iii-configuración-y-administración-de-nginx)
  - [IV. Despliegue y Administración de Contenedores con Docker](#iv-despliegue-y-administración-de-contenedores-con-docker)
  - [V. Despliegue de Aplicaciones Específicas (Java y .NET)](#v-despliegue-de-aplicaciones-específicas-java-y-net)
  - [VI. Monitorización y Logs Estructurados](#vi-monitorización-y-logs-estructurados)
  - [VII. Proxy Inverso, Balanceo de Carga y Arquitectura](#vii-proxy-inverso-balanceo-de-carga-y-arquitectura)
  - [VIII. Despliegue con GitHub Pages y CI/CD](#viii-despliegue-con-github-pages-y-cicd)
  - [IX. Despliegue con Netlify](#ix-despliegue-con-netlify)
  - [X. Comandos y Conceptos Adicionales](#x-comandos-y-conceptos-adicionales)


### I. Fundamentos de Servidores Web y Protocolo HTTP

1.  Según las referencias, ¿cuál es el protocolo que utiliza un servidor web para servir archivos a los usuarios?
    A) FTP
    B) SMTP
    C) HTTP
    D) SSH

2.  ¿Qué tipo de servidor web genera contenido a partir de una base de datos usando un lenguaje de programación del lado del servidor (como PHP, Python o Java)?
    A) Servidor web estático
    B) Servidor web dinámico
    C) Servidor de caché
    D) Servidor proxy

3.  ¿Cuál de las siguientes afirmaciones describe mejor la naturaleza del Protocolo de Transferencia de Hipertexto (HTTP)?
    A) Es un protocolo con estado, manteniendo la información de cada sesión.
    B) Es un protocolo sin estado, donde cada solicitud y respuesta es independiente.
    C) Es un protocolo exclusivo de redes locales.
    D) Es un protocolo que solo soporta el método GET.

4.  ¿Cuál de los siguientes no es un componente obligatorio de una solicitud HTTP, sino opcional?
    A) Método (GET, POST)
    B) URL
    C) Versión del protocolo
    D) Cuerpo de mensaje

5.  ¿Cuál es el significado del código de estado HTTP 404?
    A) La solicitud se ha completado correctamente.
    B) Error interno del servidor.
    C) El recurso solicitado no se ha encontrado en el servidor.
    D) Redirección permanente.

6.  ¿Qué servidor web de código abierto, gratuito y multiplataforma es desarrollado y mantenido por la Apache Software Foundation?
    A) Nginx
    B) Microsoft Internet Information Services (IIS)
    C) Apache HTTP Server
    D) Tomcat

7.  Apache Server soporta una amplia variedad de módulos y extensiones para añadir funcionalidades adicionales. ¿Cuál de las siguientes características de Apache soporta el cifrado de comunicación entre el servidor y los clientes?
    A) Soporte para virtual hosting
    B) Soporte para compresión de contenido
    C) Soporte para SSL/TLS
    D) Soporte para FTP

### II. Configuración y Administración de Apache

8.  En la estructura de directorios de Apache, ¿cuál es el archivo principal de configuración donde se realizan cambios generales?
    A) ports.conf
    B) envvars
    C) apache2.conf
    D) mods-enabled

9.  En el contexto de la gestión de la configuración de Apache en Linux, ¿qué directorio contiene los archivos de configuración de hosts virtuales disponibles?
    A) sites-enabled
    B) conf-enabled
    C) mods-available
    D) sites-available

10. ¿Qué comando se utiliza para activar un host virtual en Apache?
    A) a2enmod
    B) a2ensite
    C) a2dismod
    D) a2disconf

11. ¿Qué directiva de configuración de Apache permite alojar múltiples sitios web en un solo servidor, cada uno con su propio directorio raíz y configuración?
    A) <Directory>
    B) <VirtualHost>
    C) Listen
    D) ServerName

12. Si se desea añadir más usuarios a un archivo `.htpasswd` ya existente, ¿cómo debe ejecutarse el comando `htpasswd`?
    A) Con la opción `-f`
    B) Sin la opción `-c`
    C) Con la opción `-n`
    D) Con la opción `-r`

13. Para que un usuario pueda acceder a un servidor web mediante un nombre de dominio en lugar de una dirección IP durante pruebas locales, ¿qué archivo del sistema operativo Linux debe modificarse?
    A) /etc/network/interfaces
    B) /etc/resolv.conf
    C) /etc/hosts
    D) /etc/sysconfig/network

14. Si un administrador quiere personalizar el mensaje que aparece cuando el recurso solicitado no se encuentra, ¿qué código de error debe configurar en el archivo de configuración del host virtual de Apache?
    A) 200 OK
    B) 500 Internal Server Error
    C) 404 Not Found
    D) 301 Moved Permanently

15. Para configurar la seguridad con SSL/TSL en Apache, ¿qué herramienta se recomienda utilizar para generar un certificado?
    A) Wireshark
    B) OpenSSL
    C) GnuPG
    D) Certbot (no es la única opción de las fuentes, pero OpenSSL es la referida para generación)

16. ¿Qué archivo del sistema operativo Windows se debe editar con permisos de administrador para dar de alta un nombre de dominio localmente?
    A) C:\Windows\System32\config\system
    B) C:\Windows\System32\drivers\etc\hosts
    C) C:\Windows\System32\drivers\etc\network
    D) C:\Windows\System32\drivers\etc\config

### III. Configuración y Administración de Nginx

17. En la estructura de directorios de Nginx en sistemas Debian/Ubuntu, ¿dónde se encuentran los bloques *server* (virtual hosts) que están actualmente activos?
    A) /var/www/
    B) sites-available/
    C) sites-enabled/
    D) conf.d/

18. ¿Qué comando se utiliza en Nginx para verificar la sintaxis de la configuración antes de recargar o reiniciar el servicio?
    A) nginx -v
    B) nginx -s reload
    C) nginx -t
    D) nginx -p

19. A diferencia de Apache, Nginx no utiliza `a2enmod`/`a2dismod`. Si se necesitan módulos adicionales en Nginx dentro de un contenedor Docker, ¿cuál es el método recomendado?
    A) Instalar los módulos con `apt-get install` durante la ejecución.
    B) Los módulos están siempre incorporados en el binario.
    C) Construir una imagen custom con los módulos compilados o instalados.
    D) Configurar la directiva `LoadModule` en `nginx.conf`.

20. Para configurar Nginx para autenticación HTTP Basic protegiendo un directorio, ¿qué herramienta es la más recomendada para generar el archivo `.htpasswd`?
    A) `openssl`
    B) `nginx-utils`
    C) `apache2-utils` (comando `htpasswd`)
    D) `git`

21. En Nginx, si se desea personalizar las páginas de error (como el 404 o el 500), ¿qué directiva se utiliza dentro del bloque *server* o *location*?
    A) `return`
    B) `error_page`
    C) `root`
    D) `location`

22. Para lograr la terminación SSL en el proxy centralizando la seguridad en Nginx, ¿qué puerto debe ser mapeado en Docker Compose para el tráfico cifrado?
    A) 80
    B) 22
    C) 8080
    D) 443

### IV. Despliegue y Administración de Contenedores con Docker

23. ¿Cuál es el concepto clave que deben seguir los servidores web en Docker para que el Docker Engine los recolecte y gestione logs de manera efectiva?
    A) Enviar logs a un volumen persistente.
    B) Enviar logs a STDOUT (Salida Estándar) y STDERR (Salida de Error Estándar).
    C) Enviar logs solo a un archivo dentro del contenedor.
    D) Usar el driver de logging por defecto `syslog`.

24. ¿Cuál es el comando de Docker Compose que lanza todos los servicios definidos en el archivo `docker-compose.yml` en modo *detached* (segundo plano)?
    A) `docker-compose start`
    B) `docker-compose up -d`
    C) `docker-compose run`
    D) `docker-compose build`

25. ¿Qué comando permite ejecutar una terminal (bash) interactiva dentro de un contenedor llamado `apache_server`?
    A) `docker logs apache_server`
    B) `docker stop apache_server`
    C) `docker exec -it apache_server bash`
    D) `docker attach apache_server`

26. Después de montar nuevos Virtual Hosts por volumen en un contenedor Apache, ¿qué comando de Docker Compose se debe ejecutar para que Apache relea los archivos de configuración sin detener el servicio?
    A) `docker-compose restart`
    B) `docker exec apache_server apache2ctl configtest`
    C) `docker exec apache_server service apache2 reload`
    D) `docker-compose stop`

27. Si se desea empezar un despliegue de Docker Compose totalmente desde cero, eliminando contenedores, redes y volúmenes nombrados, ¿qué comando debe ejecutarse?
    A) `docker-compose stop`
    B) `docker-compose down`
    C) `docker-compose down -v`
    D) `docker-compose kill`

28. En la documentación esencial de un proyecto de despliegue con Docker Compose, ¿qué archivo se recomienda incluir en la raíz para documentar los pasos de despliegue, la arquitectura y los comandos clave?
    A) `docker-compose.yml`
    B) `runbook.txt`
    C) `Dockerfile`
    D) `README.md`

### V. Despliegue de Aplicaciones Específicas (Java y .NET)

29. Para desplegar una aplicación Java en un contenedor Docker, ¿qué extensión de archivo se asume que se encuentra en el directorio `target` para ser utilizada por el Dockerfile?
    A) `.exe`
    B) `.war`
    C) `.jar`
    D) `.apk`

30. ¿Qué enfoque se utiliza en el Dockerfile de una aplicación Java (JVM) para compilar y construir el archivo `.jar` dentro del contenedor, copiándolo posteriormente a una imagen base más ligera?
    A) Single-stage build
    B) Enfoque de múltiples etapas (Multi-stage build)
    C) Uso de *sidecars*
    D) Usando solo la imagen base de OpenJDK

31. Si se quiere ejecutar un contenedor Java mapeando el puerto 8080 del contenedor al puerto 8080 de la máquina local, ¿qué opción de `docker run` se utiliza?
    A) `-v 8080:8080`
    B) `-p 8080:8080`
    C) `--link 8080:8080`
    D) `--env PORT=8080`

32. ¿Qué se necesita principalmente para desplegar una aplicación .NET en un contenedor Docker?
    A) Un archivo de configuración de IIS.
    B) Un Dockerfile que defina cómo se construirá el contenedor y el ejecutable.
    C) Un archivo de manifiesto Kubernetes.
    D) Una base de datos MySQL adjunta.

33. ¿Cuál es la herramienta recomendada por las fuentes para gestionar el despliegue de una aplicación .NET junto con una base de datos MySQL?
    A) Docker Run
    B) Docker Compose
    C) Kubernetes
    D) Vagrant

### VI. Monitorización y Logs Estructurados

34. ¿Cuál es el driver de logging que captura por defecto la salida de STDOUT y STDERR de Docker Engine y la almacena en el sistema de archivos del host en formato JSON?
    A) Fluentd driver
    B) Syslog driver
    C) Json-file driver
    D) Logstash driver

35. ¿Cuál es el objetivo principal de configurar la **rotación de logs** en el `docker-compose.yml`?
    A) Aumentar la velocidad de transferencia de logs.
    B) Evitar que los archivos de logs consuman todo el espacio del disco.
    C) Formatear los logs a un formato CSV.
    D) Enviar los logs a un servidor Syslog externo.

36. En el contexto de la consolidación y análisis centralizado de logs, ¿qué término se utiliza para referirse a la herramienta que recolecta y envía los logs a un servidor centralizado (ej. Fluentd, Logstash)?
    A) Database connector
    B) Log Shipper
    C) Data transformer
    D) Output plugin

37. ¿Cuál es el motor de búsqueda y análisis distribuido donde se almacenan y estructuran los logs recibidos, como parte del Elastic Stack (ELK)?
    A) Kibana
    B) Fluentd
    C) Logstash
    D) Elasticsearch

38. ¿Cuál es la interfaz gráfica de usuario estándar para buscar, analizar y visualizar los datos indexados en Elasticsearch?
    A) Grafana
    B) Graylog
    C) Kibana
    D) Prometheus

39. Si en un despliegue Docker Compose se utiliza el `logging driver fluentd`, ¿a qué componente se redirigen los logs capturados por el Docker Engine?
    A) A la base de datos
    B) Al servicio `fluentd:24224`
    C) Directamente a Elasticsearch
    D) A un archivo simple de texto en el host

40. Al desplegar el Elastic Stack, ¿cuál es el puerto por defecto para acceder a la interfaz de Kibana?
    A) 9200
    B) 8080
    C) 5601
    D) 24224

41. Para que Fluentd envíe los logs de Apache/Nginx a Elasticsearch, ¿qué archivo se debe reconfigurar para cambiar el *output* de un archivo local a Elasticsearch?
    A) `docker-compose.yml`
    B) `td-agent.conf`
    C) `apache2.conf`
    D) `ports.conf`

42. ¿Qué plataforma de gestión de logs es mencionada en las fuentes como una solución integral (agregación, almacenamiento, análisis y visualización) a menudo vista como una alternativa al Elastic Stack completo?
    A) Grafana
    B) Prometheus
    C) Graylog
    D) Logstash

### VII. Proxy Inverso, Balanceo de Carga y Arquitectura

43. ¿Cuál es la función principal de un proxy inverso?
    A) Recibir solicitudes de clientes y reenviarlas directamente a Internet.
    B) Recibir solicitudes de clientes y reenviarlas a uno o más servidores de origen o backend.
    C) Actuar como caché del lado del cliente.
    D) Gestionar la autenticación de usuarios.

44. ¿Qué ventaja del uso de un proxy inverso se relaciona directamente con la distribución de peticiones entre varios servidores backend?
    A) Escalabilidad
    B) Centralización de SSL
    C) Seguridad (oculta servidores internos)
    D) Balanceo de carga

45. En Nginx, ¿qué directiva se utiliza para agrupar varios servidores backend para que el proxy pueda distribuir las peticiones?
    A) `server`
    B) `location`
    C) `upstream`
    D) `proxy_pass`

46. ¿Cuál es el algoritmo de balanceo de carga que Nginx utiliza por defecto, enviando las peticiones de manera alternada a los servidores backend?
    A) least_conn
    B) ip_hash
    C) round-robin
    D) hash $variable

47. ¿Qué algoritmo de balanceo de Nginx se asegura de que un cliente siempre sea enviado al mismo servidor en función de su dirección IP?
    A) round-robin
    B) least_conn
    C) ip_hash
    D) hash $variable

48. ¿Qué sistema de Docker Compose permite a cada contenedor resolver el nombre de otro servicio dentro de la misma red usando su nombre como *hostname*?
    A) Service Discovery
    B) Mapeo de puertos
    C) Redireccionamiento DNS
    D) Host aliasing

49. Para garantizar la seguridad en un entorno Dockerizado con proxy inverso, ¿qué práctica se recomienda respecto a los backends?
    A) Deben exponer todos los puertos.
    B) Deben ser accesibles directamente desde el exterior.
    C) Deben comunicarse exclusivamente a través de la red interna.
    D) Deben usar direcciones IP públicas.

50. En una arquitectura con proxy inverso, ¿cuál es el único componente que debe exponer puertos al exterior?
    A) Los servidores backend
    B) La base de datos
    C) Solo el proxy
    D) Todos los contenedores

### VIII. Despliegue con GitHub Pages y CI/CD

51. ¿Qué es GitHub Pages?
    A) Un servicio de alojamiento de bases de datos.
    B) Un servicio de alojamiento web gratuito para sitios estáticos, directamente desde un repositorio de GitHub.
    C) Una herramienta para construir pipelines CI/CD.
    D) Un registro de contenedores.

52. Para crear un sitio web personal en GitHub Pages, ¿cuál es la estructura recomendada para el nombre del repositorio?
    A) `mi-proyecto/web`
    B) `nombreusuario/mi-proyecto`
    C) `nombreusuario.github.io`
    D) `ghcr.io/nombreusuario`

53. Si un usuario tiene un proyecto en GitHub llamado `nombreproyecto`, ¿cuál será la URL predeterminada para acceder a dicho proyecto publicado con GitHub Pages?
    A) `https://nombreusuario.github.io`
    B) `https://nombreusuario.github.io/nombreproyecto`
    C) `https://githubpages/nombreproyecto`
    D) `https://nombreusuario.io`

54. ¿Qué ramas se suelen configurar en GitHub Pages para que publique el sitio web?
    A) master y dev
    B) feature y release
    C) main o doc
    D) Todas las ramas

55. ¿Qué significa la sigla CI en el contexto de un pipeline CI/CD?
    A) Configuración Interna
    B) Contenedor Inteligente
    C) Integración Continua
    D) Infraestructura de Contenedores

56. En los conceptos clave de CI/CD, ¿qué término se refiere al proceso de publicar artefactos (como imágenes Docker) y actualizar el sitio?
    A) CI (Integración Continua)
    B) CD (Entrega Continua/Despliegue)
    C) Build
    D) Test

57. ¿Qué token de GitHub es necesario utilizar para que una GitHub Action realice operaciones como hacer *commit* y *push* en el propio repositorio?
    A) CR_PAT
    B) DOCKERHUB_TOKEN
    C) GITHUB_TOKEN
    D) Personal Access Token (PAT)

58. Si una GitHub Action realiza un *commit* en la rama `main` usando `GITHUB_TOKEN`, ¿qué comportamiento seguro previene que este *commit* dispare de nuevo el mismo workflow?
    A) Se usa una rama de despliegue separada.
    B) GitHub Actions no re-dispara workflows por commits hechos por `GITHUB_TOKEN` por defecto.
    C) El *workflow* siempre está condicionado a la existencia de un PR.
    D) El `GITHUB_TOKEN` caduca inmediatamente.

59. En una buena práctica de CI/CD, ¿dónde se recomienda guardar los tokens sensibles para que sean utilizados por GitHub Actions?
    A) En un archivo `.env` del repositorio.
    B) En el archivo `index.html`.
    C) En GitHub > Repository > Settings > Secrets and variables > Actions.
    D) Directamente en el `Dockerfile`.

60. ¿Cuál es el propósito del paso de un *workflow* de GitHub Actions que inserta información de *build* (timestamp, commit SHA) en el `index.html` y hace *commit* del cambio?
    A) Para reducir el tamaño del repositorio.
    B) Para que la web muestre automáticamente información del último *build* y del artefacto Docker publicado.
    C) Para que Netlify no necesite hacer un *deploy*.
    D) Para deshabilitar GitHub Pages.

### IX. Despliegue con Netlify

61. Netlify es una plataforma que integra servicios para desplegar sitios estáticos. ¿Qué componente proporciona Netlify para ejecutar lógica de backend (funciones serverless)?
    A) AWS Lambda
    B) Netlify Identity
    C) Netlify Functions
    D) Netlify Build Servers

62. ¿Cuál es la forma de despliegue en Netlify recomendada para prototipos rápidos, que consiste en arrastrar y soltar la carpeta de salida?
    A) Start from template
    B) Import an existing project
    C) Continuous Deployment
    D) Deploy manually

63. ¿Qué concepto de Netlify se refiere a los *builds* automáticos que se generan con una URL temporal cuando se abre un *pull request*?
    A) Branch deploys
    B) Production context
    C) Deploy previews
    D) Build settings

64. ¿Qué archivo se puede utilizar para definir comandos, variables de entorno y *behavior* por *context* dentro de un proyecto Netlify?
    A) `package.json`
    B) `index.html`
    C) `netlify.toml`
    D) `docker-compose.yml`

65. ¿Qué mecanismo se puede generar en Netlify, a través de una URL, para disparar un *deploy* desde un servicio externo (como un `curl`/`post`)?
    A) Netlify Function
    B) Deploy hook
    C) Build command
    D) Environment variable

66. Si un proyecto incluye un sitio estático y también una imagen Docker, ¿cuál de las siguientes tareas **no** realiza Netlify?
    A) Despliega el sitio estático desde el repositorio.
    B) Proporciona un CDN global.
    C) Ejecuta imágenes Docker como hosting de la web estática.
    D) Ejecuta *builds* automáticos.

67. Para que un formulario web en un sitio estático desplegado en Netlify pueda recoger envíos sin necesidad de un backend tradicional, ¿qué atributo HTML debe contener?
    A) `data-netlify="true"`
    B) `id="netlify-form"`
    C) `action="/submit"`
    D) `method="GET"`

68. ¿Qué servicio utiliza Netlify para activar HTTPS automático en dominios `.netlify.app` y dominios personalizados?
    A) Cloudflare
    B) OpenSSL
    C) Let's Encrypt
    D) Proprietary certificates

69. ¿Qué solución de Netlify ofrece autenticación integrada (signup/login) para proteger rutas mediante acceso basado en roles?
    A) Netlify CLI
    B) Netlify Functions
    C) Netlify Identity
    D) GitHub Actions

70. ¿Qué herramienta se puede instalar localmente para emular funciones, *redirects* y *proxys* de Netlify?
    A) Docker Compose
    B) Netlify CLI (`netlify dev`)
    C) OpenSSL
    D) Apache

### X. Comandos y Conceptos Adicionales

71. Para la administración de un despliegue Apache en Docker Compose, ¿cuál es el comando esencial de seguridad para verificar que un nuevo Virtual Host no romperá el servidor antes de recargar?
    A) `docker-compose logs`
    B) `docker exec apache_server apache2ctl configtest`
    C) `docker-compose down`
    D) `docker exec apache_server a2ensite`

72. En un despliegue de Nginx, ¿cuál es el comando de gestión de Nginx para listar los procesos?
    A) `nginx -s stop`
    B) `service nginx status`
    C) `service nginx reload`
    D) `nginx -V`

73. Al desplegar Nginx con Docker Compose, si el *entrypoint* es un script `nginx-init.sh`, ¿cuál es la tarea principal de este script que se menciona en las fuentes?
    A) Construir la imagen de Nginx.
    B) Crear enlaces simbólicos para activar *sites* y lanzar Nginx.
    C) Generar certificados SSL.
    D) Crear el archivo `docker-compose.yml`.

74. ¿Qué configuración debe añadirse en el `docker-compose.yml` para limitar el tamaño de los logs y la cantidad de archivos retenidos, usando el *json-file driver*?
    A) La directiva `volume`
    B) La directiva `loggin`
    C) La directiva `environment`
    D) La directiva `network`

75. ¿Qué herramienta de visualización de logs se conecta a Elasticsearch (u otras bases de datos) y es popular para crear *dashboards* sobre logs y métricas?
    A) Logstash
    B) Kibana
    C) Grafana
    D) Graylog

76. En el contexto de la documentación (README.md) de un despliegue de Docker Compose, ¿dónde se recomienda montar las configuraciones de Virtual Hosts (como `sites-available/`)?
    A) En `/tmp`
    B) Montados en `/etc/apache2/sites-available`
    C) En `/var/log`
    D) Directamente en `/usr/local/bin`

77. Si un administrador quiere recargar la configuración de Nginx dentro de un contenedor, ¿cuál es el comando correcto?
    A) `docker exec nginx_server nginx -s stop`
    B) `docker exec nginx_server service nginx reload`
    C) `docker exec nginx_server systemctl reload nginx`
    D) `docker-compose restart nginx_server`

78. Al usar Fluentd para logs, y configurar el *input* para recibir logs por TCP/TLS (por defecto del driver de Docker), ¿qué se configura para escribirlos en un archivo simple de texto (para demostración)?
    A) Un *input* de archivo
    B) Un *output* a archivo simple de texto
    C) Un *output* a Elasticsearch
    D) Un *filter* de logs

79. Para configurar el *logging driver* `fluentd` en el servicio web de `docker-compose.yml`, ¿qué información se requiere principalmente?
    A) El puerto donde escucha Kibana.
    B) La dirección IP del servidor Fluentd y su puerto.
    C) La ruta absoluta de los logs de Apache/Nginx.
    D) El nombre de la imagen de Fluentd.

80. En el despliegue de un servidor web Apache con Docker Compose, ¿qué directorio del host contiene los archivos de los sitios web (DocumentRoot)?
    A) `sites-available/`
    B) `apache2.conf`
    C) `websites/`
    D) `certs/`

81. En la arquitectura con proxy inverso, ¿cuál es el componente que reenvía la petición al backend correspondiente?
    A) El cliente
    B) El motor de Docker
    C) El proxy Nginx
    D) El servidor de base de datos

82. ¿Qué significa la sigla TSL en el contexto de Seguridad Web (SSL/TSL)?
    A) Transmisión Segura de Logs
    B) Transferencia de Servidor Local
    C) Transport Layer Security
    D) Tunneling Service Layer

83. ¿Qué comando de Docker Compose se utiliza para garantizar la escalabilidad de un servicio *backend* lanzando múltiples instancias del mismo?
    A) `docker-compose up --build`
    B) `docker-compose scale`
    C) `docker-compose exec`
    D) `docker-compose config`

84. Además de HTTP, ¿qué otro protocolo es mencionado como soportado por Apache HTTP Server?
    A) DNS
    B) SMTP
    C) FTP
    D) ICMP

85. Si un servidor web está generando contenido de forma dinámica utilizando un lenguaje de programación como Ruby o Node.js, ¿qué tipo de servidor web es?
    A) Estático
    B) De caché
    C) Dinámico
    D) Proxy reverso

86. ¿Cuál de los siguientes métodos forma parte de una solicitud HTTP?
    A) CREATE
    B) GET
    C) RUN
    D) START

87. En la configuración de Apache, ¿qué directorio se gestiona con los comandos `a2enmod` y `a2dismod`?
    A) `conf-available`
    B) `sites-available`
    C) `mods-available`
    D) `ports.conf`

88. ¿Qué archivo se utiliza en Apache para incluir configuraciones como `ServerName` y `DocumentRoot` al definir un host virtual?
    A) El archivo ubicado en `sites-available`
    B) `apache2.conf`
    C) `ports.conf`
    D) `.htaccess`

89. ¿Qué comando se debe usar para detener de forma controlada la ejecución de los contenedores de Docker Compose, preservando su estado?
    A) `docker-compose down`
    B) `docker-compose stop`
    C) `docker-compose kill`
    D) `docker-compose rm`

90. ¿Qué característica de Nginx se utiliza para gestionar certificados de forma centralizada y simplificar la gestión de certificados?
    A) Servidor estático
    B) Balanceo de carga
    C) Proxy reverso
    D) FastCGI

91. ¿Qué se recomienda crear para evitar incluir secretos directamente en los artefactos públicos de un pipeline CI/CD?
    A) Tags semánticos
    B) Secrets en GitHub
    C) Archivos `.htaccess`
    D) Un *branch deploy*

92. En el ejemplo de despliegue de Apache con Docker Compose, si el *entrypoint* automatiza la activación de dominios virtuales y el reinicio de Apache, ¿qué comando de Apache ejecuta dicho *entrypoint*?
    A) `systemctl start apache2`
    B) `service apache2 reload`
    C) Activación de dominios virtuales y reinicio de apache (implícito en el flujo)
    D) `apache2ctl configtest`

93. ¿Qué concepto de Netlify permite a los desarrolladores usar variables de entorno distintas para los contextos de `production`, `deploy-preview` y `branch-deploy`?
    A) Netlify Identity
    B) Build settings
    C) Deploy contexts
    D) Deploy hooks

94. En el ejemplo de workflow de GitHub Actions para Netlify, ¿qué servicio se utiliza para hacer *commit* fácilmente con `GITHUB_TOKEN` y evitar bucles?
    A) `actions/checkout@v4`
    B) `stefanzweifel/git-auto-commit-action`
    C) `docker/login-action`
    D) `docker/build-push-action`

95. ¿Qué plataforma es mencionada como alternativa para alojar imágenes Docker si no se utiliza GitHub Container Registry (GHCR)?
    A) Netlify
    B) Docker Hub
    C) Kubernetes
    D) Apache

96. ¿Cuál es el código de estado HTTP que indica que la solicitud se ha completado correctamente?
    A) 404
    B) 500
    C) 200 OK
    D) 302

97. Para configurar un *upstream* con el algoritmo de balanceo `least_conn`, ¿cuál es el criterio que utiliza Nginx para enviar la petición?
    A) La IP del cliente
    B) El servidor con menos conexiones activas
    C) La distribución alternada
    D) Una variable personalizada

98. ¿Dónde se debe configurar la limitación del tamaño de los logs y la rotación en Docker Compose para el servicio *web*?
    A) En el Dockerfile
    B) Dentro de la directiva `logging` en el servicio
    C) En el archivo `logrotate.conf`
    D) En la configuración de Apache/Nginx

99. En un entorno Dockerizado, si se requiere habilitar módulos como *rewrite* o *ssl* en Apache, ¿cuándo se realiza esta activación modular?
    A) Después de que el contenedor se detenga.
    B) Durante la construcción de la imagen en el Dockerfile o al inicio del contenedor (entrypoint).
    C) Usando `docker-compose down`.
    D) Usando el comando `a2dismod`.

100. En el proceso de despliegue con Netlify, ¿qué paso se realiza inmediatamente después de otorgar acceso al repositorio Git?
    A) Generar certificados SSL.
    B) Crear un nuevo sitio seleccionando importar desde Git.
    C) Instalar Netlify CLI.
    D) Configurar las Netlify Functions.