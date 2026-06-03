---
name: Hallazgos de evaluación del modelo v1
description: Resultados de las pruebas del modelo unillanos-v1 y plan de mejora identificado
metadata:
  type: project
---

Evaluación realizada el 2026-05-30 con `scripts/test_model.py` (8 preguntas).
Resultados completos en `Proyecto5/data/test_results.txt`.

## Resultados

| Pregunta | Veredicto |
|----------|-----------|
| ¿Qué es el requisito de segunda lengua? | ❌ Cita resolución inexistente (inventó "Rectoral 067/2025") |
| ¿Requisito segunda lengua para graduarse? | ❌ Cita resolución inventada, respuesta incorrecta |
| ¿Cómo funciona fraccionamiento matrícula? | ❌ Proceso completamente inventado |
| ¿Cuántos pagos permite fraccionamiento? | ⚠️ Número de resolución incorrecto (078 vs 074 real) |
| ¿Qué hacer con incapacidad médica? | ❌ Pasos inventados, no cita Res. 068/2025 |
| ¿Opciones de grado Fac. Ciencias Básicas? | ⚠️ Parcialmente correcto, mezcla programas |
| ¿Movilidad saliente requisitos? | ⚠️ Incompleta, fuente no citada |
| ¿Costo matrícula 2026-2? (fuera de dominio) | ❌ PELIGROSO: inventó "2.300.000 pesos" con falsa precisión |

**Resumen:** 0/8 fuentes correctas. 5/8 alucinaciones significativas.

## Causa raíz

- El modelo aprendió el **formato** (citar resoluciones, listas numeradas) pero no el **contenido**
- 228 ejemplos heurísticos insuficientes para un modelo de 1.5B params
- Sin ejemplos de preguntas fuera de dominio → inventa en lugar de decir "no sé"
- Documentos con nombres largos y números similares → el modelo los confunde

## Plan de mejora (en orden)

**Why:** La calidad actual no es aceptable para uso real ni para la tesis sin mejoras.
**How to apply:** Ejecutar en este orden antes de considerar la Fase 6 (validación formal).

1. **System prompt mejorado** (inmediato, 0 reentrenamiento)
   - Instrucción explícita: citar solo fuentes conocidas con certeza
   - Instrucción: si la pregunta está fuera del dominio, decirlo claramente
   - Archivo a modificar: `config/config.yaml` → `chat.system_prompt`

2. **Dataset v2** (reconstruir con calidad)
   - Cada respuesta debe incluir nombre exacto del documento y artículo
   - Añadir 10-20 pares de preguntas fuera de dominio con respuesta "No tengo esa información"
   - Revisar manualmente los 228 pares existentes para corregir errores
   - Script: `scripts/build_dataset.py` + revisión manual

3. **Reentrenamiento en Colab** (~20 min con el dataset mejorado)
   - Misma guía de 4 celdas, mismo proceso
   - Guardar como `unillanos-v2`

4. **Evaluación formal** (para la tesis — Fase 6)
   - Crear test set de ~30 preguntas con respuestas correctas verificadas en los PDF fuente
   - Métricas: exactitud de cita, BLEU, evaluación cualitativa
