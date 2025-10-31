## 📝 Enunciado de Práctica: Despliegue Multi-Stage, Service Discovery y Pila EFK 🚀

- [📝 Enunciado de Práctica: Despliegue Multi-Stage, Service Discovery y Pila EFK 🚀](#-enunciado-de-práctica-despliegue-multi-stage-service-discovery-y-pila-efk-)
  - [🎯 Objetivo de la Práctica](#-objetivo-de-la-práctica)
  - [1. 🏗️ Requerimientos de Arquitectura y Servicios](#1-️-requerimientos-de-arquitectura-y-servicios)
  - [2. 🐳 Tareas de Contentorización y Redes](#2--tareas-de-contentorización-y-redes)
    - [2.1. Dockerfile del Microservicio Spring Boot](#21-dockerfile-del-microservicio-spring-boot)
    - [2.2. Configuración de Redes y Seguridad (CRÍTICO)](#22-configuración-de-redes-y-seguridad-crítico)
  - [3. ⚙️ Tareas de Orquestación y Enrutamiento](#3-️-tareas-de-orquestación-y-enrutamiento)
    - [3.1. Configuración de Docker Compose (`docker-compose.yml`)](#31-configuración-de-docker-compose-docker-composeyml)
    - [3.2. Configuración de NGINX (`nginx.conf`)](#32-configuración-de-nginx-nginxconf)
  - [4. 📝 Tarea de Análisis, Logs y Justificación Teórica](#4--tarea-de-análisis-logs-y-justificación-teórica)
    - [4.1. Configuración y Visualización EFK](#41-configuración-y-visualización-efk)
    - [4.2. Justificación de Arquitectura (CRÍTICO)](#42-justificación-de-arquitectura-crítico)
  - [5. 💡 Entregable](#5--entregable)


### 🎯 Objetivo de la Práctica

El objetivo es que el estudiante implemente una arquitectura completa de microservicios contentorizados, abordando no solo el despliegue (*Multi-Stage Build* y *Load Balancing*), sino también aspectos críticos de **redes, seguridad** (*Service Discovery* de Docker) y **observabilidad** (*EFK Stack*).

---

### 1. 🏗️ Requerimientos de Arquitectura y Servicios

La solución debe desplegar la siguiente arquitectura mediante **Docker Compose** en una **red interna privada**.

| Servicio                      | Tecnología        | Requerimiento de Despliegue                                           | Ruta de Acceso Externa (NGINX) |
| :---------------------------- | :---------------- | :-------------------------------------------------------------------- | :----------------------------- |
| **Microservicio Core**        | Spring Boot       | **3 réplicas** balanceadas.                                           | `/api`                         |
| **Servidor de Tests**         | NGINX             | Servir **reportes de tests** (artefactos extraídos del *build*).      | `/test`                        |
| **Servidor de Documentación** | Apache/NGINX      | Servir **documentación estática** (artefactos extraídos del *build*). | `/doc`                         |
| **Proxy/Balanceador**         | NGINX             | Punto único de entrada. **Proxy Inverso** y **Balanceador de Carga**. | Puerto **80** del Host         |
| **Log Processor**             | **Fluentd**       | Recolectar *logs* de NGINX y reenviarlos a Elasticsearch.             | N/A                            |
| **Search Engine**             | **Elasticsearch** | Almacenar datos de los *logs*.                                        | N/A                            |
| **Visualization**             | **Kibana**        | Interfaz web para analizar los *logs*.                                | Puerto **5601** del Host       |

---

### 2. 🐳 Tareas de Contentorización y Redes

#### 2.1. Dockerfile del Microservicio Spring Boot

El estudiante debe crear un único `Dockerfile` que use la estrategia **Multi-Stage Build** con las siguientes **tres etapas**:

1.  **Etapa `builder`:** Compilación, ejecución de tests y generación de artefactos (JAR, reportes, docs).
2.  **Etapa `artifacts`:** Extracción de **reportes de tests** y **documentación** a directorios dedicados para su posterior montaje.
3.  **Etapa `runtime`:** Creación de la imagen final **ligera** con solo el JRE y el JAR ejecutable.

#### 2.2. Configuración de Redes y Seguridad (CRÍTICO)

La configuración de `docker-compose.yml` debe asegurar:

1.  **Red Aislamiento (`app-network`):** Definir una única red `app-network` de tipo *bridge* para la comunicación interna de todos los servicios (API, servidores web, NGINX, EFK).
2.  **Aislamiento de Backends:** Garantizar que los servicios **`api-X`**, **`test-server`** y **`doc-server`** solo tengan conectividad a la red `app-network` y **NO expongan puertos al Host** (salvo el NGINX y Kibana). Esto garantiza que el único punto de acceso sea el **Proxy NGINX**.

---

### 3. ⚙️ Tareas de Orquestación y Enrutamiento

#### 3.1. Configuración de Docker Compose (`docker-compose.yml`)

El alumno debe configurar el archivo `docker-compose.yml` para cumplir con:

1.  **Escalado y Service Discovery:**
    * Definir las **3 réplicas de la API** (`api-1`, `api-2`, `api-3`) utilizando la etapa `runtime`.
    * **No utilizar `links` ni direcciones IP estáticas.** El Service Discovery de Docker Compose debe resolver automáticamente los nombres de servicio.
2.  **Montaje de Artefactos:** Utilizar un servicio `artifacts_extractor` (referenciando la etapa `artifacts` del `Dockerfile`) para **montar directamente** los directorios de tests y documentación en los contenedores `test-server` y `doc-server` (utilizando la sintaxis `type: service` para volúmenes).

#### 3.2. Configuración de NGINX (`nginx.conf`)

* Definir el `upstream` para la API, utilizando los **nombres de servicio** (`api-1`, `api-2`, `api-3`) como si fueran hosts DNS, confiando en la capacidad de **Service Discovery de Docker Compose**.
* Implementar el **Balanceo de Carga** para `/api`.
* Configurar los *logs* de acceso para que se escriban a un volumen compartido para su lectura por Fluentd.
* Implementar el *Reverse Proxy* para las rutas `/api`, `/test` y `/doc`.

---

### 4. 📝 Tarea de Análisis, Logs y Justificación Teórica

El alumno deberá demostrar la correcta trazabilidad y análisis del sistema, y la comprensión de la arquitectura.

#### 4.1. Configuración y Visualización EFK

* Configurar **`fluentd.conf`** para leer los *logs* de NGINX (desde el volumen compartido) y enviarlos a Elasticsearch.
* Acceder a Kibana (puerto 5601) y crear un *Index Pattern*.
* Demostrar mediante una **consulta/visualización** en Kibana que las peticiones a `/api` se están distribuyendo entre las **3 réplicas** de la API.

#### 4.2. Justificación de Arquitectura (CRÍTICO)

El alumno debe incluir en su informe una **sección de análisis** que responda a lo siguiente:

1.  **Service Discovery:** Explique cómo el Service Discovery de Docker Compose, al usar una red interna, permite que el `upstream` de NGINX resuelva `api-1`, `api-2`, `api-3` sin conocer sus direcciones IP internas. 
2.  **Seguridad:** Justifique por qué la configuración de la red interna (no exponer puertos de backends al host) es fundamental para la seguridad de esta arquitectura de *Reverse Proxy*.
3.  **Extracción de Artefactos:** Explique por qué el uso de la etapa `artifacts` en el *Dockerfile* y el montaje `type: service` en `docker-compose.yml` es superior a simplemente copiar los reportes a la etapa `runtime` o extraerlos al *host*.

---

### 5. 💡 Entregable

El alumno deberá entregar un repositorio con el código y archivos de configuración, además de un informe que documente el proceso y contenga las capturas de pantalla de la visualización de logs en Kibana, y las respuestas a la Sección 4.2.