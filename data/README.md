# Datos

Aquí van los CSV de la competencia ENIGMA. No se suben al repositorio (ver `.gitignore`).

Archivos que espera el notebook:

| Archivo | Contenido |
|---|---|
| `train.csv` | Historial mensual por zona (`t = 1..232`): `target`, `freq`, `target_a`, `target_b`, `target_c` |
| `test.csv` | Filas a predecir (`id`, `zone_id`, `company_id`, `t = 233..244`, `month`) |
| `substations.csv` | Subestaciones por zona: capacidad, demanda, tensión, propiedad y coordenadas |
| `consumption.csv` | Clientes y consumo por zona, tarifa y segmento (`t = 209..232`) |
| `sample_submission.csv` | Formato de la entrega (`id`, `target`) |

Se pueden descargar desde la página de la competencia en Kaggle (pestaña *Data*).
