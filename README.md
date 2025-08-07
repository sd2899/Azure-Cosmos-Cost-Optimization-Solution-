# Azure-Cosmos-Cost-Optimization-Solution

This repository contains a reference architecture for building a highly available, cost-optimized Cosmos DB architecture to store billing records with a tiered (hot/cold) storage model.

---

## Use Case

- Read-heavy billing system.
- Over 2 million records.
- Each record ~300 KB.
- Records older than 3 months are rarely accessed but must be retained.
- Response time for any record must be under a few seconds.
- Reduce Cosmos DB cost without losing availability.

---

## Architecture

- **Hot Container** (`billing-hot`): Stores recent billing records (last 3 months).
- **Cold Container** (`billing-cold`): Stores older records (>3 months) at reduced cost.
- **Azure Functions**: Automatically move records from hot to cold container.
- **Multi-region Writes**: Ensures high availability and low-latency global access.

---

## Cost Optimizations

- Serverless or low RU/s `billing-cold`.
- Disabled indexing in `billing-cold` (except ID).
- Optional compression of old data.
- Use Redis Cache or Blob fallback if needed.

---

## Detailed Solution 

1. Active Tier (Hot data):
   Keep only the last 3 months of billing records in Azure Cosmos DB ensures low-latency access.

3. Archive Tier (Cold data):
   Move older records (older than 3 months) to Azure Blob Storage, ideally in Cool or Archive access tiers (much cheaper) and compressed by year in json like format.

4. Azure Function(Automation):
   create a Azure Function daily or weekly to Query the records older than 3 months from Cosmos DB. Write those records to Azure Blob Storage. Delete the archived records from Cosmos DB.

5. High Availability:
   enable autoscale on a single container, or provision autoscale throughput on a database and share it among all the containers in the database.
   
