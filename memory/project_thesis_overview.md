---
name: Visión general del proyecto de tesis
description: Título, problema, objetivos, entregables, estado actual y restricciones del proyecto de grado
type: project
---

**Título:** Implementación de un asistente inteligente orientado a la gestión del conocimiento normativo basado en modelos de lenguaje generativo para la Universidad de los Llanos

**Problema:** Los documentos normativos de la Universidad de los Llanos (resoluciones, acuerdos, reglamentos, manuales) están dispersos en múltiples portales institucionales, presentados en formatos heterogéneos (PDF, documentos escaneados, imágenes, Office). No existe un sistema unificado de búsqueda semántica. La comunidad universitaria pierde tiempo buscando normativa vigente, usa documentos derogados por error y satura los canales de atención.

**Objetivo general:** Implementar un asistente inteligente (chatbot) que facilite la gestión del conocimiento normativo de la Universidad de los Llanos utilizando modelos de lenguaje generativo.

**Objetivos específicos:**
1. Consolidar el corpus de documentos normativos: recolección, transformación, preprocesamiento y normalización en texto plano.
2. Diseñar y entrenar un modelo de lenguaje generativo adaptado al contexto normativo para responder consultas específicas.
3. Desarrollar una interfaz de usuario integrada al sistema SIRIUS existente.
4. Evaluar el desempeño del asistente con métricas cuantitativas (BLEU, similitud coseno) y pruebas cualitativas con usuarios.

**Entregables:**
- Artículo científico sobre aplicación de NLP/ML en resoluciones académicas
- Prototipo funcional (MVP) del asistente inteligente integrado a SIRIUS
- Trabajo de grado sustentado y aprobado
- Ponencia/póster en evento académico nacional o internacional

**Duración:** 6 meses (inicio aproximado: 2026)
**Metodología:** FDD (Feature-Driven Development) — 6 fases secuenciales

**Arquitectura decidida (2026-05-29):**
- **Solo fine-tuning** (sin RAG): el modelo aprende la normativa durante el entrenamiento
- **Modelo base recomendado:** `Qwen/Qwen2.5-7B-Instruct` (multilingual, Apache 2.0, LLaMA-Factory compatible)
- El fine-tuning se hace con una herramienta externa (completamente independiente de Proyecto5)
- **Proyecto5/**: sistema completo de extracción + generación de dataset + inferencia + API + UI

**Estado actual (2026-05-29):**
- ✅ Propuesta de grado aprobada
- ✅ Proyecto5 reescrito con arquitectura fine-tuning only (código funcional)
- ✅ 20 PDFs disponibles en `Tesis/PDF/` (carpeta raíz, no dentro de Proyecto5)
- ❌ Texto extraído de PDFs: pendiente (correr `python scripts/extract_text.py`)
- ❌ Dataset QA: pendiente (correr `python scripts/build_dataset.py`)
- ❌ Modelo fine-tuneado: pendiente (requiere dataset + entrenamiento externo)

**Why:** El chatbot busca reducir desinformación, carga operativa y tiempo de búsqueda de normativa para estudiantes, docentes y personal administrativo de Unillanos.

**How to apply:** La próxima tarea concreta es ejecutar la extracción de texto de los 20 PDFs y revisar la calidad. Luego generar el dataset QA. El entrenamiento se hace fuera de Proyecto5.
