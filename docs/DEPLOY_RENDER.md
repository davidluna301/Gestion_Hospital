# Despliegue en producción (Render + PostgreSQL)

ConsultaMed es una aplicación **Django** (plantillas + API en el mismo servicio). No hay un front separado: el “frontend” son las plantillas servidas por Gunicorn junto con el backend.

## Requisitos previos

1. Cuenta en [Render](https://render.com) (plan gratuito).
2. Repositorio en GitHub: `davidluna301/Gestion_Hospital`, rama **`develop`** con los últimos cambios (`render.yaml`, `settings.py`).
3. Conectar GitHub en Render: **Account Settings → GitHub → Connect**.

## Despliegue automático (Blueprint)

1. En Render: **New → Blueprint**.
2. Conecte el repositorio `Gestion_Hospital`.
3. Render detectará `render.yaml` y creará:
   - **Web Service** `consultamed` (Python, Gunicorn).
   - **PostgreSQL** `consultamed-db` (variable `DATABASE_URL` enlazada al web).
4. Rama: **`develop`** (ya definida en `render.yaml`).
5. Pulse **Apply** y espere el primer build (5–15 min en plan free).

Al terminar el build verá una URL como `https://consultamed-xxxx.onrender.com`.

## Qué hace el build

```text
pip install -r requirements.txt
python manage.py collectstatic --noinput
python manage.py migrate --noinput
python manage.py seed_demo
```

- **Migraciones:** crean tablas en PostgreSQL.
- **seed_demo:** solo si la BD está vacía; crea especialidades, usuarios de prueba y citas de ejemplo.

## Usuarios de prueba (tras seed_demo)

| Rol        | Correo                      | Contraseña    |
|-----------|-----------------------------|---------------|
| Admin     | admin@consultamed.local     | Admin123!     |
| Operador  | operador@consultamed.local  | Operador123!  |
| Médico    | medico@consultamed.local    | Medico123!    |
| Paciente  | paciente@consultamed.local  | Paciente123!  |

Inicio de sesión: `https://SU-URL.onrender.com/cuentas/iniciar-sesion/`

## Variables de entorno (ya en Blueprint)

| Variable | Origen |
|----------|--------|
| `DJANGO_SECRET_KEY` | Generada por Render |
| `DJANGO_DEBUG` | `False` |
| `DJANGO_ALLOWED_HOSTS` | `.onrender.com,localhost,127.0.0.1` |
| `DATABASE_URL` | Enlace automático a `consultamed-db` |
| `RENDER_EXTERNAL_URL` | Inyectada por Render (CSRF/hosts) |

Opcional (correo real): configure SMTP en el servicio web según `.env.example`.

## Comprobar que todo funciona

1. Abrir la URL pública → landing.
2. Iniciar sesión como **admin@consultamed.local**.
3. Ir a `/panel/` → gráficos (admin).
4. Crear o listar citas en `/citas/`.
5. Descargar reporte PDF en `/reportes/citas.pdf` (admin/médico/operador).

## Subir cambios locales a GitHub

Desde la carpeta del proyecto:

```powershell
git add hospital_gestion/settings.py render.yaml docs/DEPLOY_RENDER.md
git commit -m "fix: ajustes de producción Render (CSRF, hosts y seed en build)"
git push origin develop
```

Luego en Render: **Manual Deploy → Deploy latest commit** (o redeploy automático si está activado).

## Problemas frecuentes

| Síntoma | Solución |
|---------|----------|
| **502 / Application failed to respond** | Revisar logs del servicio; suele ser error en `migrate` o dependencias. |
| **403 CSRF** en login | Asegure que el despliegue incluye `RENDER_EXTERNAL_URL` (Render lo añade solo) y la versión actual de `settings.py`. |
| **DisallowedHost** | Añada su hostname en `DJANGO_ALLOWED_HOSTS` en el dashboard de Render. |
| **BD vacía sin usuarios** | En Shell de Render: `python manage.py seed_demo` |
| **Archivos subidos (certificados)** | En plan free el disco es efímero; los uploads pueden perderse al redeploy. Para producción estable use almacenamiento externo (S3, etc.). |

## Alternativas

- **Railway / PythonAnywhere:** mismas variables que `.env.example`; comando de arranque: `gunicorn hospital_gestion.wsgi:application --bind 0.0.0.0:$PORT`.
