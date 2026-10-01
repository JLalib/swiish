# 🚀 Swiish - Digital Business Cards Platform

[![GitHub](https://img.shields.io/badge/GitHub-mrcrin%2Fswiish-181717?style=for-the-badge&logo=github)](https://github.com/mrcrin/swiish)
[![Docker](https://img.shields.io/badge/Docker-ghcr.io%2Fmrcrin%2Fswiish-2496ED?style=for-the-badge&logo=docker)](https://github.com/mrcrin/swiish/pkgs/container/swiish)
[![License](https://img.shields.io/badge/License-AGPL--3.0-orange?style=for-the-badge)](https://www.gnu.org/licenses/agpl-3.0.html)

## 📋 Descripción general

**Swiish** es una plataforma auto-hospedable para crear y compartir tarjetas de presentación digitales modernas. Permite diseñar tarjetas personalizadas con tu logo, colores y datos de contacto, generando códigos QR únicos para cada tarjeta. Soporta **Progressive Web App (PWA)** para una experiencia fluida incluso offline, y ofrece control total sobre tus datos al alojarse en tu propia infraestructura.

Ideal para profesionales, freelancers y cualquiera que busque una alternativa digital, sostenible y profesional a las tarjetas de presentación tradicionales.

## ✨ Características principales

- 🎨 **Tarjetas Digitales Personalizables** – Diseña tu tarjeta con logo, colores y detalles de contacto
- 📱 **Códigos QR** – Genera códigos QR únicos para cada tarjeta para fácil escaneo
- ⚡ **Soporte PWA** – Funciona como aplicación web progresiva: instalable en pantalla de inicio y uso offline
- 🏠 **Auto-hospedable** – Control total sobre tus datos y la plataforma en tu propio servidor
- 🔗 **Fácil de compartir** – Comparte tu tarjeta mediante enlace directo o código QR
- 👤 **Gestión de Perfiles** – Organiza y actualiza tu información de contacto fácilmente
- 📄 **Código Abierto (AGPL-3.0)** – Libre para usar, modificar y distribuir con total transparencia
- 🐳 **Soporte Node.js y Docker** – Tecnologías modernas para despliegue eficiente y escalable

## 📋 Requisitos del sistema

- **Docker** ≥ 20.10
- **Docker Compose** ≥ 2.0 (plugin `docker compose` o `docker-compose` standalone)
- **Puerto 8095** disponible en el host (configurable)
- **Mínimo 512 MB RAM** y **1 GB espacio en disco** para datos y subidas
- **Navegador moderno** con soporte PWA (Chrome, Firefox, Edge, Safari)

## 🐳 Instalación

### Paso 1: Crear archivo `.env` (Recomendado)

Crea un archivo llamado `.env` en el mismo directorio que tu `docker-compose.yml`:

```bash
# .env
JWT_SECRET=un_secreto_muy_seguro_y_largo
APP_URL=http://localhost:8095
NODE_ENV=production
PORT=3000
JWT_EXPIRES_IN=24h
ALLOWED_ORIGINS=http://localhost:8095
MAX_FILE_SIZE=5242880
```

> ⚠️ **Importante**: `JWT_SECRET` **debe** ser una cadena larga y aleatoria. Genera una con: `openssl rand -base64 32`

### Paso 2: Crear `docker-compose.yml`

```yaml
version: '3.8'

services:
  swiish:
    image: ghcr.io/mrcrin/swiish:latest
    container_name: swiish
    restart: unless-stopped
    ports:
      - "8095:3000"
    environment:
      # Usa las variables de tu archivo .env
      - JWT_SECRET=${JWT_SECRET}
      - APP_URL=${APP_URL:-http://localhost:8095}
      - NODE_ENV=${NODE_ENV:-production}
      - PORT=3000
      - JWT_EXPIRES_IN=${JWT_EXPIRES_IN:-24h}
      - ALLOWED_ORIGINS=${ALLOWED_ORIGINS:-http://localhost:8095}
      - MAX_FILE_SIZE=${MAX_FILE_SIZE:-5242880}
    volumes:
      - ./data:/app/data
      - ./uploads:/app/uploads
```

### Paso 3: Iniciar Swiish

```bash
# Levantar en segundo plano
docker compose up -d

# Ver logs en tiempo real
docker compose logs -f swiish
```

### Paso 4: Acceder a Swiish

Abre en tu navegador: **http://localhost:8095**

## ⚙️ Configuración

1. **JWT_SECRET** – Clave secreta para firmar tokens JWT (obligatoria, ≥ 32 chars)
2. **APP_URL** – URL pública donde se accederá a Swiish (ej: `https://swiish.tudominio.com`)
3. **NODE_ENV** – Entorno de ejecución (`production` | `development`)
4. **PORT** – Puerto interno del contenedor (por defecto `3000`, no cambiar)
5. **JWT_EXPIRES_IN** – Expiración del token (ej: `24h`, `7d`, `30d`)
6. **ALLOWED_ORIGINS** – Orígenes permitidos para CORS (separados por coma si múltiples)
7. **MAX_FILE_SIZE** – Tamaño máximo de subida en bytes (por defecto `5242880` = 5 MB)

## 🚀 Primeros pasos

1. **Clona o crea el directorio del proyecto**
   ```bash
   mkdir swiish && cd swiish
   ```

2. **Crea el archivo `.env`** con tus valores (ver sección Instalación)

3. **Crea el `docker-compose.yml`** (ver sección Instalación)

4. **Inicia el contenedor**
   ```bash
   docker compose up -d
   ```

5. **Verifica que está corriendo**
   ```bash
   docker compose ps
   # Deberías ver: swiish   Up   0.0.0.0:8095->3000/tcp
   ```

6. **Accede a la interfaz web** en `http://localhost:8095` (o tu `APP_URL`)

7. **Crea tu primera tarjeta** – Regístrate, personaliza tu perfil y genera tu código QR

## 💡 Casos de uso

- 👔 **Profesionales y ejecutivos** – Tarjetas digitales elegantes para networking
- 🎨 **Freelancers y creativos** – Portafolio visual con enlace directo a proyectos
- 🏢 **Empresas y equipos** – Tarjetas corporativas consistentes y actualizables al instante
- 🌱 **Eventos y conferencias** – Intercambio de contactos sin papel, escaneo rápido
- 📱 **Uso personal** – Tarjeta de contacto única con todos tus enlaces (LinkedIn, GitHub, Web, etc.)

## 🔒 Acceso remoto seguro

Para exponer Swiish de forma segura en Internet:

1. **Reverse Proxy (Recomendado)** – Usa **Nginx Proxy Manager**, **Traefik** o **Caddy** con:
   - Terminación SSL (Let's Encrypt)
   - Rate limiting
   - Headers de seguridad (HSTS, CSP, etc.)

2. **Ejemplo con Caddy** (`Caddyfile`):
   ```caddyfile
   swiish.tudominio.com {
       reverse_proxy swiish:3000
       header {
           Strict-Transport-Security "max-age=31536000"
           X-Content-Type-Options "nosniff"
           X-Frame-Options "DENY"
           Referrer-Policy "strict-origin-when-cross-origin"
       }
   }
   ```

3. **Actualiza `.env`** con tu dominio:
   ```env
   APP_URL=https://swiish.tudominio.com
   ALLOWED_ORIGINS=https://swiish.tudominio.com
   ```

4. **Reinicia**: `docker compose up -d`

## 🛠️ Gestión y mantenimiento

| Acción | Comando |
|--------|---------|
| Ver logs | `docker compose logs -f swiish` |
| Reiniciar | `docker compose restart swiish` |
| Actualizar imagen | `docker compose pull && docker compose up -d` |
| Backup datos | `tar -czf swiish-backup-$(date +%F).tar.gz data/ uploads/` |
| Restaurar backup | `tar -xzf swiish-backup-YYYY-MM-DD.tar.gz` |
| Detener | `docker compose down` |
| Eliminar todo (⚠️ datos) | `docker compose down -v` |

> 💡 **Consejo**: Programa backups automáticos de `./data` y `./uploads` con `cron` o herramientas como `restic`/`borg`.

## 📝 Licencia

Este proyecto está licenciado bajo **AGPL-3.0** – Consulta el archivo [LICENSE](https://github.com/mrcrin/swiish/blob/main/LICENSE) para detalles.

---

> 📖 **Guía completa en el blog**: [Cómo instalar y configurar Swiish en Docker | Digital Business Card | QR Codes | PWA | Self-Hosted](https://genbyte.blogspot.com/2026/10/como-instar-y-configurar-swiish-en.html)