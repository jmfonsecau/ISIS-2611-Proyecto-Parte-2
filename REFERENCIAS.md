# Referencias y Declaración de Uso de Recursos Externos — Proyecto Parte 2

## 1. Modelos Preentrenados y Licencias

- **`sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2`**
  - **Autores / Organización:** Nils Reimers y Iryna Gurevych (UKP Lab, TU Darmstadt) / Sentence-Transformers.
  - **Enlace:** https://huggingface.co/sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2
  - **Revisión fijada:** `e8f8c211226b894fcb81acc59f3b34ba3efd5f42`.
  - **Licencia:** Apache License 2.0.
  - **Uso en el proyecto:** Base multilingüe utilizada tanto en el Modelo 1 (representaciones congeladas de 384 dimensiones + MLP) como en el Modelo 2 (*fine-tuning* conjunto de encoder + cabeza lineal mediante `AutoModel`, `AutoTokenizer` y `get_linear_schedule_with_warmup`), empaquetados localmente para inferencia sin conexión.
  - **Documentación de fine-tuning consultada:** https://huggingface.co/docs/transformers/main/en/training
  - **Publicación asociada:** Reimers, N., & Gurevych, I. (2019). *Sentence-BERT: Sentence Embeddings using Siamese BERT-Networks*. En Proceedings of the 2019 Conference on Empirical Methods in Natural Language Processing (EMNLP). https://arxiv.org/abs/1908.10084

## 2. Librerías de Software y Herramientas

- **PyTorch (`torch 2.9.1`):** Entrenamiento e inferencia de redes neuronales profundas (MLP, funciones de pérdida, optimizador `AdamW`, aceleración MPS en Apple Silicon). Licencia BSD-3-Clause. https://pytorch.org/
- **Hugging Face Transformers (`transformers 4.57.3`) y Sentence-Transformers (`sentence-transformers 5.1.2`):** Carga de tokenizadores y modelos transformer multilingües. Licencia Apache 2.0. https://huggingface.co/docs/transformers/
- **langid (`langid 1.1.6`):** Identificación local de idioma (`es`, `en`, `pt`, `fr`) sin llamadas a servicios externos para análisis exploratorio, partición estratificada y desglose de métricas por idioma. Autor: Marco Lui y Timothy Baldwin. Licencia BSD-2-Clause. https://github.com/saffsd/langid.py
- **scikit-learn (`scikit-learn`):** Partición estratificada por grupos (`StratifiedGroupKFold`) y cálculo de métricas (`accuracy_score`, `f1_score`, `classification_report`, `confusion_matrix`). Licencia BSD-3-Clause. https://scikit-learn.org/
- **NumPy, pandas y Matplotlib:** Manipulación de datos tabulares y visualización de curvas de entrenamiento y matrices de confusión.

## 3. Declaración de Uso de Inteligencia Artificial Generativa

- Se utilizó asistencia de IA generativa como apoyo de programación para estructurar el código de exploración, configurar la partición reproducible con comprobaciones de grupos y posibles cruces, implementar el entrenamiento en PyTorch, verificar la recarga offline del modelo empaquetado y redactar documentación técnica.
- **No se utilizó IA generativa para etiquetar directamente las reseñas de `eval.csv`.** Todas las predicciones enviadas a Kaggle provienen exclusivamente de la inferencia de los modelos entrenados en el notebook sobre los datos de entrenamiento.


## 4. Corrección y reproducción del Modelo 2

Se corrigieron la inferencia dependiente de globals, la ponderación de los
últimos microlotes y el calendario del ajuste final. La parada y recuperación
se documentan como eventos distintos. El paquete registra versiones de Python
y librerías, configuración, horizonte, revisión y huellas de los datos y de
particiones. Se probó también la recarga por defecto en CPU sin conexión.

El horizonte de warmup/decaimiento es de seis épocas, aunque se ejecuten solo
las seleccionadas; no implica que el ajuste con todo train tenga las mismas
actualizaciones que el de desarrollo. Las métricas reportadas son las de
validación de la configuración corregida, no una evaluación del modelo que
aprendió con todas las etiquetas.

La comparación frente al primer modelo cambia cabeza y normalización además
del entrenamiento del encoder; no constituye una ablación de un único factor.
Se usó IA como apoyo para revisar, corregir, probar y documentar estas decisiones.
No se usó IA generativa para etiquetar eval ni se entrenó con eval. El aumento
sigue pendiente. Los originales del segundo intento quedaron respaldados
fuera del repositorio antes de reemplazar el artefacto local.

### Versiones de la ejecución corregida

- torch: 2.9.1
- transformers: 4.57.3
- sentence-transformers: 5.1.2
- numpy: 2.5.5
- pandas: 3.1.1
- scikit-learn: 1.9.1
- Python: 3.12.13

Se corrigió también el falso rechazo de particiones por diferencias int32/int64. Se verificaron valores idénticos y se conserva el CSV original byte a byte.

## Distribución de los paquetes

Los modelos entrenados se comparten como adjuntos de GitHub Releases. [MODELOS.md](MODELOS.md) registra enlaces, procedencia, revisión, modificaciones y huellas SHA-256. Se acompaña el texto de [Apache-2.0](LICENSE_MODELOS_APACHE-2.0.txt) correspondiente al modelo preentrenado.
