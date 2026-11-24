# Configuración de Looker Studio

Esta guía describe cómo debe quedar el dashboard con los CSV corregidos. Incluye los cambios que
respondieron a la retroalimentación docente. Al terminar, re-exportar el PDF y las capturas.

## 1. Actualizar las fuentes

1. En Looker Studio: Recurso, Administrar fuentes de datos agregadas, Editar la fuente `datos_limpios_ep2`.
2. Reemplazar el archivo `content_clean.csv` por la versión nueva (31.991 filas, 39 columnas). No agregar el archivo
   sin quitar el anterior: se duplicarían las filas. Repetir con `financials_ep2` y `content_financials.csv` (3.540 filas, 20 columnas).
3. Revisar que aparezcan los cuatro campos nuevos y que sean numéricos: `es_alta_nota_baja_pop`, `puntaje_prioridad_x10`,
   `ranking_prioridad`, `n_eval_genero_tipo`.
4. Comprobar que el Catálogo total siga en 31.991 antes de seguir.

Fuentes: `datos_limpios_ep2` (content_clean.csv) y `financials_ep2` (content_financials.csv). Llave: `content_id`,
nunca `tmdb_id`.

## 2. Nombres visibles de los campos

Renombrar una sola vez en la fuente de datos (columna Nombre), así todos los gráficos heredan etiquetas legibles y se
evitan nombres técnicos como `release_year` o `Record Count`.

| Campo | Nombre visible |
|---|---|
| content_type | Tipo de contenido |
| title_label | Título |
| genre_main | Género principal |
| country_main | País |
| language | Idioma original |
| release_year | Año de estreno |
| vote_count | Votos |
| ranking_prioridad | N.º |
| Record Count | Cantidad de títulos |

## 3. Tipos de campo

Números: release_year, vote_count, vote_average_x1000, popularity_x1000, es_confiable, es_7_o_mas, es_alto_potencial,
es_alta_nota_baja_pop, puntaje_prioridad_x10, ranking_prioridad, n_eval_genero_tipo, n_genres, n_countries,
popularity_pct_tipo, release_period_years. En finanzas: budget, revenue, profit y roi_x1000000.
Texto: IDs, títulos, categorías, rating_confiable (Sí/No) y banderas financieras (Sí/No). `country_iso2` puede usarse como
código geográfico; excluir ZZ y las producciones sin país único en mapas.

## 4. Campos calculados

| Campo | Fórmula | Formato |
|---|---|---|
| Valoración (fila) | `vote_average_x1000 / 1000` | Número, 2 decimales |
| Popularidad (fila) | `popularity_x1000 / 1000` | Número, 2 decimales |
| Catálogo total | `COUNT_DISTINCT(content_id)` | Número |
| Valoración promedio | `AVG(vote_average_x1000) / 1000` con `es_confiable = 1` | Número, 2 decimales |
| Títulos con nota 7 o más (%) | `SUM(es_7_o_mas) / SUM(es_confiable)` | Porcentaje |
| Popularidad media | `AVG(popularity_x1000) / 1000` | Número, 2 decimales |
| Popularidad mediana | `MEDIAN(popularity_x1000) / 1000` | Número, 2 decimales |
| Títulos evaluables | `SUM(es_confiable)` | Número |
| Alto potencial | `SUM(es_alto_potencial)` | Número |
| Alta valoración y baja popularidad | `SUM(es_alta_nota_baja_pop)` | Número |
| Tasa de alto potencial (evaluables) | `SUM(es_alto_potencial) / SUM(es_confiable)` | Porcentaje |
| Puntaje de prioridad | `puntaje_prioridad_x10 / 10` | Número, 1 decimal |
| Países de producción única | `COUNT_DISTINCT(country_main)` con `tipo_produccion = Un solo país` | Número |
| Ingresos/presupuesto mediano (×) | `MEDIAN(roi_x1000000) / 1000000` (fuente financiera) | Número, 2 decimales |

Los porcentajes no se multiplican por 100. Mostrar sin datos si el denominador es cero. No usar los campos antiguos
`vote_average_x100` ni `roi_x1000`.

Para mostrar el signo ×, crear el campo de texto `Ingresos/presupuesto (×)`:
`CONCAT(CAST(ROUND(MEDIAN(roi_x1000000) / 1000000, 2) AS TEXT), "×")`. Si el separador decimal sale con punto,
dejar el campo numérico y poner "(veces)" en el título de la tarjeta.

## 5. Colores únicos

Una sola asignación en las dos páginas, en la infografía y en el informe. Se establece manualmente en
Estilo, Colores de series, en cada gráfico que separe por tipo.

| Elemento | Color |
|---|---|
| Película | Azul `#2563EB` |
| Serie | Naranja `#F59E0B` |
| Alto potencial (solo para esa métrica) | Violeta `#6D28D9` |
| Gráficos de un solo tipo de dato sin tipo de contenido (países, idiomas) | Azul oscuro `#1E3A8A`, para no confundirlo con Película |

Los gráficos de países e idiomas no separan por tipo; usar un tono distinto al azul de Película evita leerlos como
"solo películas". Mantener la leyenda visible y las etiquetas de valor en las barras para no depender solo del color.

## 6. Encabezados iguales en las dos páginas

Construir el encabezado en la página 1 (etiqueta superior pequeña, título, subtítulo, fila de filtros y Restablecer) y copiarlo a
la página 2 con Copiar y Pegar. Después cambiar solo el texto. Misma fuente, tamaño, color, posición y alineación a la izquierda.

- Página 1: "Visión ejecutiva del catálogo" / "Composición, diversidad y evaluación de los contenidos del extracto, estrenos 2010-2025".
- Página 2: "Gerencia de Adquisición y Gestión de Contenidos" / "Candidatos para revisión, priorizados por tipo, género y año de estreno".

## 7. Página 1: Directorio

Tarjetas (ancho suficiente para que el título no se corte; máximo 28 caracteres por título):

| Título de la tarjeta | Métrica |
|---|---|
| Catálogo total | Catálogo total |
| Valoración promedio (≥50 votos) | Valoración promedio |
| Títulos con nota ≥7 (%) | Títulos con nota 7 o más (%) |
| Popularidad media | Popularidad media |
| Títulos de alto potencial | Alto potencial |
| Países de producción única | Países de producción única |

Agregar la tabla **Indicadores por tipo de contenido** (debajo de las tarjetas), con la dimensión `Tipo de contenido` y las métricas
Cantidad de títulos, Títulos evaluables, Valoración promedio, Popularidad media, Popularidad mediana y Tasa de alto potencial (evaluables),
con fila de totales activada. Así queda explícito qué universo usa cada cifra. Valores de control:

| Tipo | Títulos | Evaluables | Valoración | Pop. media | Pop. mediana | Tasa alto potencial |
|---|---:|---:|---:|---:|---:|---:|
| Película | 16.000 | 12.185 | 6,39 | 20,38 | 10,91 | 30,4% |
| Serie | 15.991 | 3.212 | 7,52 | 64,88 | 36,20 | 29,7% |
| Total | 31.991 | 15.397 | 6,63 | 42,63 | 22,07 | 30,3% |

Agregar una nota bajo las tarjetas: "Valoración y notas 7+ usan los 15.397 títulos con 50 votos; popularidad usa los 31.991 títulos."

Gráficos:

- **Géneros que predominan:** barras apiladas horizontales, dimensión `Género principal`, desglose `Tipo de contenido`, orden descendente.
  Película azul y Serie naranja.
- **Dónde se concentra la producción:** barras horizontales, dimensión `País`, filtro `tipo_produccion = Un solo país`, 10 barras.
  Desactivar las líneas de cuadrícula del eje (el valor ya está en la etiqueta de cada barra), o activar el eje X con valores. No dejar cuadrícula sin eje.
- **Top 5 idiomas originales:** reemplazar el treemap por barras horizontales con 5 barras, dimensión `Idioma original`,
  métrica Cantidad de títulos, etiquetas de valor. Las barras eliminan la celda "Todo" del treemap, no recortan nombres
  como "Spanish" y permiten comparar longitudes con exactitud. Si se mantiene el treemap, limitar a 5 segmentos
  y ampliar el gráfico hasta que ninguna etiqueta se corte.
- **Valoración promedio por año de estreno:** líneas con `Año de estreno` ascendente, desglose por tipo, filtro `es_confiable = 1`,
  eje Y de 5 a 8 con el título "Valoración (0 a 10)". Película azul y Serie naranja.

## 8. Página 2: Gerencia de Adquisición y Gestión de Contenidos

Tarjetas:

| Título de la tarjeta | Métrica |
|---|---|
| Títulos evaluables | Títulos evaluables |
| Alto potencial | Alto potencial |
| Alta valoración, baja popularidad | Alta valoración y baja popularidad |
| Alto potencial entre evaluables | Tasa de alto potencial (evaluables) |
| Popularidad mediana | Popularidad mediana |
| Ingresos/presupuesto mediano (×) | Ingresos/presupuesto (×); solo películas |

Gráficos:

- **Tasa de alto potencial por género:** barras horizontales agrupadas, dimensión `Género principal`, desglose `Tipo de contenido`,
  métrica Tasa de alto potencial (evaluables), filtro del gráfico `n_eval_genero_tipo >= 30`, orden descendente. Mostrar el valor en la etiqueta.
  Mantener, junto a este gráfico o debajo, la cantidad de candidatos por género. El texto del gráfico debe decir: "La tasa usa como
  denominador los títulos con 50 votos del mismo tipo y género; se excluyen grupos con menos de 30 evaluables."
  Valores de control: Película, Animation 61,3% (528 de 861), War 51,7%, Family 46,8%; Serie, Soap 51,0% (25 de 49), Family 43,5%.
- **Títulos para explorar (candidatos de alto potencial):** tabla con filtro `es_alto_potencial = 1`, orden ascendente por
  `N.º` (`ranking_prioridad`). Columnas: N.º, Título, Tipo de contenido, Género principal, País, Año de estreno, Valoración (promedio),
  Popularidad (promedio), Votos (máximo), Puntaje de prioridad. Esta tabla incluye películas y series porque el puntaje usa
  percentiles dentro de cada tipo. Valores de control: 1. Interstellar (2014), 2. Flow (2024), 3. The Wild Robot (2024), 4. Henry Danger: The Movie (2025),
  5. Rick and Morty (2013, serie). Ampliar la columna Título y mostrar 10 filas con paginación.
  Texto bajo la tabla: "Orden de revisión según el promedio de los percentiles de valoración y popularidad dentro de su tipo. No es una recomendación de compra."
- **Alta valoración y baja popularidad (3.411 títulos):** segunda tabla, o una pestaña, con filtro `es_alta_nota_baja_pop = 1`,
  mismas columnas (el N.º queda vacío) y orden descendente por Votos. Ordenar por Valoración subiría títulos con apenas 50 votos. Valores de control: 3.411 en total, 2.758 películas y 653 series.
- **Popularidad mediana por año de estreno:** líneas con `Año de estreno` (título del eje "Año de estreno"), desglose por tipo, sin filtro de votos.
  Película azul y Serie naranja, igual que la página 1.

## 9. Interacción

Filtros por `Tipo de contenido`, `Género principal`, `País`, `Idioma original` y `Año de estreno`, más Restablecer, en las dos páginas.
Los umbrales de alto potencial y de baja popularidad no cambian al filtrar.

Pruebas obligatorias en modo consulta antes de exportar:

1. Filtrar por Serie: la tarjeta financiera debe quedar sin datos. Si mantiene 1,70, no recibe el filtro entre fuentes; revisar los IDs internos de los campos.
2. Combinar tipo, género y año; las tarjetas y la tabla deben cambiar de forma coherente.
3. Presionar Restablecer: debe volver a 31.991 títulos y 15.397 evaluables.
4. Cambiar de página con la navegación y comprobar que los filtros se mantienen.
5. Hacer clic en una barra de género y comprobar el filtrado cruzado.

No afirmar una prueba funcional solo a partir de la exportación PDF.

## 10. Accesibilidad y legibilidad

- Tamaño mínimo de texto de 12 pt en ejes y etiquetas. Títulos de gráficos de 14 pt o más.
- La pareja azul y naranja se distingue también con daltonismo; además cada serie lleva leyenda y, en barras, etiqueta de valor.
- Contraste de texto oscuro sobre fondo claro; no usar texto gris claro sobre fondo de página.
- Cada gráfico lleva un título que dice qué muestra y un subtítulo o nota con el universo (todos los títulos o evaluables).
- Los títulos de las tarjetas no se cortan: si un título excede el ancho, acortarlo o ensanchar la tarjeta.

## 11. Después de los cambios

1. Exportar el reporte a PDF (las dos páginas) y reemplazar `dashboard/dashboard_ep2.pdf`.
2. Reemplazar las capturas de `images/dashboard/pagina_1.png` y `pagina_2.png`.
3. Comprobar que el enlace de [enlace_dashboard.md](enlace_dashboard.md) abre en modo consulta.
4. Las capturas del informe y de la infografía deben salir de esta versión.
