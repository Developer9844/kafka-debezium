kubectl create -f 'https://strimzi.io/install/latest?namespace=kafka' -n

# create kafka namespace
kubectl create namespace kafka

# create secret file for docker creds
kubectl create secret generic regcred \
    --from-file=.dockerconfigjson=~/.docker/config.json \
    --type=kubernetes.io/dockerconfigjson

# or
base64 -w 0 ~/.docker/config.json


kubectl run -n kafka -it --rm --image=quay.io/debezium/tooling:1.2  --restart=Never watcher -- kcat -b my-cluster-kafka-bootstrap:9092 -C -o beginning -t mysql.inventory.customers


curl -X POST \
  http://localhost:8083/connectors \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "my-source-connector",
    "config": {
      "connector.class": "org.apache.kafka.connect.file.FileStreamSourceConnector",
      "topic": "my-topic",
      "tasks": "2",
      "type": "source",
      "database.hostname": "mysql.default.svc.cluster.local",
      "database.port": "3306",
      "database.user": "root",
      "database.password": "debezium",
      "database.server.i": "223344",
      "database.server.name": "mysql",
      "topic.prefix": "my-topic",
      "database.include.list": "inventory",
      "schema.history.internal.kafka.bootstrap.servers": "my-cluster-kafka-bootstrap:9092",
      "schema.history.internal.kafka.topic": "schemahistory.inventory",
      "include.schema.changes": "true"
    }
  }'


INSERT INTO customers (first_name, last_name, email) VALUES ('John', 'Doe', 'john.doe@example.com');

UPDATE customers SET email = 'new.email@example.com' WHERE first_name = 'John';



