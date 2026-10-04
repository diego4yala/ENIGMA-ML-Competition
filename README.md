# ENIGMA ML Competition: horas de interrupción por zona

Solución a la competencia ENIGMA de Kaggle, donde se predicen las horas de interrupción del servicio eléctrico por usuario para cada zona durante los 12 meses siguientes al historial. La métrica es RMSLE.

**Resultado:** 1.er puesto, RMSLE 0.82468 sobre el 100 % del test.
**Link:**https://www.kaggle.com/competitions/enigma-ml-competition/
## Enfoque

El test mezcla zonas con historia (188 zonas, 82 % de las filas) y zonas nuevas que no existen en `train` (42 zonas, 18 %). Cada grupo tiene su propio modelo.

**Zonas con historia.** LightGBM sobre un dataset de varios orígenes en el tiempo (cada 6 meses, de `t = 100` a `t = 220`). En cada origen las features se calculan solo con datos anteriores a él, igual que en el test, donde el origen es `t = 232`. El modelo usa `extra_trees` con regularización fuerte y 200 rondas, porque con más rondas el error sube. La predicción final promedia cinco semillas.

**Zonas nuevas.** Se parte de la media de su empresa en los últimos 24 meses y se corrige con un modelo de residuo (HistGradientBoosting y Ridge) que usa estacionalidad de la empresa, vecindad espacial e infraestructura. Para entrenarlo se simulan zonas nuevas a partir de zonas que aparecen a mitad de la historia y de zonas con historia a las que se les quita su propio pasado (leave-zone-out).

Ambos bloques terminan con un ajuste aditivo pequeño del nivel en escala log.

## Validación

La validación imita el test: se entrena solo con lo que ya habría terminado en cada origen y se predicen los 12 meses siguientes. Para las zonas con historia se usan siete orígenes (años consecutivos y sin solaparse), porque un solo origen es demasiado ruidoso.

| Bloque | Referencia | Modelo |
|---|---|---|
| Zonas con historia (7 orígenes) | 0.8982 (media zona × mes) | 0.8239 |
| Zonas nuevas (3 orígenes LZO) | 0.9135 (media de empresa 24 m) | 0.8797 |

En el notebook, la última sección resume qué se probó y no funcionó.

## Estructura

```
.
├── enigma_solucion.ipynb   notebook con todo el flujo y sus resultados
├── requirements.txt
├── data/                   aquí van los CSV de la competencia (no se versionan)
└── outputs/                se crea al ejecutar; contiene submission.csv
```

## Cómo ejecutarlo

1. Descargar los CSV de la competencia y copiarlos en `data/` (ver `data/README.md`).
2. Crear un entorno e instalar dependencias:

```bash
python -m venv .venv
source .venv/bin/activate        # en Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

3. Abrir y ejecutar el notebook:

```bash
jupyter notebook enigma_solucion.ipynb
```

El notebook completo tarda un par de minutos en una CPU normal y deja el archivo en `outputs/submission.csv`. Las semillas están fijas, así que los resultados deberían repetirse salvo diferencias menores entre versiones de las librerías.

## Nota sobre la submission original

La entrega que obtuvo el primer puesto mezclaba además un 27 % de un ensamble anterior para las zonas con historia. Ese ensamble salía de una cadena larga de experimentos que no se puede reproducir de forma limpia, así que este repositorio contiene solo el modelo final. Según la validación, esa mezcla aportaba menos de 0.001.
