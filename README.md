# Docker Containers - Servicios Independientes

Este repositorio contiene la configuración en Docker Compose para desplegar y administrar servicios independientes, cada uno en su propio directorio:

1. **`openwa/`**: Servidor y API para automatización de WhatsApp con soporte para sesiones persistentes.
2. **`stirling/`**: Plataforma web de código abierto para visualización, edición, conversión y manipulación de archivos PDF.
3. **`n8n/`**: Plataforma de automatización de flujos de trabajo (workflow automation) conectada a PostgreSQL.

---

## 📁 Estructura del Repositorio

```text
.
├── openwa/
│   ├── docker-compose.yml   # Definición del servicio Open-WA
│   ├── .env.example         # Plantilla de variables de entorno para Open-WA
│   ├── .env                 # Variables de entorno activas (ignorado en git)
│   └── sessions/            # Almacenamiento persistente de sesión y caché de WhatsApp
│
├── stirling/
│   ├── docker-compose.yml   # Definición del servicio Stirling-PDF
│   ├── .env.example         # Plantilla de variables de entorno para Stirling-PDF
│   ├── .env                 # Variables de entorno activas (ignorado en git)
│   ├── configs/             # Configuraciones y base de datos interna de Stirling
│   ├── customFiles/         # Personalización de marca o archivos estáticos
│   ├── logs/                # Registros de eventos
│   ├── pipeline/            # Configuraciones de flujos de trabajo automatizados
│   └── tessdata/            # Archivos de idioma para reconocimiento óptico (OCR)
│
├── n8n/
│   ├── docker-compose.yml   # Definición del servicio n8n
│   ├── .env.example         # Plantilla de variables de entorno (n8n + PostgreSQL)
│   └── .env                 # Variables de entorno activas (ignorado en git)
│
├── .gitignore               # Configuración de exclusiones de git
└── README.md                # Documentación del proyecto
```

---

## 📱 1. Servicio Open-WA

### Configuración (`openwa/.env`)
```ini
OPENWA_PORT=8082              # Puerto en el host
OPENWA_API_KEY=tu_clave       # Clave secreta que tú defines para proteger la API
OPENWA_SESSION_ID=sesion-wa   # Nombre identificador de la sesión
```

### Iniciar el servicio
```bash
cd openwa
docker compose up -d
```

* **URL local:** [http://localhost:8082](http://localhost:8082)
* **Ver código QR / Logs:**
  ```bash
  docker compose logs -f
  ```
* **Detener el servicio:**
  ```bash
  docker compose down
  ```

---

## 📄 2. Servicio Stirling-PDF

### Configuración (`stirling/.env`)
```ini
STIRLING_PORT=8080            # Puerto en el host
STIRLING_DEFAULT_LOCALE=es-ES # Idioma por defecto
STIRLING_ENABLE_LOGIN=false   # Requerir autenticación
STIRLING_MAX_FILE_SIZE=2GB    # Límite máximo de carga
```

### Iniciar el servicio
```bash
cd stirling
docker compose up -d
```

* **URL local:** [http://localhost:8080](http://localhost:8080)
* **Ver Logs:**
  ```bash
  docker compose logs -f
  ```
* **Detener el servicio:**
  ```bash
  docker compose down
  ```

---

## ⚡ 3. Servicio n8n (con PostgreSQL)

El servicio n8n está configurado para conectarse a una base de datos PostgreSQL estándar (`postgres:latest`). Utiliza un volumen con nombre (`n8n_data`) administrado internamente por Docker para almacenar credenciales y ejecuciones sin crear carpetas locales.

### Conexión a tu contenedor `postgres:latest`
* Si ejecutas tu contenedor PostgreSQL exponiendo el puerto `5432:5432` en tu Mac:
  ```bash
  docker run -d --name postgres -p 5432:5432 -e POSTGRES_PASSWORD=tu_password -e POSTGRES_DB=n8n postgres:latest
  ```
  En `n8n/.env`, mantén el host como:
  ```ini
  DB_POSTGRESDB_HOST=host.docker.internal
  ```
  *(En macOS, `host.docker.internal` permite a los contenedores conectarse a los puertos expuestos en el sistema host)*.

### Iniciar el servicio
```bash
cd n8n
docker compose up -d
```

* **URL local:** [http://localhost:5678](http://localhost:5678)
* **Ver Logs:**
  ```bash
  docker compose logs -f
  ```
* **Detener el servicio:**
  ```bash
  docker compose down
  ```
