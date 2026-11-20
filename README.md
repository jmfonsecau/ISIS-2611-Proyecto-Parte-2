# Proyecto ML - Parte 2

Clasificamos reseñas multilingües como `negativo`, `neutral` o `positivo`, usando redes profundas, transferencia de aprendizaje y aumento de datos. Conservamos una sección completa por intento: planteamiento, configuración, entrenamiento, evaluación, submission y comparación.

<img width="399" height="498" alt="siseveeen" src="https://github.com/user-attachments/assets/777ef828-c3f6-41fc-8430-8fa648003116" />

El **modelo 7, XLM-R large seleccionado por accuracy**, obtuvo **0.90078 en el leaderboard público de Kaggle** y es el candidato de entrega. El detalle del paquete y de su recarga está en [MODELOS.md](MODELOS.md).

## Integrantes

- Jose Manuel Fonseca — 202122456
- Julian Pinto
- Daniel Vergara

## Archivos

| Archivo | Contenido |
|---|---|
| `notebook_parte2.ipynb` | Versión ejecutada en Colab: siete intentos, tablas, gráficas y conclusiones. Todo el código está aquí. |
| `train.csv`, `eval.csv`, `sample_submission.csv` | Datos originales: 20.466 reseñas etiquetadas y 6.781 de evaluación. |
| `particiones.csv` | Partición fija de 16.373 reseñas para desarrollo y 4.093 para validar. |
| `datos_sha256.json` | Huellas de los datos y de la partición. |
| `resultados_v1_01.json` a `resultados_v1_07.json` | Métricas, historiales y predicciones de validación por ID. |
| `submissions/` | CSV históricos y los siete CSV generados en Colab. |
| `REFERENCIAS.md` y archivos `LICENSE_*.txt` | Bases preentrenadas, revisiones, licencias y uso de IA. |
| `MODELOS.md` | Ubicación de los paquetes pesados, que se comparten por separado. |

## Abrir sin ejecutar

El notebook conserva las salidas guardadas: tablas, gráficas, métricas y mensajes de ejecución. Puede abrirse en GitHub o VS Code sin volver a entrenar. Exportarlo no conserva las variables en RAM ni incluye los pesos de las redes.

El archivo principal del repo se llama `notebook_parte2.ipynb`; en [Colab](https://colab.research.google.com/drive/1eEPCGXDQBsUhwEDNF9gYGNxx0AmwwR0Z) se llama `notebook_parte2_colab.ipynb`. Esta copia corresponde a la ejecución terminada el 20 de noviembre de 2026, con 43 celdas de código ejecutadas y sin errores.

## Proceso y resultados de Colab

Pasamos del equipo local a una GPU L4 de Colab para reducir tiempos y guardar avances en Drive. La GPU no mejora el score por sí sola ni más épocas garantizan un mejor resultado.

Revisamos nulos, textos vacíos, duplicados, clases, longitudes e idiomas. Conservamos la misma partición, manteniendo juntos los grupos de reseñas similares. Los idiomas son estimados. Validación se usa para elegir configuraciones y épocas; no es una prueba ciega. `eval` solo se usa para inferencia.

| Modelo | Cambio principal | Accuracy validación | F1 macro | Score público |
|---|---|---:|---:|---:|
| 1 | MiniLM fijo + MLP | 73,37 % | 0,7526 | 0.71611 |
| 2 | Fine-tuning de MiniLM sin aumento | 88,37 % | 0,8923 | 0.86149 |
| 3 | La misma red con sustitución léxica controlada | 88,39 % | 0,8932 | 0.85265 |
| 4 | XLM-R base con ajuste por capas | 89,79 % | 0,9057 | 0.87524 |
| 5 | 90 % XLM-R base + 10 % MiniLM sin aumento | 90,08 % | 0,9084 | 0.87770 |
| 6 | XLM-R large, 24 capas y embeddings fijos | 89,79 % | 0,9059 | 0.88310 |
| 7 | La misma red, selección por accuracy y paciencia de dos épocas | **90,72 %** | **0,9145** | **0.90078** |

La comparación con y sin aumento corresponde a los modelos 2 y 3, con los mismos hiperparámetros y entorno de Colab. La pequeña mejora en validación no se mantuvo en Kaggle.

El modelo 6 alcanzó 90,30 % en su segunda época, pero empeoró la pérdida de validación. El criterio de ese experimento eligió la primera época por su menor pérdida. Esa versión obtuvo 0.88310 en Kaggle.

## Submissions

| Archivo en `submissions/` | Score público |
|---|---:|
| `submission_01_minilm_mlp_sin_aumento_colab.csv` | 0.71611 |
| `submission_02_minilm_finetuning_sin_aumento_colab.csv` | 0.86149 |
| `submission_03_minilm_finetuning_con_aumento_colab.csv` | 0.85265 |
| `submission_04_xlmr_finetuning_por_capas_colab.csv` | 0.87524 |
| `submission_05_ensemble_xlmr_minilm_colab.csv` | 0.87770 |
| `submission_06_xlmr_large_colab.csv` | 0.88310 |
| [`submission_07_xlmr_large_accuracy_colab.csv`](submissions/submission_07_xlmr_large_accuracy_colab.csv) | **0.90078** |

Cada archivo tiene 6.781 predicciones con las columnas `id,answer`. Los cuatro primeros se enviaron originalmente como `submission_v1_01.csv` a `submission_v1_04.csv`: sus nombres descriptivos conservan exactamente los mismos bytes. No duplicamos esos alias en el repo. Los scores provienen de las capturas de Kaggle y los registros los vinculan a las huellas de los CSV correspondientes.

También conservamos los tres CSV anteriores, sin el sufijo `_colab`, como historial. Obtuvieron 0.70923, 0.85707 y 0.85903. Son corridas distintas: sus pesos y scores no se intercambian con los de Colab.

## Ejecutar en Colab

1. Abrir el notebook en Colab y seleccionar una GPU. Los entrenamientos actuales se realizaron en L4; otras GPU pueden necesitar ajustar los lotes en un experimento nuevo.
2. Mantener datos, partición, licencias y resultados en la carpeta de Drive `ISIS-2611-Proyecto-Parte-2-Colab`. Los modelos guardados se colocan en esa misma carpeta; ver [MODELOS.md](MODELOS.md).
3. Para consultar y recalcular informes desde los registros, dejar `ENTRENAR=False` y `GENERAR_SUBMISSION=False`. La preparación y los informes vuelven a ejecutarse, pero no se entrenan redes ni se exportan CSV.
4. La copia exportada conserva los controles usados en la última ejecución: ambos están en `True`. Con ellos, Run all recorre cada sección y reutiliza los artefactos compatibles; si falta un modelo o resultado puede iniciar entrenamiento. No ejecutar Run all si solo se quiere leer.
5. Para experimentar, activar los controles necesarios en Colab. Si se cambian datos o hiperparámetros de un intento existente, usar una nueva `VERSION_CORRIDA` para no mezclar registros. Los resultados nuevos se revisan antes de subir el CSV a Kaggle.

Los entrenamientos no se ejecutan en el computador local. La configuración comprueba que se esté usando Colab con GPU antes de entrenar o generar un envío.

## Guardado, reproducción y entrega

Los modelos 1 a 4 y 6 eligen la época de menor pérdida en desarrollo; el modelo 7 elige la de mayor accuracy, con pérdida como desempate. Después se inicializa otra red desde la base pública y se ajusta con todo train durante las épocas seleccionadas, conservando el horizonte del calendario. Las métricas de validación mostradas pertenecen al modelo de desarrollo, no a los pesos finales que ya aprendieron todo train.

Los checkpoints conservan pesos, AdamW, calendario, precisión mixta y estados aleatorios. Se recupera la última época completa si coinciden versión, datos, configuración y entorno. Los paquetes finales incluyen encoder, tokenizador, cabeza, licencias y configuración. La recarga se comprobó contra las predicciones de cada CSV.

Para la entrega se usan el notebook ejecutado, el paquete entrenado `modelo_v1_07.pt` y `REFERENCIAS.md`. Los pesos se comparten aparte desde Drive. Quedan pendientes el resultado privado de Kaggle y completar los códigos y aportes individuales del grupo.

## Séptimo intento

El séptimo intento mantuvo la base, los datos, la semilla y las versiones del sexto. Cambió la selección a mayor accuracy y aumentó la paciencia a dos épocas. Eligió la cuarta época con **90,72 % en validación**, F1 macro de **0,9145** y una brecha de **5,49 puntos** frente a entrenamiento. Su score público mejoró **1,77 puntos porcentuales** frente al sexto intento. La pérdida de validación aumentó y el resultado privado sigue pendiente.
