# Tienda online - API con FastAPI

**Autor:** Alejandro Castro Rodríguez

## Descripción

API REST de una tienda online desarrollada en Python con FastAPI. Permite gestionar usuarios (clientes y administradores), productos (electrónica y ropa) y pedidos, comprobando que el usuario sea cliente y que haya stock suficiente antes de crear un pedido.

El proyecto incluye además:
- Un cliente en Python (`cliente_python/cliente.py`) que consume la API mediante peticiones HTTP.
- Un frontend hecho con Streamlit (`frontend/app.py`).
- Un `dockerfile` para ejecutar la API en un contenedor.

## Estructura

- `src/main.py`: endpoints de la API.
- `src/esquemas.py`: modelos de entrada y salida con Pydantic.
- `src/models/`: clases Usuario, Producto y Pedido.
- `src/services/`: lógica de la tienda y excepciones.
- `src/cliente_python/`: cliente que consume la API.
- `src/frontend/`: interfaz con Streamlit.
- `dockerfile`, `.dockerignore` y `requirements.txt`: configuración de Docker y dependencias.

## Endpoints principales

- `POST /usuarios`, `GET /usuarios`, `GET /usuarios/{usuario_id}`
- `POST /productos`, `GET /productos`, `GET /productos/{producto_id}`, `DELETE /productos/{producto_id}`
- `POST /pedidos`, `GET /usuarios/{cliente_id}/pedidos`

## Ejecución en local

```
pip install -r requirements.txt
cd src
uvicorn main:app --reload
```

## Docker

### Construir la imagen

```
docker build -t TU_USUARIO/tienda-api:1.0 .
```

### Ejecutar el contenedor

```
docker run -d -p 8000:8000 --name tienda TU_USUARIO/tienda-api:1.0
```

- `-d` lo ejecuta en segundo plano.
- `-p 8000:8000` conecta el puerto 8000 de tu ordenador con el del contenedor.
- Para pararlo: `docker stop tienda`. Para ver lo que imprime: `docker logs tienda`.

### Variables de entorno

La API no necesita ninguna. El cliente de Python usa `BASE_URL` para saber dónde está la API (por defecto `http://localhost:8000`).

### Pruebas

Al arrancar, `docker logs tienda` debe mostrar `Uvicorn running on http://0.0.0.0:8000`. Después:

- Abre http://localhost:8000/docs y prueba los endpoints desde Swagger.
- `GET http://localhost:8000/productos` devuelve la lista de productos (vacía al principio).
- Con la API en marcha, `python src/cliente_python/cliente.py` crea usuarios, productos y un pedido de ejemplo.

### Descargar la imagen de Docker Hub

```
docker pull TU_USUARIO/tienda-api:1.0
docker run -d -p 8000:8000 TU_USUARIO/tienda-api:1.0
```