# SmartRetailLakehouse

End-to-end Microsoft Fabric data lakehouse demo simulating the **Bronze-Silver-Gold** medallion architecture with CI/CD, GitHub integration, and optional Power BI reporting.

---

## Project Overview 

This project demonstrates a modern data engineering workflow using Microsoft Fabric, PySpark, notebooks, Delta format tables, and Azure DevOps. It includes:

-Ingesting raw sales data (Bronze Layer)
-Data transformation and cleanup (Silver Layer)
-Aggregates for analytics (Gold Layer)
-CI/CD automation with GitHub and YAML pipelines
-Semantic model + Power BI reporting

---

## Project Structure 
📁 csv/
└─ sales.csv, products.csv, customers.csv
📁 notebooks/
├─ BronzeIngestion.ipynb
├─ SilverTransformation.ipynb
└─ GoldTransformation.ipynb
📁 pipelines/
└─ data-ci.yml
📄 vw_gold_sales.sql (SQL View creation script)
📄 README.md


---

## Notebooks Summary

### `BronzeIngestion.ipynb`
- Reads `sales.csv` from Lakehouse `Files/raw/`
- Cleans nulls and standardizes types
- Writes `Tables/Bronze_Sales` as a Delta table

### `SilverTransformation.ipynb`
- Reads `Tables/Bronze_Sales`
- Casts `Quantity`, `UnitPrice`, `TaxAmount`
- Calculates `TotalPrice = Quantity x UnitPrice`
- Writes to `Tables/Silver_Sales`

### `GoldTransformation.ipynb`
- Reads `Tables/Silver_Sales`
- Groups by `Item` and calculates:
  - `AvgUnitPrice`
  - `AvgTotalPrice`
- Writes to `Tables/Gold_Sales` or `noop` (test mode)

---

## Technologies Used

| Tool             | Purpose                                          |
|------------------|--------------------------------------------------|
| Microsoft Fabric | Lakehouse, Notebooks, Pipelines, OneLake        |
| PySpark          | Ingestion, transformation, aggregation           |
| Delta Format     | Transactional table format for lakehouse         |
| Azure DevOps     | CI/CD pipelines with YAML                        |
| GitHub           | Source control, documentation                    |
| Power BI (optional) | Visual report based on Gold layer             |

---

## CI/CD: `data-ci.yml`

Automates notebook validation and dependencies:

```yaml
trigger:
  - main

pool:
  vmImage: ubuntu-latest

steps:
- task: UsePythonVersion@0
  inputs:
    versionSpec: '3.x'
  displayName: 'Set up Python'

- script: |
    pip install pyspark
    echo "Simulating Fabric notebook validation..."
  displayName: 'Install dependencies and check notebooks'

- script: echo "CI pipeline executed successfully!"
  displayName: 'Finish pipeline'

---

## SQL View
CREATE OR ALTER VIEW dbo.vw_gold_sales AS
SELECT *
FROM OPENROWSET(
    BULK 'https://goldlayerdata123.dfs.core.windows.net/Gold_Sales/',
    FORMAT = 'DELTA'
) AS rows;

---

## Local Testing Without ADLS Gen2

Simulate writes with noop mode:

 df.write.format("noop").mode("overwrite").save("noop")

Or set up a Spark local session:

  os.environ["SPARK_LOCAL_DIRS"] = "/tmp"

spark = SparkSession.builder \
    .appName("SmartRetailLakehouse-Gold") \
    .config("spark.sql.warehouse.dir", "/tmp/warehouse") \
    .config("spark.sql.catalogImplementation", "in-memory") \
    .getOrCreate()

## Roadmap (Future Enhancements)
 Add Row-Level Security (RLS)
 Add MLFlow forecasting layer
 Export .pbix to GitHub for dashboard snapshots

Add dashboard links to README

