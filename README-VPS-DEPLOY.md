Deploy a VPS con Docker desde GitHub Actions

Resumen
------
Este repositorio incluye un workflow de GitHub Actions (`.github/workflows/deploy-to-vps.yml`) que construye una imagen Docker, la sube a un registry y luego se conecta por SSH a tu VPS para ejecutar `docker compose up -d` y actualizar el servicio.

Requisitos previos
------------------
- Un registry donde subir imágenes (Docker Hub, GHCR, GitLab Registry, etc.).
- Un VPS con Docker y Docker Compose instalados.
- Acceso SSH al VPS con clave privada.

Secrets que debes añadir en GitHub (Repository → Settings → Secrets and variables → Actions):
- REGISTRY: URL del registry (ej. ghcr.io o docker.io)
- REPOSITORY: owner/repo del image (ej. myorg/myapp)
- REGISTRY_USERNAME
- REGISTRY_PASSWORD
- VPS_HOST
- VPS_USER
- VPS_SSH_KEY (tu clave privada, sin passphrase o usa el agente)
- VPS_SSH_PORT (opcional, default 22)

Preparar el VPS (ejemplo mínimo)
--------------------------------
En tu VPS como root/usuario sudo:

1. Instalar Docker y Docker Compose (ejemplo para Ubuntu):

   sudo apt update; sudo apt install -y docker.io docker-compose
   sudo systemctl enable --now docker

2. Crear directorio del proyecto y dar permisos:

   sudo mkdir -p /var/www/your-app
   sudo chown -R $USER:$USER /var/www/your-app

3. Colocar el `docker-compose.yml` en `/var/www/your-app` (usa el ejemplo del repo o adapta el tuyo).

4. Desde el VPS prueba:

   export IMAGE=ghcr.io/your-org/your-repo:latest
   cd /var/www/your-app
   docker compose pull
   docker compose up -d

Uso del workflow
----------------
Haz push a `main` o correlo manualmente desde la pestaña Actions → tu workflow → Run workflow.

Notas de seguridad
------------------
- Guarda tu clave SSH en Secrets. Si tiene passphrase, considera usar un agente SSH en el runner (más avanzado).
- Restringe el acceso al puerto SSH mediante firewall y usa un usuario con permisos limitados.
