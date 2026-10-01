# Apparchar

Plataforma web para que organizadores de eventos de Casanare vendan boletas con
código QR y controlen el aforo en la puerta.

**Prototipo publicado:** <https://sneyderuva.github.io/apparchar/>
**Plataforma en producción (primera versión):** <https://apparchar.sbs>
**Contrato de la API:** [`api/openapi.yaml`](api/openapi.yaml) · **Modelo de datos:** [`docs/modelo-datos.md`](docs/modelo-datos.md) · **Versión:** 0.2.0 (Entrega 2)

## El problema

Los organizadores de eventos de 50 a 2.000 personas en Casanare (bares, colectivos,
fundaciones, consejos estudiantiles) venden boletas en papel o por WhatsApp: no
saben cuántas quedan ni pueden detectar en la puerta una boleta falsa o repetida.

## Ruta y equipo

| Dato | Valor |
|---|---|
| Curso | Tecnologías Web 2026-B · Ingeniería de Sistemas · Unitrópico |
| Ruta de profundización | Electiva de profundización en Desarrollo Web (Tecnologías Web) |
| Equipo | Grupo 3 · «Los insanos» |
| Grupo del curso | G2 |
| Profesor | Juan Carlos Fonseca |

| Integrante | Rol | Usuario de GitHub |
|---|---|---|
| Freddy Sneyder Cedeño Uva | Integrante · repositorio, integración y revisión humana | [@sneyderuva](https://github.com/sneyderuva) |
| Elver Riaño Díaz | Integrante | — |
| Javier Alberto Gómez Wilches | Integrante | — |
| Juan David Rivera Goyeneche | Integrante | — |

La sustentación la presenta cualquiera de los integrantes; todos responden por el contenido.

## Qué hay en este repositorio

| Producto | Dónde | Qué es |
|---|---|---|
| Prototipo navegable | [`web/`](web/) | Cuatro pantallas en HTML, CSS y JavaScript: cartelera, detalle con compra, publicar evento y validar en la puerta, con los estados de carga, vacío y error. Lee los datos de [`web/datos/ejemplo.json`](web/datos/ejemplo.json). |
| Contrato de API | [`api/openapi.yaml`](api/openapi.yaml) | OpenAPI 3.1: 6 recursos, 20 operaciones, errores documentados y ejemplos con datos de Casanare. |
| Modelo de datos | [`docs/modelo-datos.md`](docs/modelo-datos.md) y [`docs/modelo-datos.png`](docs/modelo-datos.png) | 7 entidades en PostgreSQL 17, con atributos, claves, relaciones, índices y el DDL probado. Incluye la traducción desde la base NoSQL (Firebase) de la primera versión (§12). |
| Decisiones | [`docs/decisiones.md`](docs/decisiones.md) | Decisiones de arquitectura y su porqué. |
| Wireframes | [`docs/wireframes/`](docs/wireframes/) | Las cuatro pantallas en móvil y escritorio, redibujadas en la Entrega 2 según el prototipo (los originales de la Entrega 1, si se conservan, van con el prefijo `entrega-1-`). |
| Evidencias | [`docs/evidencias/`](docs/evidencias/) | Capturas y salidas de las verificaciones: Lighthouse, 360 px y zoom al 200 %, teclado, estados, validación del contrato en Swagger Editor y Redocly, servidor simulado, coherencia, pruebas del modelo y el README seguido en un clon limpio. Su [índice](docs/evidencias/README.md) dice qué requisito cubre cada una. |

```
proyecto/
├── README.md
├── .gitignore
├── .env.example            Variables que usará el backend (solo nombres)
├── package.json            Herramientas del proyecto con versiones fijas
├── .nvmrc                  Versión de Node.js (20.19)
├── serve.json              Servidor local sin redirecciones (conserva ?evento=)
├── redocly.yaml            Reglas del validador del contrato
├── docs/
│   ├── modelo-datos.md     Entidades, atributos, relaciones, índices, DDL
│   ├── modelo-datos.png    Diagrama del modelo
│   ├── decisiones.md       Decisiones de arquitectura y su porqué
│   ├── wireframes/         Wireframes de las cuatro pantallas
│   └── evidencias/         Capturas y salidas de las verificaciones
├── api/
│   └── openapi.yaml        Contrato de la API (OpenAPI 3.1)
├── web/
│   ├── index.html          Cartelera (pantalla principal)
│   ├── detalle.html        Detalle del evento y compra
│   ├── publicar.html       Formulario del organizador
│   ├── puerta.html         Validación de boletas en la puerta
│   ├── css/estilos.css     Un solo archivo; variables en :root
│   ├── js/app.js           Interacción y carga de datos de ejemplo
│   ├── img/                Ícono del sitio
│   └── datos/ejemplo.json  Datos realistas del caso regional
├── scripts/
│   ├── verificar-coherencia.mjs   Compara modelo, contrato, datos y formularios
│   └── probar-modelo.sh           Prueba el DDL en PostgreSQL 17
└── .github/                Publicación en GitHub Pages, revisión de PR y plantilla de PR
```

## Cómo ejecutarlo en local

Requisitos: **Git 2.30 o superior** y **Node.js 20.19 o superior en la línea 20,
o 22.12 o superior** (con npm 10). Compruébalo con `git --version` y
`node --version`; el archivo `.nvmrc` fija la 20.19 para quien use nvm. No hace
falta base de datos ni cuentas. En Windows, copia los comandos en **Git Bash**
(viene con Git para Windows): así funcionan tal cual, incluidas las comillas de
`curl`.

1. Clona el repositorio y entra a la carpeta:
   ```bash
   git clone <URL-del-repositorio> apparchar
   cd apparchar
   ```
2. Instala las herramientas con las versiones exactas del `package-lock.json`
   (Redocly, Prism, serve y Ajv; unos 100 MB en `node_modules/`):
   ```bash
   npm ci
   ```
3. Levanta el prototipo y ábrelo en el navegador en http://localhost:8080:
   ```bash
   npm run web
   ```
   Sin Node, desde la raíz del repositorio también sirve Python 3:
   `python3 -m http.server 8080 --directory web` (en Windows: `py -m http.server 8080 --directory web`).
   No abras `web/index.html` con doble clic: el navegador bloquea la lectura de
   `datos/ejemplo.json` desde `file://` y verás el estado de error.
4. En otra terminal, levanta el servidor simulado de la API en http://127.0.0.1:4010:
   ```bash
   npm run mock
   ```
   Y pruébalo:
   ```bash
   curl -H "Prefer: example=yopal_conciertos" "http://127.0.0.1:4010/eventos?municipio=Yopal&categoria=concierto"
   curl -H "Prefer: code=404" http://127.0.0.1:4010/eventos/2e4c1c1d-d769-4047-9880-c8295fa22263
   curl -i -X POST http://127.0.0.1:4010/eventos -H "Content-Type: application/json" -d '{}'
   ```
   El último responde 401 porque crear eventos exige token. Si agregas
   `-H "Authorization: Bearer demo"`, Prism responde 422 con el primer ejemplo de
   error de la operación y pone la falla real del cuerpo en la cabecera
   `sl-violations`; el backend de la Entrega 3 responderá 400
   `solicitud_invalida`, como dice el contrato. Para ver ese ejemplo 400 en el
   simulador, envía un cuerpo válido con `-H "Prefer: code=400"`.
   Prism no filtra: sin `Prefer: example=…` devuelve siempre el primer ejemplo
   (la cartelera completa). Si el cuerpo no es JSON válido, Prism responde 400 con
   su propio formato (`{"error":{"code":"invalid_json",…}}`); el backend
   responderá 400 `solicitud_invalida`. La confirmación de un pago exige la clave
   de la pasarela (sin ella, 401):
   ```bash
   curl -X POST http://127.0.0.1:4010/ordenes/be8a62aa-a0e5-4bbd-be4b-74ab09a9d89a/pago -H "X-Clave-Pasarela: demo" -H "Content-Type: application/json" -d '{"referencia_pago":"PSE-20260929-185302"}'
   ```
5. Valida el contrato (debe terminar sin errores ni advertencias; las reglas
   están en `redocly.yaml`):
   ```bash
   npm run validar:api
   ```
6. Comprueba que el modelo, el contrato, los datos de ejemplo y los formularios
   usan los mismos campos:
   ```bash
   npm run verificar
   ```
7. Opcional, si tienes Docker: prueba el modelo de datos en un PostgreSQL 17
   desechable (crea el esquema y corre 44 comprobaciones de sus restricciones):
   ```bash
   docker run --rm -d --name apparchar-pg -p 5433:5432 -e POSTGRES_HOST_AUTH_METHOD=trust postgres:17
   DATABASE_URL=postgres://postgres@localhost:5433/postgres npm run probar:modelo
   docker stop apparchar-pg
   ```

### Publicación

GitHub Pages no deja elegir la carpeta `web/` desde la configuración de la rama
(solo la raíz o `/docs`), así que el repositorio trae una acción que la publica:

1. Sube el repositorio a GitHub (rama `main`).
2. En GitHub: **Settings → Pages → Build and deployment → Source: GitHub Actions**.
3. La acción [`.github/workflows/pages.yml`](.github/workflows/pages.yml) publica
   `web/` en cada cambio de `main` (o a mano desde **Actions → Publicar prototipo
   en GitHub Pages → Run workflow**). La URL aparece en el resumen de la acción y
   en **Settings → Pages**.

Todas las rutas del prototipo son relativas, así que funciona igual en GitHub
Pages, en local y bajo cualquier subcarpeta.

### Estados del prototipo para la demostración

| Estado | Enlace |
|---|---|
| Cartelera cargando | `index.html?simular=cargando` |
| Cartelera vacía | `index.html?simular=vacio` |
| Cartelera con error | `index.html?simular=error` |
| Evento que no existe | `detalle.html?evento=evento-que-no-existe` |
| Error de red al publicar | `publicar.html?simular=error` |

Los mismos enlaces están en el pie de cada página. En `puerta.html`, validar dos
veces el mismo código muestra el rechazo por doble ingreso.

## Estado del proyecto

| Entrega | Alcance | Estado |
|---|---|---|
| 1 · Anteproyecto | Problema, usuarios y wireframes | Entregada |
| 2 · Prototipo y contrato | Prototipo navegable, contrato OpenAPI 3.1, modelo de datos, tablero | **Esta entrega (v0.2.0)** |
| 3 · Backend | API según el contrato, PostgreSQL, autenticación, Docker, pruebas | Pendiente |
| 4 · Despliegue | Despliegue con dominio propio | Pendiente |

| Pieza | Implementado hoy |
|---|---|
| Interfaz | Sí: cuatro pantallas navegables con datos de ejemplo; la compra y la publicación se simulan en el navegador. |
| Contrato de API | Sí: valida sin errores y levanta un servidor simulado con Prism. |
| Modelo de datos | Sí: documentado y con el DDL probado en PostgreSQL 17. |
| Backend, base de datos real y autenticación | No (Entrega 3). |
| Despliegue con dominio propio | No (Entrega 4). |

## Uso de inteligencia artificial

| Herramienta | Para qué | Alcance |
|---|---|---|
| Claude (Anthropic), mediante Claude Code | Analizar el código de la primera versión de Apparchar; redactar el modelo de datos, el DDL y sus pruebas; escribir el contrato OpenAPI; construir el prototipo (HTML, CSS y JavaScript); generar los datos de ejemplo, el diagrama y los wireframes; escribir el verificador de coherencia, este README y las decisiones; correr Lighthouse, axe-core, Redocly, Spectral, Prism y las pruebas del DDL en PostgreSQL 17, y corregir lo que encontraron. | Borradores y verificaciones automáticas. Las decisiones de producto, la revisión de cada archivo y la sustentación son del equipo. |

Declaración: los integrantes del equipo «Los insanos» declaran de manera libre y
transparente que usaron una herramienta de inteligencia artificial generativa
(Claude, de Anthropic) como apoyo académico y técnico, en continuidad con la
declaración del anteproyecto. La idea del proyecto, el contexto regional, las
decisiones de producto y la validación final del contenido son responsabilidad
del equipo.

Revisión humana: **Freddy Sneyder Cedeño Uva** revisó la totalidad de lo
producido con apoyo de la IA antes de integrarlo (prototipo, contrato OpenAPI,
modelo de datos y su traducción desde NoSQL, decisiones, README y evidencias) y
ajustó varios puntos: el alcance frente a la plataforma que ya opera en
apparchar.sbs, los datos de ejemplo del caso regional de Casanare, la redacción
y el tono de la documentación, y la forma de presentar las evidencias y las
salvedades del repositorio. Lo que no coincidía con la realidad del proyecto se
corrigió o se retiró.
