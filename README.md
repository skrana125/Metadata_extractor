# 🚀 SAS-to-PySpark Metadata Extraction Framework

![Python](https://img.shields.io/badge/Python-3.13%2B-blue?logo=python&logoColor=white)
![ANTLR4](https://img.shields.io/badge/ANTLR-4.9.3-red?logo=java&logoColor=white)
![PySpark](https://img.shields.io/badge/PySpark-Optimized-E25A1C?logo=apachespark&logoColor=white)
![Enterprise](https://img.shields.io/badge/Scale-Enterprise_Grade-success)

> A compiler-driven automation pipeline designed to accelerate the modernization of legacy SAS assets into cloud-native PySpark architectures.

By leveraging an **ANTLR4 Island Grammar**, this pipeline ingests complex corporate SAS scripts, safely bypasses volatile procedural logic, and extracts a comprehensive structural metadata blueprint. This blueprint acts as a highly organized inventory, enabling engineering teams to plan data lineages, identify cross-script execution dependencies, map transformations, and eliminate technical debt with zero manual guesswork.

---

## 📑 Table of Contents
1. [Core Extraction Targets](#-core-extraction-targets)
2. [Automated Table Classification Engine](#-automated-table-classification-engine)
3. [Project Directory Structure](#-project-directory-structure)
4. [Prerequisites & Environment Setup](#-prerequisites--environment-setup)
5. [Execution Guide](#-execution-guide)
6. [Operational Metadata Output Example](#-operational-metadata-output-example)

---

## 🎯 Core Extraction Targets

The automation engine parses legacy codebases to isolate three foundational operational pillars:

* 🗄️ **Data Environment Configurations (Libraries):** * Logical directory definitions (`LIBNAME`)
  * Target database engines (Oracle, Teradata)
  * Physical network / JDBC connection strings
* ⚙️ **Automation Frameworks (Macros):** * Template structures (`%MACRO` to `%MEND`)
  * Dynamic variable scopes (`%LET`)
  * Passed parameters
* 🛠️ **Analytical Machinery (Procedures):** * Data-manipulation engines (`PROC SORT`, `PROC TRANSPOSE`, `PROC MEANS`)
  * Active control variables (`BY`, `CLASS`, `VAR`, `ID`)

---

## 📊 Automated Table Classification Engine

The framework executes automated graph-based lineage analysis on all discovered data structures, classifying every table into one of five mutually exclusive categories. This drives memory management and storage strategies in the cloud:

| 🏷️ Table Category | 🔍 Code Detection Logic | 💡 PySpark Engineering Strategy |
| :--- | :--- | :--- |
| **1. Permanent Input** | Read from via `SET`/`MERGE`/`DATA=`; contains a schema prefix; never modified. | Initialize as a standard read node (e.g., `spark.read.table`). |
| **2. Permanent Output** | Written to via `DATA`/`OUT=`; contains a permanent storage schema prefix. | Map to a persistence terminal node (e.g., `.write.saveAsTable`). |
| **3. Intermediate** | Simultaneously read from and written to within the exact same script file. | Map as an **in-memory virtual DataFrame**. Do not write to physical disk. |
| **4. Temporary Input** | Read from prior to any local write; lacks a permanent schema prefix. | Flag as a cross-script execution dependency for orchestrators (Airflow). |
| **5. Pure Temporary** | Created locally but never referenced by any subsequent downstream statement. | Redundant legacy clutter. **Omit completely** to optimize cloud runtime. |

---

## 📁 Project Directory Structure

```text
c:/FedExWork/FedEx_GCP/Metadata_extraction/
│
├── SAS.g4                      # ANTLR4 Unified Grammar Definition File
├── extract_metadata.py         # Defensive Python Extraction Utility & Graph Engine
├── antlr-4.9.3-complete.jar    # Java Compiler Core Executable
│
├── SASLexer.py                 # Generated Tokenizer (Auto-compiled)
├── SASParser.py                # Generated Syntactic Parser (Auto-compiled)
├── SASListener.py              # Generated Structural Listener (Auto-compiled)
│
├── BU-Marketing/               # Default Local Input Folder (Contains raw .sas files)
└── metadata_bu_sas/            # Default Local Output Folder (Contains structured .json blueprints)
