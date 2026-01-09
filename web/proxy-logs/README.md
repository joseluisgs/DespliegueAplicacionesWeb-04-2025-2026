# LEEME

no los levantes todos

Hazlo así, para fluente no falle

```sh
docker compose up -d fluentd
docker compose up -d elasticsearch kibana
docker compose up -d nginx apache proxy
```


# **Tutorial: Monitorización de contenedores con ELK y Fluentd**

**Objetivo:** Configurar un stack ELK con Fluentd para recoger logs de contenedores Docker y visualizarlos en Kibana.

**Pre-requisitos:**

* Docker y Docker Compose instalados.
* Contenedores Nginx, Apache y Proxy corriendo.
* Stack ELK (Elasticsearch + Kibana + Fluentd) levantado en Docker Compose.

---

## **1️⃣ Verificar que los contenedores están corriendo**

Ejecuta:

```bash
docker ps
```

Debes ver algo como:

```
CONTAINER ID   NAMES
xxxxxx         proxy-logs-nginx-1
xxxxxx         proxy-logs-apache-1
xxxxxx         proxy-logs-proxy-1
xxxxxx         fluentd
xxxxxx         elasticsearch
xxxxxx         kibana
```

* Si algún contenedor falla, revisa sus logs:

```bash
docker logs -f <nombre_contenedor>
```

---

## **2️⃣ Generar tráfico para que se creen logs**

Los contenedores recién lanzados aún no han generado logs, así que hacemos algunas solicitudes de prueba:

```bash
# Nginx
docker exec -it proxy-logs-nginx-1 curl http://localhost/

# Apache
docker exec -it proxy-logs-apache-1 curl http://localhost/

# Proxy (si quieres probar)
docker exec -it proxy-logs-proxy-1 curl http://localhost/
```

Esto genera registros de acceso (`access.log`) que Fluentd podrá recoger.

---

## **3️⃣ Comprobar que Fluentd está funcionando**

```bash
docker logs -f fluentd
```

Debes ver mensajes tipo:

```
fluent.info: {"message":"starting fluentd worker"}
fluent.info: {"message":"fluentd worker is now running"}
```

* Fluentd recoge automáticamente logs de los contenedores que tengan `logging.driver: fluentd` y los envía a Elasticsearch.

---

## **4️⃣ Verificar índices en Elasticsearch**

Para confirmar que los logs llegaron a Elasticsearch:

```bash
curl http://localhost:9200/_cat/indices?v
```

Ejemplo de salida:

```
health status index                   docs.count docs.deleted store.size
green  open   docker-logs-2025.11.05  10         0            50kb
```

* Los índices deberían empezar por `docker-logs-*` (según tu configuración de Fluentd).

---

## **5️⃣ Crear un Data View en Kibana**

1. Accede a Kibana en el navegador:

```
http://localhost:5601
```

2. Ve a **Stack Management → Data Views**.

3. Haz clic en **Create data view**.

4. Rellena los campos:

| Campo           | Valor           |
| --------------- | --------------- |
| Index pattern   | `docker-logs-*` |
| Name            | `Docker Logs`   |
| Timestamp field | `@timestamp`    |

5. Haz clic en **Create data view**.

---

## **6️⃣ Explorar los logs en Discover**

1. Ve a **Discover** en Kibana (menú lateral bajo Analytics).
2. Selecciona tu **Data View**: `docker-logs-*`.
3. Deberías ver todos los logs de tus contenedores.

---

## **7️⃣ Filtrar por servicio**

Si quieres ver solo Nginx o Apache:

1. Haz clic en **Add filter** arriba de la tabla.
2. Configura el filtro:

| Campo    | Valor                         |
| -------- | ----------------------------- |
| Field    | `service_name`                |
| Operator | `is`                          |
| Value    | `nginx` (o `apache`, `proxy`) |

3. Aplica el filtro. Ahora verás solo los logs de ese servicio.

---

## **8️⃣ Crear dashboards (opcional)**

1. Ve a **Dashboard → Create dashboard**.
2. Agrega visualizaciones desde **Visualize Library**.
3. Guarda el dashboard con un nombre, por ejemplo: `Logs de Servicios`.

* Así podrás ver logs en tiempo real de todos los servicios en una sola pantalla.

---

## **9️⃣ Consejos finales**

* Fluentd debe correr **antes de los servicios** que envían logs, pero con la opción de logging driver `json-file` también puedes generar logs mientras Fluentd se levanta.
* Si los índices no aparecen en Elasticsearch, asegúrate de:

  * Que los contenedores están generando logs.
  * Que Fluentd está activo y correctamente configurado.
  * Que el plugin de Elasticsearch funciona (usa la imagen `fluent/fluentd:edge-debian-1.0` para evitar errores de plugins).

