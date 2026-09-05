# API de Equipos

Ejemplo básico de una API REST construida con **FastAPI** siguiendo una arquitectura por capas (rutas → servicios → schemas). El tema del recurso es **equipos**.

## Stack

- Python 3.12
- [FastAPI](https://fastapi.tiangolo.com/)
- Uvicorn (servidor ASGI)
- Pydantic (validación de datos)

## Estructura del proyecto

```
api_minimal/
└── app/
    ├── main.py                    # Instancia de FastAPI y registro de routers
    ├── routers/
    │   └── equipos.py             # Definición de los endpoints (rutas)
    ├── services/
    │   └── equipo_service.py      # Lógica de negocio (datos en memoria)
    └── schemas/
        └── equipo.py              # Modelos Pydantic (EquipoCreate, EquipoResponse)
```

## Requisitos e instalación

1. (Opcional pero recomendado) Crear y activar un entorno virtual:

   ```bash
   python -m venv venv
   source venv/bin/activate      # Linux / macOS
   # venv\Scripts\activate       # Windows
   ```

2. Instalar las dependencias:

   ```bash
   pip install -r requirements.txt
   ```

## Ejecución

Desde la raíz del proyecto (`api_minimal/`):

```bash
uvicorn app.main:app --reload
```

La API quedará disponible en `http://127.0.0.1:8000`.

## Dónde probar la API

Con la app corriendo, abre en el navegador:

| Interfaz | URL |
|----------|-----|
| **Swagger UI** (recomendada) | http://127.0.0.1:8000/docs |
| **ReDoc** | http://127.0.0.1:8000/redoc |
| Endpoint raíz | http://127.0.0.1:8000/ |

Swagger te permite probar cada endpoint de forma interactiva sin escribir código.

## Endpoints ya implementados

| Método | Ruta | Descripción |
|--------|------|-------------|
| `GET` | `/equipos/` | Lista todos los equipos |
| `GET` | `/equipos/{equipo_id}` | Obtiene un equipo por su id |
| `POST` | `/equipos/` | Crea un nuevo equipo |

Modelo de un equipo:

```json
{
  "id": 1,
  "nombre": "Barcelona",
  "categoria": "Futbol",
  "disponible": true
}
```

Para crear un equipo solo se envían `nombre` (3–80 caracteres) y `categoria` (3–50 caracteres); el `id` y `disponible` los genera la API.

---

## 🎯 El reto: completar el CRUD

La API ya permite **crear** y **leer** equipos. Tu tarea es implementar los dos endpoints que faltan del CRUD:

### 1. Actualizar un equipo — `PUT /equipos/{equipo_id}`

- Recibe el `equipo_id` (int) y en el cuerpo los datos a actualizar (`EquipoCreate`: `nombre`, `categoria`).
- Actualiza el equipo con ese id y devuelve el equipo actualizado.
- Si no existe un equipo con ese id, devuelve **HTTP 404** ("Equipo no encontrado").
- Si el nuevo `nombre` ya pertenece a **otro** equipo, devuelve **HTTP 400** (nombre duplicado).

### 2. Eliminar un equipo — `DELETE /equipos/{equipo_id}`

- Recibe el `equipo_id` (int).
- Elimina el equipo con ese id y lo devuelve como respuesta.
- Si no existe un equipo con ese id, devuelve **HTTP 404** ("Equipo no encontrado").

### Requisitos de implementación

- Sigue la **arquitectura por capas** del proyecto:
  - Escribe la lógica en `app/services/equipo_service.py` (p. ej. `actualizar_equipo(equipo_id, datos)` y `eliminar_equipo(equipo_id)`).
  - Expón los endpoints en `app/routers/equipos.py` (declarados con `@router.put(...)` y `@router.delete(...)`).
- Reutiliza `app/schemas/equipo.py` (`EquipoCreate` y `EquipoResponse`) para los modelos de entrada/salida.
- Investiga cómo lo hacen las funciones ya existentes (`crear_equipo`, `obtener_equipo`) para replicar el estilo y el manejo de errores con `HTTPException`.

### Pistas

- El listado de equipos vive en memoria en la variable `_equipos` de `equipo_service.py`, y cada equipo es un `dict` con claves `id`, `nombre`, `categoria`, `disponible`.
- Para actualizar/eliminar puedes recorrer la lista y comparar `e["id"] == equipo_id`, igual que hace `obtener_equipo`.
- En `actualizar` recuerda comprobar que el nuevo nombre no esté duplicado **salvo en el propio equipo** que se está editando.

---

## 📤 Entrega y evidencia

Completar los dos endpoints y probar que la API queda con el **CRUD completo** (crear, leer, actualizar, borrar). La entrega debe incluir **dos partes**:

### Parte 1 — Captura de pantalla del Swagger UI

Con la app corriendo, abre **Swagger UI** (`http://127.0.0.1:8000/docs`) y prueba los endpoints. Sube una **captura de pantalla** donde se vean los endpoints y al menos una prueba de cada operación del CRUD (típicamente: crear, listar, actualizar y eliminar un equipo, mostrando las respuestas con código 200/201).

### Parte 2 — Explicación escrita

Debes demostrar que **entiendes lo que construiste**, explicando con tus propias palabras cómo implementaste cada endpoint. Responde las siguientes preguntas:

**Sobre `PUT /equipos/{equipo_id}` (actualizar):**
1. ¿Qué recibe el endpoint y qué devuelve?
2. ¿Cómo localiza el equipo a actualizar dentro de la lista (por `id`)?
3. ¿Cómo valida el nombre duplicado excluyendo al propio equipo que se edita? ¿Por qué es importante esa exclusión?
4. ¿Cómo maneja el caso de "equipo no encontrado"? ¿Qué código HTTP devuelve y por qué?

**Sobre `DELETE /equipos/{equipo_id}` (eliminar):**
5. ¿Qué recibe el endpoint y qué devuelve?
6. ¿Cómo elimina el equipo de la lista?
7. ¿Cómo maneja el caso de "equipo no encontrado"? ¿Qué código HTTP devuelve y por qué?

**Sobre la arquitectura (responde aunque el código "ya te funcionara"):**
8. ¿Por qué la lógica va en `equipo_service.py` y los endpoints en `equipos.py`? ¿Qué ventaja tiene separar estas capas?
9. ¿Qué papel juegan `EquipoCreate` y `EquipoResponse` de `schemas/equipo.py`?
10. ¿Qué es `HTTPException` y por qué se usa para reflejar errores?

> Consejo: redacta como si le explicaras tu solución a un compañero. Respuestas tipo "copié y pegó" o sin justificación no demuestran comprensión. La captura de Swagger debe respaldar el comportamiento que describes.

---

## 🍴 Cómo entregar (flujo con GitHub)

Vas a trabajar sobre **una copia propia** del proyecto usando *fork* y entregarás el **link de tu fork**.

### 1. Haz un *fork* del repositorio

Entra al repositorio original en GitHub y pulsa el botón **Fork** (arriba a la derecha). Esto crea una **copia del proyecto en tu cuenta** de GitHub.

### 2. Clona tu fork en tu máquina

Abre la terminal y clona **tu fork** (usa el enlace de tu copia, no el original):

```bash
git clone https://github.com/TU_USUARIO/api_minimal.git
cd api_minimal
```

### 3. (Recomendado) Crea una rama para tu solución

```bash
git checkout -b solucion
```

### 4. Completa el reto

Implementa los endpoints faltantes (`PUT` y `DELETE`) siguiendo las instrucciones de este README y prueba la API.

### 5. Confirma y sube tus cambios a tu fork

```bash
git add .
git commit -m "Completo el CRUD de la API de equipos"
git push origin solucion
```

### 6. Entrega el link de tu fork

Copia el enlace de tu repositorio fork (por ejemplo `https://github.com/TU_USUARIO/api_minimal`) y **envíalo como tu entrega**, junto con la **captura de Swagger** y la **explicación escrita** de las partes 1 y 2. Asegúrate de que tu fork contenga tus cambios subidos antes de enviarlo.
