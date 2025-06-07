# SmartRetailLakehouse
End-to-end Microsoft Fabric data-lakehouse demo.

# Notebooks

# BronzeIngestion.ipynb
This notebook performs the Bronze layer ingestion for sales data in Microsoft Fabric. It:
- Reads `sales.csv` from `Files/raw/` in the Lakehouse
- Previews and cleans the data (drops nulls from key columns)
- Writes a Delta Lake table to `Tables/Bronze_Sales`
- Runs on PySpark within Fabric's notebook environment
