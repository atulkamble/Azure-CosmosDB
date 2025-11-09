**complete Cosmos DB practical + theory guide**, ideal for both **Terraform/ARM training** and **real-world Azure practice**. Let’s enhance this into a **final, training-ready “README.md” format** that you can publish in your GitHub repo (e.g., `azure-cosmosdb-lab`) 👇

---

# ☁️ **Azure Cosmos DB – Hands-On Guide (Portal, CLI, ARM, Terraform)**

## 🧭 Overview

Azure Cosmos DB is a **globally distributed, multi-model NoSQL database** designed for **high availability, low latency, and horizontal scalability**.
It supports **multiple APIs**, offers **99.999% availability**, and powers **AI-ready, real-time apps** worldwide.

---

## 📘 **1. Core Concepts**

| Concept                 | Description                                                             |
| ----------------------- | ----------------------------------------------------------------------- |
| **Global Distribution** | Automatically replicate data across regions.                            |
| **Multi-Model**         | Supports SQL (Core), MongoDB, Cassandra, Gremlin, and Table APIs.       |
| **Consistency Levels**  | Control trade-off between performance and accuracy (Strong → Eventual). |
| **Throughput (RU/s)**   | “Request Units” define performance capacity.                            |
| **Partitioning**        | Distributes data for scalability and balanced throughput.               |

---

## 🧪 **2. Practice Setup**

| Tool             | Action                                  |
| ---------------- | --------------------------------------- |
| **Azure Portal** | Manual GUI setup and region replication |
| **Azure CLI**    | Scripting and testing automation        |
| **Terraform**    | Infrastructure-as-Code provisioning     |
| **ARM Template** | Declarative JSON-based deployment       |

---

## 🏗️ **3. Create Cosmos DB Account**

### 🔹 Azure CLI

```bash
az cosmosdb create \
  --name mycosmosdb \
  --resource-group myResourceGroup \
  --kind GlobalDocumentDB \
  --locations regionName=eastus failoverPriority=0 isZoneRedundant=False
```

### 🔹 Terraform

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

### 🔹 ARM Template

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
          { "locationName": "East US", "failoverPriority": 0 }
        ]
      }
    }
  ]
}
```

---

## 🗄️ **4. Create Database and Container**

### 🔹 Azure CLI

```bash
az cosmosdb sql database create \
  --account-name mycosmosdb \
  --resource-group myResourceGroup \
  --name mydatabase

az cosmosdb sql container create \
  --account-name mycosmosdb \
  --resource-group myResourceGroup \
  --database-name mydatabase \
  --name mycontainer \
  --partition-key-path "/myPartitionKey"
```

### 🔹 Terraform

```hcl
resource "azurerm_cosmosdb_sql_database" "example" {
  name                = "mydatabase"
  resource_group_name = azurerm_resource_group.example.name
  account_name        = azurerm_cosmosdb_account.example.name
}

resource "azurerm_cosmosdb_sql_container" "example" {
  name                = "mycontainer"
  resource_group_name = azurerm_resource_group.example.name
  account_name        = azurerm_cosmosdb_account.example.name
  database_name       = azurerm_cosmosdb_sql_database.example.name
  partition_key_path  = "/myPartitionKey"
}
```

---

## 💾 **5. Insert & Manage Data**

### 🔹 Python SDK Example

```python
from azure.cosmos import CosmosClient, PartitionKey

url = "https://mycosmosdb.documents.azure.com:443/"
key = "your_primary_key"

client = CosmosClient(url, credential=key)
database = client.create_database_if_not_exists(id="mydatabase")
container = database.create_container_if_not_exists(
    id="mycontainer", partition_key=PartitionKey(path="/id"))

container.create_item({"id": "1", "name": "Atul", "city": "Pune"})
```

---

## ⚙️ **6. Scaling & Performance**

| Task                       | CLI Command                                                     |
| -------------------------- | --------------------------------------------------------------- |
| Update throughput          | `az cosmosdb sql container throughput update --throughput 1000` |
| Enable multi-region writes | `az cosmosdb update --enable-multiple-write-locations true`     |
| Change consistency         | via Portal or Terraform `consistency_policy` block              |

---

## 🔒 **7. Security & Access**

| Feature            | Command                                              |
| ------------------ | ---------------------------------------------------- |
| Private Endpoint   | `az network private-endpoint create ...`             |
| Firewall Rules     | `az cosmosdb update --ip-range-filter "192.168.1.1"` |
| Managed Identity   | `az cosmosdb update --assign-identity`               |
| Encryption at rest | Enabled by default with Microsoft-managed keys       |

---

## ♻️ **8. Backup & Restore**

```bash
az cosmosdb update \
  --name mycosmosdb \
  --resource-group myResourceGroup \
  --backup-policy-type Continuous
```

* **Point-in-time restore** available for continuous backups.
* Can restore a deleted account or specific container within the retention window.

---

## 📊 **9. Monitoring**

```bash
az monitor diagnostic-settings create \
  --name cosmos-monitor \
  --resource cosmos-resource-id \
  --logs '[{"category":"DataPlaneRequests","enabled":true}]'

az monitor metrics list \
  --resource cosmos-resource-id \
  --metric-name TotalRequests
```

---

## 🧠 **10. Interview & Exam Tips**

| Topic              | Key Point                                                       |
| ------------------ | --------------------------------------------------------------- |
| APIs               | SQL (Core), MongoDB, Cassandra, Gremlin, Table                  |
| Consistency Levels | Strong, Bounded Staleness, Session, Consistent Prefix, Eventual |
| Scaling            | Manual RU/s vs Autoscale                                        |
| Security           | Private Endpoint, Firewall, Role-Based Access                   |
| Backup             | Continuous or Periodic + Point-in-time restore                  |
| Monitoring         | Azure Monitor, Diagnostic Logs, Metrics                         |

---

## 🧩 **11. Real-World Project Integration**

| Scenario                 | Tool                        | Example                  |
| ------------------------ | --------------------------- | ------------------------ |
| **App with MongoDB API** | Terraform                   | `kind = "MongoDB"`       |
| **IoT data ingestion**   | Azure Functions + CosmosDB  | Stream IoT Hub data      |
| **AI Vector Search**     | CosmosDB + OpenAI           | Store embeddings for RAG |
| **Multi-region web app** | Cosmos DB + Traffic Manager | Global replication       |

---

## 🚀 **12. Useful References**

* [Azure Cosmos DB Documentation](https://learn.microsoft.com/azure/cosmos-db/)
* [Terraform AzureRM Provider – Cosmos DB](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/cosmosdb_account)
* [Azure CLI Reference](https://learn.microsoft.com/cli/azure/cosmosdb)
* [Azure ARM Templates Gallery](https://learn.microsoft.com/azure/templates/)

---
