---
title: Automated Data Retrieval and Empirical Analysis Using EDINET
description: A structured guide to collecting, processing, and analyzing Japanese financial reports using EDINET and Python.
categories: [Research]
tags: [EDINET, Python, Accounting, Text Analysis]
---

> **Note:** The materials previously scattered across different pages on this site have now been reorganized and compiled into this single, structured note. This version is intended to improve clarity, consistency, and ease of reference. 
{: .prompt-tip }

---

# About

This article documents the automated data retrieval tools I developed and shared during [the 67th HARS](https://www2.econ.osaka-u.ac.jp/~shiiba/seminar/hars2024.html) (Handai Accounting Research Seminar) 2024.

It provides a practical guide that consolidates environment setup, data retrieval, text processing, and regression analysis into a single workflow.


---

# 1. Environment Setup

Before starting data collection and text analysis, the following preparations are required:

1. Set up the basic environment  
2. Learn basic PowerShell operations  
3. Create a Python virtual environment  


## 1.1 Prerequisites

### System Requirements

- Windows or macOS (Linux not tested)  
- At least 2GB of free storage  
- Stable internet connection  

### Python Requirements

- Python 3.8 or higher  
- Updated pip  

#### Check Python Version

~~~~powershell
# Check Python version
python -V
~~~~



## 1.2 PowerShell Basics

PowerShell is used to run Python and manage project files.

### Common Commands

~~~~powershell
# Show current directory
pwd

# List files
ls

# Enter Downloads directory
cd Downloads

# Move to parent directory
cd ..

# Start Python interactive shell
python
~~~~

---

# 2. Virtual Environment Configuration

To avoid dependency conflicts between projects, a virtual environment is recommended.



## 2.1 Create and Activate Virtual Environment

The following commands create, activate, and configure the environment.

### Navigate to Project Directory

~~~~powershell
cd /path/to/your/project
~~~~

### Create Virtual Environment

~~~~powershell
python -m venv my_venv
~~~~

### Activate Environment (Windows)

~~~~powershell
my_venv\Scripts\Activate.ps1
~~~~

### Activate Environment (macOS)

~~~~bash
source my_venv/bin/activate
~~~~

### Install Required Packages

~~~~powershell
pip install edinet-explorer
pip install jtext
pip list
~~~~

---

# 3. EDINET Data Collection

After setting up the environment, financial reports can be retrieved.

EDINET Explorer provides:

- Automated report downloading  
- Data preprocessing  
- Export to CSV or Excel  



## 3.1 Launch Application

~~~~powershell
edinet-explorer
~~~~



## 3.2 Retrieve Data

Example date range:

~~~~text
2022/04/01~2022/05/01
~~~~

Reports can be exported for later analysis.

---

# 4. Text Analysis with JText

After obtaining financial reports, textual analysis is performed using JText.

Main functions:

- Calculate text length  
- Evaluate readability  
- Provide linguistic indicators  



## 4.1 Example Usage

~~~~python
from jtext import JText

# Create text object
text = JText("日本語のテキストを解析します。")

# Get text length
length = text.get_length()

# Print readability metrics
text.get_readability(print_detail=True)

print("Length:", length)
~~~~

---

# 5. Empirical Research Example

This section follows:

Li, F. (2008). Annual report readability, current earnings, and earnings persistence. Journal of Accounting and Economics, 45(2–3), 221–247. https://doi.org/10.1016/j.jacceco.2008.02.003



## 5.1 Model Specification

The regression model is:

~~~~text
Earnings_t = α + β × Length_t + ε_t
~~~~

Where:

- Earnings: Operating profit / Total assets  
- Length: Log of total word count in annual reports  

---

# 6. Regression Analysis in Python

The following code integrates data processing and regression analysis.

~~~~python
# Import required libraries
import pandas as pd
import numpy as np
import statsmodels.api as sm
from jtext import JText


# Load financial data
fin_df = pd.read_csv(
    "financials.csv",
    encoding="utf-16",
    sep="\t"
)


# Select relevant columns
fin_df = fin_df[
    ["docID", "annual_csv", "資産", "経常利益又は経常損失（△）"]
]


# Construct earnings variable
fin_df["Earnings"] = (
    fin_df["経常利益又は経常損失（△）"] / fin_df["資産"]
)


# Compute log text length
len_list = []

for f in fin_df["annual_csv"]:
    text = JText(f)
    length = text.get_length()
    log_len = np.log(length)
    len_list.append(log_len)

fin_df["length"] = len_list


# Remove missing values
fin_df = fin_df.dropna()


# Prepare regression variables
X = sm.add_constant(fin_df["length"])
y = fin_df["Earnings"]


# Fit OLS model
model = sm.OLS(y, X).fit()


# Display results
print(model.summary())
~~~~

---

# 7. Workflow Summary

The complete workflow consists of:

- Environment setup  
- Data collection using EDINET  
- Text processing with JText  
- Financial variable construction  
- Regression estimation  

This framework can be extended to:

- Readability analysis  
- Sentiment analysis  
- Disclosure quality studies  
- Corporate text research  

---

# 8. Conclusion

This article consolidates the core workflow for conducting text-based financial analysis using EDINET and Python.

It is intended as a reference for future research, teaching, and personal documentation.

Future work may extend this framework with additional variables, alternative models, and broader datasets.
