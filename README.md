# SAS-to-PySpark Metadata Extraction Framework

## 1. Overview
This framework is an enterprise-grade, compiler-driven automation pipeline designed to accelerate the modernization of legacy SAS assets into cloud-native PySpark architectures.

By leveraging an ANTLR4 Island Grammar, the pipeline ingests complex corporate SAS scripts, safely bypasses volatile procedural logic, and extracts a comprehensive structural metadata blueprint. This blueprint acts as a highly organized inventory, enabling engineering teams to plan data lineages, identify cross-script execution dependencies, map transformations, and eliminate technical debt with zero manual guesswork.

## 2. Core Extraction Targets
The automation engine parses legacy codebases to isolate three foundational operational pillars:

* **Data Environment Configurations (Libraries):** Captures logical directory definitions (`LIBNAME`), target database engines (Oracle, Teradata), and physical network or JDBC connection strings.
* **Automation Frameworks (Macros):** Maps template structures (`%MACRO` to `%MEND`), dynamic variable scopes (`%LET`), and passed parameters to streamline translation into native Python functions.
* **Analytical Machinery (Procedures):** Detects data-manipulation engines (`PROC SORT`, `PROC TRANSPOSE`, `PROC MEANS`) and tracks active control variables (`BY`, `CLASS`, `VAR`, `ID`) to instantly determine equivalent PySpark DataFrame API methods.

## 3. Automated Table Classification Engine
The framework executes automated graph-based lineage analysis on all discovered data structures, classifying every table into one of five mutually exclusive categories. This drives memory management and storage strategies in the cloud:

| Table Category | Code Detection Logic | PySpark Engineering Strategy |
| :--- | :--- | :--- |
| **1. Permanent Input Tables** | Read from via `SET`/`MERGE`/`DATA=`; contains a schema prefix; never modified. | Initialize as a standard read node (e.g., `spark.read.table`). |
| **2. Permanent Output Tables** | Written to via `DATA`/`OUT=`; contains a permanent storage schema prefix. | Map to a persistence terminal node (e.g., `.write.saveAsTable`). |
| **3. Intermediate Tables** | Simultaneously read from and written to within the exact same script file. | Map as an in-memory virtual DataFrame. Do not write to physical disk. |
| **4. Temporary Input Tables** | Read from prior to any local write; lacks a permanent schema prefix. | Flag as a cross-script execution dependency for orchestrators (Airflow). |
| **5. Pure Temporary Tables** | Created locally but never referenced by any subsequent downstream statement. | Redundant legacy clutter. Omit completely to optimize cloud runtime. |

## 4. Project Directory Structure

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
```

## 5. Prerequisites & Environment Setup
Ensure the following system architectures are installed and accessible via your console:

* **Java Runtime Environment (JRE):** Required by ANTLR4 to compile the grammar file.
* **Python 3.13+**
* **ANTLR4 Python Runtime Library:** Install the runtime package via your terminal:

```powershell
pip install antlr4-python3-runtime==4.9.3
```

## 6. Execution Guide

### Step 1: Regenerate the Parser Assemblies
If you modify rule boundaries inside `SAS.g4`, recompile the structural engine to update the underlying Python target classes:

```powershell
java -jar antlr-4.9.3-complete.jar -Dlanguage=Python3 SAS.g4
```

### Step 2: Execute the Batch Processing Pipeline
The Python utility features smart defaults. Running the script with zero arguments automatically processes the configured local directories:

```powershell
& C:/Users/sumit.rana/AppData/Local/Microsoft/WindowsApps/python3.13.exe c:/FedExWork/FedEx_GCP/Metadata_extraction/extract_metadata.py
```

*Note: To process specific directories on demand, pass your custom input and output folder locations as command-line arguments:*

```powershell
& C:/Users/sumit.rana/AppData/Local/Microsoft/WindowsApps/python3.13.exe c:/FedExWork/FedEx_GCP/Metadata_extraction/extract_metadata.py "C:/Your/Custom/SAS_Folder" "C:/Your/Custom/Output_Folder"
```

## 7. Operational Metadata Output Example
The extraction utility outputs clean, machine-readable JSON files. Below is an example of a generated architectural blueprint mapping a complex SAS macro and an analytical procedure step:

```json
{
    "libraries": [
        {
            "name": "MKT_DB",
            "path": "ORACLE USER=SUMIT SCHEMA=MARKETING PATH=DB_PROD"
        }
    ],
    "macro_variables_set": {
        "SEGMENT": "HIGH_VALUE"
    },
    "macros_defined": [
        {
            "name": "RUN_REPORT",
            "parameters": [
                "TARGET_YEAR"
            ]
        }
    ],
    "macros_invoked": [],
    "table_classifications": {
        "permanent_input_tables": [],
        "permanent_output_tables": [],
        "temporary_input_tables": [
            "SOURCE.SALES_&TARGET_YEAR"
        ],
        "intermediate_tables": [],
        "pure_temporary_tables": [
            "WORK.PIVOTED_&SEGMENT"
        ]
    },
    "data_steps": [],
    "procedures": [
        {
            "proc_type": "TRANSPOSE",
            "inputs": [
                "SOURCE.SALES_&TARGET_YEAR"
            ],
            "outputs": [
                "WORK.PIVOTED_&SEGMENT"
            ],
            "variables_used": {
                "BY": [
                    "CUSTOMER_ID"
                ],
                "VAR": [
                    "REVENUE"
                ],
                "CLASS": [],
                "MODEL": [],
                "TABLES": [],
                "ID": [
                    "MONTH_NAME"
                ]
            }
        }
    ]
}
```
