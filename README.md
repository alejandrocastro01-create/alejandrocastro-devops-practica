# Tienda online - API con FastAPI

**Autor:** Alejandro Castro Rodríguez

## Descripción

API REST de una tienda online desarrollada en Python con FastAPI. Permite gestionar usuarios (clientes y administradores), productos (electrónica y ropa) y pedidos, comprobando que el usuario sea cliente y que haya stock suficiente antes de crear un pedido.

El proyecto incluye además:
- Un cliente en Python (`src/cliente_python/cliente.py`) que consume la API mediante peticiones HTTP.
- Un frontend hecho con Streamlit (`src/frontend/app.py`).
- Un `dockerfile` para ejecutar la API en un contenedor, un `dockerfile.frontend` para el frontend y un `docker-compose.yml` que levanta los dos.

## Estructura

- `src/main.py`: endpoints de la API.
- `src/esquemas.py`: modelos de entrada y salida con Pydantic.
- `src/models/`: clases Usuario, Producto y Pedido.
- `src/services/`: lógica de la tienda y excepciones.
- `src/cliente_python/`: cliente que consume la API.
- `src/frontend/`: interfaz con Streamlit.
- `dockerfile` y `requirements.txt`: imagen y dependencias de la API.
- `dockerfile.frontend` y `requirements-frontend.txt`: imagen y dependencias del frontend.
- `docker-compose.yml`: levanta la API y el frontend juntos.
- `.dockerignore`: ficheros que no se copian al construir las imágenes.

## Endpoints principales

- `POST /usuarios`, `GET /usuarios`, `GET /usuarios/{usuario_id}`
- `POST /productos`, `GET /productos`, `GET /productos/{producto_id}`, `DELETE /productos/{producto_id}`
- `POST /pedidos`, `GET /usuarios/{cliente_id}/pedidos`

## Ejecución en local

```
pip install -r requirements.txt -r requirements-frontend.txt
cd src
uvicorn main:app --reload
```

En otra terminal, desde la raíz del repositorio, el frontend:

```
streamlit run src/frontend/app.py
```

## Docker

### Construir la imagen

```
docker build -t alejandrocastrorodriguez/tienda-api:1.1 .
```

### Ejecutar el contenedor

```
docker run -d -p 8000:8000 --name tienda alejandrocastrorodriguez/tienda-api:1.1
```

- `-d` lo ejecuta en segundo plano.
- `-p 8000:8000` conecta el puerto 8000 de tu ordenador con el del contenedor.
- Para pararlo: `docker stop tienda`. Para ver lo que imprime: `docker logs tienda`.

### Variables de entorno

La API no necesita ninguna. El cliente de Python usa `BASE_URL` y el frontend usa `API_URL` para saber dónde está la API (por defecto `http://localhost:8000` y `http://127.0.0.1:8000`).

### Pruebas

Al arrancar, `docker logs tienda` debe mostrar `Uvicorn running on http://0.0.0.0:8000`. Después:

- Abre http://localhost:8000/docs y prueba los endpoints desde Swagger.
- `GET http://localhost:8000/productos` devuelve la lista de productos (vacía al principio).
- Con la API en marcha, `python src/cliente_python/cliente.py` crea usuarios, productos y un pedido de ejemplo.

### Descargar la imagen de Docker Hub

```
docker pull alejandrocastrorodriguez/tienda-api:1.1
docker run -d -p 8000:8000 alejandrocastrorodriguez/tienda-api:1.1
```

La versión `1.0` es la anterior: incluía también las librerías del frontend (streamlit) y ocupaba unos 835 MB. En la `1.1` la API solo instala sus dependencias y ocupa unos 219 MB.

## Docker Compose (API + frontend)

`docker-compose.yml` define dos servicios:

| Servicio | Imagen | Puerto |
|---|---|---|
| `api` | `alejandrocastrorodriguez/tienda-api:1.1` | 8000 |
| `frontend` | `alejandrocastrorodriguez/tienda-frontend:1.0` | 8501 |

El frontend recibe la variable `API_URL=http://api:8000`: dentro de la red de compose cada servicio se llama por su nombre, así que el frontend encuentra la API en `http://api:8000`.

### Construir y levantar

```
docker compose up -d --build
```

### Pruebas

- `docker compose ps` muestra los dos contenedores en estado `Up`.
- http://localhost:8000/ devuelve `{"mensaje": "La tienda online funciona"}`.
- http://localhost:8501 abre el frontend. Arriba aparece `http://api:8000` como dirección de la API y desde ahí se pueden crear y listar productos, usuarios y pedidos.
- `docker compose exec api whoami` y `docker compose exec frontend whoami` responden `appuser` (no se ejecutan como root).
- `docker compose logs` muestra lo que imprime cada contenedor.

### Parar

```
docker compose down
```

### Descargar las imágenes de Docker Hub

```
docker pull alejandrocastrorodriguez/tienda-api:1.1
docker pull alejandrocastrorodriguez/tienda-frontend:1.0
docker compose up -d
```
