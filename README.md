# 🚀 Incremental Data Pipeline in Azure Data Factory

This project demonstrates how to build an **incremental data pipeline** in **Azure Data Factory (ADF)**.  
The pipeline reads data from **Azure SQL Database**, processes incremental changes, and saves the results in **Parquet** format.

---

## 📌 Problem Statement
We need to perform **incremental data ingestion** from Azure SQL Server to Parquet.  
To track the **last processed timestamp**, we use a **JSON file** that stores the `max date` value.  

- Initially, the JSON file contains a **backdate**.  
- Each pipeline run updates this JSON file with the **latest max timestamp**.  
- Directly updating a JSON file in ADF is not possible, so we implement a **workaround**.

---

## ⚙️ Solution Approach

1. **Lookup Activity** – Reads the existing `max date` value from the JSON file.  
2. **Script Activity** – Runs a query against SQL Server to fetch the latest data greater than the `max date`.  
3. **If Condition Activity** – Checks whether new data is available.  
   - ✅ **True branch** → Executes incremental load:
     - **Copy Data Activity** → Copies new records.  
     - **Script Activity** → Extracts the new max timestamp.  
     - **Copy Data Activity** → Overwrites the original JSON file with the updated max date.  
   - ❌ **False branch** → No new data, pipeline ends.  

---


## 🛠 Key Implementation Details

- **JSON File Handling**  
  - Since ADF does not support direct JSON updates, we use an **empty.json** file.  
  - ADF **Copy Activity** overwrites the original JSON file with the new `max date`.  

- **Parquet Sink**  
  - Data is written in **Parquet format** for efficient storage and analytics.  

- **Incremental Load Logic**  
  - Data is always filtered based on `Last_Updated > max_date` from the JSON file.  
  - After ingestion, `max_date` is updated for the next run.  

---

## 📊 Benefits

- Efficient **incremental data ingestion**.  
- Avoids reloading entire datasets.  
- Works around ADF’s limitation of not being able to directly update JSON files.  
- Parquet format ensures **scalable storage** and **query performance**.  

---

## 📸 Screenshots
- **Main Pipeline**
  
  ![Main Pipeline](https://github.com/salmansajidsattar/Azure-Incremental-Data-Loading/blob/main/img/Screenshot%202025-10-03%20233601.png)

- **Validation step**
  
  ![True Branch](https://github.com/salmansajidsattar/Azure-Incremental-Data-Loading/blob/main/img/Screenshot%202025-10-03%20233650.png)


- **Max Query**
  
  ![True Branch](https://github.com/salmansajidsattar/Azure-Incremental-Data-Loading/blob/main/img/Screenshot%202025-10-02%20014909.png)


- **True Branch Execution**
  
  ![True Branch](https://github.com/salmansajidsattar/Azure-Incremental-Data-Loading/blob/main/img/Screenshot%202025-10-03%20233619.png)

---

## 📝 Next Steps
- Automate pipeline execution with **triggers** (schedule/event-based).  
- Add **data quality checks** before ingestion.  
- Extend to handle **Slowly Changing Dimensions (SCDs)**.  

---

## 🤝 Contributing
Feel free to fork this repo, raise issues, and submit pull requests to improve the pipeline design.  

---

