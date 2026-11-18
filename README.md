# Proyecto ML - Parte 2

Proyecto de Aprendizaje de Máquina de la Universidad de los Andes. Buscamos clasificar reseñas multilingües de productos (español, inglés, portugués, francés y mezclas español-inglés con *code-switching*) como `positivo`, `negativo` o `neutral`, utilizando arquitecturas de aprendizaje profundo (*Deep Learning*) y aumento de datos.

**Estado:** dos envíos públicos confirmados (`0.70923` y `0.85707`) y tercer experimento con aumento completado en GPU Tesla T4 (`88,35 %` validación, `submission_03` listo para subir a Kaggle). Todavía faltan al menos dos envíos adicionales para completar los cinco requeridos.

<img width="399" height="498" alt="siseveeen" src="https://github.com/user-attachments/assets/777ef828-c3f6-41fc-8430-8fa648003116" />

## Integrantes

- Jose Manuel Fonseca — 202122456
- Julian Pinto
- Daniel Vergara

## Archivos

| Archivo | Contenido |
|---|---|
| `notebook_parte2.ipynb` | Exploración multilingüe, partición controlada, entrenamiento de redes profundas, evaluación por idioma y generación de envíos. |
| `train.csv` | 20.466 reseñas etiquetadas con las columnas `id`, `text` y `label`. |
| `eval.csv` | 6.781 reseñas sin etiqueta para generar las predicciones de Kaggle. |
| `sample_submission.csv` | Plantilla del formato de envío: `id,answer` (6.781 filas). |
| `particiones.csv` | Partición fija reproducible (`train`: 16.373, `valid`: 4.093) con idioma estimado y agrupación de textos casi idénticos. |
| `REFERENCIAS.md` | Registro de modelos preentrenados, licencias, librerías y declaración de uso de IA generativa. |

## Proceso y resultados actuales

Comprobamos calidad, clases, longitudes y coincidencias textuales. No encontramos nulos, textos vacíos ni duplicados exactos. Las etiquetas son positivo 7.486 (36,58 %), negativo 7.381 (36,06 %) y neutral 5.599 (27,36 %).

El idioma se estima localmente con langid y heurísticas de mezcla; no son anotaciones oficiales. Mantuvimos juntos tres pares de textos casi idénticos con etiquetas distintas. La separación fija por sentimiento e idioma estimado contiene **16.373 ejemplos de entrenamiento y 4.093 de validación**, con grupos sin cruces detectados y semilla 42. Esto reduce ciertos riesgos, no descarta toda fuga ni convierte la validación en una prueba ciega. La exploración incluyó train completo.

| Modelo | Aumento | Accuracy validación | F1 macro | Público registrado |
|---|---|---:|---:|---:|
| 1. MiniLM congelado + MLP | Sin aumento | 72,27 % | 73,78 % | 0.70923 |
| 2 corregido. Fine-tuning MiniLM + cabeza lineal | Sin aumento | **88,76 %** | **89,62 %** | **0.85707** |
| 3. MiniLM + sustitución léxica controlada | Con aumento | 88,35 % | 89,26 % | Pendiente |

El primer modelo identificaba bien neutral, pero tenía dificultades con positivo y negativo. El segundo modifica el encoder, la cabeza y la normalización: la comparación respalda esa configuración completa, no aísla el efecto de descongelar.

En el segundo se detuvo el ajuste en la época **4** y se recuperó la **2** por menor pérdida de validación (0.2314). Su accuracy de entrenamiento fue **92,37 %**, frente a **88,76 %** en validación: una brecha de **3,60 puntos**. No afirmamos haber eliminado el sobreajuste. El recall fue 90,45 % negativo, 99,38 % neutral y 79,16 % positivo.

### Idiomas estimados en validación

| Idioma | Ejemplos | Accuracy M1 | Accuracy M2 corregido | Diferencia |
|---|---:|---:|---:|---:|
| Español | 2,700 | 70,56 % | 90,70 % | 20,15 pp |
| Inglés | 470 | 77,66 % | 86,38 % | 8,72 pp |
| Portugués | 462 | 74,89 % | 86,15 % | 11,26 pp |
| Francés | 414 | 74,64 % | 81,88 % | 7,25 pp |
| Mezcla es/en probable | 22 | 54,55 % | 81,82 % | 27,27 pp |
| Otra mezcla probable | 15 | 73,33 % | 86,67 % | 13,33 pp |
| Incierto | 10 | 100,00 % | 100,00 % | 0,00 pp |

Los grupos pequeños no permiten conclusiones firmes; las diferencias tampoco demuestran que su causa sea exclusivamente el desbalance de idiomas.

El segundo envío obtuvo **85,707 % público**, frente a **70,923 %** del primero (+14,78 puntos). Queda 3,05 puntos por debajo del accuracy local; la diferencia entre conjuntos no demuestra por sí sola sobreajuste. `modelo_02_finetuning.pt` es el candidato de entrega por el mejor score público registrado; todavía no conocemos su resultado privado. Los scores fueron reportados por el equipo, no consultados automáticamente en Kaggle.

## Correcciones del segundo intento

- La comprobación de particiones compara valores exactos, sin rechazar grupos idénticos por int32 frente a int64. El CSV guardado no se reescribe si ya es válido.

- La inferencia recibe su tokenizador y obtiene el dispositivo desde los pesos. La recarga por defecto en CPU no depende del dispositivo global de entrenamiento.
- El encoder inicial se carga desde la carpeta local de la revisión fijada, evitando una consulta de metadatos inesperada de Transformers 4.57.3 en modo offline.
- La acumulación pondera todos los ejemplos por igual, incluidos los grupos incompletos 16+5 y 16+2, y normaliza antes de recortar los gradientes.
- Warmup y decaimiento mantienen un horizonte de **seis épocas** al reentrenar con todo train, aunque solo se ejecuten las **2 épocas seleccionadas**. El número de actualizaciones por época cambia por el tamaño del conjunto; no se comprime el calendario al detenerse.
- El paquete incluye Python y versiones de librerías, configuración, horizonte y huellas. Se verificó la recarga completa de predicciones y otra muestra en un proceso offline independiente.

La implementación original del segundo intento obtuvo 88,59 % de validación. Se respaldó antes de corregir y reentrenar; ese número no se atribuye a esta ejecución ni a su CSV nuevo. El primer submission no se cambió.

## Archivos de envío

- `submissions/submission_01_minilm_mlp_sin_aumento.csv`: público registrado **0.70923**.
- `submissions/submission_02_minilm_finetuning_sin_aumento.csv`: **0.85707 público**, reportado tras subir el CSV corregido. Es el mejor envío público registrado.
- `submissions/submission_03_minilm_finetuning_con_aumento.csv`: **pendiente de subir a Kaggle** (entrenado finalmente con las 20.466 reseñas durante 2 épocas).

Los CSV contienen 6.781 filas con columnas `id,answer`. Generarlos no los sube a Kaggle ni cuenta como participación. El modelo 2 se reentrenó con todas las etiquetas solo después de cerrar la evaluación; sus métricas corresponden al ajuste de desarrollo, no a una nueva evaluación del artefacto final.

## Entrega y reproducción

1. Usar el kernel de `.venv` con las versiones registradas en las salidas del notebook. Instalar las dependencias y descargar la revisión pública del encoder si no está en caché. La recarga del paquete ya exportado no necesita Internet.
2. Ejecutar el notebook en orden con los tres CSV y `particiones.csv` en la raíz. No se deben cambiar las particiones para buscar mejores resultados.
3. `modelo_01_multilingue.pt` y `modelo_02_finetuning.pt` incluyen pesos y archivos del tokenizador. Se descargan desde [GitHub Releases](MODELOS.md) y se guardan en la raíz del proyecto. Por su tamaño siguen excluidos por `.gitignore`. Para recargar el segundo, ejecutar antes los imports y las definiciones de `DatasetResenas02`, `collate_02`, `ClasificadorMiniLM02`, `predecir_probas_02` y `cargar_modelo_02`, sin repetir los ajustes.
4. `cargar_modelo_02(ruta)` devuelve tokenizador, modelo CPU y metadatos; `predecir_probas_02(modelo, secuencias, tokenizador)` usa ese mismo tokenizador y el dispositivo del modelo. Solo cargar paquetes de confianza.

## Descargar los modelos

Los dos paquetes entrenados están en [GitHub Releases](https://github.com/jmfonsecau/ISIS-2611-Proyecto-Parte-2/releases/tag/modelos-parte2-20261118):

- [Descargar modelo 1: MiniLM congelado + MLP](https://github.com/jmfonsecau/ISIS-2611-Proyecto-Parte-2/releases/download/modelos-parte2-20261118/modelo_01_multilingue.pt).
- [Descargar modelo 2: MiniLM con fine-tuning](https://github.com/jmfonsecau/ISIS-2611-Proyecto-Parte-2/releases/download/modelos-parte2-20261118/modelo_02_finetuning.pt).

Guardar los `.pt` junto al notebook. No se descargan con `git clone` y no hace falta Git LFS. Ver [MODELOS.md](MODELOS.md) para tamaños, huellas y licencia. La descarga permite reutilizar los pesos sin repetir el entrenamiento; antes hay que ejecutar los imports y las definiciones de carga del notebook.

## Tercer experimento: aumento controlado

Se mantiene MiniLM, la partición y los hiperparámetros del modelo 2. Se preparan variantes léxicas de 11.853 de las 16.373 reseñas de entrenamiento, con reglas propias en cuatro idiomas. Cada época usa una variante con probabilidad 0,30 cuando existe: no se duplican filas y se mantienen 512 actualizaciones por época.

La validación conserva sus textos originales y eval solo se usa para inferencia. Se comprobaron negaciones, conectores y números, cambios visibles en el tokenizador y posibles colisiones de etiqueta dentro del entrenamiento. El notebook muestra ejemplos y limitaciones.

En validación (4.093 reseñas) obtuvo **88,35 % de accuracy** (`-0,415 pp` frente al `88,76 %` del modelo 2 sin aumento), **89,26 % de F1 macro** y pérdida de validación **0.2296** en la época 2 (detención en época 4), con una brecha entrenamiento–validación de **4,79 pp** (`93,14 %` entrenamiento). Por idioma: español `90,30 %`, inglés `86,17 %`, portugués `86,58 %` (`+0,43 pp`), francés `80,19 %`, mezcla es/en probable `81,82 %`, otra mezcla `86,67 %` e incierto `100,00 %`. La conclusión es que las sustituciones léxicas simples no mejoraron el accuracy global frente al modelo 2, pues la dificultad principal sigue estando en reseñas mixtas, negaciones e ironía.

Las definiciones del modelo 2 se separaron de su ajuste para poder reutilizarlas sin repetirlo. Antes del tercer intento se liberan los modelos anteriores de memoria.

### Reentrenamiento final y caché local

Cerrada la evaluación, se realizó el segundo ajuste final en GPU Tesla T4 con las **20.466 reseñas de `train.csv`** (14.846 variantes aceptadas y 5.620 sin regla) durante `mejor_epoca_03 = 2` épocas, generando `submissions/submission_03_minilm_finetuning_con_aumento.csv` y `modelo_03_minilm_aumento.pt` (692,6 MB), con recarga offline verificada.

Con `REENTRENAR_03 = False`, las celdas del tercer modelo recargan el paquete guardado si está en la raíz sin repetir el ajuste. Comprueban datos, particiones, configuración y reglas antes de reutilizarlo. Para volver a ajustar sus pesos se cambia a `True`.

## Pendientes de la rúbrica

- Subir `submission_03_minilm_finetuning_con_aumento.csv` a Kaggle y registrar su score público.
- Implementar los siguientes experimentos: faltan al menos dos envíos más distintos para llegar a cinco.
- Confirmar la línea base oficial y superarla en el leaderboard privado; el accuracy local y el score público no lo demuestran.
- Completar códigos y aportes reales de los integrantes.
