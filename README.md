# Proyecto ML - Parte 2

Proyecto de Aprendizaje de Máquina de la Universidad de los Andes. Buscamos clasificar reseñas multilingües como `positivo`, `negativo` o `neutral`, usando redes profundas, transferencia de aprendizaje y aumento de datos.

<img width="399" height="498" alt="siseveeen" src="https://github.com/user-attachments/assets/777ef828-c3f6-41fc-8430-8fa648003116" />

## Integrantes

- Jose Manuel Fonseca — 202122456
- Julian Pinto
- Daniel Vergara

## Archivos

| Archivo | Contenido |
|---|---|
| `notebook_parte2.ipynb` | Exploración, entrenamiento, evaluación y documentación de los experimentos. Todo el código está aquí. |
| `train.csv` | 20.466 reseñas con `id`, `text` y `label`. |
| `eval.csv` | 6.781 reseñas sin etiqueta para generar las predicciones de Kaggle. |
| `sample_submission.csv` | Plantilla del envío: `id,answer`. |
| `particiones.csv` | Separación fija de 16.373 ejemplos de entrenamiento y 4.093 de validación. |
| `REFERENCIAS.md` | Fuentes, licencias, entornos usados y declaración de uso de IA. |
| `MODELOS.md` | Descarga de los paquetes entrenados y sus huellas. |

## Proceso y resultados actuales

Revisamos nulos, textos vacíos, duplicados, clases, longitudes e idiomas. No eliminamos filas. Conservamos juntos tres pares de reseñas casi idénticas; los idiomas son estimados, no anotaciones oficiales.

La validación se mantiene fija y sin aumento. Se utiliza para elegir la época, por lo que no es una prueba ciega. `eval` solo se usa para inferencia.

| Modelo | Aumento | Accuracy validación | F1 macro | Público |
|---|---|---:|---:|---:|
| MiniLM congelado + MLP | Sin aumento | 72,27 % | 73,78 % | 0.70923 |
| Fine-tuning de MiniLM | Sin aumento | 88,76 % | 89,62 % | 0.85707 |
| Fine-tuning de MiniLM | Sustitución léxica | 88,35 % | 89,26 % | **0.85903** |

Los dos primeros modelos se midieron en MPS. El tercero se ejecutó en T4 con otras versiones. La comparación describe estas ejecuciones; no aísla únicamente el aumento. El notebook incluye un control opcional para repetir ambos casos en una misma GPU, todavía sin resultados publicados.

## Estado del proyecto

Hay **tres envíos confirmados**. El tercero obtuvo **0.85903**, el mejor score público registrado, y pasa a ser el candidato de entrega. Mejoró 0,196 puntos porcentuales frente al segundo, aunque su accuracy local bajó 0,415 puntos. Esto no garantiza una mejora en la tabla privada.

La comparación con y sin aumento y el análisis por idioma ya están documentados. Faltan dos envíos diferentes para completar los cinco requeridos.

## Archivos de envío

- `submissions/submission_01_minilm_mlp_sin_aumento.csv`: público **0.70923**.
- `submissions/submission_02_minilm_finetuning_sin_aumento.csv`: público **0.85707**.
- `submissions/submission_03_minilm_finetuning_con_aumento.csv`: público **0.85903**.

Cada CSV contiene 6.781 filas con `id,answer`. Los scores fueron reportados por el grupo. La tabla del notebook comprueba la huella del CSV antes de asociarle su score: un archivo nuevo no hereda automáticamente el resultado del anterior.

## Últimos modelos

El primero usa MiniLM congelado y entrena una MLP. El segundo ajusta el encoder y una cabeza lineal. El cambio también incluye cabeza y normalización, por lo que no es una prueba aislada de descongelar pesos.

El tercero mantiene la red y los hiperparámetros del segundo. Cambia como máximo una expresión por reseña elegible, con probabilidad 0,30 por época, usando reglas propias en cuatro idiomas. Hay 11.853 variantes disponibles en desarrollo y 14.846 al usar todo train; no todas se utilizan en cada época. Se conservan negaciones, conectores y números, aunque las equivalencias pueden fallar según el contexto.

En desarrollo recuperó la época 2 tras detenerse en la 4. Obtuvo 93,14 % en entrenamiento y 88,35 % en validación: una brecha de 4,79 puntos. Mejoró el recall de positivo y redujo el de negativo; no demostró una mejora global local. Los errores sugieren dificultades con opiniones contradictorias, negaciones y mezclas, pero esa hipótesis no está cuantificada por tipo de error.

Cerrada la evaluación, el modelo 3 se volvió a entrenar con las **20.466 reseñas** durante dos épocas, manteniendo un horizonte de seis para el calendario. El CSV proviene de ese modelo final; las métricas locales corresponden al ajuste de desarrollo anterior.

## Entrega y reproducción

1. Descargar [el paquete del modelo 3](https://github.com/jmfonsecau/ISIS-2611-Proyecto-Parte-2/releases/download/modelo03-parte2-20261118/modelo_03_minilm_aumento.pt) y guardarlo junto al notebook. [MODELOS.md](MODELOS.md) incluye los tres paquetes y sus huellas.
2. En VS Code usar el entorno local; en Colab ejecutar la preparación inicial. Las versiones originales de MPS y T4 están separadas en [REFERENCIAS.md](REFERENCIAS.md).
3. Ejecutar las celdas en orden. Por defecto, `Run All` **no entrena ninguna red**: carga la partición publicada, muestra registros anteriores y verifica el tercer CSV con su paquete. Si falta el `.pt`, se detiene y pide descargarlo.
4. Para repetir los modelos 1 y 2 activar `ENTRENAR_MODELOS_ANTERIORES=True`. Para un tercer ajuste nuevo activar `REENTRENAR_03=True`. Estos cambios son explícitos y pueden actualizar sus artefactos. El control de comparación en una misma GPU también está desactivado por defecto.
5. La caché verifica semilla, clases, límite de tokens, modelo, revisión, datos, partición, hiperparámetros y aumento. Las métricas históricas se recuperan de sus predicciones de desarrollo, sin evaluar el modelo final sobre ejemplos que ya usó para entrenar.
6. Entregar el notebook ejecutado, `modelo_03_minilm_aumento.pt` y `REFERENCIAS.md` en Bloque Neón mientras el tercero conserve el mejor score público.

La descarga no instala las dependencias. La recarga del paquete funciona sin Internet una vez instaladas y se comprueba contra el CSV existente. Las descargas del encoder público se limitan a pesos y tokenizador y solo se necesitan para entrenamientos nuevos.

## Pendientes de la rúbrica

Completar dos envíos distintos, confirmar la superación de la línea base y el resultado privado, y registrar los códigos y aportes reales de cada integrante. La repetición controlada en T4 queda como siguiente comprobación; no se presenta como ya ejecutada.
