---
name: Fuentes de documentos normativos de Unillanos
description: URLs y tipos de documentos donde está la normativa institucional que constituirá el corpus
type: reference
---

## Portales institucionales públicos

| Fuente | URL | Contenido |
|--------|-----|-----------|
| Consejo Académico | https://www.unillanos.edu.co/index.php/consejo-academico-1/ | Acuerdos y resoluciones académicas |
| Consejo Superior | https://unillanos.edu.co/index.php/documentacion/consejo-superior-universitario/ | Acuerdos superiores, resoluciones superiores |
| SIRIUS (admin) | https://sirius.unillanos.edu.co/admin/_ | Repositorio centralizado (acceso interno) |

## Tipos de documentos a recopilar (scope Fase 1)

- Acuerdos superiores
- Resoluciones superiores
- Resoluciones rectorales
- Acuerdos académicos
- Resoluciones académicas
- Resoluciones de consejo de facultad
- Acuerdo CESU
- Planes (plan de desarrollo, plan de acción)
- Ordenanzas de asambleas
- Leyes (aplicables a la universidad)
- CONPES (relacionados con educación superior)
- Actas de consejo superior
- Actas de consejo académico

## Scope temporal

Documentos desde **2015** en adelante (definido en la propuesta de grado).

## Formatos esperados

- PDF (texto seleccionable) — extracción directa con PyMuPDF/pdfplumber
- PDF escaneado / imágenes — requiere OCR (Tesseract u otro)
- Archivos de Office (.docx, .xlsx) — python-docx / openpyxl
- Documentos en la tabla `si_normatividad` de SIRIUS con campo `archivo_soporte` (ruta física) o `href` (URL pública)

## Nota sobre dispersión del corpus

Los documentos están distribuidos en múltiples URLs dentro del portal institucional, sin un endpoint único de descarga masiva. La Fase 1 requiere scraping o descarga manual sistematizada. SIRIUS centraliza la metadata pero los archivos físicos pueden estar en rutas del servidor.
