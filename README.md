# Uso de pantallas

Informe académico de una sola página sobre cómo se usa el celular con niños menores de 8 años en lugares públicos de Quito. El sitio publica la observación de campo de Joshua Alexander Lozada Zurita para el Taller de Investigación (Diseño de Medios Interactivos, Universidad San Francisco de Quito), con la docente María Naranjo Ayala.

La observación registrada hasta ahora se hizo el 18 de octubre en el patio de comidas del Quicentro Sur. El informe recorre el planteamiento, la posición del investigador, los lugares, el diario de campo, las categorías culturales, las conclusiones, la reflexión sobre el proceso, las referencias y una nota sobre el uso de inteligencia artificial. El texto, las fotos y las fichas viven en Markdown: la página los lee y los arma sola.

Sitio en línea: [https://uso-de-pantallas.vercel.app](https://uso-de-pantallas.vercel.app)

Está pensado para escritorio, con un ancho mínimo de 1280 px.

## Qué incluye

- Una sola página con barra de secciones y un recorrido lateral que marca en qué parte del informe está el lector.
- Portada con el título de la investigación, autor, docente, materia y los conteos de salidas, observaciones y entradas del diario.
- Planteamiento con la pregunta, el objetivo general y los objetivos específicos.
- Reflexividad: posición del investigador, sesgo declarado y nivel de participación.
- Escena cultural con los lugares de observación.
- Diario de campo. Cada entrada trae fecha, hora, espacio, descripción, impresiones, reflexión, frases, categorías, imagen principal, galería, preguntas pendientes y nota de privacidad. Las fotos se presentan en blanco y negro, con desenfoque cuando hace falta proteger a las personas.
- Categorías culturales ligadas a las entradas del diario. Hoy son «El celular como calmante» y «Aislamiento o compartir».
- Conclusiones, reflexión sobre el proceso y referencias ordenadas.
- Nota de uso de IA (Cursor y Claude) al cierre del informe.
- Animación de scroll con GSAP y desplazamiento suave con Lenis: título de la portada, carrusel de fotos, secuencia de cámara junto al diario y cursor propio. Si el sistema pide menos movimiento, esas animaciones no se ejecutan.
- El marco teórico y la metodología siguen en el contenido y se pueden editar en el panel, aunque la página de inicio no los muestra.

## Tecnologías

- [Astro](https://astro.build) 7, con salida estática (`output: 'static'`).
- TypeScript y colecciones de contenido validadas con Zod.
- Tailwind CSS 4, integrado por Vite.
- GSAP (ScrollTrigger y SplitText) y Lenis.
- [Decap CMS](https://decapcms.org) 3, cargado en `/admin`.
- Despliegue en Vercel. El dominio del sitio está en `astro.config.mjs` y en `public/admin/config.yml`.

El contenido está en `src/content/`:

| Carpeta | Para qué sirve |
| --- | --- |
| `textos/` | Datos del proyecto: título, autor, docente, materia, ciudad y conteos. |
| `secciones/` | Planteamiento, escena, reflexividad, conclusiones, reflexión, marco teórico y metodología. |
| `diario/` | Entradas del diario de campo. |
| `categorias/` | Categorías culturales. |
| `referencias/` | Bibliografía. |

Las imágenes del panel se guardan en `public/uploads/`.

## Correr el proyecto en local

Hace falta Node.js 22.22 o superior y npm 9.6.5 o superior.

```bash
npm install
npm run dev
```

Abrir [http://localhost:4321](http://localhost:4321).

Otros comandos:

```bash
npm run build
npm run preview
```

`npm run build` genera el sitio estático en `dist/`. `npm run preview` sirve esa carpeta para revisarla antes de publicar.

## Cómo se edita con Decap CMS

El panel está en `/admin`: en local, [http://localhost:4321/admin](http://localhost:4321/admin); en producción, [https://uso-de-pantallas.vercel.app/admin](https://uso-de-pantallas.vercel.app/admin).

La configuración vive en `public/admin/config.yml`. El idioma del panel es español. Estas son las colecciones:

- **Textos generales.** Datos del proyecto.
- **Secciones.** Bloques del informe.
- **Diario de campo.** Entradas nuevas.
- **Referencias.** Citas, con un número de orden.
- **Categorías culturales.** Título, descripción, color y orden.

Publicar desde el panel hace un commit en GitHub, en la rama `main` del repositorio `Scorpwik/uso-de-pantallas`. Vercel vuelve a construir el sitio con ese commit. La cuenta que inicia sesión necesita permiso de escritura en el repositorio.

### En local, sin GitHub

1. En `public/admin/config.yml`, descomentar `local_backend: true`.
2. En otra terminal, correr `npx decap-server`.
3. Abrir `/admin` y editar. Los cambios se escriben directo en `src/content/` y en `public/uploads/`.

Esa línea tiene que volver a quedar comentada antes de subir el cambio a producción. Si no, el panel publicado no usa GitHub.

### En producción, con GitHub

Decap usa el backend de GitHub. El secreto de la aplicación OAuth no puede vivir en el frontend, así que hace falta un proxy:

1. En GitHub: **Settings → Developer settings → OAuth Apps → New OAuth App**.
2. Homepage URL: `https://uso-de-pantallas.vercel.app`.
3. Authorization callback URL: `https://TU-PROXY-OAUTH.vercel.app/callback`.
4. Desplegar un proxy OAuth (por ejemplo el de [decap-cms-backend-github](https://github.com/decap-cms/decap-cms/blob/main/packages/decap-cms-backend-github/README.md), o uno equivalente en Vercel) con `OAUTH_CLIENT_ID` y `OAUTH_CLIENT_SECRET`.
5. En `public/admin/config.yml`, el backend debe quedar así:

```yml
backend:
  name: github
  repo: Scorpwik/uso-de-pantallas
  branch: main
  base_url: https://TU-PROXY-OAUTH.vercel.app
```

Hoy el archivo ya apunta al repositorio `Scorpwik/uso-de-pantallas` y a la rama `main`. `base_url` se agrega cuando el proxy OAuth esté publicado.

### Agregar una entrada del diario

1. Entrar a `/admin` y abrir **Diario de campo**.
2. Crear una entrada. Completar fecha, hora, espacio, textos, frases, categorías, imagen, galería, preguntas y la nota de privacidad.
3. Marcar **La imagen tiene desenfoque aplicado** si el archivo ya está desenfocado.
4. Subir las fotos desde el propio panel. Quedan en `public/uploads/`. Usar archivos ya desenfocados.
5. Publicar. Decap hace el commit y Vercel reconstruye el sitio.
6. La entrada aparece sola en **Diario de campo** y dentro de las **Categorías** que se hayan marcado. No hace falta tocar código.

La categoría se elige entre `mediador-crisis` y `aislamiento-consumo`, que son los identificadores definidos en el esquema de Astro.

## Despliegue

El sitio ya está publicado en Vercel desde `Scorpwik/uso-de-pantallas`.

- Comando de build: `npm run build`.
- Carpeta de salida: `dist`.
- No hace falta un adapter de servidor: la salida es estática.
- Si cambia el dominio, hay que actualizar `site` en `astro.config.mjs` y `site_url` y `display_url` en `public/admin/config.yml`.
