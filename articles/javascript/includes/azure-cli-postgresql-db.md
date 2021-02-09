---
ms.custom: devx-track-js
ms.topic: include
ms.date: 02/04/2021
---


## Create an Azure Database for PostgreSQL server resource with Azure CLI

Use the following Azure CLI [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) command in the [Azure Cloud Shell](https://shell.azure.com) to create a new PostgreSQL server resource for your database. 

```azurecli
az postgres server create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --location eastus \
    --name YOURRESOURCENAME \
    --admin-user YOUR-ADMIN-USER \
    --ssl-enforcement Disabled \
    --enable-public-network Enabled 
```

This command may take a couple of minutes to complete and creates a publicly available resource in the `eastus` region. 

The response includes your server's configuration details including: 
* Autogenerated password for the admin account
* Connection string

```text
{
  "additionalProperties": {},
  "administratorLogin": "YOUR-ADMIN-USER",
  "byokEnforcement": "Disabled",
  "connectionString": "postgres://YOUR-ADMIN-USER%40YOURRESOURCENAME:123456789@YOURRESOURCENAME.postgres.database.azure.com/postgres?sslmode=require",
  "earliestRestoreDate": "2021-02-08T21:56:20.130000+00:00",
  "fullyQualifiedDomainName": "YOURRESOURCENAME.postgres.database.azure.com",
  "id": "/subscriptions/.../resourceGroups/YOUR-RESOURCE-GROUP/providers/Microsoft.DBforPostgreSQL/servers/YOURRESOURCENAME",
  "identity": null,
  "infrastructureEncryption": "Disabled",
  "location": "eastus",
  "masterServerId": "",
  "minimalTlsVersion": "TLSEnforcementDisabled",
  "name": "YOURRESOURCENAME",
  "password": "123456789",
  "privateEndpointConnections": [],
  "publicNetworkAccess": "Enabled",
  "replicaCapacity": 5,
  "replicationRole": "None",
  "resourceGroup": "YOUR-RESOURCE-GROUP",
  "sku": {
    "additionalProperties": {},
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Disabled",
  "storageProfile": {
    "additionalProperties": {},
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageAutogrow": "Enabled",
    "storageMb": 51200
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

Before you can connect to the server, you need to configure your firewall rules to allow your client IP address through. 

## Add a firewall rule for your client IP address to PostgreSQL resource

By default, the firewall rules are not configured. You should add your client IP address so your client connection to the server with JavaScript is successful.

Use the following Azure CLI [az postgres server firewall-rule create](/cli/azure/postgres/server#az_postgres_server_firewall_rule_create) command in the [Azure Cloud Shell](https://shell.azure.com) to create a new firewall rule for your database. 


```azurecli
az postgres server firewall-rule create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --server YOURRESOURCENAME \
    --name AllowMyIP \
    --start-ip-address 123.123.123.123 \
    --end-ip-address 123.123.123.123
```

If you don't know your client IP address, use one of these methods:
* Use the Azure portal to view and change your firewall rules, which includes adding your detected client IP
* Run your Node.js code, the error about your firewall rules violation includes your client IP address

While you can add the full range of internet addresses as a firewall rule, 0.0.0.0-255.255.255.255, this leaves your server open to attacks. 

## Create a PostgreSQL database on the server with Azure CLI

Use the following Azure CLI [az postgres db create](/cli/azure/postgres/db#az_postgres_db_create) command in the [Azure Cloud Shell](https://shell.azure.com) to create a new PostgreSQL database on your server. 

```azurecli
az postgres db create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --server-name YOURRESOURCENAME \
    --name YOURDATABASENAME
```

## Get the PostgreSQL connection string with Azure CLI

Retrieve the PostgreSQL connection string for this instance with the [az postgres server show-connection-string](/cli/azure/postgres/server#az_postgres_server_show_connection_string) command:

```azurecli
az postgres server show-connection-string \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --server-name YOURRESOURCENAME
```

This returns the connection strings for the popular languages as a JSON object. You need to replace `{database}`, `{username}`, and `{password}` with your own values before using the connection string. Replace `YOURRESOURCENAME` with your resource name.

```json
{
  "connectionStrings": {
    "C++ (libpq)": "host=YOURRESOURCENAME.postgres.database.azure.com port=5432 dbname={database} user={username}YOURRESOURCENAME password={password} sslmode=require",
    "ado.net": "Server=YOURRESOURCENAME.postgres.database.azure.com;Database={database};Port=5432;User Id={username}@YOURRESOURCENAME;Password={password};",
    "jdbc": "jdbc:postgresql://YOURRESOURCENAME.postgres.database.azure.com:5432/{database}?user={username}@YOURRESOURCENAME&password={password}",
    "node.js": "var client = new pg.Client('postgres://{username}@YOURRESOURCENAME:{password}@YOURRESOURCENAME.postgres.database.azure.com:5432/{database}');",
    "php": "host=YOURRESOURCENAME.postgres.database.azure.com port=5432 dbname={database} user={username}@YOURRESOURCENAME password={password}",
    "psql_cmd": "postgresql://{username}@YOURRESOURCENAME:{password}@YOURRESOURCENAME.postgres.database.azure.com/{database}?sslmode=require",
    "python": "cnx = psycopg2.connect(database='{database}', user='{username}@YOURRESOURCENAME', host='YOURRESOURCENAME.postgres.database.azure.com', password='{password}', port='5432')",
    "ruby": "cnx = PG::Connection.new(:host => 'YOURRESOURCENAME.postgres.database.azure.com', :user => '{username}@YOURRESOURCENAME', :dbname => '{database}', :port => '5432', :password => '{password}')"
  }
}
``` 