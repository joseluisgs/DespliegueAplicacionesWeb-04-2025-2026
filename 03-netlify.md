- [3. Despliegue de Web con Netlify](#3-despliegue-de-web-con-netlify)
  - [3.1. Netlify](#31-netlify)
  - [3.2. Desplegando una web con Netlify](#32-desplegando-una-web-con-netlify)
    - [3.2.1. Deploy manually](#321-deploy-manually)
    - [3.2.2. Start from template](#322-start-from-template)
    - [3.2.3. Import an existing project](#323-import-an-existing-project)
  - [3.3. Continuous Deployment en Netlify](#33-continuous-deployment-en-netlify)
    - [3.3.1. Build settings y deploy contexts](#331-build-settings-y-deploy-contexts)
    - [3.3.2. Deploy Previews y Branch Deploys](#332-deploy-previews-y-branch-deploys)
    - [3.3.3. Deploy hooks y Webhooks](#333-deploy-hooks-y-webhooks)
  - [3.4. Integración con registros de contenedores y Docker](#34-integración-con-registros-de-contenedores-y-docker)
    - [3.4.1. Ejemplo: GitHub Actions para build Docker](#341-ejemplo-github-actions-para-build-docker)
    - [3.4.2. Ejemplo alternativo: Push a Docker Hub](#342-ejemplo-alternativo-push-a-docker-hub)
  - [3.5. Netlify Functions y Formularios](#35-netlify-functions-y-formularios)
  - [3.6. Seguridad y SSL/TLS](#36-seguridad-y-ssltls)
  - [3.7. Protegiendo rutas](#37-protegiendo-rutas)
  - [3.8. Monitorización y Logs de despliegue](#38-monitorización-y-logs-de-despliegue)
  - [3.9. Netlify CLI y flujos locales](#39-netlify-cli-y-flujos-locales)
  - [3.10. Práctica Netlify](#310-práctica-netlify)
  - [3.11. Buenas prácticas y consideraciones](#311-buenas-prácticas-y-consideraciones)


# 3. Despliegue de Web con Netlify

## 3.1. Netlify

Netlify es una plataforma de desarrollo web moderna que ofrece servicios de hosting, almacenamiento, funciones serverless, formularios y mucho más. Es ideal para sitios web estáticos, aplicaciones web progresivas y aplicaciones web modernas.

Netlify ofrece las siguientes características principales:
- **Hosting de sitios web estáticos**: Netlify puede alojar cualquier sitio web estático (HTML, CSS, JavaScript, imágenes, etc.) y sirve los archivos desde una red de distribución global (CDN).
- **Funciones serverless**: Netlify Functions son funciones serverless que puedes ejecutar en respuesta a eventos (como solicitudes HTTP, cambios en la base de datos, etc.).
- **Formularios**: Netlify Forms es un servicio de gestión de formularios que te permite recibir y gestionar envíos de formularios sin necesidad de un backend.
- **SSL/TLS**: Netlify proporciona certificados SSL/TLS gratuitos para todos los sitios web alojados en su plataforma.
- **Despliegue continuo**: Netlify puede desplegar automáticamente tu sitio web cada vez que realices un push a tu repositorio de Git.

![Netlify](https://www.netlify.com/v3/img/components/netlify-logo.svg)

💡 **Tip del Examinador:** Netlify es ideal para sitios web estáticos y aplicaciones web modernas. Ofrece una amplia gama de funciones que facilitan el desarrollo y despliegue de aplicaciones web.

## 3.2. Desplegando una web con Netlify

Netlify ofrece varias formas de desplegar un sitio web:

### 3.2.1. Deploy manually

Puedes desplegar un sitio web manualmente arrastrando y soltando una carpeta con los archivos de tu sitio web en el panel de control de Netlify.

1. Accede a [Netlify](https://netlify.com) e inicia sesión.
2. Haz clic en "Add new site" y selecciona "Deploy manually".
3. Arrastra y suelta la carpeta con los archivos de tu sitio web.
4. Netlify desplegará tu sitio web y te proporcionará una URL única.

### 3.2.2. Start from template

Netlify ofrece una serie de plantillas preconfiguradas que puedes usar para comenzar tu proyecto.

1. Accede a [Netlify](https://netlify.com) e inicia sesión.
2. Haz clic en "Add new site" y selecciona "Start from template".
3. Elige una plantilla que se adapte a tus necesidades.
4. Netlify creará un repositorio en tu cuenta de GitHub con la plantilla seleccionada.
5. Netlify desplegará automáticamente tu sitio web.

### 3.2.3. Import an existing project

Puedes importar un proyecto existente desde GitHub, GitLab o Bitbucket.

1. Accede a [Netlify](https://netlify.com) e inicia sesión.
2. Haz clic en "Add new site" y selecciona "Import an existing project".
3. Conecta tu cuenta de GitHub, GitLab o Bitbucket.
4. Selecciona el repositorio que deseas desplegar.
5. Netlify configurará automáticamente el despliegue continuo.
6. Haz clic en "Deploy site" para desplegar tu sitio web.

📝 **Nota del Profesor:** Netlify puede detectar automáticamente el framework de tu proyecto (React, Vue, Angular, Hugo, Jekyll, etc.) y configurar los comandos de compilación y la carpeta de salida.

## 3.3. Continuous Deployment en Netlify

Netlify ofrece despliegue continuo (Continuous Deployment) para que tu sitio web se actualice automáticamente cada vez que realices un push a tu repositorio.

### 3.3.1. Build settings y deploy contexts

Puedes configurar los ajustes de compilación y los contextos de despliegue en el archivo `netlify.toml`.

```toml
[build]
  command = "npm run build"
  publish = "dist"

[context.production]
  environment = { NODE_ENV = "production" }

[context.deploy-preview]
  environment = { NODE_ENV = "preview" }

[context.branch-deploy]
  environment = { NODE_ENV = "staging" }
```

### 3.3.2. Deploy Previews y Branch Deploys

Netlify ofrece Deploy Previews y Branch Deploys para que puedas previsualizar los cambios antes de desplegarlos en producción.

- **Deploy Previews**: Cada pull request genera un Deploy Preview que te permite previsualizar los cambios antes de fusionarlos.
- **Branch Deploys**: Cada rama genera un Branch Deploy que te permite previsualizar los cambios en un entorno de staging.

### 3.3.3. Deploy hooks y Webhooks

Netlify ofrece deploy hooks y webhooks para que puedas activar despliegues manualmente o en respuesta a eventos externos.

- **Deploy hooks**: Puedes crear un deploy hook que active un despliegue cuando reciba una solicitud HTTP.
- **Webhooks**: Puedes configurar un webhook en tu repositorio de GitHub para que active un despliegue cuando se produzcan ciertos eventos (por ejemplo, un push a la rama main).

💡 **Tip del Examinador:** Los deploy hooks son útiles para activar despliegues en respuesta a eventos externos, como una compilación de CI/CD o un webhook de un servicio de terceros.

## 3.4. Integración con registros de contenedores y Docker

Netlify puede integrarse con registros de contenedores y Docker para construir y desplegar imágenes Docker.

### 3.4.1. Ejemplo: GitHub Actions para build Docker

Puedes usar GitHub Actions para construir una imagen Docker y desplegarla en Netlify.

```yaml
name: Build and Deploy Docker

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      - name: Build and push
        uses: docker/build-push-action@v4
        with:
          context: .
          push: true
          tags: netlify/my-app:latest
```

### 3.4.2. Ejemplo alternativo: Push a Docker Hub

Puedes usar GitHub Actions para construir y publicar una imagen Docker en Docker Hub.

```yaml
name: Build and Push Docker

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      - name: Log in to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}

      - name: Build and push
        uses: docker/build-push-action@v4
        with:
          context: .
          push: true
          tags: username/my-app:latest
```

📝 **Nota del Profesor:** Netlify puede ejecutar funciones serverless en respuesta a eventos de Docker, como la construcción de imágenes o el despliegue de contenedores.

⚠️ **Advertencia de Seguridad:** Nunca almacenes secretos (contraseñas, tokens, claves API) directamente en tu repositorio. Usa secretos de GitHub Actions para almacenar esta información de forma segura.

## 3.5. Netlify Functions y Formularios

Netlify ofrece funciones serverless (Netlify Functions) y formularios (Netlify Forms) para que puedas añadir funcionalidad dinámica a tu sitio web estático.

- **Netlify Functions**: Puedes crear funciones serverless que se ejecutan en respuesta a eventos (como solicitudes HTTP, cambios en la base de datos, etc.).
- **Netlify Forms**: Puedes añadir formularios a tu sitio web y recibir envíos de formularios sin necesidad de un backend.

```javascript
// Netlify Function
exports.handler = async function(event, context) {
  return {
    statusCode: 200,
    body: JSON.stringify({ message: "¡Hola desde Netlify Functions!" })
  };
};
```

## 3.6. Seguridad y SSL/TLS

Netlify proporciona certificados SSL/TLS gratuitos para todos los sitios web alojados en su plataforma. Los certificados se generan automáticamente mediante Let's Encrypt y se renuevan automáticamente.

Para configurar SSL/TLS, sigue estos pasos:

1. Accede a tu sitio web en el panel de control de Netlify.
2. Haz clic en "Domain management" y selecciona "SSL/TLS certificate".
3. Haz clic en "Provision certificate" para generar un certificado SSL/TLS.
4. Netlify generará y configurará automáticamente el certificado.

💡 **Tip del Examinador:** Netlify ofrece HTTPS forzado y HSTS (HTTP Strict Transport Security) para mejorar la seguridad de tu sitio web.

## 3.7. Protegiendo rutas

Puedes proteger rutas de tu sitio web usando autenticación básica o acceso restringido.

- **Autenticación básica**: Puedes añadir autenticación básica a tu sitio web para restringir el acceso a ciertas rutas.
- **Acceso restringido**: Puedes configurar el acceso restringido para que solo ciertos usuarios puedan acceder a tu sitio web.

```toml
[[headers]]
  for = "/admin/*"
  [headers.values]
    X-Frame-Options = "DENY"
    X-XSS-Protection = "1; mode=block"
    Referrer-Policy = "strict-origin-when-cross-origin"
```

## 3.8. Monitorización y Logs de despliegue

Netlify ofrece monitorización y logs de despliegue para que puedas supervisar el estado de tu sitio web.

- **Logs de despliegue**: Puedes ver los logs de cada despliegue en el panel de control de Netlify.
- **Monitorización**: Puedes ver métricas de rendimiento, uso de ancho de banda y errores de tu sitio web.

📝 **Nota del Profesor:** Los logs de despliegue son útiles para depurar errores y optimizar el rendimiento de tu sitio web.

## 3.9. Netlify CLI y flujos locales

Netlify CLI es una herramienta de línea de comandos que te permite gestionar tus sitios web de Netlify desde tu máquina local.

```bash
# Instalar Netlify CLI
npm install -g netlify-cli

# Iniciar sesión en Netlify
netlify login

# Desplegar un sitio web localmente
netlify dev

# Desplegar un sitio web en producción
netlify deploy --prod
```

💡 **Tip del Examinador:** Netlify CLI es útil para probar funciones serverless y configuraciones de redireccionamiento localmente antes de desplegarlas en producción.

## 3.10. Práctica Netlify

Crea un sitio web con Netlify que incluya:
- Un formulario de contacto con Netlify Forms.
- Una función serverless que envíe un correo electrónico cuando se envíe el formulario.
- Autenticación básica para una ruta de administración.

## 3.11. Buenas prácticas y consideraciones

- Usa un archivo `netlify.toml` para configurar los ajustes de compilación y los contextos de despliegue.
- Usa variables de entorno para almacenar información sensible (contraseñas, tokens, claves API).
- Usa deploy hooks para activar despliegues en respuesta a eventos externos.
- Usa Netlify Functions para añadir funcionalidad dinámica a tu sitio web estático.
- Usa Netlify Forms para gestionar envíos de formularios sin necesidad de un backend.
- Usa SSL/TLS para proteger la comunicación entre el cliente y el servidor.
- Usa monitorización y logs de despliegue para supervisar el estado de tu sitio web.

⚠️ **Advertencia de Seguridad:** Nunca almacenes secretos (contraseñas, tokens, claves API) directamente en tu repositorio. Usa variables de entorno y secretos de Netlify para almacenar esta información de forma segura.
