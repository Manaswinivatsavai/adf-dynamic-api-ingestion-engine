# Dynamic Multi-Source API Extractor & Lakehouse Ingest Engine

## 🏗️ Project Architecture Overview
This project implements an enterprise-grade, metadata-driven ingestion framework using **Azure Data Factory (ADF)**. The framework completely eliminates rigid, hardcoded extraction pipelines by implementing a generic template architecture that dynamically routes data from varied REST API endpoints directly into an **Azure Data Lake Storage Gen2 (ADLS Gen2)** raw landing zone.



## 🛠️ Tech Stack & Core Concepts
* **Orchestration Engine:** Azure Data Factory (ADF)
* **Storage Architecture:** Azure Data Lake Storage Gen2 (Hierarchical Namespace)
* **Source Systems:** REST APIs (JSON Payloads)
* **Key Mechanisms:** Parameterization, Iterative Control Loops, Dynamic Routing Expressions

---

## 🚀 Pipeline Logic & Data Flow

1. **Metadata Ingestion (Lookup Layer):** 
   The pipeline initiates a `Lookup Activity` to query a configuration system or directory endpoint. By disabling the `First Row Only` constraint, the lookup captures the full dynamic inventory array of target datasets.
   
2. **Dynamic Parameterization (Dataset Layer):** 
   Both the HTTP Source and the ADLS Sink datasets are decoupled using custom dataset parameters (`ds_p_Endpoint` and `ds_p_FileName`). No endpoints or file names are hardcoded.

3. **Parallel Iteration Loop (ForEach Layer):** 
   The array payload from the lookup (`@activity('lkp_get_api_inventory').output.value`) is passed into a `ForEach Activity`. The loop spins up concurrent execution channels to process each record independently.

4. **Dynamic File Generation (Copy Engine):** 
   Inside the execution loop, a single `Copy Data Activity` evaluates dynamic string concatenation expressions at runtime:
   * **Source URL Resolution:** `@concat('posts/', string(item().id))`
   * **Sink Filename Resolution:** `@concat('post_record_', string(item().id), '.json')`

---

## 📈 Key Engineering Achievements (ATS Keywords)
* **Zero-Hardcoding Framework:** Developed a reusable pipeline architecture where a single template processes an infinite number of target API paths simply by scaling the input metadata array.
* **Race-Condition Mitigation:** Parameterized sink file paths dynamically using unique runtime indices (`item().id`), eliminating file-locking conflicts and ensuring zero data collisions during parallel batch loading.
* **Enterprise Blueprinting:** Packaged the entire multi-layered infrastructure into a clean, reusable Azure Resource Manager (ARM) template for rapid deployment environments.
