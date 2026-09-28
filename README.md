# Egg Vision

Egg Vision es un proyecto de visión artificial que utiliza un modelo YOLO para analizar huevos mediante una cámara en tiempo real.

El sistema captura imágenes de la cámara, las envía a un backend desarrollado con FastAPI y utiliza un modelo de inteligencia artificial entrenado para realizar las detecciones.

Además, el proyecto incluye la posibilidad de comunicarse con un ESP32 para controlar un motor que puede ser utilizado posteriormente en el sistema físico de clasificación de huevos.

---

## Objetivo del proyecto

El objetivo es desarrollar un sistema capaz de utilizar visión artificial para reconocer el estado de los huevos y apoyar su clasificación automática.

El proyecto combina:

- Inteligencia artificial.
- Visión por computador.
- Desarrollo web.
- API REST.
- ESP32.
- Automatización mediante motores.

---

## ¿Cómo funciona?

El funcionamiento general es el siguiente:

```text
Cámara
   ↓
Página web
   ↓
Captura de frames
   ↓
Backend FastAPI
   ↓
Modelo YOLO
   ↓
Detección
   ↓
Resultado mostrado en pantalla
   ↓
ESP32 / Motor
```

### Paso a paso

1. El usuario inicia la cámara desde la página web.
2. La aplicación obtiene imágenes de la cámara automáticamente.
3. Cada imagen se envía al backend.
4. FastAPI recibe la imagen.
5. El modelo YOLO analiza la imagen.
6. El backend devuelve las detecciones encontradas.
7. La página dibuja las cajas de detección sobre el video.
8. El sistema también puede enviar órdenes a un ESP32 para controlar un motor.

Los frames utilizados para la detección no se guardan como fotografías.

---

## Inteligencia artificial

El proyecto utiliza YOLO de Ultralytics para realizar las detecciones.

El modelo entrenado se encuentra en:

```text
models/best.pt
```

El backend carga este modelo automáticamente cuando necesita realizar una predicción.

Por defecto, se utiliza un nivel mínimo de confianza de:

```text
0.35
```

Este valor puede modificarse mediante una variable de entorno.

---

## Estructura del proyecto

```text
Huevos/
│
├── app/
│   ├── index.html
│   ├── styles.css
│   ├── app.js
│   └── README.md
│
├── backend/
│   ├── backend.py
│   ├── requirements.txt
│   └── README.md
│
├── models/
│   └── best.pt
│
└── README.md
```

### Carpeta `app`

Contiene la aplicación web.

Se encarga de:

- Acceder a la cámara.
- Mostrar el video en vivo.
- Enviar imágenes al backend.
- Dibujar las detecciones.
- Mostrar la confianza del modelo.
- Mostrar el número de detecciones.
- Consultar el estado del backend.
- Enviar órdenes para encender o apagar el motor.

### Carpeta `backend`

Contiene la API desarrollada con FastAPI.

Se encarga de:

- Recibir las imágenes.
- Cargar el modelo YOLO.
- Ejecutar las predicciones.
- Devolver las detecciones.
- Comunicarse con el ESP32.
- Informar el estado general del sistema.

### Carpeta `models`

Contiene el modelo de inteligencia artificial entrenado:

```text
best.pt
```

---

## Tecnologías utilizadas

| Tecnología | Uso |
|---|---|
| Python | Backend e inteligencia artificial |
| FastAPI | Creación de la API |
| Uvicorn | Servidor del backend |
| YOLO / Ultralytics | Detección de objetos |
| OpenCV | Procesamiento de imágenes |
| NumPy | Manejo de imágenes y matrices |
| HTML | Interfaz web |
| CSS | Diseño de la página |
| JavaScript | Cámara, peticiones y detecciones |
| ESP32 | Control del sistema físico |
| HTTP | Comunicación entre los componentes |
| AWS | Despliegue del sistema |

---

## Ejecutar el proyecto

### 1. Descargar el repositorio

```bash
git clone URL_DEL_REPOSITORIO
```

Después entra a la carpeta:

```bash
cd Huevos
```

---

## Backend

### 2. Crear un entorno virtual

Es recomendable utilizar un entorno virtual de Python.

En Windows:

```bash
python -m venv venv
```

Activarlo:

```bash
venv\Scripts\activate
```

En Linux:

```bash
python3 -m venv venv
```

Activarlo:

```bash
source venv/bin/activate
```

---

### 3. Instalar las dependencias

Entra a la carpeta del backend:

```bash
cd backend
```

Instala las librerías:

```bash
pip install -r requirements.txt
```

Las principales dependencias son:

```text
FastAPI
Uvicorn
Ultralytics
OpenCV
NumPy
python-multipart
```

---

### 4. Iniciar el backend

Desde la carpeta `backend` ejecuta:

```bash
uvicorn backend:app --reload --host 0.0.0.0 --port 8000
```

El servidor estará disponible normalmente en:

```text
http://localhost:8000
```

---

## Frontend

Abre otra terminal y entra a:

```bash
cd app
```

Ejecuta:

```bash
python -m http.server 5173
```

Después abre en el navegador:

```text
http://localhost:5173
```

El navegador solicitará permiso para utilizar la cámara.

Selecciona Permitir.

---

## Detección en vivo

Una vez iniciada la aplicación:

1. Presiona el botón para iniciar el video.
2. La cámara comenzará a funcionar.
3. Los frames serán enviados al backend.
4. YOLO analizará cada imagen.
5. Las detecciones aparecerán sobre el video.

La interfaz también muestra:

- Número de detecciones.
- Confianza máxima.
- Estado del backend.
- Estado del modelo.
- Velocidad aproximada de análisis.

---

## Endpoints de la API

### Estado del sistema

```http
GET /health
```

Permite saber:

- Si el backend funciona.
- Si existe el modelo.
- Si el modelo está cargado.
- Si el ESP32 está configurado.
- El estado actual del motor.

---

### Analizar una imagen

```http
POST /predict-frame
```

Recibe una imagen y devuelve las detecciones realizadas por YOLO.

Ejemplo simplificado de respuesta:

```json
{
  "width": 1280,
  "height": 720,
  "detections": [
    {
      "class_id": 0,
      "class_name": "clase_detectada",
      "confidence": 0.95,
      "box": {
        "x1": 100,
        "y1": 150,
        "x2": 300,
        "y2": 400
      }
    }
  ]
}
```

---

### Controlar el motor

```http
POST /motor
```

Para encender:

```json
{
  "enabled": true
}
```

Para apagar:

```json
{
  "enabled": false
}
```

---

## Integración con ESP32

El backend permite comunicarse con un ESP32 mediante HTTP.

El ESP32 debe tener disponible un endpoint para recibir las órdenes del motor.

Ejemplo:

```http
POST /motor
```

Para encender:

```json
{
  "enabled": true
}
```

Para apagar:

```json
{
  "enabled": false
}
```

Si no se configura ningún ESP32, el backend puede continuar funcionando en modo simulación.

Esto permite probar la aplicación sin tener conectado el sistema físico.

---

## Variables de entorno

El proyecto permite modificar algunas configuraciones sin cambiar directamente el código.

### Ruta del modelo

```text
EGG_MODEL
```

Permite indicar otra ubicación para el archivo `.pt`.

Por defecto:

```text
models/best.pt
```

### Confianza de YOLO

```text
YOLO_CONFIDENCE=0.35
```

Controla la confianza mínima necesaria para aceptar una detección.

### ESP32

```text
ESP32_URL=http://IP_DEL_ESP32
```

Indica dónde se encuentra el ESP32.

### CORS

```text
CORS_ORIGINS=http://localhost:5173
```

Permite indicar qué páginas pueden comunicarse con la API.

---

## Despliegue

El proyecto también puede funcionar en un servidor remoto utilizando AWS.

La arquitectura puede ser:

```text
Usuario
   ↓
Navegador
   ↓
Frontend
   ↓
HTTPS
   ↓
Servidor AWS
   ↓
FastAPI
   ↓
YOLO
   ↓
ESP32
```

Esto permite que el procesamiento de inteligencia artificial se realice en un servidor y no necesariamente en el computador desde donde se utiliza la cámara.

---

## Seguridad

Los archivos que contienen claves privadas, credenciales o configuraciones sensibles nunca deben subirse a GitHub.

Ejemplos:

```text
*.pem
.env
venv/
__pycache__/
```

Se recomienda agregar un archivo `.gitignore` con:

```gitignore
# Claves privadas
*.pem

# Variables de entorno
.env

# Python
__pycache__/
*.pyc

# Entorno virtual
venv/
.venv/

# Sistema operativo
.DS_Store
Thumbs.db
```

---

## Mejoras futuras

Algunas mejoras que se pueden realizar en el proyecto son:

- Integrar completamente el ESP32.
- Automatizar el mecanismo físico de clasificación.
- Controlar servomotores según la detección realizada.
- Llevar un conteo de huevos clasificados.
- Guardar estadísticas de clasificación.
- Crear un historial de resultados.
- Mejorar la velocidad de procesamiento.
- Ejecutar el sistema desde una Raspberry Pi.
- Integrar una cámara dedicada.
- Integrar el sistema con un mecanismo de transporte.
- Crear un sistema completo de separación automática de huevos.

---

## Estado del proyecto

Actualmente el proyecto cuenta con:

- Modelo YOLO entrenado.
- Backend con FastAPI.
- Detección mediante imágenes.
- Detección utilizando video en vivo.
- Interfaz web.
- Visualización de cajas de detección.
- Visualización de confianza.
- Comprobación del estado del backend.
- Endpoint para controlar un motor.
- Modo de simulación del ESP32.
- Integración del mecanismo físico todavía en desarrollo.

---

## Proyecto académico

Este proyecto fue desarrollado con fines académicos como aplicación de conceptos de:

- Inteligencia artificial.
- Visión artificial.
- Internet de las Cosas.
- Desarrollo de APIs.
- Programación web.
- Sistemas embebidos.
- Automatización.

El objetivo final es integrar el software de visión artificial con un prototipo físico capaz de ayudar a automatizar el proceso de clasificación de huevos.
