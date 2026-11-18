# Modelos entrenados

Los modelos 1 y 2 se descargan desde la [versión `modelos-parte2-20261118` de GitHub Releases](https://github.com/jmfonsecau/ISIS-2611-Proyecto-Parte-2/releases/tag/modelos-parte2-20261118). El modelo 3 se descarga desde [su propia versión](https://github.com/jmfonsecau/ISIS-2611-Proyecto-Parte-2/releases/tag/modelo03-parte2-20261118). Guardar cada `.pt` en la raíz, junto a `notebook_parte2.ipynb`.

| Archivo | Tamaño | Score público registrado |
|---|---:|---:|
| [modelo_01_multilingue.pt](https://github.com/jmfonsecau/ISIS-2611-Proyecto-Parte-2/releases/download/modelos-parte2-20261118/modelo_01_multilingue.pt) | 692.66 MiB | 0.70923 |
| [modelo_02_finetuning.pt](https://github.com/jmfonsecau/ISIS-2611-Proyecto-Parte-2/releases/download/modelos-parte2-20261118/modelo_02_finetuning.pt) | 692.34 MiB | 0.85707 |
| [modelo_03_minilm_aumento.pt](https://github.com/jmfonsecau/ISIS-2611-Proyecto-Parte-2/releases/download/modelo03-parte2-20261118/modelo_03_minilm_aumento.pt) | 692.61 MiB | **0.85903** |

El primero usa MiniLM congelado y una MLP. El segundo ajusta MiniLM por fine-tuning y usa una cabeza lineal. El tercero añade aumento léxico durante el fine-tuning. Los tres incluyen pesos, tokenizador y configuración. Para reutilizarlos, ejecutar los imports y las definiciones de carga del notebook; no hace falta repetir el entrenamiento.

## Verificar la descarga

La versión incluye `SHA256SUMS.txt`. Descargarlo en la misma carpeta y ejecutar:

```bash
shasum -a 256 -c SHA256SUMS.txt
```

Para el tercero descargar [SHA256SUMS_MODELO03.txt](https://github.com/jmfonsecau/ISIS-2611-Proyecto-Parte-2/releases/download/modelo03-parte2-20261118/SHA256SUMS_MODELO03.txt) y ejecutar `shasum -a 256 -c SHA256SUMS_MODELO03.txt`. Los resultados deben ser `OK`. Las huellas corresponden a las versiones publicadas, no a un reentrenamiento posterior. Las huellas son:

- `modelo_01_multilingue.pt`: `d091019504b78ab27f6fa084b8e97c848443d4e4450b883f352d7b300758f091`
- `modelo_02_finetuning.pt`: `9ee2c5fc4b6249b91a97f5752f56760e930a63438ff3402ebd02f33c5dc807f9`

- `modelo_03_minilm_aumento.pt`: `70dccf0b3c06f22af318ed2d58292b1d0e9e0d3fb71cdee135f3c3067d6d7a3c`

## Procedencia

Base: [sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2](https://huggingface.co/sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2), revisión `e8f8c211226b894fcb81acc59f3b34ba3efd5f42`, con licencia Apache-2.0. El equipo añadió una MLP al primer paquete y modificó los pesos del encoder y la cabeza de clasificación en el segundo.

Se incluye el [texto de la licencia del modelo base](LICENSE_MODELOS_APACHE-2.0.txt), también como adjunto de la versión. Este aviso no cambia la licencia del resto del código ni de los datos. Más detalles en [REFERENCIAS.md](REFERENCIAS.md).

## Almacenamiento

Los archivos se publican como adjuntos de una versión y siguen ignorados por Git. No se descargan al clonar y no requieren Git LFS. Se eligió Releases porque GitHub bloqueó la subida LFS por el presupuesto de la cuenta; no se modificó la facturación.

GitHub permite adjuntos de menos de 2 GiB por archivo en [Releases](https://docs.github.com/en/repositories/releasing-projects-on-github/about-releases). Los tres paquetes están por debajo de ese límite.

## Tercer modelo y ejecución normal

El paquete del modelo 3 fue entrenado en T4 con las 20.466 reseñas durante dos épocas, después de elegir la época en desarrollo. Es el candidato por su score público 0.85903. Sus métricas locales de 88,35 % de accuracy y 89,26 % de F1 pertenecen a los pesos de desarrollo anteriores al ajuste final.

Para cargarlo se usa `cargar_modelo_02`, compartida por ambas redes MiniLM. La ejecución normal del notebook verifica datos, semilla, configuración y clases, y comprueba sus respuestas contra el CSV existente. No hace falta entrenarlo de nuevo ni descargar el encoder público para recargarlo.
