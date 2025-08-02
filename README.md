# Azure-CosmosDB

Azure Cosmos DB is a globally distributed, multi-model database service designed for scalability and performance. Since you are practicing **Azure ARM Templates** and **Terraform**, you can practice Cosmos DB provisioning and management using these tools along with the **Azure Portal** and **Azure CLI**.

---

## **Azure Cosmos DB Practice Topics**

### **1. Basics of Cosmos DB**

* Understand Cosmos DB’s features (global distribution, multi-model support, consistency levels).
* Explore different APIs:

  * **Core (SQL)** – NoSQL document DB.
  * **MongoDB** – MongoDB-compatible.
  * **Gremlin** – Graph database.
  * **Table** – Key-value store.
  * **Cassandra** – Column-family database.

---

### **2. Provisioning Cosmos DB**

✅ **Using Azure Portal**

* Create a new Cosmos DB account.
* Choose an API (SQL, MongoDB, etc.).
* Set up a database and a container.

✅ **Using Azure CLI**

```sh
az cosmosdb create --name mycosmosdb --resource-group myResourceGroup --kind GlobalDocumentDB --locations regionName=eastus failoverPriority=0 isZoneRedundant=False
```

✅ **Using Terraform**

```hcl
resource "azurerm_cosmosdb_account" "example" {
  name                = "mycosmosdb"
  location            = "East US"
  resource_group_name = "myResourceGroup"
  offer_type          = "Standard"
  kind                = "GlobalDocumentDB"

  consistency_policy {
    consistency_level = "Session"
  }

  geo_location {
    location          = "East US"
    failover_priority = 0
  }
}
```

✅ **Using ARM Templates**

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2021-06-15",
      "name": "mycosmosdb",
      "location": "East US",
      "properties": {
        "databaseAccountOfferType": "Standard",
        "locations": [
          {
            "locationName": "East US",
            "failoverPriority": 0
          }
        ]
      }
    }
  ]
}
```

---

### **3. Creating a Database and a Container**

✅ **Using Azure CLI**

```sh
az cosmosdb sql database create --account-name mycosmosdb --resource-group myResourceGroup --name mydatabase
az cosmosdb sql container create --account-name mycosmosdb --resource-group myResourceGroup --database-name mydatabase --name mycontainer --partition-key-path "/myPartitionKey"
```

✅ **Using Terraform**

```hcl
resource "azurerm_cosmosdb_sql_database" "example" {
  name                = "mydatabase"
  resource_group_name = azurerm_resource_group.example.name
  account_name        = azurerm_cosmosdb_account.example.name
}

resource "azurerm_cosmosdb_sql_container" "example" {
  name                  = "mycontainer"
  resource_group_name   = azurerm_resource_group.example.name
  account_name          = azurerm_cosmosdb_account.example.name
  database_name         = azurerm_cosmosdb_sql_database.example.name
  partition_key_path    = "/myPartitionKey"
}
```

---

### **4. Working with Data**

✅ **Insert Data using Azure CLI**

```sh
az cosmosdb sql container create --account-name mycosmosdb --resource-group myResourceGroup --database-name mydatabase --name mycontainer --partition-key-path "/id"

az cosmosdb sql container throughput update --account-name mycosmosdb --resource-group myResourceGroup --database-name mydatabase --name mycontainer --throughput 400
```

✅ **Using Python SDK**

```python
from azure.cosmos import CosmosClient

url = "https://mycosmosdb.documents.azure.com:443/"
key = "your_primary_key"

client = CosmosClient(url, credential=key)
database = client.create_database_if_not_exists(id="mydatabase")
container = database.create_container_if_not_exists(id="mycontainer", partition_key=PartitionKey(path="/id"))

container.create_item({"id": "1", "name": "Atul", "city": "Pune"})
```

---

### **5. Scaling and Performance Tuning**

✅ **Change Throughput**

```sh
az cosmosdb sql container throughput update --account-name mycosmosdb --resource-group myResourceGroup --database-name mydatabase --name mycontainer --throughput 1000
```

✅ **Consistency Levels**

* **Strong**
* **Bounded Staleness**
* **Session** (Default)
* **Consistent Prefix**
* **Eventual**

✅ **Enable Multi-Region Replication**

```sh
az cosmosdb update --name mycosmosdb --resource-group myResourceGroup --enable-multiple-write-locations true
```

---

### **6. Implementing Security**

✅ **Enable Private Endpoint**

```sh
az network private-endpoint create --name myPrivateEndpoint --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet --private-connection-resource-id mycosmosdb
```

✅ **Enable Firewall**

```sh
az cosmosdb update --name mycosmosdb --resource-group myResourceGroup --ip-range-filter "192.168.1.1"
```

✅ **Use Managed Identity for Access**

```sh
az cosmosdb update --name mycosmosdb --resource-group myResourceGroup --assign-identity
```

---

### **7. Backup & Restore**

✅ **Enable Continuous Backup**

```sh
az cosmosdb update --name mycosmosdb --resource-group myResourceGroup --backup-policy-type Continuous
```

---

### **8. Monitoring Cosmos DB**

✅ **Enable Diagnostic Logs**

```sh
az monitor diagnostic-settings create --name cosmos-monitor --resource cosmos-resource-id --logs '[{"category": "DataPlaneRequests", "enabled": true}]'
```

✅ **Check Metrics in Azure Monitor**

```sh
az monitor metrics list --resource cosmos-resource-id --metric-name TotalRequests
```

---




