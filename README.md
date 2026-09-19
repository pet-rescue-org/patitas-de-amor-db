# Base de Datos CMS - Patitas de Amor

Este repositorio es gestionado automáticamente como el **backend de almacenamiento** (Storage) para el Portal de Adopción de Mascotas. Funciona como una base de datos Serverless gestionada a través de la API de GitHub.

⚠️ **ADVERTENCIA CRÍTICA** ⚠️
**NO MODIFIQUES** los archivos de este repositorio de forma manual a menos que sepas exactamente lo que estás haciendo. Los commits y la actualización de archivos son manejados automáticamente por el sistema CMS a través de Cloudflare Pages Functions. Cambios manuales mal formados pueden corromper la lectura de datos en producción.

## Estructura del Repositorio

- `/data/pets.json`: Registro principal JSON con todas las mascotas y sus metadatos.
- `/data/announcements.json`: Registro principal JSON con los eventos y anuncios.
- `/data/settings/`: Archivos JSON con configuraciones globales del refugio.
- `/images/pets/`: Imágenes subidas para los perfiles de mascotas (`.webp`).
- `/images/announcements/`: Imágenes subidas para los anuncios (`.webp`).

Este repositorio debe mantenerse privado si contiene información sensible, o público si se desea usar GitHub Pages / jsDelivr como CDN directa.
