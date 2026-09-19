# 🗄️ Patitas de Amor - Repositorio de Almacenamiento (patitas-de-amor-tb)

> Sistema Ligero de CMS Headless y Base de Datos Desacoplada sincronizada con Cloudflare Pages y Upstash Redis.

---

## 📌 ¿Qué es este repositorio?

Este repositorio es una pieza fundamental de la arquitectura desacoplada de **Patitas de Amor**. No contiene código ejecutable de la interfaz. Funciona exclusivamente como **Base de Datos documental y CDN Ligero (Git-based Headless CMS)** para la organización.

La aplicación principal (desplegada en **Cloudflare Pages**) se comunica con este repositorio (`patitas-de-amor-tb`) mediante la API REST de GitHub para realizar operaciones CRUD (Crear, Leer, Actualizar, Borrar) sobre archivos `.json` e imágenes optimizadas.

### Ventajas de esta arquitectura:
* **Costes mínimos de infraestructura:** Sin servidores de base de datos tradicionales dedicados para archivos pesados.
* **Versionado histórico estricto:** Auditoría completa de commits para cada adopción, ficha de rescate o jornada publicada.
* **Distribución global de alto rendimiento:** Aprovechamiento de la CDN global y gratuita de GitHub (`raw.githubusercontent.com`) para servir los datos con baja latencia.

---

## 🌳 Árbol Simétrico de Directorios

El repositorio mantiene una estructura estricta y predecible entre los datos de Mascotas y Anuncios/Eventos:

```
📦 patitas-de-amor-tb
 ┣ 📂 data/                           # Entidades y colecciones JSON
 ┃ ┣ 📂 announcements/                # Respaldo atómico individual de cada anuncio
 ┃ ┃ ┣ 📜 announcement-12345.json
 ┃ ┃ ┗ 📜 announcement-67890.json
 ┃ ┣ 📂 pets/                         # Respaldo atómico individual de cada mascota
 ┃ ┃ ┣ 📜 pet-11111.json
 ┃ ┃ ┗ 📜 pet-22222.json
 ┃ ┣ 📜 announcements.json            # 🔴 ÍNDICE MAESTRO DE ANUNCIOS / JORNADAS (Array)
 ┃ ┗ 📜 pets.json                     # 🔴 ÍNDICE MAESTRO DE MASCOTAS (Array)
 ┃
 ┣ 📂 images/                         # Archivos Multimedia Optimizados (WebP)
 ┃ ┣ 📂 announcements/                # Flyers, banners e imágenes de jornadas
 ┃ ┃ ┣ 🖼️ announcement-12345.webp
 ┃ ┃ ┗ 🖼️ announcement-67890.webp
 ┃ ┗ 📂 pets/                         # Fotografías oficiales de las mascotas
 ┃   ┣ 🖼️ pet-11111.webp
 ┃   ┗ 🖼️ pet-22222.webp
 ┃
 ┗ 📜 README.md                       # Documentación técnica del almacenamiento
```

---

## ♻️ Flujo del Ciclo de Vida de los Datos

El backend de **Patitas de Amor** gestiona este repositorio de manera automatizada mediante un patrón de **Persistencia Dual**:

### 1. Creación / Edición (Optimización WebP en origen)
1. **Compresión previa:** Al adjuntar una fotografía desde el panel administrativo, el cliente (Frontend) la comprime y convierte a formato `.webp`.
2. **Subida multimedia:** El backend sube el buffer optimizado a `images/.../{id}.webp`.
3. **Persistencia atómica:** Se genera el archivo unitario `data/.../{id}.json` para salvaguardar el estado individual.
4. **Actualización de índice (Upsert):** Se inyecta la entidad al inicio (`unshift`) del array dentro de `data/pets.json` o `data/announcements.json`.

### 2. Lectura y Distribución Rápida
* La plataforma pública consume directamente los índices consolidados (`pets.json` y `announcements.json`).
* Se minimiza el consumo de cuota de la API de GitHub al evitar múltiples lecturas unitarias y permitir cacheo perimetral.

### 3. Borrado Sincronizado (Limpieza en Cascada)
Al eliminar un registro desde el panel administrativo, el sistema destruye tres elementos de forma concurrente:
1. El archivo de imagen `.webp` en `images/`.
2. El archivo `.json` unitario en `data/`.
3. La entrada específica dentro del índice maestro correspondiente (`pets.json` o `announcements.json`).

> **Nota:** La autenticación de sesiones y la gestión de roles de usuarios se procesa de forma paralela y segura a través de **Upstash Redis**, manteniendo este repositorio exclusivamente enfocado en el contenido público de la organización.

---

## ⚠️ Advertencias y Recomendaciones de Edición

> [!CAUTION]
> **EDICIÓN MANUAL DESACONSEJADA:** Este repositorio es administrado automáticamente por la API de la aplicación. La manipulación manual inadecuada de los archivos puede comprometer la integridad del portal público.

Si se requiere una intervención manual de emergencia, es imperativo cumplir las siguientes reglas:

1. **Validación estricta de sintaxis JSON:** Una coma extra o faltante en `pets.json` o `announcements.json` romperá el parseo de datos en el cliente. Valida siempre el JSON antes de confirmar un commit.
2. **Preservar la simetría:** Si eliminas un recurso multimedia en `images/pets/`, debes purgar simultáneamente su referencia en el array de `pets.json`.
3. **Inmutabilidad en nomenclatura:** El frontend resuelve los assets basándose estrictamente en el identificador único (`{id}.webp`). Modificar el nombre de un archivo impedirá que la aplicación lo localice.
4. **Estándar WebP:** No subas archivos `.png` o `.jpg` directamente. Convierte cualquier recurso gráfico a formato `.webp` para garantizar tiempos de carga óptimos y bajo consumo de datos.

---

*Patitas de Amor — Repositorio de persistencia documental para la gestión y rescate animal.*