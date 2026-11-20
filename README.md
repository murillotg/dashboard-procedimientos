# Dashboard de procedimientos por año y por zona (Power BI)

Proyecto de Power BI Desktop con **datos 100 % ficticios**, pensado para mostrar cómo está armado el dashboard.
No necesita base de datos, servidor ni conexión: los datos de ejemplo están incluidos dentro del propio modelo.

## Cómo abrirlo

1. Instalá **Power BI Desktop**, que es gratis y solo funciona en Windows. Se descarga desde la Microsoft Store.
2. Descargá este repositorio (botón **Code → Download ZIP**) y descomprimilo.
3. Abrí `procedimientos-por-anio.pbip` con doble clic.
4. La primera vez los gráficos aparecen vacíos. Hacé clic en **Actualizar ahora**, en la barra amarilla de arriba (o en **Inicio → Actualizar**).

## Contenido

### Página 1: Procedimientos por año
- Tarjetas: total de procedimientos, procedimientos del último año, variación interanual del último año y promedio anual.
- Gráfico de columnas con los procedimientos de cada año.
- Tabla por año con la variación interanual.

### Página 2: Por zona
- Segmentador de año. Filtra las tarjetas y el ranking de zonas.
- Tarjetas: procedimientos, zonas con procedimientos y zona principal.
- Ranking de zonas (partidos) por cantidad de procedimientos.
- Procedimientos por año y provincia.
- Matriz zona × año. Con el **+** de cada partido se ven sus localidades.

## Cómo está armado

El proyecto está en formato **PBIP**: todo se guarda como texto, así que se puede leer y versionar en Git.

| Carpeta | Qué contiene |
|---|---|
| `procedimientos-por-anio.SemanticModel/definition/` | El **modelo de datos** en TMDL: tablas, relación, medidas DAX y datos de ejemplo |
| `procedimientos-por-anio.Report/definition/` | El **informe** en PBIR: páginas y visuales en JSON |

**Modelo (esquema estrella):**

```
fecha (calendario) ──1:N──► incautaciones ◄──N:1── ubicacion (zonas)
```

- `incautaciones`: un registro por ítem incautado, con el procedimiento (sumario), la fecha y la zona.
- Un **procedimiento** se cuenta una vez aunque tenga varios ítems (`DISTINCTCOUNT`).
- La tabla `fecha` se genera con Power Query y está marcada como tabla de fechas, lo que habilita la comparación interanual.

**Medidas principales** (en `tables/incautaciones.tmdl`):

| Medida | Cálculo |
|---|---|
| `Procedimientos` | `DISTINCTCOUNT ( incautaciones[ProcedimientoKey] )` |
| `Variación interanual` | Contra `SAMEPERIODLASTYEAR` |
| `Procedimientos último año` y `Variación último año` | Sobre el último año con datos |
| `Promedio anual` | Promedio de los años con datos |
| `Zonas con procedimientos` y `Zona principal` | Partidos con datos, y el que tiene más procedimientos |
| `% del total` | Participación de cada zona |

## Sobre los datos

Todos los datos son **inventados** y solo sirven de ejemplo: sumarios, fechas y cantidades. Las zonas usan nombres de partidos y barrios reales solo como referencia geográfica.
En la versión real, el modelo se conecta a una base PostgreSQL que se carga con un proceso ETL a partir de las planillas de incautaciones. Ese proceso y los datos reales no forman parte de este repositorio.
