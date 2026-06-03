---
name: Directorio de trabajo correcto para la tesis
description: Proyecto5 es completamente independiente. LLaMA-Factory es un proyecto separado que no tiene nada que ver con Proyecto5.
type: feedback
---
**Proyecto5 es 100% independiente.** No mezclar con LLaMA-Factory ni con ningún otro proyecto del directorio Tesis/.

**Why:** El usuario lo confirmó explícitamente: LLaMA-Factory es un proyecto aparte de la tesis, no la herramienta de fine-tuning de Proyecto5. Proyecto5 maneja su propio fine-tuning internamente.

**How to apply:** Al trabajar en Proyecto5, nunca mencionar ni referenciar LLaMA-Factory como parte del flujo. Todo el código (extracción, dataset, fine-tuning, inferencia, API, UI) vive en `Tesis/Proyecto5/`. Los archivos nuevos siempre bajo `Tesis/Proyecto5/`.
