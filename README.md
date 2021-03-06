# TDC 2017

## Contatos
- vinicius.remigio@gmail.com
- https://linkedin.com/in/viniremigio/

## Serasa Experian DataLab
- Blog: https://www.serasaexperian.com.br/datalabs-noticias/
- Projetos: https://www.serasaexperian.com.br/datalabs/

## Links
- https://developerzen.com/best-practices-writing-production-grade-pyspark-jobs-cb688ac4d20f
- https://aws.amazon.com/pt/blogs/big-data/running-jupyter-notebook-and-jupyterhub-on-amazon-emr/
- https://www.mapd.com/demos/
- https://github.com/mapd
- https://www.mapd.com/blog/2017/05/08/mapd-open-sources-gpu-powered-database/
- https://www.mapd.com/docs/mapd2/latest/MapDGuide.pdf
- https://www.mapd.com/docs/latest/getting-started/hwRefCfgGuide/
- https://www.mapd.com/blog/2017/01/23/what-is-a-gpu-and-why-do-i-care-a-businesspersons-guide-2/
- http://gpuopenanalytics.com/
- https://insidebigdata.com/2017/09/08/2017-year-gpu-databases/
- https://devblogs.nvidia.com/parallelforall/goai-open-gpu-accelerated-data-analytics/
- https://hackernoon.com/which-gpu-database-is-right-for-me-6ceef6a17505 

## Snippets

### MapD-core no Docker
```
nvidia-docker run \
-d \
-p 9090-9093:9090-9093 \
--name mapd-ce \
--net=host \
-v /mapd-storage:/mapd-storage \
-v /usr/share/glvnd/egl_vendor.d:/usr/share/glvnd/egl_vendor.d \
mapd/mapd-ce-cuda
```
### MapD Importers
```
COPY example from '/tmp/example.csv' WITH (nulls = 'NA');
```

#### Sqoop
```
sqoop-export\
--table sptrans\
--export-dir hdfs:///data/example\
--fields-terminated-by ";"\
--connect "jdbc:mapd:data.example.com:9091:mapd"\
--driver com.mapd.jdbc.MapDDriver\
--direct\
--batch
```

#### Kafka
```
# Producer
cat example.csv | bin/kafka-console-producer.sh --broker-list localhost:9097
--topic example_topic
# Consumer
./bin/kafka-simple-consumer-shell.sh --broker-list localhost:9097 --topic example_topic
--from-beginning | /home/mapd2/build/bin/StreamInsert --port 9091 -p
HyperInteractive --database mapd --table example --user mapd --batch 1
```

#### Spark
```
df = spark.read.option("header", "true").csv(args.input)

df.createOrReplaceTempView(“smartzones_data")
df = spark.sql("""
SELECT data, hora, linha, carro, latitude, longitude
FROM smartzones_data
""")

df.write.jdbc(url="jdbc:mapd:<HOST>:9091:mapd", 
   table="smartzones_data", mode="append", 
   properties= {"driver":"com.mapd.jdbc.MapDDriver","user":“<USER>",         "password":“<PASSWORD>"})

```

#### EMR + Jupyter
```
BootstrapActions=[
  {
'Name': 'Jupyter Notebook',
'ScriptBootstrapAction': {
'Path': 's3://aws-bigdata-blog/artifacts/aws-blog-emr-jupyter/install-jupyter-emr5.sh',
'Args': [
'--s3fs', '--python3',
'--python-packages', 'pandas matplotlib findspark boto3',
'--port', '8880', '--password' ,'jupyter', 
'--cached-install', 
'--notebook-dir', 's3://mybucket/notebooks/']
	}
  },
],

```
