# Despliegue de Web con Netlify

- [Despliegue de Web con Netlify](#despliegue-de-web-con-netlify)
  - [Netlify](#netlify)
  - [Desplegando una web con Netlify](#desplegando-una-web-con-netlify)
    - [Deploy manually](#deploy-manually)
    - [Start from template](#start-from-template)
    - [Import an existing project](#import-an-existing-project)
  - [Continuous Deployment en Netlify (Conceptos)](#continuous-deployment-en-netlify-conceptos)
    - [Build settings y deploy contexts](#build-settings-y-deploy-contexts)
    - [Deploy Previews y Branch Deploys](#deploy-previews-y-branch-deploys)
    - [Deploy hooks y Webhooks](#deploy-hooks-y-webhooks)
  - [Integración con registros de contenedores y Docker (CI/CD externo)](#integración-con-registros-de-contenedores-y-docker-cicd-externo)
    - [Ejemplo: GitHub Actions para build Docker y push a GHCR y actualizar site en repo](#ejemplo-github-actions-para-build-docker-y-push-a-ghcr-y-actualizar-site-en-repo)
    - [Ejemplo alternativo: Push a Docker Hub](#ejemplo-alternativo-push-a-docker-hub)
  - [Netlify Functions (Serverless) y Formularios](#netlify-functions-serverless-y-formularios)
  - [Seguridad y SSL/TLS](#seguridad-y-ssltls)
  - [Protegiendo rutas (autenticación)](#protegiendo-rutas-autenticación)
  - [Monitorización y Logs de despliegue](#monitorización-y-logs-de-despliegue)
  - [Netlify CLI y flujos locales](#netlify-cli-y-flujos-locales)
  - [Práctica Netlify](#práctica-netlify)
  - [Buenas prácticas y consideraciones](#buenas-prácticas-y-consideraciones)

## Netlify

Netlify es una plataforma para desplegar sitios estáticos y aplicaciones JAMstack con integración con repositorios Git (GitHub, GitLab, Bitbucket). Proporciona build servers, HTTPS automático, CDN global, funciones serverless (Netlify Functions), formularios, redirecciones y deploy previews.

Documentación: https://docs.netlify.com/

## Desplegando una web con Netlify

Pasos principales:

1. Crear cuenta en Netlify y otorgar acceso a tu repositorio Git (p. ej. GitHub).
2. Crear un nuevo sitio: "Add new site" → elegir importar desde Git.
3. Seleccionar repositorio, rama (ej. `main`), y configurar los build commands y el directorio de publicación (publish directory).
4. Netlify ejecuta builds automáticos en cada push y publica el sitio en una URL netlify.app.

### Deploy manually

Arrastrar y soltar la carpeta de salida (ej. carpeta `dist/`, `public/`, o `site/`) en la interfaz de Netlify. Útil para prototipos rápidos.

### Start from template

Netlify ofrece plantillas y starters (ej. para Hugo, Gatsby, Next.js, Astro) que puedes clonar y desplegar.

### Import an existing project

Conectar directamente el repo de Git, configurar build command y publish directory. Ejemplo para un proyecto estático simple:

- Build command: (si no hace build) dejar en blanco.
- Publish directory: / (o ./public, ./dist según el proyecto).

## Continuous Deployment en Netlify (Conceptos)

Netlify ejecuta builds automáticamente al detectar commits en la rama configurada. Conceptos importantes:

- Build settings: comando de build y carpeta de publish (por ejemplo `npm run build` y `build/`).
- Deploy contexts: Netlify distingue entre `production`, `deploy-preview`, y `branch-deploy`. Puedes usar variables de entorno distintas por context.
- Deploy previews: al abrir un pull request Netlify hace un build y genera una URL preview.
- Branch deploys: puedes publicar ramas concretas en URLs separadas.

### Build settings y deploy contexts

En Site settings → Build & deploy puedes definir variables de entorno (ENV), comandos y ramas. También puedes usar archivos netlify.toml para definir behavior por context.

Ejemplo netlify.toml:

```toml
[build]
  publish = "public"
  command = "npm run build"

[context.production.environment]
  NODE_ENV = "production"

[context.deploy-preview]
  command = "npm run build:preview"
```

### Deploy Previews y Branch Deploys

- Deploy previews: builds automáticos para PRs con URL temporal.
- Branch deploys: builds automáticos por rama y publicación en subdominio.

### Deploy hooks y Webhooks

Puedes generar deploy hooks para disparar un deploy desde un servicio externo (curl/post) o desde una Action. En Site settings → Build & deploy → Deploy hooks creas un URL que, al recibir un POST, inicia un deploy.

Uso:

```bash
curl -X POST -d '{}' https://api.netlify.com/build_hooks/<HOOK_ID>
```

## Integración con registros de contenedores y Docker (CI/CD externo)

Netlify no ejecuta imágenes Docker como hosting de la web estática — su foco es static hosting + functions. Si tu proyecto incluye una imagen Docker (ej. servidor Nginx/Apache personalizado) y quieres construir y publicar la imagen a un registro (GHCR o Docker Hub) desde GitHub Actions, puedes integrar ambos flujos:

- Netlify: despliega el sitio estático desde el repositorio.
- GitHub Actions: construye imagen Docker, la sube a un registro y opcionalmente actualiza archivos del repo (por ejemplo inyectando información de build en `index.html`) para que Netlify vuelva a desplegar.

A continuación tienes un ejemplo de workflow para GitHub Actions que, al hacer push a `main`, construye la imagen Docker (con el Dockerfile del repo), la publica en GHCR, e inserta info del build en `index.html` y commitea ese cambio para que Netlify despliegue la nueva versión.

> Nota: hacer commit en la rama que dispara la workflow está permitido; commits hechos con `GITHUB_TOKEN` por defecto no re-disparan workflows, lo que evita bucles.

### Ejemplo: GitHub Actions para build Docker y push a GHCR y actualizar site en repo

Crea el archivo .github/workflows/netlify-ci-docker-publish.yml con el siguiente contenido (ajusta las rutas y nombres de imagen):

```yaml
name: CI - Build Docker, Push to GHCR and Update Netlify Site

on:
  push:
    branches:
      - main

permissions:
  contents: write
  packages: write

jobs:
  build-push-and-update-site:
    runs-on: ubuntu-latest
    env:
      IMAGE_NAME: ghcr.io/${{ github.repository_owner }}/mi-web-app
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
        with:
          persist-credentials: true

      - name: Set up QEMU (optional multi-arch)
        uses: docker/setup-qemu-action@v2

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      - name: Log in to GHCR
        uses: docker/login-action@v2
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build and push Docker image to GHCR
        uses: docker/build-push-action@v4
        with:
          context: .
          file: ./Dockerfile               # Ajustar la ruta al Dockerfile del proyecto
          push: true
          tags: |
            ${{ env.IMAGE_NAME }}:latest
            ${{ env.IMAGE_NAME }}:${{ github.sha }}

      - name: Create build info
        id: buildinfo
        run: |
          echo "BUILD_TS=$(date -u +"%Y-%m-%dT%H:%M:%SZ")" >> $GITHUB_ENV
          echo "BUILD_SHA=${GITHUB_SHA}" >> $GITHUB_ENV
          echo "BUILD_TAG=${IMAGE_NAME}:${GITHUB_SHA}" >> $GITHUB_ENV

      - name: Inject build info into index.html
        run: |
          SITE_INDEX=index.html           # Ajusta la ruta (ej. ./public/index.html o ./docs/index.html)
          if [ ! -f "$SITE_INDEX" ]; then
            echo "<html><body><h1>Autogenerated site</h1></body></html>" > "$SITE_INDEX"
          fi
          BUILD_BLOCK="<p>Build: $BUILD_TS - SHA: $BUILD_SHA - Image: $BUILD_TAG</p>"
          if grep -q "<!-- BUILD_INFO -->" "$SITE_INDEX"; then
            perl -0777 -pe "s/<!-- BUILD_INFO -->.*<!-- END_BUILD_INFO -->/<!-- BUILD_INFO -->\n$BUILD_BLOCK\n<!-- END_BUILD_INFO -->/s" -i "$SITE_INDEX"
          else
            if grep -q "</body>" "$SITE_INDEX"; then
              perl -0777 -pe "s|</body>|<!-- BUILD_INFO -->\n$BUILD_BLOCK\n<!-- END_BUILD_INFO -->\n</body>|s" -i "$SITE_INDEX"
            else
              echo -e "\n<!-- BUILD_INFO -->\n$BUILD_BLOCK\n<!-- END_BUILD_INFO -->" >> "$SITE_INDEX"
            fi
          fi

      - name: Commit and push changes
        uses: stefanzweifel/git-auto-commit-action@v4
        with:
          commit_message: "ci: update site with build info ${{ env.BUILD_TS }} (${{ env.BUILD_SHA }})"
          file_pattern: "index.html"
          branch: main
          author_name: github-actions[bot]
          author_email: 41898282+github-actions[bot]@users.noreply.github.com
```

Qué hace este flujo:
- Construye y publica la imagen Docker en GHCR.
- Añade información del build en `index.html`.
- Hace commit del `index.html` en `main` para que Netlify haga el deploy automático.

Ajustes posibles:
- Cambiar `file: ./Dockerfile` a `./nginx/Dockerfile` o `./apache/Dockerfile`.
- En lugar de escribir directamente en `index.html`, puedes actualizar un archivo de metadatos que tu página lea dinámicamente.
- Usar Docker Hub: ver sección siguiente.

### Ejemplo alternativo: Push a Docker Hub

Si prefieres Docker Hub, crea secrets `DOCKERHUB_USERNAME` y `DOCKERHUB_TOKEN` y sustituye el step de login y tags:

```yaml
- name: Log in to Docker Hub
  uses: docker/login-action@v2
  with:
    username: ${{ secrets.DOCKERHUB_USERNAME }}
    password: ${{ secrets.DOCKERHUB_TOKEN }}

- name: Build and push to Docker Hub
  uses: docker/build-push-action@v4
  with:
    context: .
    file: ./Dockerfile
    push: true
    tags: |
      ${{ secrets.DOCKERHUB_USERNAME }}/mi-web-app:latest
      ${{ secrets.DOCKERHUB_USERNAME }}/mi-web-app:${{ github.sha }}
```

## Netlify Functions (Serverless) y Formularios

- Netlify Functions te permite desplegar funciones serverless (Node.js / Go). Se colocan en `netlify/functions/` o se definen en `netlify.toml`.
- Formularios manejados por Netlify: usa atributos `data-netlify="true"` y Netlify recogerá envíos (sin backend).
- Ejemplo netlify.toml para funciones:

```toml
[build]
  command = "npm run build"
  publish = "dist"

[[redirects]]
  from = "/api/*"
  to = "/.netlify/functions/:splat"
  status = 200
```

## Seguridad y SSL/TLS

- Netlify activa HTTPS automático con Let's Encrypt en dominios netlify.app y dominios personalizados.
- Puedes configurar HSTS, headers y redirect rules en `netlify.toml`.
- Para dominios personalizados añade CNAME/A records según instrucciones de Netlify.

Ejemplo headers en netlify.toml:

```toml
[[headers]]
  for = "/*"
  [headers.values]
    Strict-Transport-Security = "max-age=31536000; includeSubDomains; preload"
    X-Frame-Options = "DENY"
    X-Content-Type-Options = "nosniff"
```

## Protegiendo rutas (autenticación)

Netlify Identity ofrece autenticación (signup/login) integrada. Para protección simple de rutas estáticas puedes:
- Usar Netlify Identity + Role-based access.
- Proteger contenido en la generación del sitio (build time).
- Para autenticación básica en funciones o serverless usar middleware o comprobar tokens.

## Monitorización y Logs de despliegue

- Netlify muestra el historial de deploys con logs por cada build. Desde la UI → Deploys puedes ver errores y etapas.
- Para alertas puedes configurar notificaciones por Slack o webhooks para recibir eventos de deploy.
- Deploy hooks permiten notificaciones y despliegues desde sistemas externos.

## Netlify CLI y flujos locales

Instalar Netlify CLI:

```bash
npm install -g netlify-cli
# Login
netlify login
# Link site en un repo
netlify init
# Deploy manual desde CLI
netlify deploy --dir=public --prod
# Ejecutar funciones/local server
netlify dev
```

`netlify dev` emula funciones, redirects y proxys localmente.

## Práctica Netlify

Tareas propuestas:
1. Crear un repo con tu web personal y curriculum (HTML + Bootstrap).
2. Conectar a Netlify con Continuous Deployment (branch `main`).
3. Añadir un simple `index.html` que muestre `<!-- BUILD_INFO -->` y probar que las GitHub Actions inyectan el build info.
4. Probar Netlify Functions con un formulario y ver envíos en UI.
5. Generar certificados (Netlify lo hace automáticamente) y añadir headers de seguridad en `netlify.toml`.

Ejemplo de index.html con marca para build info:

```html
<!doctype html>
<html>
  <head><meta charset="utf-8"><title>Mi Sitio</title></head>
  <body>
    <h1>Mi sitio en Netlify</h1>
    <!-- BUILD_INFO -->
    <!-- END_BUILD_INFO -->
  </body>
</html>
```

## Buenas prácticas y consideraciones

- Variables sensibles: usa Netlify Environment Variables (Site settings) para claves, tokens y secrets. Para GitHub Actions usa GitHub Secrets.
- Build reproducible: fijar versiones de Node, dependencias y acciones en workflows.
- Deploy previews: revisa previews antes de mergear.
- Evitar loops: si tu CI modifica la rama que dispara el build, recuerda las limitaciones del token y posible re-triggering.
- Escaneo de seguridad: integrar SAST/DAST o escaneo de imágenes con trivy en tus pipelines.
- Tagging y releases: publica imágenes Docker con tags semánticos y guarda changelogs.

