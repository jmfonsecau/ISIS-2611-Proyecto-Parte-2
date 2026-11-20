# Modelos entrenados

Los pesos se guardan fuera de Git. El repo contiene el notebook ejecutado, las métricas y los CSV; abrirlo sin ejecutar permite consultar los resultados sin descargar modelos.

## Paquetes actuales de Colab

Los archivos están en la [carpeta del proyecto en Drive](https://drive.google.com/drive/folders/1IFjsKIHVJsrEsbk5aXiHWeDRJNS7QC3c). No se han subido estos paquetes nuevos a GitHub. Al terminar los experimentos se preparará el ZIP del modelo elegido.

| Modelo | Archivo | Score público |
|---|---|---:|
| 1 | [modelo_v1_01.pt](https://drive.google.com/file/d/1oxFQVOV2ws2IJBoWBTP_dOJ6itZA1l98/view) | 0.71611 |
| 2 | [modelo_v1_02.pt](https://drive.google.com/file/d/1AGRm5br27zuDGYZBnXUgr8z9bEfbgxYH/view) | 0.86149 |
| 3 | [modelo_v1_03.pt](https://drive.google.com/file/d/10Wu27f_JlRCShIqkqIl1QPLU-TH1rbYe/view) | 0.85265 |
| 4 | [modelo_v1_04.pt](https://drive.google.com/file/d/1e4uGy_wz98trfWTPsXyMaatglu5ma8q5/view) | 0.87524 |
| 5 | [modelo_v1_05.zip](https://drive.google.com/file/d/12lBUXi1fm-Ns9Dv-1vf-gsd6amQezqsk/view) | 0.87770 |
| 6 | [modelo_v1_06.pt](https://drive.google.com/file/d/18zbFv6p_EvrMnhLCW9I0TRxVFeYNhBuf/view) | Pendiente de envío |

Los paquetes `.pt` incluyen encoder, tokenizador, cabeza, configuración, licencias y registros. Se leen mediante `cargar_modelo_completo` después de ejecutar las definiciones y la configuración correspondiente del notebook. La carga usa `weights_only=True` y no descarga pesos base si el paquete ya está disponible.

El ZIP del modelo 5 contiene las redes finales 4 y 2, sus tokenizadores y el manifiesto con pesos 0,90 y 0,10. `predecir_ensamble_05` comprueba las huellas y combina sus probabilidades. El notebook verificó su recarga sobre las 6.781 predicciones.

El ZIP que ya existe corresponde únicamente al ensamble 5; no es un archivo con todos los experimentos. El paquete de entrega definitivo se preparará al cerrar el proyecto.

## Reutilizar en Colab

Colocar los paquetes que se quieran reutilizar en la raíz de `ISIS-2611-Proyecto-Parte-2-Colab`, junto con los datos, los seis JSON de resultados, las licencias y la carpeta `submissions/`. El nombre del archivo debe coincidir con la tabla.

Para consultar los informes desde los JSON no se necesitan pesos: desactivar `ENTRENAR` y `GENERAR_SUBMISSION`. Para generar predicciones sí se necesitan los paquetes correspondientes. Con generación activada y un paquete ausente, el notebook puede iniciar el ajuste final; no asumir que clonar este repo incluye los modelos.

Los resultados de validación se guardaron antes de entrenar con todo train. El score público se asocia a la huella del CSV, no solo al nombre de la arquitectura. Los modelos y comprimidos quedan excluidos mediante `.gitignore`.

## Versiones anteriores

Se conservan las publicaciones históricas de los tres primeros intentos. Sus nombres, pesos y scores son diferentes de las nuevas corridas de Colab; no deben usarse como reemplazo de `modelo_v1_01.pt` a `modelo_v1_03.pt`.

| Archivo histórico | Score público |
|---|---:|
| [modelo_01_multilingue.pt](https://github.com/jmfonsecau/ISIS-2611-Proyecto-Parte-2/releases/download/modelos-parte2-20261118/modelo_01_multilingue.pt) | 0.70923 |
| [modelo_02_finetuning.pt](https://github.com/jmfonsecau/ISIS-2611-Proyecto-Parte-2/releases/download/modelos-parte2-20261118/modelo_02_finetuning.pt) | 0.85707 |
| [modelo_03_minilm_aumento.pt](https://github.com/jmfonsecau/ISIS-2611-Proyecto-Parte-2/releases/download/modelo03-parte2-20261118/modelo_03_minilm_aumento.pt) | 0.85903 |

Las versiones publicadas incluyen sus archivos de huellas. Las referencias y licencias de las bases están en [REFERENCIAS.md](REFERENCIAS.md).
