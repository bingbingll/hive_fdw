# Hive_FDW


基于postgresql 封装的查询hive 库的组件。
pgsql  版本9.1以上


## Prepare

jdk要求1.8 以上。并确保当前机器可以与hive库联通。

## Building from Source

First, download the source code under the contrib subdirectory of the
PostgreSQL source tree and then build and install the FDW as below:

1) Create a link to your JVM in the PostgreSQL lib folder with commands something like

```
cd ~/pg/12.2/lib
ln -s /etc/alternatives/jre_1.8.0/lib/amd64/server/libjvm.so libjvm.so
   or
ln -s /usr/java/latest/jre/lib/amd64/server/libjvm.so libjvm.so
```

2) Build the FDW source

```
cd hive_fdw
make
make install
```

## To execute the FDW

1) Set the environment variables PGHOME,HIVE_HOME,HADOOP_HOME & HIVE_FDW_CLASSPATH before starting up PG.
These environment variables are read at JVM initialization time.

    PGHOME = Path to the PostgreSQL installation. 
    HIVECLIENT_JAR_HOME = The path containing the Hive JDBC client jar files required for the FDW to run successfully.
     
    配置环境变量 export HIVE_FDW_CLASSPATH=/usr/local/pgsql/lib/hive_fdw.jar:$(echo /opt/hive/hive-client-lib/*.jar | tr ' ' :)

## Usage

The following parameters can be set on a Hive2 foreign server object:

  * **`host`**: the address or hostname of the Hive2 server, Examples: "localhost" "127.0.0.1" "server1.domain.com".
  * **`port`**: the port number of the Hive2 server.


The following parameters can be set on a Hive foreign table object:

  * **`schema_name`**: the name of the schema in which the table exists. Defaults to "default".
  * **`table_name`**: the name of the Hive table to query.  Defaults to the foreign table name used in the relevant CREATE command.

Here is an example:


	-- load EXTENSION first time after install.
	CREATE EXTENSION hive_fdw;

        -- create server object
	CREATE SERVER hive_serv FOREIGN DATA WRAPPER hive_fdw
		OPTIONS(host 'localhost', port '10000');

	-- Create a user mapping for the server.
	CREATE USER MAPPING FOR public SERVER hive_serv OPTIONS(username 'test', password 'test');

	-- Create a foreign table on the server.
	CREATE FOREIGN TABLE test (id int) SERVER hive_serv OPTIONS (schema 'example',table 'oorder');

	-- Query the foreign table.
	SELECT * FROM test limit 5;
