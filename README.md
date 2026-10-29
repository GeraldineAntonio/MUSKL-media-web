# MUSKL · Videos de ejercicios

Proyecto web independiente para que un entrenador activo grabe demostraciones con la cámara y las vincule a ejercicios del catálogo MUSKL. Inicia sesión contra el API existente; el archivo se envía al API y este lo guarda en el bucket privado de Cloudflare R2. PostgreSQL conserva la referencia y el formato. La app Flutter recibe una liga temporal para reproducir el video.

## Requisitos

- Node.js 20 o posterior
- El API MUSKL desplegado o ejecutándose localmente
- `MUSKL_R2_ACCOUNT_ID`, `MUSKL_R2_ACCESS_KEY_ID`, `MUSKL_R2_SECRET_ACCESS_KEY` y `MUSKL_R2_PRIVATE_BUCKET` configurados en el entorno del API
- Aplicar `backend/db/026_exercise_catalog_media.sql` a PostgreSQL antes de desplegar el API actualizado

## Ejecutar localmente

```sh
cp .env.example .env.local
npm install
npm run dev
```

Abre la URL que indique Vite (normalmente `http://localhost:5173`). La cámara del navegador requiere `localhost` o HTTPS. Para una API local, configura `VITE_MUSKL_API_URL=http://localhost:8000` en `.env.local`. El valor predeterminado apunta al API de producción.

## Publicar como sitio independiente en Render

1. Sube este proyecto a su propio repositorio GitHub.
2. En Render crea un **Static Site** conectado a ese repositorio.
3. Usa `npm ci` como comando de instalación y `npm run build` como comando de compilación.
4. Publica la carpeta `dist` y configura una regla rewrite de `/*` a `/index.html`.
5. Define `VITE_MUSKL_API_URL` con la URL pública del API al construir la web.
6. Añade el origen HTTPS publicado a `MUSKL_CORS_ORIGINS` del API, separado por comas de otros orígenes autorizados.
7. En la configuración CORS del bucket R2 permite `GET` y `HEAD` desde el origen web; permite el encabezado `Range` y expón `Content-Length`, `Content-Range` y `Accept-Ranges` para que el reproductor pueda solicitar fragmentos del video.

El video acepta MP4, MOV o WebM y un máximo de 50 MB. La grabación no se convierte a GIF: MP4 mantiene mejor calidad y ocupa menos almacenamiento.
