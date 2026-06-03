---
name: Stack técnico de LLMs y hardware del proyecto
description: Hardware disponible, modelo base seleccionado y técnicas de fine-tuning
type: reference
---

## Hardware disponible

### Laptop del estudiante (desarrollo / extracción / pruebas)
- **CPU:** Intel Core i5-7200U @ 2.50 GHz
- **RAM:** 16 GB DDR4
- **GPU:** GeForce MX150 (NO apta para LLMs)
- **Uso:** desarrollo, extracción de texto, generación de datasets, pruebas ligeras

### Workstation universitaria (entrenamiento e inferencia)
- **CPU:** Intel Xeon Silver 4208 (8 cores)
- **RAM:** 32 GB DDR4-2933 ECC
- **GPU:** **RTX 4090i — 24 GB VRAM** — para fine-tuning e inferencia con modelos 7B
- **Uso:** entrenamiento del modelo, inferencia con checkpoint completo

## Modelos seleccionados

### Laptop (desarrollo actual) — `Qwen/Qwen2.5-1.5B-Instruct`

| Propiedad | Valor |
|-----------|-------|
| HuggingFace ID | `Qwen/Qwen2.5-1.5B-Instruct` |
| Parámetros | 1.54B |
| RAM (CPU, FP32) | ~6.2 GB |
| Velocidad en i5-7200U | ~15 tok/s estimado |
| Idioma español | ✅ bueno |
| Vocabulario | 152K tokens |
| Licencia | Apache 2.0 |

**Por qué 1.5B para laptop:** elegido sobre 3B porque cabe mejor en el i5-7200U, el smoke test confirmó ~53 s/paso. El modelo ya está descargado en `C:\Users\Equipo\.cache\huggingface\hub\models--Qwen--Qwen2.5-1.5B-Instruct\` (2.88 GB).

**Entrenamiento realizado (2026-05-30):** fine-tuning completado en Google Colab T4, LoRA rank=8, 3 épocas, 228 ejemplos, ~20 minutos.
**Checkpoint desplegado:** `data/checkpoints/unillanos-v1/` (4 shards safetensors, ~2.94 GB BF16→FP32 en CPU).
**Estado:** funcional pero con alucinaciones. Ver [[project_evaluation_findings]] para detalles y plan de mejora.

### Workstation (producción futura) — `Qwen/Qwen2.5-7B-Instruct`

| Propiedad | Valor |
|-----------|-------|
| HuggingFace ID | `Qwen/Qwen2.5-7B-Instruct` |
| Parámetros | 7.61B |
| VRAM (BF16) | ~15 GB |
| VRAM (QLoRA 4-bit) | ~4 GB |
| Idioma español | ✅ excelente (29+ idiomas) |
| Vocabulario | 152K tokens |
| Contexto | 128K tokens |
| Licencia | Apache 2.0 |

**Transición:** cuando el workstation esté disponible, cambiar `checkpoint_path` en config.yaml. El código no requiere cambios.

**Decisión del estudiante (2026-05-29):** usar 3B en laptop para desarrollo inmediato, 7B en workstation para el modelo final de la tesis.

## Técnica de fine-tuning

- **SFT + LoRA** (Supervised Fine-Tuning con Low-Rank Adapters)
- LoRA rank: 8–16, alpha: 16–32
- QLoRA 4-bit en workstation (reduce VRAM ~75%)
- El fine-tuning se realiza con una herramienta externa — completamente separada de Proyecto5
- Formato de dataset: **alpaca** (instruction / input / output) o **sharegpt** (conversations)

## Métricas de evaluación (Fase 6)

- BLEU — calidad de generación de texto
- Similitud coseno — relevancia semántica
- Pruebas qualitativas con usuarios de Unillanos

## Nota sobre la decisión arquitectónica

Se descartó RAG (Retrieval-Augmented Generation) por decisión del estudiante:
- El fine-tuning puro alinea mejor con el objetivo 2 de la propuesta ("entrenar un modelo generativo")
- Tradeoff conocido: sin RAG, las citas de documentos fuente son menos verificables; cuando el corpus crezca con SIRIUS se requerirá re-entrenamiento en lugar de re-indexación
- Esta decisión debe documentarse en la tesis con justificación técnica
