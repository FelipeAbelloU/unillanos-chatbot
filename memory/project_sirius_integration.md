---
name: Sistema SIRIUS y punto de integración del chatbot
description: Stack técnico de SIRIUS, tablas clave de la BD y estrategia para integrar el chatbot al sistema existente
type: project
---

## Qué es SIRIUS

SIRIUS es el sistema de información institucional de la Universidad de los Llanos. Funciona como repositorio de normatividad, información académica, programas, personas y dependencias. Centraliza acuerdos superiores, resoluciones rectorales, acuerdos académicos, resoluciones académicas, resoluciones de consejo de facultad, acuerdos CESU, planes, ordenanzas, leyes, CONPES, y actas de consejos.

**URL admin:** https://sirius.unillanos.edu.co/admin/_  
**Stack tecnológico:** Django 2.2.10 — Python 3.5 — PostgreSQL

## Restricción crítica

Python 3.5 es incompatible con LLaMA-Factory y la mayoría de librerías modernas de LLMs (requieren Python 3.11+). La integración debe ser via **microservicio separado**: el modelo LLM corre en un servicio independiente (Python 3.11+) y SIRIUS lo consume a través de una API REST HTTP.

## Tablas clave para el chatbot

### `si_normatividad` — tabla principal de documentos normativos
| Campo | Tipo | Descripción |
|-------|------|-------------|
| id | integer | PK |
| nombre | varchar(50) | Nombre/código del documento (ej. "Acuerdo Superior 001") |
| descripcion | text | Resumen del contenido |
| fecha | date | Fecha de expedición |
| year | integer | Año |
| numero | integer | Número del documento |
| archivo_soporte | varchar(100) | Ruta al archivo físico |
| href | text | URL pública del documento |
| estado_id | FK → si_estado | Vigente / Derogado / etc. |
| tpnormatividad_id | FK → si_tpnormatividad | Tipo de documento |

### `si_tpnormatividad` — tipos de documentos normativos
Categorías: acuerdo superior, resolución superior, resolución rectoral, acuerdo académico, resolución académica, resolución consejo de facultad, acuerdo CESU, plan, ordenanza, ley, CONPES, acta.

### `si_normatividaddependencia` — normativa por dependencia
Relaciona cada documento con la(s) dependencia(s) que lo emiten o aplican.

### `si_normatividadplanestudio` — normativa por plan de estudios
Relaciona documentos normativos con planes de estudio específicos.

## Estrategia de integración recomendada

```
[Usuario] → [SIRIUS (Django 2.2.10/Python 3.5)]
                    ↓ HTTP request
           [API del chatbot (FastAPI/Python 3.11+)]
                    ↓
           [Modelo LLM fine-tuneado]
                    ↓
           [Base de conocimiento (corpus normalizado)]
```

La Fase 4 del proyecto incluye específicamente "desarrollo de la interfaz de integración e incorporación mediante la creación de una API en el software SIRIUS".

## Archivos del MER SIRIUS

El MER completo está en: `C:\Users\Equipo\OneDrive\Documentos\Tesis\SIRIUS_CLARO.pdf` y `MER SIRIUS.pdf`. El esquema tiene dos grandes módulos:
- **Prefijo `si_`**: Sistema de información (normatividad, personas, programas, dependencias, trámites)
- **Prefijo `dgi_`**: Dirección de Investigación (proyectos, grupos, productos, convocatorias)

**Why:** SIRIUS es el sistema donde vivirá el chatbot. Entender su estructura evita proponer integraciones incompatibles con el stack legacy.  
**How to apply:** Cuando se trabaje en la integración (Fase 4), diseñar el chatbot como microservicio independiente y conectarlo a SIRIUS via HTTP, nunca como código Python 3.11+ dentro de Django 2.2.10.
