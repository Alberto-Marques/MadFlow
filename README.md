# MadFlow

MadFlow es un proyecto en el que se implementa una Arquitectura Big Data completa de análisis de los datos de flujos de
 personas en la ciudad de Madrid a partir de distintas fuentes de datos.
 
##Guía de Instalación

Para el desarrollo se ha hecho la instalación en Mac pero es compatible Linux adaptando la instalación de Hadoop

###Hadoop
Instalación con brew:
```
$ brew install hadoop
```
Seguir la siguiente guía de configuración:

https://medium.com/beeranddiapers/installing-hadoop-on-mac-a9a3649dbc4d

Una vez configurado exportar las variables de configuración
```
$ export HADOOP_INSTALL=/usr/local/opt/hadoop/libexec
$ export HADOOP_CONF_DIR=$HADOOP_INSTALL/etc/hadoop
```

###Kafka
```
$ wget http://apache.rediris.es/kafka/2.4.0/kafka_2.12-2.4.0.tgz
$ tar -xvf kafka_2.12-2.4.0.tgz
```

###Spark
```
$ wget https://apache.brunneis.com/spark/spark-3.0.1/spark-3.0.1-bin-hadoop3.2.tgz
```

Exportar la variable de entorno del Spark Home
```
$ export SPARK_HOME=/Users/alberto/TFM/spark-3.0.1-bin-hadoop3.2/
```
###Druid

```
$ wget http://apache.uvigo.es/druid/0.17.0/apache-druid-0.17.0-bin.tar.gz && 
tar -xzf apache-druid-0.17.0-bin.tar.gz && cd apache-druid-0.17.0
```
configurar el número máximo de tasks simultáneas que puede ejecutar seteando la propiedad 
druid.worker.capacity=4 editando el siguiente fichero:
```
$ vi conf/druid/single-server/micro-quickstart/middleManager/runtime.properties
```

###Superset
seguir los pasos de la siguiente guía:

https://superset.apache.org/docs/installation/installing-superset-from-scratch

Crear un fichero de configuración y añadirle las siguientes configuraciones:
```
vi /incubator-superset/superset/superset_config.py

MAPBOX_API_KEY = "TU PROPIO TOKEN"
DRUID_IS_ACTIVE = True
```

Exportar las variables de entorno (con el path en el que se haya descargado):
```
$ export SUPERSET_CONFIG_PATH="/Users/alberto/TFM/incubator-superset/superset/superset_config.py"
$ export SUPERSET_HOME="/Users/alberto/TFM/incubator-superset/superset"
$ export FLASK_APP=superset
```
##Guía de Arranque

###Hadoop
```
$ source ~/.profile
$ hstart
```
###Zookeeper
```
$ bin/zookeeper-server-start.sh config/zookeeper.properties
```

###Kafka
Arrancamos Kafka en segundo plano:
```
$ bin/kafka-server-start.sh -daemon config/server.properties
```

Creamos los Topics necesarios para el proyecto:
```
$ bin/kafka-topics.sh --zookeeper localhost:2181 --create --topic traffic-topic --partitions 1 --replication-factor 1

$ bin/kafka-topics.sh --zookeeper localhost:2181 --create --topic bicimad-topic --partitions 1 --replication-factor 1

$ bin/kafka-topics.sh --zookeeper localhost:2181 --create --topic parkings-topic --partitions 1 --replication-factor 1

$ bin/kafka-topics.sh --zookeeper localhost:2181 --create --topic traffic-druid-stream --partitions 1 --replication-factor 1

$ bin/kafka-topics.sh --zookeeper localhost:2181 --create --topic bicimad-druid-stream --partitions 1 --replication-factor 1

$ bin/kafka-topics.sh --zookeeper localhost:2181 --create --topic parkings-druid-stream --partitions 1 --replication-factor 1

$ bin/kafka-topics.sh --zookeeper localhost:2181 --create --topic traffic-druid-history-topic --partitions 1 --replication-factor 1
```
Comprobamos la lista de topics creados
```
$ bin/kafka-topics.sh --list --zookeeper localhost:2181
```

###Spark
Para ejecutar los procesos spark usar:
```
spark-submit --packages org.apache.spark:spark-sql-kafka-0-10_2.11:2.3.4 NOMBRE_PROCESO_SPARK.py
```
###Druid
```
$ env DRUID_SKIP_JAVA_CHECK=1 ./bin/start-micro-quickstart
```
Comprobamos que está funcionando en http://localhost:8888/unified-console.html


###Superset

```
$ superset run -p 8088 --with-threads --reload --debugger
```
Comprobamos que está funcionando en http://localhost:8088/

