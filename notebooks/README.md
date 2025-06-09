# SmartRetailLakehouse

End-to-end Microsoft Fabric data-lakehouse demo.

## Notebooks

### BronzeIngestion.ipynb
This notebook performs the **Bronze layer ingestion** for sales data in Microsoft Fabric. It:
- Reads `sales.csv` from the `Files/raw/` directory in the Lakehouse
- Previews and cleans the data (drops nulls from key columns)
- Writes a Delta Lake table to `Tables/Bronze_Sales`
- Runs on PySpark within Fabric's notebook environment

### SilverTransformation.ipynb
This notebook builds the **Silver layer** for the sales dataset. It:
- Reads the `Tables/Bronze_Sales` Delta table
- Casts `Quantity`, `UnitPrice`, and `TaxAmount` to proper datatypes
- Adds a `TotalPrice` column (`Quantity × UnitPrice`)
- Writes the result to `Tables/Silver_Sales`

### GoldTransformation.ipynb
This notebook performs **Gold layer aggregation** on the sales dataset. It:
- Reads the `Tables/Silver_Sales` table
- Groups by `Item` and calculates:
  - `AvgUnitPrice`: Average of `UnitPrice`
  - `AvgTotalPrice`: Average of `TotalPrice`
- Simulates writing the result to `Tables/Gold_Sales` (or `noop`) for local testing
- Runs on PySpark with a local Spark session when permissions to ADLS Gen2 are restricted

---

## Technologies Used
- **Microsoft Fabric**: Lakehouse and Notebook environment
- **PySpark**: Data ingestion, transformation, and aggregation
- **Delta format**: Used for storing and querying transactional datasets

---

## Local Testing & ADLS Gen2 Simulation
If ADLS Gen2 write permissions are unavailable (common in personal or trial environments), the following local Spark configuration is used to simulate the notebook runs:
```python
import os
from pyspark.sql import SparkSession

os.environ["SPARK_LOCAL_DIRS"] = "/tmp"

spark = SparkSession.builder \
    .appName("SmartRetailLakehouse-Gold") \
    .config("spark.sql.execution.arrow.pyspark.enabled", "true") \
    .config("spark.sql.warehouse.dir", "/tmp/warehouse") \
    .config("spark.sql.catalogImplementation", "in-memory") \
    .getOrCreate()
