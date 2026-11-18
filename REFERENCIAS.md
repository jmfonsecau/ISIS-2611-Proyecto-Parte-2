# Referencias y uso de recursos externos - Parte 2

## Modelo preentrenado

- Base: [sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2](https://huggingface.co/sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2).
- Organización: Sentence-Transformers / UKP Lab. Autores asociados: Nils Reimers e Iryna Gurevych.
- Revisión: `e8f8c211226b894fcb81acc59f3b34ba3efd5f42`.
- Licencia: **Apache-2.0**. Se incluye su texto en `LICENSE_MODELOS_APACHE-2.0.txt` y en las versiones de descarga.
- Uso: encoder congelado con MLP en el modelo 1; encoder ajustado y cabeza lineal en los modelos 2 y 3. Los paquetes contienen los pesos y el tokenizador.
- Publicación: Reimers y Gurevych (2019), [Sentence-BERT](https://arxiv.org/abs/1908.10084).

## Aumento de datos

Inspiración: Wei y Zou (2019), [EDA: Easy Data Augmentation Techniques for Boosting Performance on Text Classification Tasks](https://aclanthology.org/D19-1670/). Adaptamos la idea de sustitución léxica; no reutilizamos su código ni WordNet ni implementamos sus cuatro operaciones.

Las listas de equivalencias son propias, redactadas con asistencia de IA y revisadas en ejemplos de entrenamiento. Se cambia como máximo una expresión, con semilla 42 y probabilidad 0,30 por reseña elegible y época. Se rechazan cambios invisibles para el tokenizador y colisiones con otra etiqueta en entrenamiento. Conservar negaciones y números no prueba equivalencia semántica en todos los casos.

No se añadieron corpus externos ni se usaron APIs para generar o etiquetar reseñas. Las variantes de desarrollo heredan la partición de su original. Las variantes de todo train se construyen únicamente después de cerrar la evaluación.

## Entornos usados

La tabla distingue la referencia sin aumento de la ejecución que produjo el tercer CSV. Las versiones de una sesión de recarga no reemplazan este registro histórico.

| Componente | Modelo 2: MPS local | Modelo 3: T4 en Colab |
|---|---|---|
| Python | 3.12.13 | 3.13.16 |
| PyTorch | 2.9.1 | 2.14.1+cu130 |
| Transformers | 4.57.3 | 4.57.3 |
| Sentence-Transformers | 5.1.2 | 5.1.2 |
| NumPy | 2.5.5 | 2.4.6 |
| pandas | 3.1.1 | 2.2.3 |
| scikit-learn | 1.9.1 | 1.6.1 |

La detección de idioma usa **langid 1.1.6**, localmente. Los grupos y la separación originales se construyeron con scikit-learn 1.9.1 y SciPy 1.18.1. El paquete del tercer modelo no registró la versión de SciPy: no la inventamos. La ejecución normal carga la partición publicada y comprueba su huella; no necesita reconstruirla con otra versión.

La preparación de Colab alinea las dependencias registradas del tercer experimento. `REPRODUCIR_ENTORNO_03=True` solicita también PyTorch 2.14.1 y exige Python 3.13; se debe comprobar el build CUDA `+cu130`. Para inferencia pueden usarse otros entornos compatibles comprobando las predicciones contra el CSV. PyTorch [no garantiza igualdad entre plataformas y versiones](https://docs.pytorch.org/docs/2.9/notes/randomness.html), incluso con la misma semilla.

La comparación M2/M3 es descriptiva: cambia el aumento, pero también el dispositivo y parte del software. El notebook prepara una repetición de ambos casos en una misma GPU. Esa repetición no se ha ejecutado ni se atribuyen a ella los resultados publicados.

## Librerías y licencias

- [PyTorch](https://pytorch.org/): redes, pérdida, AdamW e inferencia. BSD-3-Clause.
- [Transformers](https://huggingface.co/docs/transformers/) y [Sentence-Transformers](https://www.sbert.net/): encoder y tokenizador. Apache-2.0.
- [langid](https://github.com/saffsd/langid.py): idioma estimado para exploración, partición y métricas por subgrupo. BSD-2-Clause; Marco Lui y Timothy Baldwin.
- [scikit-learn](https://scikit-learn.org/): agrupación, separación y métricas. BSD-3-Clause.
- NumPy, pandas, SciPy y Matplotlib: datos, operaciones numéricas y figuras.

## Partición y modelo entregado

Se mantiene la separación de **16.373 reseñas de entrenamiento y 4.093 de validación**. Las etiquetas de validación no actualizan pesos durante el desarrollo. La selección por pérdida recuperó la época 2 tras observar cuatro épocas. Se guardaron sus predicciones de validación por identificador.

Después se inicializó otro modelo desde los pesos públicos y se entrenó con **las 20.466 reseñas**, con aumento, durante **dos épocas**. Conservó el horizonte de seis épocas para warmup y decaimiento. Hubo 512 actualizaciones por época en desarrollo y 640 en el ajuste final. No se evaluó este modelo final sobre la validación que ya había aprendido.

`modelo_03_minilm_aumento.pt` contiene ese modelo final, con `entrenado_con_todo_train=True`, su configuración, versiones, huellas, historiales y las predicciones de desarrollo. En modo de recarga se muestran esas métricas históricas, no métricas artificialmente obtenidas con los pesos finales. El CSV correspondiente obtuvo **0.85903 público**, reportado por el grupo.

Las correcciones previas del modelo 2 preservan la ponderación por ejemplos en la acumulación de gradientes, el horizonte del calendario y la recarga sin depender de un dispositivo global. Los tres paquetes se comparten mediante GitHub Releases, con enlaces y huellas en `MODELOS.md`.

## Uso de IA generativa

Se utilizó IA como apoyo para organizar y depurar el código, revisar el entrenamiento y las reglas de aumento, comprobar la recarga y redactar documentación. El grupo debe entender y sustentar esas decisiones.

No se usó IA generativa para etiquetar `eval.csv`. Las predicciones de Kaggle provienen de los modelos guardados. No se entrenó con eval ni se utilizaron sus etiquetas.
