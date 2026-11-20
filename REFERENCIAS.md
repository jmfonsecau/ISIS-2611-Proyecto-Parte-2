# Referencias y uso de recursos - Parte 2 (Colab)

## Modelos preentrenados

| Base | Revisión | Uso | Licencia |
|---|---|---|---|
| [paraphrase-multilingual-MiniLM-L12-v2](https://huggingface.co/sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2) | `e8f8c211226b894fcb81acc59f3b34ba3efd5f42` | Encoder fijo con MLP en el modelo 1; fine-tuning con cabeza lineal en 2 y 3. | Apache-2.0 |
| [FacebookAI/xlm-roberta-base](https://huggingface.co/FacebookAI/xlm-roberta-base) | `e73636d4f797dec63c3081bb6ed5c7b0bb3f2089` | Encoder y cabeza de clasificación; embeddings fijos en el modelo 4. | MIT |

| [FacebookAI/xlm-roberta-large](https://huggingface.co/FacebookAI/xlm-roberta-large) | `c23d21b0620b635a76227c604d44e43a9f0ee389` | Encoder de 24 capas y cabeza de clasificación del modelo 6; embeddings fijos. | MIT |

Se incluyen `LICENSE_MODELOS_APACHE-2.0.txt` y `LICENSE_XLMR_MIT.txt`. Los paquetes conservan también los textos de licencia. Se usan encoders para clasificar, sin generación de texto.

Publicaciones: Reimers y Gurevych (2019), [Sentence-BERT](https://aclanthology.org/D19-1410/); Conneau et al. (2020), [Unsupervised Cross-lingual Representation Learning at Scale](https://aclanthology.org/2020.acl-main.747/).

## Datos y aumento

Solo se usan los CSV de la Parte 2. No se añaden corpus externos ni etiquetas de eval. La partición conserva 16.373 reseñas de ajuste y 4.093 de validación, con grupos de textos similares y estratificación por clase e idioma estimado. Se incluye el archivo completo y su huella para reproducirlo sin recalcularlo con otras versiones.

Los idiomas se estimaron con [langid 1.1.6](https://github.com/saffsd/langid.py), licencia BSD-2-Clause, y reglas de palabras y segmentos para las mezclas. No son anotaciones oficiales ni probabilidades calibradas de sentimiento.

La sustitución léxica se inspira en Wei y Zou (2019), [EDA](https://aclanthology.org/D19-1670/). Las listas y el código de sustitución son propios. Se cambia como máximo una expresión por reseña elegible, con probabilidad 0,30 por época y semilla 42. Se conservan negaciones, conectores y números, se rechazan cambios invisibles para el tokenizador y colisiones conocidas con otra etiqueta. No se garantiza equivalencia semántica en todos los contextos.

## Protocolo y reproducción

Las variantes de desarrollo se construyen únicamente con la parte de ajuste. Validación y eval permanecen sin aumento. La época se elige por pérdida de validación. Después se inicializa otra red desde la base pública y se ajusta con todo train durante las épocas seleccionadas, manteniendo el horizonte original del calendario.

Los resultados de desarrollo conservan predicciones por ID. Las métricas mostradas después del ajuste final provienen de esos registros, no de evaluar el modelo final sobre ejemplos aprendidos.

Se fijan Transformers 4.57.3, Tokenizers 0.22.2, Hugging Face Hub 0.36.2 y langid 1.1.6. Se conserva el PyTorch del runtime de Colab y se registran todas las versiones relevantes, Python, CUDA y GPU. Los checkpoints exigen el mismo entorno para continuar. PyTorch [no garantiza igualdad entre versiones y plataformas](https://docs.pytorch.org/docs/stable/notes/randomness.html), incluso con la misma semilla.

Referencias de implementación: [Transformers](https://huggingface.co/docs/transformers/v4.57.3/en/model_doc/xlm-roberta), Apache-2.0; [PyTorch y checkpoints](https://docs.pytorch.org/tutorials/recipes/recipes/saving_and_loading_a_general_checkpoint.html), BSD-3-Clause. NumPy, pandas, SciPy, scikit-learn y Matplotlib se usan para datos, métricas y figuras.

## Uso de IA

Se utilizó IA para organizar y corregir el código, unificar el notebook, revisar el guardado y recuperación, proponer las reglas de aumento y redactar documentación. El grupo debe entender y sustentar el trabajo.

No se usa IA generativa para etiquetar eval. Los CSV de envío provienen de la red guardada y verificada. Las nuevas métricas solo se registrarán después de ejecutar cada experimento en Colab.

## Experimentos adicionales

El modelo 5 promedia probabilidades de las redes finales 4 y 2 con pesos 0,90 y 0,10. Se eligieron entre seis opciones en la validación de desarrollo; esa elección puede sobreestimar su resultado. El ZIP conserva los dos paquetes completos y el manifiesto de combinación.

El modelo 6 prueba XLM-R large sin corpus adicional. Usa precisión mixta y [recomputación de activaciones](https://huggingface.co/docs/transformers/v4.57.3/en/main_classes/model#transformers.PreTrainedModel.gradient_checkpointing_enable) para reducir memoria. Las métricas se registran después de ejecutar; el score público requiere un envío real a Kaggle.

## Historial de las primeras corridas

Antes del flujo actual en L4 se generaron tres CSV históricos: MiniLM fijo + MLP (0.70923), MiniLM sin aumento (0.85707) y MiniLM con aumento (0.85903). El segundo se ejecutó en MPS local y el tercero en T4 con versiones diferentes; esa comparación no aislaba únicamente el aumento. Los experimentos actuales 2 y 3 sí registran el mismo entorno e hiperparámetros.

El notebook conserva la explicación del cambio a Colab y distingue estos scores. Los paquetes históricos publicados en Releases no sustituyen a los paquetes actuales `modelo_v1_*.pt`.
