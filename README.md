# Tienda online - API con FastAPI

**Autor:** Alejandro Castro Rodríguez

## Descripción

API REST de una tienda online desarrollada en Python con FastAPI. Permite gestionar usuarios (clientes y administradores), productos (electrónica y ropa) y pedidos, comprobando que el usuario sea cliente y que haya stock suficiente antes de crear un pedido.

El proyecto incluye además:
- Un cliente en Python (`cliente_python/cliente.py`) que consume la API mediante peticiones HTTP.
- Un frontend hecho con Streamlit (`frontend/app.py`).
- Un `dockerfile` para ejecutar la API en un contenedor.

## Estructura

- `main.py`: endpoints de la API.
- `esquemas.py`: modelos de entrada y salida con Pydantic.
- `models/`: clases Usuario, Producto y Pedido.
- `services/`: lógica de la tienda y excepciones.

## Endpoints principales

- `POST /usuarios`, `GET /usuarios`, `GET /usuarios/{usuario_id}`
- `POST /productos`, `GET /productos`, `GET /productos/{producto_id}`, `DELETE /productos/{producto_id}`
- `POST /pedidos`, `GET /usuarios/{cliente_id}/pedidos`

## Cómo ejecutarlo

En local:

    pip install -r requirements.txt
    uvicorn main:app --reload

Con Docker:

    docker build -t tienda-api .
    docker run -p 8000:8000 tienda-api

La documentación interactiva queda en http://localhost:8000/docs
