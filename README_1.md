# Sitio web EMARQ - con seguimiento de proyecto

Este paquete contiene:

- `index.html` - pagina principal (landing) con seccion "Seguimiento de proyecto"
- `seguimiento.html` - panel que ve el CLIENTE (acceso por link/codigo, sin login)
- `admin/login.html` y `admin/panel.html` - panel interno del EQUIPO EMARQ (con login)
- `assets/style.css` - estilos con los colores de EMARQ (verde y azul)
- `assets/config.js` - configuracion de Supabase y los 17 dias del recorrido
- `supabase_schema.sql` - script para crear la base de datos en Supabase
- `assets/logo-emarq.jpeg` - tu logo

---

## Paso 1: Crear el proyecto en Supabase (gratis)

1. Entra a https://supabase.com y crea una cuenta gratuita.
2. Crea un nuevo proyecto (elige una contrasena de base de datos y guardala).
3. Ve a **SQL Editor** (menu izquierdo) > **New query**.
4. Copia y pega TODO el contenido de `supabase_schema.sql` y dale **Run**.
   Esto crea las tablas `proyectos` y `avances`, las reglas de seguridad,
   y la funcion `crear_proyecto`.

## Paso 2: Conectar el sitio con Supabase

1. En Supabase, ve a **Project Settings > API**.
2. Copia el **Project URL** y la **anon public key**.
3. Abre el archivo `assets/config.js` y reemplaza:
   ```js
   const SUPABASE_URL = "https://TU-PROYECTO.supabase.co";
   const SUPABASE_ANON_KEY = "TU-ANON-PUBLIC-KEY";
   ```
   con tus valores reales.

## Paso 3: Crear usuarios para tu equipo (panel interno)

1. En Supabase, ve a **Authentication > Users > Add user**.
2. Crea un usuario por cada persona del equipo que necesite acceso al panel
   interno (ej. arquitecto, ingeniero, economista), con su correo y contrasena.
3. Estos son los datos que usaran para entrar en `admin/login.html`.

## Paso 4: Crear tu primer proyecto

Tienes dos opciones:

**Opcion A - desde el panel interno (recomendado):**
1. Sube el sitio (ver Paso 5) o pruebalo localmente.
2. Entra a `admin/login.html` con un usuario creado en el paso 3.
3. En el panel, llena "Codigo", "Nombre del cliente" y "Servicio", y haz clic
   en **Crear**. Esto genera automaticamente los 17 dias en estado "pendiente"
   (el dia 1 queda "activo").

**Opcion B - desde Supabase directamente:**
En el SQL Editor, ejecuta:
```sql
select crear_proyecto('EMQ-2026-001', 'Juan Perez', 'Vivienda unifamiliar');
```

## Paso 5: Publicar el sitio (Netlify, Vercel o GitHub Pages - gratis)

### Opcion Netlify (mas sencilla)
1. Entra a https://app.netlify.com
2. Arrastra la carpeta completa de este proyecto a la zona de "Deploy".
3. Netlify te da una URL publica, por ejemplo `https://emarq.netlify.app`.

### Opcion Vercel
1. Sube esta carpeta a un repositorio en GitHub.
2. Entra a https://vercel.com, importa el repositorio, y dale "Deploy".
   No necesita configuracion especial (es un sitio estatico).

### Opcion GitHub Pages
1. Sube la carpeta a un repositorio en GitHub.
2. Ve a Settings > Pages > selecciona la rama y la carpeta raiz.
3. GitHub te dara una URL como `https://tuusuario.github.io/emarq`.

---

## Como funciona el link de seguimiento para el cliente

Cada proyecto tiene un codigo unico, ej. `EMQ-2026-001`.

El link que le das al cliente es:
```
https://tu-sitio.netlify.app/seguimiento.html?codigo=EMQ-2026-001
```

Al abrirlo:
- El cliente ve el avance de los 17 dias de su proyecto.
- Puede hacer clic en cualquier dia (completado o en proceso) para ver el
  detalle, las notas del equipo y los archivos/enlaces que se hayan subido.
- Si tu equipo actualiza el estado desde el panel interno, el cliente ve el
  cambio en tiempo real, sin recargar la pagina (gracias a Supabase Realtime).

## Como actualiza tu equipo el avance

1. Entrar a `https://tu-sitio.netlify.app/admin/login.html` con su correo y
   contrasena (creados en el Paso 3).
2. Seleccionar el proyecto del cliente.
3. Por cada dia: cambiar el estado (Pendiente / En proceso / Completado),
   escribir una nota opcional, y opcionalmente pegar un enlace a un archivo
   (render, plano en PDF, presupuesto, etc. -- puedes subir esos archivos a
   Google Drive y pegar el link de compartir aqui).
4. Hacer clic en "Guardar dia X".

---

## Notas importantes

- El panel del cliente (`seguimiento.html`) es de **solo lectura** y publico
  (no requiere contrasena), por eso se accede mediante el codigo/link unico.
- El panel interno (`admin/`) requiere inicio de sesion y es el unico que
  puede modificar el estado de los proyectos (proteccion configurada en
  `supabase_schema.sql` mediante Row Level Security).
- Los archivos (renders, planos, PDFs) no se almacenan en Supabase en este
  esquema basico: se recomienda subirlos a Google Drive y pegar el link de
  "compartir" en el campo correspondiente del panel interno.
