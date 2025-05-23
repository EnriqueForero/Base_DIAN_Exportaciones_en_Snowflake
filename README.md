# Creación y mantenimiento de base DIAN de exportaciones en Snowflake
## 📝 Descripción

Este proyecto automatiza la ingesta, limpieza, consolidación y carga de datos históricos de exportaciones de la DIAN (Dirección de Impuestos y Aduanas Nacionales de Colombia), cubriendo un período de 25 años (ej. 2001-2025), hacia una tabla maestra en Snowflake. Está diseñado específicamente para manejar las variaciones estructurales (nombres de columnas, tipos de datos) y de formato presentes en los archivos a lo largo del tiempo. Adicionalmente, genera reportes de diagnóstico detallados en formato Word (.docx) sobre la estructura, calidad y valores únicos de los datos procesados por año.

## 🌟 Características

*   **Lectura eficiente:** Procesa archivos ZIP que contienen datos en formato CSV, TXT o Excel, leyendo directamente desde memoria para optimizar rendimiento.
*   **Consolidación robusta:** Unifica datos de múltiples archivos mensuales o anuales en un solo conjunto de datos coherente o en archivos consolidados por año.
*   **Manejo de inconsistencias:** Implementa lógica para alinear esquemas de datos variables entre diferentes años, mapeando columnas y normalizando tipos de datos.
*   **Carga optimizada a Snowflake:** Utiliza el conector de Snowflake y `write_pandas` para cargar grandes volúmenes de datos de manera eficiente (en chunks) a una tabla destino.
*   **Procesamiento por Lotes:** Permite procesar y cargar archivos año por año, o por meses o en lotes definidos.
*   **Análisis y Diagnóstico:** Genera reportes automáticos en formato Word (.docx) que detallan la estructura, estadísticas descriptivas, valores únicos y análisis de valores nulos para cada archivo o año procesado.
*   **Entorno Colab:** Preparado para ejecutarse en Google Colab, incluyendo montaje de Google Drive. Esto se puede extender a otros entornos.

## 📁 Estructura del Proyecto

*   `2025_05_08_dian_procesar_exportaciones_y_subir_a_snowflake.ipynb`: Script principal de Python con el código completo (originalmente un notebook Colab).
*   `README.md`: Este archivo, que proporciona documentación del proyecto.
*   `snowflake_credentials.json`: Archivo para almacenar credenciales de Snowflake de forma segura.
*   `/Word/`: (Directorio de salida) Contiene los reportes de diagnóstico generados.
*   `/Procesados/`: (Directorio de salida) Contiene los archivos ZIP consolidados por año.
*   `/Dataframe/`: (Directorio de salida, opcional) Podría contener DataFrames de valores únicos exportados.

## 🛠️ Requisitos

*   **Python 3.9+**
*   **Bibliotecas:**
    *   `pandas`: Manipulación y análisis de datos.
    *   `numpy`: Soporte para operaciones numéricas.
    *   `snowflake-connector-python[pandas]`: Conector oficial de Snowflake con extras para Pandas (`write_pandas`).
    *   `snowflake-sqlalchemy`: Para algunas interacciones con Snowflake (puede ser opcional dependiendo del método de conexión final).
    *   `python-docx`: Para generar los reportes de diagnóstico en formato Word.
    *   `openpyxl`, `xlrd`: Necesarios para que Pandas pueda leer archivos Excel (.xlsx, .xls).
    *   `missingno`: Para visualización de matrices de nulidad (en el análisis exploratorio).
    *   `matplotlib`, `seaborn`: Para generar gráficos en los reportes (opcionalmente usados en funciones de diagnóstico).
    *   `pytz`: Para manejo de zonas horarias.
    *   `psutil`: (Opcional) Para monitorear el uso de memoria durante ejecuciones largas.

## ⚙️ Configuración y Personalización

*   **Credenciales de Snowflake:** Modificar el diccionario `configuracion_sf` o las variables individuales (`USER_SNOWFLAKE`, etc.) con sus credenciales. **Importante:** Se recomienda encarecidamente externalizar las credenciales a un archivo de configuración seguro (como `snowflake_credentials.json` manejado por la función `guardar/cargar_configuracion`) o variables de entorno, y *no* dejarlas hardcodeadas en el script.
*   **Rutas de directorios:** Ajustar las variables de ruta (`ruta_raw_data`, `ruta_exportar_word`, `ruta_archivos_procesados`, etc.) según la ubicación de sus datos y donde desee guardar los resultados.
*   **Tabla destino Snowflake:** Modificar la variable `tabla_maestra` con el nombre exacto de la tabla destino en Snowflake.
*   **Parámetros de procesamiento:** Ajustar variables como `chunk_size`, `limite_valores_std`, `limite_valores_reducido` para optimizar el rendimiento y el detalle de los análisis según los recursos disponibles.
*   **Selección de Años/Meses/Archivos:** Modificar las listas `lista_años` o `archivos_filtrados` para controlar qué período o archivos específicos se procesan en cada ejecución.
*   **Codificación:** Revisar el parámetro `encoding_lectura` o `encoding_` en las funciones si sus archivos fuente tienen una codificación diferente a la estándar (ej. 'latin1' en lugar de 'utf-8').

## 📝 Funciones Principales

*   `procesar_zip_optimizado`: Lee eficientemente el contenido (CSV/TXT/Excel) de un único archivo ZIP.
*   `unificar_exportaciones` / `unify_export_zips`: Consolida datos de múltiples archivos ZIP en un archivo ZIP de salida (con un TXT interno).
*   `sf_append_file_to_snowflake_in_chunks_final`: Carga datos de un archivo (DataFrame en memoria) a una tabla existente en Snowflake por bloques (chunks), manejando alineación de columnas y tipos de datos.
*   `cargar_multiples_archivos_snowflake_mejorado`: Orquesta la carga de múltiples archivos ZIP a Snowflake, llamando a la función anterior para cada uno y mostrando un resumen detallado.
*   `ejecutar_carga_completa`: Función principal que envuelve el proceso completo de carga de una lista de archivos.
*   `diagnosticar_archivo_exportacion_año`: Genera un reporte detallado en Word para un único archivo/año consolidado.
*   `procesar_multiples_archivos`: Ejecuta la función anterior para una lista de archivos, generando un reporte por cada uno y un resumen general.
*   `analizar_nulls_in_batches`, `categorizar_valores_nulos`, `generar_reporte_calidad_datos`: Funciones para el análisis de calidad de datos (valores nulos) consultando Snowflake.
*   `sf_query_to_snowflake_and_print`, `st_query_to_snowflake_and_return_dataframe`: Funciones auxiliares para ejecutar consultas SQL en Snowflake y obtener resultados (impresos o como DataFrame).

## 🤝 Contribuciones

Las contribuciones son bienvenidas. Si encuentras errores, tienes sugerencias de mejora o quieres añadir nuevas funcionalidades, por favor, abre un issue o envía un pull request.

## 📄 Licencia

Este proyecto está bajo la licencia MIT. Consulta el archivo LICENSE (si existe) para más detalles.

## ✉️ Contacto

Si tienes preguntas o comentarios, no dudes en contactarme:

Enrique Forero - enrique.economista@gmail.com

GitHub: https://github.com/enriqueforero

## 📝 Notas Adicionales y Mejoras

*   **Entorno de Ejecución:** El script fue desarrollado y probado principalmente en Google Colab. Podrían requerirse ajustes menores para ejecutarlo en otros entornos (ej. manejo de rutas, instalación de dependencias).
*   **Seguridad de Credenciales:** Como se mencionó, es fundamental mover las credenciales de Snowflake en un archivo externo y no subirlo a repositorios compartidos públicamente.
*   **Manejo de Memoria:** Para datasets extremadamente grandes (cientos de GB), la consolidación completa en memoria antes de la carga podría ser inviable. En esos casos, se requeriría una estrategia de carga directa por chunks desde el archivo fuente a Snowflake o el uso de almacenamiento intermedio en disco.
*   **Optimización Snowflake:** Una vez cargados los datos, se pueden aplicar optimizaciones adicionales en Snowflake como definir `CLUSTER KEYS` en la tabla maestra (ej. por año, país o NIT) para acelerar consultas.
*   **Orquestación:** Para ejecuciones recurrentes o más complejas, integrar este script en un orquestador como Apache Airflow o Prefect sería beneficioso.
