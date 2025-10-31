# Despliegue de Web con GitHub Pages

Aquí tienes la tabla de contenido completa y ordenada del documento GitHub-Pages.md, incluyendo la nueva sección de Pipelines CI/CD y sus subapartados:

- [Despliegue de Web con GitHub Pages](#despliegue-de-web-con-github-pages)
- [GitHub Pages](#github-pages)
    - [Desplegando mi primer sitio personal](#desplegando-mi-primer-sitio-personal)
    - [Ejemplo alternativo: Push a Docker Hub](#ejemplo-alternativo-push-a-docker-hub)
    - [Buenas prácticas y consideraciones](#buenas-prácticas-y-consideraciones)
    - [¿Qué modifica la Action en la web?](#qué-modifica-la-action-en-la-web)



# GitHub Pages
GitHub Pages es un servicio de alojamiento web gratuito y fácil de usar que ofrece GitHub para alojar sitios web estáticos directamente desde un repositorio de GitHub. Puedes crear un sitio web personal, un proyecto, una documentación o cualquier otro tipo de sitio web estático directamente desde tu repositorio de GitHub.

Para más información, visita la [documentación de GitHub Pages](https://pages.github.com/).

### Desplegando mi primer sitio personal
Lo primero es hacernos un repositorio en GitHub. Para ello, vamos a nuestro perfil y creamos un nuevo repositorio. Le damos un nombre, por ejemplo, `nombreusuario.github.io` y lo creamos.

![GitHub Pages](https://docs.github.com/assets/cb-29762/mw-1440/images/help/repository/repo-create-global-nav-update.webp)

![GitHub Pages](https://docs.github.com/assets/cb-48480/mw-1440/images/help/pages/create-repository-name-pages.webp)

Posteriormente clonamos el repositorio en nuestro equipo y creamos un archivo `index.html` con el contenido que queramos. Por ejemplo:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Mi primer sitio web</title>
  </head>
  <body>
    <h1>Hola Mundo</h1>
    <p>Este es mi primer sitio web en GitHub Pages</p>
  </body>
</html>
```	

Ahora cambiamos la configuración en nuestro repositorio para que GitHub Pages use la rama `main` o `doc` y la carpeta raíz para ello. Para ello, vamos a `Settings` y en la sección `GitHub Pages` seleccionamos la rama y la carpeta raíz.

![GitHub Pages](https://docs.github.com/assets/cb-28260/mw-1440/images/help/repository/repo-actions-settings.webp)

![GitHub Pages](https://docs.github.com/assets/cb-81119/mw-1440/images/help/pages/click-pages-url-to-preview.webp)

Una vez creado, lo subimos a nuestro repositorio y en unos minutos, ya tendremos nuestro sitio web en `https://nombreusuario.github.io`.

## Publicando otros proyectos sobre tu usuario
Si tienes un proyecto en GitHub y quieres publicarlo en tu usuario, puedes hacerlo de la siguiente manera:

1. Crea un repositorio por ejemplo `nombreusuario/nombreproyecto`.
2. Clona el repositorio en tu equipo.
3. Crea un archivo `index.html` en la raíz del proyecto y el resto de ficheros
4. Configura GitHub Pages para que use la rama `main` o `doc` y la carpeta raíz.
5. Sube el proyecto a tu repositorio.
6. En unos minutos, ya tendrás tu proyecto publicado en `https://nombreusuario.github.io/nombreproyecto`.

¡Y listo! Ya tienes tu proyecto publicado en GitHub Pages.


## Práctica GitHub Pages

Usando GitHub Pages, crea un sitio web personal con tu nombre y apellidos, una breve descripción sobre ti y un enlace a tu perfil de GitHub. Deberás acceder desde `https://nombreusuario.github.io`.

Luego crea un repositorio llamado curriculum con estilos Bootstrap adaptativo a cada dispositivo, donde subirás tu curriculum en formato HTML y lo publicarás en GitHub Pages. Deberás acceder desde `https://nombreusuario.github.io/curriculum`. Deberás aportar imágenes de su renderizado usando la herramientas de desarrollador de tu navegador para varios dispositivos.




## Pipelines CI/CD con GitHub Actions (Build, Push, y cambios en la web)

Esta sección introduce el concepto de pipelines CI/CD aplicados a proyectos que publican contenido estático con GitHub Pages y, a la vez, construyen imágenes Docker de servidores web o aplicaciones. Veremos cómo configurar una GitHub Action que, tras un push a la rama `main`:

¿Que es un pipeline CI/CD? Un pipeline CI/CD (Integración Continua y Entrega/Despliegue Continuo) es un conjunto automatizado de procesos que permiten a los desarrolladores integrar cambios en el código, construir artefactos (como imágenes Docker), probarlos y desplegarlos de manera rápida y confiable. En este caso, el pipeline se encargará de:
- Construir la imagen Docker usando el Dockerfile del proyecto (por ejemplo los Dockerfiles usados en los apuntes de Apache/Nginx).
- Subir la imagen a un registro de contenedores (GitHub Container Registry - ghcr.io o Docker Hub).
- Actualizar la web (por ejemplo, inyectando la versión/build-timestamp en `index.html`) y haga commit/push de ese cambio al repositorio para que GitHub Pages publique la nueva versión.

Conceptos clave:
- CI (Integración Continua): automatizar compilación y pruebas al hacer push.
- CD (Entrega Continua/Despliegue): publicar artefactos (imágenes) y actualizar el sitio.
- Tokens y permisos: usar `GITHUB_TOKEN` para operaciones en el propio repo y `CR_PAT` (o `DOCKERHUB_USERNAME`/`DOCKERHUB_TOKEN`) para push a registros externos.

### Flujo propuesto
1. Push a `main` dispara workflow.
2. Workflow construye imagen Docker (por ejemplo ./Dockerfile-web o ./nginx/Dockerfile).
3. Workflow sube la imagen a ghcr.io (recomendada) o Docker Hub.
4. Workflow modifica `index.html` (añade build id, commit SHA o timestamp) y commitea el cambio en la misma rama `main` (o en una rama de despliegue, según política).
5. GitHub Pages detecta el cambio y publica la nueva versión del sitio.

IMPORTANTE: Si la Action hace commit en la rama que la dispara (main) debe evitar bucles; GitHub Actions no re-dispara workflows por commits hechos por `GITHUB_TOKEN` si el workflow no está configurado para hacerlo (comportamiento seguro por defecto). Aun así, es buena práctica condicionar commits para que no repitan infinitamente.


### Ejemplo: Workflow completo (build + push a GHCR + actualización de index.html)

Coloca este archivo en .github/workflows/ci-cd-build-and-publish.yml

```yaml
name: CI/CD - Build Docker, Push to GHCR and Update Site

on:
  push:
    branches:
      - main

permissions:
  contents: write   # necesario para hacer commit en el repo
  packages: write   # para publicar en GHCR
  id-token: write   # si usas OIDC con ghcr (opcional)

jobs:
  build-and-push:
    runs-on: ubuntu-latest
    env:
      IMAGE_NAME: ghcr.io/${{ github.repository_owner }}/mi-web-app
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
        with:
          persist-credentials: true

      - name: Set up QEMU (optional for multi-arch)
        uses: docker/setup-qemu-action@v2

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      - name: Log in to GitHub Container Registry
        uses: docker/login-action@v2
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build and push Docker image to GHCR
        uses: docker/build-push-action@v4
        with:
          context: .
          file: ./Dockerfile            # <- ajusta la ruta al Dockerfile del proyecto (por ejemplo ./nginx/Dockerfile)
          push: true
          tags: |
            ${{ env.IMAGE_NAME }}:latest
            ${{ env.IMAGE_NAME }}:${{ github.sha }}
          # platforms: linux/amd64,linux/arm64   # opcional

      - name: Create build-info (timestamp + sha)
        id: buildinfo
        run: |
          echo "BUILD_TS=$(date -u +"%Y-%m-%dT%H:%M:%SZ")" >> $GITHUB_ENV
          echo "BUILD_SHA=${GITHUB_SHA}" >> $GITHUB_ENV
          echo "BUILD_TAG=${IMAGE_NAME}:${GITHUB_SHA}" >> $GITHUB_ENV

      - name: Update index.html with build info
        id: update-site
        run: |
          # Ajusta la ruta al index.html de tu site (ej. ./docs/index.html o ./index.html)
          SITE_INDEX=index.html
          if [ ! -f "$SITE_INDEX" ]; then
            echo "<!-- created by workflow -->" > "$SITE_INDEX"
            echo "<html><body><h1>Autogenerated site</h1></body></html>" >> "$SITE_INDEX"
          fi

          # Inserta (o reemplaza) un párrafo con la info de build
          # Usamos una marca HTML <!-- BUILD_INFO --> para localizar dónde insertar
          BUILD_BLOCK="<p>Build: $BUILD_TS - SHA: $BUILD_SHA - Image: $BUILD_TAG</p>"
          # Si existe la marca, reemplázala; si no, añade al final
          if grep -q "<!-- BUILD_INFO -->" "$SITE_INDEX"; then
            perl -0777 -pe "s/<!-- BUILD_INFO -->.*<!-- END_BUILD_INFO -->/<!-- BUILD_INFO -->\n$BUILD_BLOCK\n<!-- END_BUILD_INFO -->/s" -i "$SITE_INDEX"
          else
            # Añadir antes de cierre de body si existe
            if grep -q "</body>" "$SITE_INDEX"; then
              perl -0777 -pe "s|</body>|<!-- BUILD_INFO -->\n$BUILD_BLOCK\n<!-- END_BUILD_INFO -->\n</body>|s" -i "$SITE_INDEX"
            else
              echo -e "\n<!-- BUILD_INFO -->\n$BUILD_BLOCK\n<!-- END_BUILD_INFO -->" >> "$SITE_INDEX"
            fi
          fi

      - name: Commit and push changes to main (if any)
        uses: stefanzweifel/git-auto-commit-action@v4
        with:
          commit_message: "ci: update site with build info ${{ env.BUILD_TS }} (${{ env.BUILD_SHA }})"
          file_pattern: "index.html"
          branch: main
          author_name: github-actions[bot]
          author_email: 41898282+github-actions[bot]@users.noreply.github.com
          # By default this action uses GITHUB_TOKEN so it won't re-trigger this workflow

```

Notas sobre el workflow anterior:
- Ajusta `file: ./Dockerfile` a la ruta real de tu Dockerfile (por ejemplo `./apache/Dockerfile` o `./nginx/Dockerfile` según los documentos anteriores).
- `IMAGE_NAME` usa GHCR: `ghcr.io/<owner>/mi-web-app`. Si prefieres Docker Hub cambia login y tags (usa usuario/imagen y secrets.DOCKERHUB_USERNAME/DOCKERHUB_TOKEN).
- La acción `stefanzweifel/git-auto-commit-action` facilita hacer commit con `GITHUB_TOKEN`. Por defecto los commits hechos con `GITHUB_TOKEN` no re-ejecutan workflows por razones de seguridad (evita bucles).
- Si prefieres controlar commits tú mismo, puedes usar `actions/checkout@v4` con `persist-credentials: true` y ejecutar `git add`/`git commit`/`git push` con `GITHUB_TOKEN`.

---

### Ejemplo alternativo: Push a Docker Hub

Si quieres usar Docker Hub en vez de GHCR, añade secrets.DOCKERHUB_USERNAME y secrets.DOCKERHUB_TOKEN en Settings > Secrets:

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

---

### Buenas prácticas y consideraciones
- Secrets: guarda tokens en GitHub > Repository > Settings > Secrets and variables > Actions.
- Evitar bucles: los commits automáticos con GITHUB_TOKEN no suelen re-disparar workflows; aun así, verifica la configuración para evitar ejecuciones infinitas.
- PR vs main: para repositorios colaborativos, es mejor que la pipeline haga build en PRs y que push a registro y commit a `main` se hagan solo cuando la PR se mergea.
- Tagging: crea tags semánticos (v1.0.0) para versiones y publica imágenes con tags coincidentes.
- Scan y seguridad: añade un job para escanear la imagen (trivy, ghcr vulnerability scanning).
- Limpieza: no incluyas secretos en los artefactos públicos.

---

### ¿Qué modifica la Action en la web?
En el ejemplo el workflow:
- Inserta en index.html (o crea uno si no existe) un bloque con la información del build: timestamp, commit SHA e imagen generada.
- Hace commit de `index.html` a la rama `main`, provocando la publicación del nuevo contenido en GitHub Pages (si Pages está configurado sobre `main`).
- De esta forma la web muestra automáticamente información del último build y del artefacto Docker publicado.