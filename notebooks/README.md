# SmartRetailLakehouse
End-to-end Microsoft Fabric data-lakehouse demo.

## Notebooks

### BronzeIngestion.ipynb
This notebook performs the Bronze layer ingestion for sales data in Microsoft Fabric. It:
- Reads `sales.csv` from `Files/raw/` in the Lakehouse
- Previews and cleans the data (drops nulls from key columns)
- Writes a Delta Lake table to `Tables/Bronze_Sales`
- Runs on PySpark within Fabric's notebook environment

### SilverTransformation.ipynb
This notebook builds the Silver layer for the sales dataset. It:
- Reads the `Bronze_Sales` Delta table
- Casts Quantity, UnitPrice, and TaxAmount to proper datatypes
- Adds a `TotalPrice` column (Quantity × UnitPrice)
- Writes the result to `Tables/Silver_Sales`
