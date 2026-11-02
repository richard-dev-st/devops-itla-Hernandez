# devops-itla-Hernandez

### Nombre

1. Richard José Hernández Valdez.

### Matricula

2. 2025-1191.

### Sección

3. Miércoles 4:00 PM - 6:00 PM - Grupo 2.


# Plantilla DevOps — Electiva 1 (ITLA 2026-C-3)

Aplicación web mínima (lista de tareas) con todo lo necesario para practicar un pipeline **CI/CD** sin cuentas pagas ni Azure:

| Pieza | Archivo | Para qué sirve |
|---|---|---|
| App Node.js/Express | `src/app.js`, `src/server.js` | API de tareas + `/health` y `/metrics` |
| Frontend estático | `public/index.html` | Se publica en GitHub Pages |
| Pruebas automatizadas | `tests/app.test.js` | Jest + Supertest con cobertura |
| Contenedor | `Dockerfile` | Imagen de producción con *healthcheck* |
| Pipeline | `.github/workflows/ci.yml` | Lint → pruebas → build Docker → deploy a Pages |

## Requisitos (gratis)
- Cuenta de **GitHub** (usa un repositorio **público**: GitHub Actions es gratuito).
- **Git** y **Node.js 20+**. Opcional para probar el contenedor en tu PC: **Docker Desktop**.

## Ejecutar en tu computadora
```bash
npm install
npm test          # pruebas + cobertura
npm start         # http://localhost:3000
```
Endpoints: `GET /health`, `GET /metrics`, `GET/POST /api/tareas`, `PATCH /api/tareas/:id`.

Con Docker:
```bash
docker build -t tareas-devops .
docker run -p 3000:3000 tareas-devops
```

## Subirlo a GitHub y activar el pipeline
1. Crea un repositorio **público** vacío en GitHub (sin README).
2. En la carpeta de la plantilla:
   ```bash
   git init
   git add .
   git commit -m "Plantilla inicial"
   git branch -M main
   git remote add origin https://github.com/TU_USUARIO/TU_REPO.git
   git push -u origin main
   ```
3. En GitHub: **Settings → Pages → Source: GitHub Actions**.
4. Abre la pestaña **Actions**: verás el workflow *CI/CD* ejecutándose. Cuando termine, la URL de tu página aparece en el job *Despliegue a GitHub Pages*.

## Práctica 3 — qué debes agregar
- Al menos **2 pruebas nuevas** (por ejemplo: borrar tareas, validar títulos largos).
- Una funcionalidad nueva pequeña con su prueba (ej. `DELETE /api/tareas/:id`).
- Provocar a propósito una prueba fallida, mostrar que el pipeline se pone en **rojo**, corregirla y mostrarlo en **verde**.
- Completar este README con la explicación de cada etapa del pipeline.

## Proyecto Final — cómo extenderla
- Persistencia con **SQLite** (o MongoDB) en lugar del arreglo en memoria.
- Publicar la imagen en **GitHub Container Registry** (`ghcr.io`) desde el pipeline.
- Análisis estático real (ESLint) y umbral de cobertura mínima.
- Monitoreo: usar `/metrics` y los logs JSON; definir al menos una **alerta** (ej. errores > 0).
- Documentar: guía de instalación, manual de operaciones y lecciones aprendidas.

## Explicación de las etapas del Pipeline (CI/CD)

El pipeline de integración y despliegue continuo está automatizado mediante GitHub Actions (`.github/workflows/ci.yml`) y consta de las siguientes fases:

1. **Checkout del Código (`actions/checkout@v4`):** Clona el repositorio en un entorno virtual limpio con Ubuntu proporcionado por los runners de GitHub para obtener la última versión del código fuente.
2. **Configuración de Node.js (`actions/setup-node@v4`):** Instala y configura la versión de Node.js requerida (versión 20) para asegurar la compatibilidad con la aplicación Express.
3. **Instalación de Dependencias (`npm install`):** Descarga e instala todos los paquetes y módulos necesarios definidos en el archivo `package.json` (como Express, Supertest y Jest).
4. **Ejecución de Pruebas y Cobertura (`npm test`):** Ejecuta la suite de pruebas unitarias e de integración utilizando Jest, validando el correcto funcionamiento de los endpoints de la API (`GET`, `POST`, `PATCH`, `DELETE`) y generando el reporte de cobertura de código.
5. **Construcción de la Imagen Docker (`docker build`):** Empaqueta la aplicación en un contenedor optimizado para producción utilizando el `Dockerfile` del proyecto.
6. **Despliegue Automático a GitHub Pages:** Publica de forma estática la interfaz web de la aplicación (`public/index.html`) para que esté accesible de manera pública y gratuita en la web.