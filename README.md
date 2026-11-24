# StreamView Analytics

Proyecto académico de **Visualización de Datos (ADY1104), Duoc UC, sede Plaza Norte**.
Equipo: **Abraham Abanto, Bastián Soto y Andrés Silva**. Docente: Alicia Ramos de Peraza.
Evaluaciones: EP5 (encargo, informe y productos) y EP6 (presentación y defensa).

## Problema y objetivo

Identificar las características de los títulos mejor evaluados y populares para ayudar a la
**Gerencia de Adquisición y Gestión de Contenidos** a priorizar su revisión. El dashboard
ofrece también una página ejecutiva para el **Directorio**.
El trabajo integra películas y series con estrenos entre **2010 y 2025**. El foco es el
problema 6, apoyado por el análisis de popularidad por género del problema 2.

## Productos

- [Dashboard interactivo en Looker Studio](dashboard/enlace_dashboard.md): https://datastudio.google.com/reporting/5b6f98f7-9a1c-49e3-aab5-77f96458c8d4
- [Configuración y cambios pendientes del dashboard](dashboard/configuracion_looker.md).
- [Notebook único: limpieza, EDA, validación y exportación](notebooks/01_limpieza_datos.ipynb).
- Exportación PDF del dashboard (`dashboard/`) y capturas (`images/dashboard/`): deben re-exportarse
  desde Looker Studio después de aplicar los cambios de `configuracion_looker.md`.

## Estructura

```text
streamview-analytics/
├── README.md
├── requirements.txt
├── requirements-notebook.txt
├── .gitignore
├── .gitattributes
├── data/
│   ├── README.md
│   ├── raw/                       # originales, sin modificar
│   ├── content_clean.csv          # fuente general de Looker Studio
│   ├── content_financials.csv     # fuente del indicador financiero
│   ├── diccionario_datos.csv      # documentación de los campos
│   ├── resumen_genero_tipo.csv    # tasas por género y tipo, para verificación
│   └── origenes.json
├── notebooks/
│   └── 01_limpieza_datos.ipynb
├── src/
│   └── ejecutar_eda.py
├── dashboard/
│   ├── configuracion_looker.md
│   ├── enlace_dashboard.md
│   └── dashboard_ep2.pdf          # exportación anterior; reemplazar
└── images/
    ├── README.md
    ├── eda/                       # 13 figuras PNG
    └── dashboard/                 # capturas anteriores; reemplazar
```

## Requisitos

Python **3.11 o 3.12** (también probado en 3.13 con las versiones fijadas en `requirements.txt`).
Los CSV finales ya están incluidos: consultarlos no requiere ejecutar Python.

### Windows, PowerShell

Abrir una terminal dentro de `streamview-analytics`:

```powershell
py -3.12 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe src/ejecutar_eda.py
```

### macOS o Linux

```bash
python3 -m venv .venv
.venv/bin/python -m pip install -r requirements.txt
.venv/bin/python src/ejecutar_eda.py
```

### Notebook interactivo

```bash
.venv/bin/python -m pip install -r requirements-notebook.txt
.venv/bin/python -m jupyterlab
```

Abrir `notebooks/01_limpieza_datos.ipynb` y seleccionar **Ejecutar todo**. En Windows usar
`.\.venv\Scripts\python.exe`. En Google Colab, abrir el notebook, ejecutar todo y subir los dos CSV
de `data/raw/` cuando se soliciten. No se requieren claves API ni Google Drive.

### Resultados de la ejecución

1. Lectura de los originales, diagnóstico e integración.
2. Limpieza y generación de variables analíticas, incluida la priorización de candidatos.
3. EDA, gráficos y conclusiones.
4. Validaciones de integridad, escalas, clasificaciones, priorización y finanzas.
5. Exportación de `content_clean.csv`, `content_financials.csv`, `diccionario_datos.csv` y `resumen_genero_tipo.csv`.

Las 13 figuras se guardan en `images/eda/`. La ejecución por consola también produce
`reports/ejecucion_eda.txt`, excluido por `.gitignore` por ser regenerable.
`src/ejecutar_eda.py` lee las celdas del notebook: no mantiene una segunda implementación.

## Valores de control, sin filtros

| Indicador | Resultado de referencia |
| --- | ---: |
| Títulos (películas / series) | 31.991 (16.000 / 15.991) |
| Títulos evaluables: mínimo 50 votos | 15.397 (48,1%) |
| Evaluables, películas / series | 12.185 (76,2%) / 3.212 (20,1%) |
| Valoración promedio entre evaluables | 6,63 |
| Valoración promedio, películas / series | 6,39 / 7,52 |
| Títulos con nota 7 o más entre evaluables | 38,1% |
| Popularidad media, todos los títulos | 42,63 |
| Popularidad media, películas / series | 20,38 / 64,88 |
| Popularidad mediana, todos los títulos | 22,07 |
| Popularidad mediana, películas / series | 10,91 / 36,20 |
| Títulos de alto potencial | 4.662 |
| Alto potencial entre evaluables (total / películas / series) | 30,3% / 30,4% / 29,7% |
| Alta valoración y baja popularidad (total / películas / series) | 3.411 / 2.758 / 653 |
| Países con producciones de un solo país | 100 |
| Películas con presupuesto e ingresos positivos | 3.540 |
| Ingresos/presupuesto individual mediano | 1,70 veces |

## Definiciones de segmentos

Umbrales por tipo, calculados entre los títulos con al menos 50 votos y fijos al filtrar:
películas, nota 6,400 y popularidad 11,4400; series, nota 7,599 y popularidad 47,6225.

- **Alto potencial** (`es_alto_potencial`): 50 votos o más, nota igual o superior a la mediana de su tipo y popularidad igual o superior a la mediana de su tipo.
- **Alta valoración y baja popularidad** (`es_alta_nota_baja_pop`): 50 votos o más, nota igual o superior a la mediana de su tipo y popularidad inferior a la mediana de su tipo.
- **Puntaje de prioridad** (`puntaje_prioridad_x10`): promedio de los percentiles de nota y popularidad dentro de su tipo, entre títulos con 50 votos. `ranking_prioridad` ordena los 4.662 títulos de alto potencial.

Estas etiquetas priorizan candidatos para revisar. No predicen éxito comercial ni equivalen a una recomendación de compra.

## Fuentes y límites de interpretación

Los dos originales son **Netflix Movies Detailed up to 2025** y **Netflix TV Shows Detailed up to 2025**,
proporcionados para el caso académico. El registro de fuentes y sus huellas SHA-256 está en
[data/origenes.json](data/origenes.json). El [diccionario](data/diccionario_datos.csv) describe los campos.

- El extracto tiene selección fija de registros por año y tipo. No representa todo el catálogo real.
- Popularidad es un índice externo, no reproducciones, usuarios, demanda ni retención de StreamView.
- El mínimo de 50 votos es una regla operativa, no una garantía estadística, y cubre distinto a películas (76,2%) y series (20,1%).
- Los gráficos por año comparan grupos según su estreno, no la evolución histórica de los mismos títulos.
- Ingresos/presupuesto corresponde a producciones cinematográficas, no a rentabilidad de la plataforma.
- La fuente no acredita costos de licencias ni permite recomendar compras automáticamente.

Al cargar una actualización completa en Looker Studio, reemplazar el archivo anterior en lugar de agregarlo,
porque agregar duplica filas. Verificar que el catálogo total sea 31.991.
