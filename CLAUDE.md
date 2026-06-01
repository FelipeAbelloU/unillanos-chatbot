# CLAUDE.md — Proyecto5

## Contexto persistente — leer al inicio de cada sesión

Al iniciar una sesión en este proyecto, leer los siguientes archivos de memoria:

- `../memory/MEMORY.md` (índice)
- `../memory/user_cristian.md`
- `../memory/project_thesis_overview.md`
- `../memory/project_methodology_phases.md`
- `../memory/project_proyecto5_structure.md`

---

## Identidad del proyecto

**Proyecto5** es un asistente conversacional para normativa universitaria de la Universidad de los Llanos (Unillanos).
Es un proyecto **completamente independiente**. No depende de ningún otro proyecto del directorio `Tesis/`.

**Directorio raíz:** `C:\Users\Equipo\OneDrive\Documentos\Tesis\Proyecto5\`

Todo el trabajo de este proyecto vive aquí. Nunca crear archivos fuera de esta carpeta en nombre de Proyecto5.

---

## Arquitectura: solo fine-tuning

El sistema **no usa RAG**. El modelo aprende la normativa durante el entrenamiento (fine-tuning).

Pipeline completo:

```
PDFs (carpeta ../PDF)
  ↓ scripts/extract_text.py
texto plano en data/extracted/
  ↓ scripts/build_dataset.py
pares QA en data/dataset/dataset_alpaca.json
  ↓ [entrenamiento externo — herramienta separada]
checkpoint del modelo (formato HuggingFace)
  ↓ config/config.yaml → model.checkpoint_path
Proyecto5 carga el checkpoint y expone API + UI
```

---

## Estructura del proyecto

```
Proyecto5/
├── CLAUDE.md
├── config/
│   ├── config.yaml         ← configuración principal (checkpoint_path, device, etc.)
│   ├── laptop.yaml         ← perfil i5-7200U / MX130 (CPU, max_new_tokens: 256)
│   └── workstation.yaml    ← perfil RTX 4090i (CUDA, max_new_tokens: 512)
├── src/
│   ├── config_loader.py    ← carga config.yaml con merge de perfiles
│   ├── factory.py          ← create_pipeline() — punto de entrada central
│   ├── extraction/         ← PDF → texto plano
│   │   ├── pdf_digital.py  ← pdfplumber + pypdf (PDFs con texto seleccionable)
│   │   └── pdf_scanned.py  ← EasyOCR + Tesseract (PDFs escaneados/imagen)
│   ├── dataset/            ← texto → pares QA → JSON para fine-tuning
│   │   ├── cleaner.py      ← limpieza y normalización de texto
│   │   └── qa_builder.py   ← genera pares QA en formato alpaca/sharegpt
│   ├── inference/          ← carga y ejecuta el modelo fine-tuneado
│   │   └── model.py        ← FineTunedModel (HuggingFace AutoModelForCausalLM)
│   ├── chat/               ← pipeline conversacional
│   │   ├── pipeline.py     ← ChatPipeline.query() → respuesta del modelo
│   │   └── history.py      ← ConversationHistory
│   └── api/                ← FastAPI REST para integración con SIRIUS
│       └── app.py          ← endpoints /query, /health, /reset
├── ui/
│   └── app.py              ← interfaz web Gradio (localhost:7860)
├── scripts/
│   ├── extract_text.py     ← paso 1: PDF → .txt
│   ├── build_dataset.py    ← paso 2: .txt → dataset QA JSON
│   └── chat_cli.py         ← chat por terminal con el modelo
├── data/
│   ├── extracted/          ← textos planos extraídos (generados por extract_text.py)
│   └── dataset/            ← dataset QA JSON (generado por build_dataset.py)
├── venv/                   ← entorno virtual Python
├── requirements.txt
└── .env.example
```

---

## Comandos frecuentes

```bat
# Activar entorno virtual
venv\Scripts\activate

# Paso 1: extraer texto de los PDFs
python scripts\extract_text.py

# Paso 2: generar dataset de fine-tuning
python scripts\build_dataset.py --mode heuristic

# Chat por terminal (requiere checkpoint configurado)
python scripts\chat_cli.py

# Interfaz web Gradio (localhost:7860)
python ui\app.py

# API REST (localhost:8000, docs en /docs)
uvicorn src.api.app:app --reload --port 8000
```

---

## Configurar el modelo fine-tuneado

Editar `config/config.yaml`:
```yaml
model:
  checkpoint_path: "ruta/al/checkpoint"  # directorio con config.json, tokenizer, etc.
  device: cpu    # o cuda en workstation
```

---

## Hardware y perfiles

| Perfil | Máquina | GPU | Uso |
|--------|---------|-----|-----|
| `laptop` | i5-7200U, 16 GB, MX130 | CPU only | desarrollo, extracción, inferencia ligera |
| `workstation` | Xeon Silver 4208, 32 GB, RTX 4090i | CUDA 24 GB | inferencia con modelo completo |

---

## Notas importantes

- No hay ChromaDB ni vector store — el modelo fine-tuneado tiene el conocimiento internalizado.
- Mientras no haya checkpoint entrenado, el chat muestra un mensaje explicativo.
- El dataset en `data/dataset/` es el insumo para el entrenamiento (herramienta externa).
- La API FastAPI está diseñada como microservicio para SIRIUS.
