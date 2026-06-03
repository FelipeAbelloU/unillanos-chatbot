---
name: Estructura y estado de CANUTO
description: Arquitectura, archivos clave y estado actual del proyecto CANUTO (antes Proyecto5)
metadata:
  type: project
---

**Nombre del proyecto:** CANUTO (Chatbot Asistente Normativo Universidad de los Llanos)
**Ruta actual en disco:** `C:\Users\Equipo\OneDrive\Documentos\Tesis\Proyecto5\`
**Nota:** El usuario quiere renombrar la carpeta a `CANUTO`. Hasta que lo haga, la ruta sigue siendo Proyecto5.
**Repositorio GitHub:** https://github.com/FelipeAbelloU/unillanos-chatbot

**IMPORTANTE:** El proyecto es completamente independiente. No mezclar con ningún otro proyecto de Tesis.

## Estado actual (2026-06-03)

- Fases 1-4 completadas
- Fase 5 (entrenamiento): modelo v2 entrenado y desplegado localmente
  - Checkpoint: `data/checkpoints/unillanos-v2/` (4 shards, 2.89 GB)
  - Config: `checkpoint_path: "data/checkpoints/unillanos-v2"`
- Interfaz web Django (CANUTO) funcional en `ui/django_chatbot/`
- Evaluación realizada: alucinaciones parcialmente reducidas vs v1

## Arquitectura

**Solo fine-tuning** — sin RAG, sin ChromaDB.
Modelo base: Qwen/Qwen2.5-1.5B-Instruct (laptop i5-7200U)
Entrenamiento: Google Colab T4, LoRA rank=8, 3 épocas

## Estructura de archivos

```
CANUTO/ (carpeta en disco: Proyecto5/)
├── config/
│   ├── config.yaml         ← checkpoint_path: "data/checkpoints/unillanos-v2"
│   ├── laptop.yaml         ← CPU, max_new_tokens: 256
│   └── workstation.yaml    ← CUDA (RTX 4090i)
├── src/
│   ├── factory.py          ← create_pipeline() — punto de entrada
│   ├── inference/model.py  ← FineTunedModel (HF AutoModelForCausalLM)
│   ├── chat/pipeline.py    ← ChatPipeline.query()
│   ├── extraction/         ← pdf_digital.py + pdf_scanned.py
│   ├── dataset/            ← cleaner.py + qa_builder.py
│   └── api/app.py          ← FastAPI: /query, /health, /reset
├── ui/
│   ├── app.py              ← Gradio (alternativa, localhost:7860)
│   └── django_chatbot/     ← UI PRINCIPAL Django
│       └── run.py          ← python ui/django_chatbot/run.py → localhost:8000
├── scripts/
│   ├── extract_text.py     ← PDF → .txt
│   ├── build_dataset.py    ← .txt → dataset QA JSON
│   ├── add_knowledge.py    ← agrega pares curados al dataset
│   ├── colab_train.py      ← guía entrenamiento Google Colab
│   ├── train.py            ← entrenamiento CPU (alternativa lenta)
│   ├── test_model.py       ← pruebas automáticas 8 preguntas
│   └── chat_cli.py         ← chat terminal
├── data/
│   ├── extracted/          ← textos .txt extraídos de PDFs (35 archivos)
│   ├── dataset/            ← dataset_alpaca.json (387 pares QA)
│   └── checkpoints/        ← unillanos-v1/, unillanos-v2/ (gitignored)
├── PDF/                    ← 34 PDFs de normativa Unillanos
├── notas y ayudas/         ← guía_correr_canuto.txt y scripts de referencia
└── requirements.txt
```

## Comandos principales

```bat
venv\Scripts\activate

# Interfaz web CANUTO (PRINCIPAL)
python ui\django_chatbot\run.py       → http://127.0.0.1:8000/

# Chat terminal
python scripts\chat_cli.py

# Reconstruir dataset y reentrenar
python scripts\build_dataset.py --mode heuristic
python scripts\add_knowledge.py
# → subir dataset_alpaca.json a Colab y correr colab_train.py

# Pruebas del modelo
python scripts\test_model.py          → resultados en data/test_results.txt
```

## Próximos pasos pendientes

1. Renombrar carpeta Proyecto5 → CANUTO (el usuario lo hace manualmente)
2. Reentrenar v3 con 387 pares (3 nuevos sobre matrícula por semestre)
3. Evaluación formal con test set verificado (para la tesis — Fase 6)
4. Integración con SIRIUS cuando esté disponible el acceso

**Why:** Documenta el estado real del proyecto para que futuras sesiones sepan exactamente dónde estamos.
**How to apply:** Verificar config.yaml y que el checkpoint exista antes de sugerir trabajo nuevo.
