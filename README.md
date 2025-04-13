# BIGDATA
# Análisis de Datos de Freelancers (Batch y Streaming)

Este proyecto realiza un análisis exploratorio de datos (EDA) sobre un conjunto de datos de ganancias de freelancers y también demuestra un pipeline de procesamiento en tiempo real usando Kafka y Spark Streaming.

## Descripción

1.  **Análisis Exploratorio (Batch):**
    * Carga datos desde un archivo CSV (`freelancer_earnings_bd.csv`).
    * Limpia los datos (elimina valores nulos).
    * Calcula indicadores clave (ingresos promedio, duración, tarifas, calificaciones) agrupados por diferentes categorías (país, región, tipo de proyecto, etc.).
    * Genera visualizaciones (gráficas de barras) para los indicadores clave usando Matplotlib y Seaborn.
    * Exporta los indicadores calculados a un archivo CSV (`output/indicadores_calculados.csv`).
    * Guarda las gráficas generadas en la carpeta `output/graficas/`.
2.  **Procesamiento en Tiempo Real (Streaming):**
    * Utiliza un script productor (`scripts/kafka_producer.py`) para simular datos de freelancers y enviarlos a un topic de Kafka (`freelancer_data`).
    * Utiliza un script consumidor (`scripts/spark_consumer.py`) con PySpark Streaming para leer datos del topic de Kafka, parsearlos y realizar agregaciones en tiempo real (ej: ganancias promedio por región y plataforma).
    * Muestra los resultados agregados en la consola.

## Prerrequisitos

Asegúrate de tener instalados los siguientes componentes:

* **Python 3:** `python3 --version`
* **Java:** (Necesario para Kafka y Spark) `java -version`
* **Kafka:** Instalado y corriendo. Verifica con `kafka-topics --version` (o un comando similar según tu instalación).
* **Spark:** Instalado. Verifica con `spark-submit --version`.
* **Bibliotecas Python:** `pip install -r requirements.txt`

## Estructura del Proyecto

freelancer_analysis/
├── data/
│   └── freelancer_earnings_bd.csv
├── output/
│   ├── graficas/
│   └── indicadores_calculados.csv
├── scripts/
│   ├── eda_analysis.py
│   ├── kafka_producer.py
│   ├── spark_consumer.py
│   └── setup_infra.sh
├── requirements.txt
└── README.md

## Configuración y Ejecución

1.  **Clonar el Repositorio:**
    ```bash
    git clone <URL_DEL_REPOSITORIO>
    cd freelancer_analysis
    ```

2.  **Colocar el Archivo de Datos:**
    * Asegúrate de que tu archivo `freelancer_earnings_bd.csv` esté en la ruta correcta. Edita la ruta en `scripts/eda_analysis.py` si es necesario. Por ejemplo, colócalo en la carpeta `data/`.

3.  **Instalar Dependencias Python:**
    ```bash
    pip install -r requirements.txt
    ```

4.  **(Opcional) Preparar Infraestructura (Kafka Topic):**
    * Asegúrate de que Kafka (Zookeeper y Broker) esté corriendo.
    * Puedes usar el script `scripts/setup_infra.sh` (si lo creas) o ejecutar el comando manualmente:
      ```bash
      # Reemplaza '/opt/Kafka/bin/' con la ruta a tus binarios de Kafka si es diferente
      /opt/Kafka/bin/kafka-topics.sh \
        --create \
        --bootstrap-server localhost:9092 \
        --replication-factor 1 \
        --partitions 1 \
        --topic freelancer_data
      ```
    * Verifica las versiones de las herramientas si es necesario:
      ```bash
      java -version
      python3 --version
      spark-submit --version
      # Reemplaza '/opt/Kafka/bin/' si es necesario
      /opt/Kafka/bin/kafka-topics.sh --version
      ```

5.  **Ejecutar el Análisis Exploratorio (Batch):**
    * Edita las rutas de entrada (`pd.read_csv`) y salida (`plt.savefig`, `df_indicadores.to_csv`) en `scripts/eda_analysis.py` para que coincidan con tu estructura (ej: `data/freelancer_earnings_bd.csv`, `output/graficas/...`, `output/indicadores_calculados.csv`).
    * Ejecuta el script:
      ```bash
      python scripts/eda_analysis.py
      ```
    * Revisa los resultados en la carpeta `output/`.

6.  **Ejecutar el Pipeline de Streaming:**
    * Abre dos terminales.
    * **Terminal 1 (Productor Kafka):**
      ```bash
      python scripts/kafka_producer.py
      ```
      (Verás datos siendo enviados).
    * **Terminal 2 (Consumidor Spark Streaming):**
      * Asegúrate de que la configuración de Spark (ej. `master`, dependencias de Kafka) sea adecuada para tu entorno.
      * Ejecuta el script usando `spark-submit`. Necesitarás el paquete de integración Spark-Kafka. La forma exacta puede variar según tu instalación de Spark, pero un ejemplo común es:
        ```bash
        # Reemplaza <SPARK_VERSION> y <SCALA_VERSION> con las tuyas (ej: 3.3.0 y 2.12)
        # Reemplaza <KAFKA_CLIENT_VERSION> con la tuya (ej: 3.3.0)
        spark-submit --packages org.apache.spark:spark-sql-kafka-0-10_<SCALA_VERSION>:<SPARK_VERSION> scripts/spark_consumer.py
        ```
      (Verás las agregaciones actualizándose en la consola).

## Notas

* El script `eda_analysis.py` asume que las columnas tienen nombres específicos como "pais", "ingresos_promedio", "region", etc. Ajusta los nombres si tu CSV es diferente.
* El manejo de valores nulos (`df.dropna()`) es simple. Podrías necesitar estrategias más sofisticadas (imputación) dependiendo del caso.
* Las rutas de archivo están como placeholders (`/ruta/al/archivo/`, `/ruta/de/exportacion/`). **¡Debes reemplazarlas!** Se sugiere usar rutas relativas como en la estructura de carpetas propuesta.
* El productor Kafka genera datos *aleatorios* basados en listas predefinidas; no lee del CSV original.
* La configuración de Spark (`spark-submit`) puede requerir ajustes adicionales (memoria, cores, etc.) según tu entorno y la carga de trabajo.
