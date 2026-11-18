# Modelos entrenados

Los dos modelos se descargan desde la [versión `modelos-parte2-20261118` de GitHub Releases](https://github.com/jmfonsecau/ISIS-2611-Proyecto-Parte-2/releases/tag/modelos-parte2-20261118). Guardar cada `.pt` en la raíz, junto a `notebook_parte2.ipynb`.

| Archivo | Tamaño | Score público registrado |
|---|---:|---:|
| [modelo_01_multilingue.pt](https://github.com/jmfonsecau/ISIS-2611-Proyecto-Parte-2/releases/download/modelos-parte2-20261118/modelo_01_multilingue.pt) | 692.66 MiB | 0.70923 |
| [modelo_02_finetuning.pt](https://github.com/jmfonsecau/ISIS-2611-Proyecto-Parte-2/releases/download/modelos-parte2-20261118/modelo_02_finetuning.pt) | 692.34 MiB | 0.85707 |

El primero usa MiniLM congelado y una MLP. El segundo ajusta MiniLM por fine-tuning y usa una cabeza lineal. Ambos incluyen pesos, tokenizador y configuración. Para reutilizarlos, ejecutar los imports y las definiciones de carga del notebook; no hace falta repetir el entrenamiento.

## Verificar la descarga

La versión incluye `SHA256SUMS.txt`. Descargarlo en la misma carpeta y ejecutar:

```bash
shasum -a 256 -c SHA256SUMS.txt
```

Ambos resultados deben ser `OK`. Las huellas son:

- `modelo_01_multilingue.pt`: `d091019504b78ab27f6fa084b8e97c848443d4e4450b883f352d7b300758f091`
- `modelo_02_finetuning.pt`: `9ee2c5fc4b6249b91a97f5752f56760e930a63438ff3402ebd02f33c5dc807f9`

## Procedencia

Base: [sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2](https://huggingface.co/sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2), revisión `e8f8c211226b894fcb81acc59f3b34ba3efd5f42`, con licencia Apache-2.0. El equipo añadió una MLP al primer paquete y modificó los pesos del encoder y la cabeza de clasificación en el segundo.

Se incluye el [texto de la licencia del modelo base](LICENSE_MODELOS_APACHE-2.0.txt), también como adjunto de la versión. Este aviso no cambia la licencia del resto del código ni de los datos. Más detalles en [REFERENCIAS.md](REFERENCIAS.md).

## Almacenamiento

Los archivos se publican como adjuntos de una versión y siguen ignorados por Git. No se descargan al clonar y no requieren Git LFS. Se eligió Releases porque GitHub bloqueó la subida LFS por el presupuesto de la cuenta; no se modificó la facturación.

GitHub permite adjuntos de menos de 2 GiB por archivo en [Releases](https://docs.github.com/en/repositories/releasing-projects-on-github/about-releases). Los dos paquetes están por debajo de ese límite.
