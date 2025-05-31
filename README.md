# Análisis de Avistamientos OVNI - Proyecto Big Data

# Nombre: Jose Esteban Romero Huentrepan
# Profesor: Fernando Esteban Fuentes Gallegos
# Fecha: xx/05/2025

# 1. Descripción General
Este proyecto tiene como objetivo analizar el dataset (ufo_sighting.xlsx) de avistamiento de OVNIs, utilizando herramientas de google cloud skills boots (google cloud (GCP) y alteryx designer (Dataprep) para limpiar, transformar y visualizar los datos de una manera efectiva, BigQuery) asi realizando consultas SQL para tener metricas significativas y visualizarlas en Looker Studio.
Detallare paso por paso, justificando las decisiones tomadas durante el proceso.

# 2. Herramientas Utilizadas
- Google Cloud Storage: se utilizo para el almacenamiento (Cloud Storage) y procesamiento (BigQuery).
- BigQuery: Se utilizo para ejecutar consultas SQL y preparar los datos para los dashboards
- Alteryx Designer Cloud / Dataprep: Se utilizo para transformar y limpiar los datos.
- Looker Studio: Se utilizo para generar visualizaciones interactivas.

# 3. Flugo general del proyecto
1. Carga de Datos a GCP:
  - Se creo un bucket en cloud storage llamado jose-huentrepan
  - Se cargo el archivo ufo_sightings.xlsx
2. Creacion del Dataset en BigQuery:
  - Se creo un flujo llamado avistamientos_ufo
  - Se conecto el archivo .xlsx desde Cloud Storage
  - Se aplico una receta de limpieza y transformacion de datos.

4.Limpieza y transformacion de Datos (Dataprep)
4.1 Columnas Procesadas
  - Date_time: Eliminacion de nulos.
  - State/province: Se renombro a state_province, se transformo a mayuscula.
  - Country: Se transformo a mayuscula.
  - UFO_shape: Se hizo una estandarizacion de mayusculas y formato tipo "Propercase"
  - Latitude y Longitude:
    - Se escalaron dividiendo entre 1.000.000
    - Se filtraron registros con latitudes fuera de rango [-90, 90] y longitudes     
    [-180,180]

4.2 Reglas aplicadas
  1. Delete rows where ISMISSING([Date_time])
  2. Delete rows where ISMISMATCHED([state/province], '[State]')
  3. Delete rows where ISMISSING([state/province])
  4. Rename state/province to 'state_province'
  5. Delete rows where ISMISSING([country])
  6. Delete rows where ISMISSING([UFO_shape])
  7. Delete rows where ISMISSING([description])
  8. Convert text in state_province to UPPERCASE
  9. Convert text in country to UPPERCASE
  10. Convert text in UFO_shape to Propercase
  11. Standardize UFO_shape
  12. Lock length_of_encounter_seconds type to Decimal
  13. Set latitude to latitude / 1000000
  14. Set longitude to longitude / 1000000
  15. Keep rows where latitude >= -90 && latitude <= 90
  16. Keep rows where longitude >= -180 && longitude <= 180

# 5. Consultas SQL Realizadas (BigQuery)
   1. Total de avistamientos:
      SELECT COUNT(*) AS total_sightings
      FROM `ovni.avistamientos`

   2. Avistamientos por forma de OVNI:
      SELECT UFO_shape, COUNT(*) AS Sightings
      FROM `ovni.avistamientos`
      WHERE country = 'US'
      GROUP BY UFO_shape
      ORDER BY Sightings DESC
      LIMIT 5;

   3. Avistamientos por estado:
      SELECT state_province AS State, COUNT(*) AS Sightings
      FROM `ovni.avistamientos`
      WHERE country = 'US'
      GROUP BY state_province
      ORDER BY Sightings DESC
      LIMIT 10;

   4. Avistamientos por año:
      SELECT EXTRACT(YEAR FROM DATETIME(Date_time)) AS Year, COUNT(*) AS Sightings
      FROM `ovni.avistamientos`
      WHERE country = 'US'
      GROUP BY Year
      ORDER BY Sightings DESC;

   5. Promedio de duracion por forma:
      SELECT UFO_shape, ROUND(AVG(length_of_encounter_seconds), 2) AS                 
      Avg_Encounter_Seconds
      FROM `ovni.avistamientos`
      GROUP BY UFO_shape
      ORDER BY Avg_Encounter_Seconds DESC
      LIMIT 10;

# 6. Visualizaciones (Looker Studio)
   - Indicador de resumen (Scorecards o KPIs)
      * Indicador total de avistamientos: 487
      * Indicador de rango de años: 1945-2023
      * Indicador de estados con reportes: 37
      * Indicador de formas distintas: 23
    
   - Tabla y grafico de barras: Formas mas vistas de OVNIs
       * Tabla: top 5 formas de OVNIs (Other, Light, Circle, Triangle, Fireball)
       * Grafico de barras verticales:  Frecuencia de cada forma, con colores morados.
       * Tabla ordenada (con numeracion y columnas: UFO_shape y Sightings)
       * Grafico de barra categorico

   - Mapa de calor por estado
     * Mapa de EE.UU coloreado por intensidad de avistamientos
     * Indicadores de estado con mas y menos avistamientos (CA y PR)
     * Cantidad total de estados con reportes: 52
     * Mapa de coropletas con escala de intensidad
     * Scorecards adicionales

   - Grafico de torta: Distribucion de duracion de encuentros
     * Grafico circular con porcentajes por rango de duracion
       * <1 min
       * 1-5 min
       * 5-15 min
       * 15-60 min
       * 1-4 hrs
       * 4 hrs tipo: grafico de pastel con leyenda categorica y porcentajes
      
   - Graficos de evolucion temporal por año
     * Grafico de lineas o columnas de tendencia: Vision historica desde 1950 hasta 2020 aprox.
     * Grafico de barras verticales por año: Top de años con mas avistamientos (2012, 2013, 
     etc.)
     * Tabla con cantidad por año.
     * Serie temporal (linea o barras).
     * Barras categoricas por año.
     * Tabla detallada.

# 7. Conclusiones
- El mayor numero de avistamientos se concentran entre 2000 y 2012.
- Las formas mas vistas fueron "Light", "Circle" y "Triangle".
- Los estados con mayor numero de avistamientos fueron IL, CA, TX y FL.
- La mayoria de los encuentros dura menos de 5 minutos, sugiriendo que son breves y espontaneos.
     
