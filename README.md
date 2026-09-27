# Docker Containers - Open-WA & Stirling-PDF

Este repositorio contiene la configuración mediante Docker y Docker Compose para desplegar y administrar dos servicios independientes:

1. **Stirling-PDF**: Plataforma web de código abierto para visualización, edición, conversión y manipulación de archivos PDF.
2. **Open-WA (wa-automate)**: Servidor de API para automatización de WhatsApp con soporte para sesiones persistentes.

---

## 📋 Estructura del Proyecto

```text
.
├── docker-compose.yml       # Definición de los servicios de Docker
├── .env.example             # Plantilla de variables de entorno
├── .env                     # Variables de entorno activas (ignorado en git)
├── .gitignore               # Configuración de exclusiones para git
├── README.md                # Documentación del proyecto
├── openwa/
│   └── sessions/            # Almacenamiento persistente de sesión y caché de WhatsApp
└── stirling/
    ├── configs/             # Configuraciones y base de datos interna de Stirling
    ├── customFiles/         # Personalización de marca o archivos estáticos
    ├── logs/                # Registros de eventos
    ├── pipeline/            # Configuraciones de flujos de trabajo automatizados
    └── tessdata/            # Archivos de idioma para reconocimiento óptico (OCR)
```

---

## ⚙️ Configuración (.env)

Las variables principales se encuentran en el archivo `.env`. Puedes modificarlas según tus necesidades:

```bash
# Stirling-PDF
STIRLING_PORT=8080            # Puerto expuesto en el host
STIRLING_DEFAULT_LOCALE=es-ES # Idioma por defecto
STIRLING_ENABLE_LOGIN=false   # Habilitar inicio de sesión
STIRLING_MAX_FILE_SIZE=2GB    # Límite máximo de carga

# Open-WA
OPENWA_PORT=8082              # Puerto expuesto en el host
OPENWA_API_KEY=tu_clave       # Token de seguridad para la API
OPENWA_SESSION_ID=sesion-wa   # Nombre de la sesión activa
```

---

## 🚀 Inicio de los Servicios

> **Nota para macOS (Apple Silicon M1/M2/M3/M4):**  
> El servicio `openwa` incluye la directiva `platform: linux/amd64` en `docker-compose.yml` para garantizar la compatibilidad con el motor de Chromium mediante Rosetta 2 en Docker Desktop.

### Iniciar ambos servicios en segundo plano:
```bash
docker compose up -d
```

### Iniciar un servicio individualmente:
* **Solo Stirling-PDF**:
  ```bash
  docker compose up -d stirling-pdf
  ```
* **Solo Open-WA**:
  ```bash
  docker compose up -d openwa
  ```

---

## 🌐 Acceso a las Aplicaciones

Una vez iniciados los contenedores:

| Servicio | URL local | Puerto Host por defecto |
| :--- | :--- | :--- |
| **Stirling-PDF** | [http://localhost:8080](http://localhost:8080) | `8080` |
| **Open-WA Server** | [http://localhost:8082](http://localhost:8082) | `8082` |

---

## 🔍 Comandos Útiles

* **Ver estado de los contenedores:**
  ```bash
  docker compose ps
  ```

* **Ver registros (logs) en tiempo real:**
  ```bash
  # Ambos servicios
  docker compose logs -f

  # Solo Open-WA (útil para ver el código QR de autenticación)
  docker compose logs -f openwa

  # Solo Stirling-PDF
  docker compose logs -f stirling-pdf
  ```

* **Detener los servicios:**
  ```bash
  docker compose down
  ```

* **Reiniciar un contenedor específico:**
  ```bash
  docker compose restart openwa
  docker compose restart stirling-pdf
  ```
