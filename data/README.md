# Datos

| Archivo | Función | Dimensiones |
| --- | --- | --- |
| raw/netflix_movies_detailed_up_to_2025.csv | Fuente original de películas | 16.000 × 18 |
| raw/netflix_tv_shows_detailed_up_to_2025.csv | Fuente original de series | 16.000 × 16 |
| content_clean.csv | Fuente general para Looker Studio | 31.991 × 39 |
| content_financials.csv | Fuente del indicador financiero para Looker Studio | 3.540 × 20 |
| diccionario_datos.csv | Descripción y tipo de las 59 columnas exportadas | 59 filas |
| resumen_genero_tipo.csv | Títulos, evaluables y tasas de alto potencial por tipo y género; verificación y apoyo al informe | 37 filas |
| origenes.json | Procedencia, dimensiones y SHA-256 de los dos originales | Metadatos |

Los originales permanecen intactos. Los CSV generados se regeneran al final del notebook,
después del EDA y las validaciones. Para Looker Studio solo se cargan `content_clean.csv`
y `content_financials.csv`; el diccionario, el resumen y los metadatos son documentación.

Usar `content_id` como llave. `tmdb_id` puede repetirse entre películas y series.
`vote_average_x1000` y `popularity_x1000` se dividen por 1.000. `puntaje_prioridad_x10` se divide por 10.
`roi_x1000000` se divide por 1.000.000 y representa ingresos/presupuesto, no utilidad neta.

Campos agregados en esta versión, al final de `content_clean.csv`: `es_alta_nota_baja_pop`,
`puntaje_prioridad_x10`, `ranking_prioridad` y `n_eval_genero_tipo`. Las 35 columnas anteriores
conservan nombre, orden y valores.

`genre_main` es el primer género homologado. Las coproducciones se agrupan sin inventar
un país principal. `date_added` se conserva, pero no acredita incorporación a la plataforma.
Los años analizados son años de estreno y el extracto no representa todo el catálogo real.

En `resumen_genero_tipo.csv`, `tasa_alto_potencial_evaluables_pct` usa como denominador los títulos con 50 votos
y `tasa_alto_potencial_todos_pct` usa todos los títulos del grupo. Los grupos con menos de 30 evaluables
tienen tasas poco estables y no deben ordenarse como ranking.
