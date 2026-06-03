---
name: Fases metodológicas FDD del proyecto
description: Las 6 etapas del proyecto con actividades, entregables y estado de completitud
type: project
---

Metodología: **Feature-Driven Development (FDD)** — 6 fases secuenciales de ~1 mes cada una.

| Fase | Nombre | Estado (2026-05-30) |
|------|--------|---------------------|
| 1 | Recolección y selección de datos | ✅ **COMPLETADA** — 20 PDFs disponibles en `Tesis/PDF/` |
| 2 | Construcción y preparación del dataset | ✅ **COMPLETADA** — 228 pares QA en `data/dataset/dataset_alpaca.json` |
| 3 | Estudio y selección de técnicas | ✅ **DECIDIDA** — fine-tuning solo (sin RAG) |
| 4 | Diseño e implementación del sistema | ✅ **COMPLETADA** — Proyecto5 funcional con nueva arquitectura |
| 5 | Entrenamiento y ajuste del modelo | 🔄 **EN PROGRESO** — entrenamiento completado en Colab, pendiente descargar checkpoint |
| 6 | Validación y evaluación | ❌ Pendiente |

---

### Fase 1 — Recolección y selección de datos ✅
**Entregable:** 20 PDFs en `C:\Users\Equipo\OneDrive\Documentos\Tesis\PDF\`
Incluye resoluciones rectorales, resoluciones académicas, acuerdos y convocatorias de Unillanos 2023–2026.
Pendiente a futuro: integración con SIRIUS para corpus completo.

### Fase 2 — Construcción y preparación del dataset ✅
**Entregable:** `data/dataset/dataset_alpaca.json` — 228 pares QA en formato Alpaca
- Tokens promedio: 347 | Tokens máximo: 680 | Ninguno excede seq_len=750
- Generado con `scripts/build_dataset.py --mode heuristic` sobre los 20 PDFs

**Nota importante:** los pares QA fueron generados heurísticamente. Para mejorar la calidad del modelo en iteraciones futuras, deben revisarse y ampliarse con preguntas reales de la comunidad universitaria.

### Fase 3 — Técnica de modelado ✅ (decisión tomada)
**Decisión:** Fine-tuning supervisado (SFT) + LoRA sobre `Qwen/Qwen2.5-7B-Instruct`
- No se usa RAG (el modelo internaliza el conocimiento)
- El fine-tuning se hace con una herramienta externa completamente separada de Proyecto5
- Técnica: SFT + LoRA (rank 8-16, alpha 16-32) con QLoRA 4-bit para caber en 24GB VRAM

### Fase 4 — Diseño e implementación ✅
**Entregable:** Proyecto5 con pipeline completo:
- Extracción (src/extraction/): pdfplumber, pypdf, EasyOCR, Tesseract
- Dataset (src/dataset/): limpieza, generación QA, formato alpaca/sharegpt
- Inferencia (src/inference/): carga de checkpoint HuggingFace
- Chat (src/chat/): pipeline conversacional sin RAG
- API REST (src/api/): FastAPI para SIRIUS
- UI (ui/): Gradio

### Fase 5 — Entrenamiento 🔄 EN PROGRESO (iteración 1 completa, mejoras pendientes)

**Lo que se completó (2026-05-30):**
- Entrenamiento en Google Colab T4: Qwen2.5-1.5B-Instruct, LoRA rank=8, 3 épocas, 228 ejemplos, ~20 min
- Checkpoint descargado y desplegado en `data/checkpoints/unillanos-v1/` (4 shards, ~2.94 GB)
- `config/config.yaml` actualizado: `checkpoint_path: "data/checkpoints/unillanos-v1"`
- Pipeline funcional: `python scripts/chat_cli.py` responde

**Evaluación con `scripts/test_model.py` (8 preguntas):**
- 0/8 respuestas citan correctamente el documento fuente
- 5/8 alucinaciones significativas (inventa números de resoluciones, procesos)
- Caso crítico: pregunta fuera de dominio → modelo inventó precio de matrícula ("2.300.000 pesos")
- Resultados completos en `data/test_results.txt`

**Causa raíz identificada:**
El modelo aprendió el formato (citar resoluciones, hacer listas) pero no internalizó el contenido.
228 ejemplos heurísticos son insuficientes para 1.5B params. Sin ejemplos de preguntas fuera de dominio.

**Próximos pasos — en orden de prioridad:**
1. **Mejorar system prompt** (inmediato, sin reentrenar) — forzar honestidad sobre incertidumbre
2. **Reconstruir dataset** — respuestas con fuente exacta siempre, ejemplos fuera de dominio
3. **Reentrenar en Colab** (~20 min) con el dataset mejorado
4. **Evaluación formal** — test set con respuestas correctas conocidas para medir precisión (tesis)

### Fase 6 — Validación ❌
**Actividades:** BLEU, similitud coseno → pruebas cualitativas con usuarios → documentación final

---

**How to apply:** El modelo ya funciona. La siguiente tarea es mejorar la calidad: primero el system prompt (sin reentrenar), luego reconstruir el dataset con citas explícitas y ejemplos fuera de dominio, luego reentrenar en Colab.
