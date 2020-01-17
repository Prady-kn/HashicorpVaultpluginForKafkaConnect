# Docker image for Confluent Kafka Connect with Hashicorp Vault Plugin
***

## Prerequisites

* Running instance of Kakfa
* Configure the Hashicrop Vault with required secrets and start the container as shown below.

## Running the Confluent Kafka connector container with Hashicorp Vault plugin:
*** 

$ docker run -d --name=cp-kafkaconnect-with-hashicorpvault --hostname kafkaconnect --link zookeeper --link kafka -p 28083:28083 -e CONNECT_BOOTSTRAP_SERVERS=kafka:29092 -e CONNECT_REST_PORT=28083 -e CONNECT_GROUP_ID="quickstart-avro" -e CONNECT_CONFIG_STORAGE_TOPIC="quickstart-avro-config" -e CONNECT_OFFSET_STORAGE_TOPIC="quickstart-avro-offsets" -e CONNECT_STATUS_STORAGE_TOPIC="quickstart-avro-status" -e CONNECT_CONFIG_STORAGE_REPLICATION_FACTOR=1 -e CONNECT_OFFSET_STORAGE_REPLICATION_FACTOR=1 -e CONNECT_STATUS_STORAGE_REPLICATION_FACTOR=1 -e CONNECT_KEY_CONVERTER="org.apache.kafka.connect.json.JsonConverter" -e CONNECT_VALUE_CONVERTER="org.apache.kafka.connect.json.JsonConverter" -e CONNECT_INTERNAL_KEY_CONVERTER="org.apache.kafka.connect.json.JsonConverter" -e CONNECT_INTERNAL_VALUE_CONVERTER="org.apache.kafka.connect.json.JsonConverter" -e CONNECT_REST_ADVERTISED_HOST_NAME="localhost" -e CONNECT_LOG4J_ROOT_LOGLEVEL=INFO -e CONNECT_CONFIG_PROVIDERS=vault -e CONNECT_CONFIG_PROVIDERS_VAULT_CLASS=*com.pradykn.kafkaconnect.core.VaultConfigProvider* -e CONNECT_CONFIG_PROVIDERS_VAULT.PARAM_ROLEID=`c33f5084-ec88-433d-a238-d19b8cf498c9` -e CONNECT_CONFIG_PROVIDERS_VAULT.PARAM_SECID=`4798b72b-b3a3-f241-4b02-cc45dfff4240` -e CONNECT_CONFIG_PROVIDERS_VAULT.PARAM_VAULTURL=`"http://172.17.0.2:8200/v1"` pradykn/confluent-kafka-connect-with-hashicorpvaultplugin



### Note:
>This is **not an official** Hashicorp Vault plugin.

> Update the highlighted Vault role id, secret id and the vault url accordingly.

> Update the other Kafka connect related values according to your environment/need.

## Creating Connector instance

POST http://localhost:28083/connectors

**Http Header:**

`Content-Type: application/json`

**Html Body:**
```javascript
{
   "name":"jdbc_source_mysql_01",
   "config":{
      "connector.class":"io.confluent.connect.jdbc.JdbcSourceConnector",
      "connection.url":"jdbc:sqlserver://<DB SERVER>:<DB PORT>;database=<DB NAME>",
      "connection.user":"${vault:/secrets/data/kg/sqlserver:userid}",
      "connection.password":"${vault:/secrets/data/kg/sqlserver:pwd}",
      "topic.prefix":"mysql-08-",
      "mode":"timestamp",
      "table.whitelist":"person_from",
      "timestamp.column.name":"dtcreatedon",
      "validate.non.null":false
   }
}
```

**Json properties:**
> connection.url: Database connection string

> connection.user : Specify the secret path and the key value as per Vault ex: ${vault:/secrets/data/kg/sqlserver:userid}, here *userid* is the key name as per Vault,  */kg/sqlserver* is the path & *secrets* is the secret engine name as per Vault.

![Vault ](./docs/vault_screen.PNG)

> connection.password : Points to Vault key containing the required password (Similar to connection.user).


