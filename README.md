# Docker Containers - Servicios Independientes

Este repositorio contiene la configuración en Docker Compose para desplegar y administrar servicios independientes, cada uno en su propio directorio:

1. **`openwa/`**: Servidor y API para automatización de WhatsApp con soporte para sesiones persistentes (Puppeteer).
2. **`evolutionapi/`**: API de WhatsApp ligera v2 (Baileys) con soporte multi-instancia, Redis y panel visual Evolution Manager.
3. **`stirling/`**: Plataforma web de código abierto para visualización, edición, conversión y manipulación de archivos PDF.
4. **`n8n/`**: Plataforma de automatización de flujos de trabajo (workflow automation) conectada a PostgreSQL.

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
├── evolutionapi/
│   ├── docker-compose.yml   # Definición de Evolution API + Redis + Evolution Manager
│   ├── .env.example         # Plantilla de variables para Evolution API
│   └── .env                 # Variables de entorno activas (ignorado en git)
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

## 🚀 2. Servicio Evolution API (Baileys v2)

Evolution API es una pasarela WhatsApp de alto rendimiento construida sobre Baileys (WebSockets puros, sin navegador Chrome pesado), perfecta para arquitecturas ARM (Raspberry Pi / Orange Pi). Incluye un contenedor Redis ultraligero y el panel web opcional **Evolution Manager**.

### Configuración (`evolutionapi/.env`)
```ini
EVOLUTION_PORT=8085           # Puerto en el host para la API
EVOLUTION_MANAGER_PORT=8086   # Puerto en el host para el panel Evolution Manager
AUTHENTICATION_API_KEY=tu_key # Clave API secreta global (definida por ti)
```

### Iniciar el servicio
```bash
cd evolutionapi
docker compose up -d
```

* **API Explorer / Documentación:** [http://localhost:8085](http://localhost:8085)
* **Panel Evolution Manager:** [http://localhost:8086](http://localhost:8086)
* **Ver Logs / QR:**
  ```bash
  docker compose logs -f evolution_api
  ```
* **Detener el servicio:**
  ```bash
  docker compose down
  ```

---

## 📄 3. Servicio Stirling-PDF

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

## ⚡ 4. Servicio n8n (con PostgreSQL)

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
