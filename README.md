This project involves extracting and analyzing key financial data from 10-K filings of Microsoft, Tesla, and Apple for the last three fiscal years. The project also includes the development of a simplified AI chatbot prototype that responds to predefined financial queries. This was accomplished during an internship with BCG X.

## Table of Contents
- [Introduction](#introduction)
- [Project Structure](#project-structure)
- [Data Extraction and Analysis](#data-extraction-and-analysis)
- [Chatbot Prototype](#chatbot-prototype)
- [Requirements](#requirements)
- [Installation](#installation)
- [Usage](#usage)
- [Contributing](#contributing)

## Introduction

The goal of this project is twofold:
1. **Data Extraction and Analysis**: Manually extract key financial data from the 10-K filings of Microsoft, Tesla, and Apple for the last three fiscal years and analyze the data using Python to identify trends and insights.
2. **Chatbot Prototype**: Develop a simplified AI chatbot prototype that can respond to predefined financial queries based on the analyzed data.

## Project Structure

```
.
├── data_extraction
│   └── financial_data.xlsx
├── analysis
│   └── financial_analysis.py
├── chatbot
│   ├── chatbot.py
│   └── intents.json
├── requirements.txt
└── README.md
```

- `data_extraction/`: Contains the raw financial data.
- `analysis/`: Contains the Python script for analyzing the extracted data.
- `chatbot/`: Contains scripts and data files for the chatbot prototype.
- `requirements.txt`: Lists the Python dependencies for the project.
- `README.md`: Project documentation.

## Data Extraction and Analysis

### Data Extraction

The data extraction phase involves manually collecting key financial data from the 10-K filings of Microsoft, Tesla, and Apple. These filings can be found on the SEC's EDGAR database.

### Analysis

The analysis script `financial_analysis.py` uses Python to analyze the extracted data. The analysis focuses on identifying trends and insights that could inform the development of an AI-powered financial chatbot.

### Analysis Script

```python
import pandas as pd
import matplotlib.pyplot as plt

# Load the data
data = pd.read_excel('data_extraction/financial_data.xlsx')

# Calculate growth rates
data['Revenue Growth'] = data.groupby('Company')['Revenue ($M)'].pct_change() * 100
data['Net Income Growth'] = data.groupby('Company')['Net Income ($M)'].pct_change() * 100

# Fill NA values that result from pct_change calculations with 0 or an appropriate value
data.fillna(0, inplace=True)

# Display the dataframe to verify the calculations
print(data)

# Optionally, you could summarize these findings for each company
summary = data.groupby('Company').agg({
    'Revenue Growth': 'mean',
    'Net Income Growth': 'mean'
}).reset_index()

print("\nYear-over-Year Average Growth Rates (%):")
print(summary)

# Plot revenue and net income trends
for company in data['Company'].unique():
    company_data = data[data['Company'] == company]
    plt.figure(figsize=(14, 7))

    plt.subplot(2, 1, 1)
    plt.plot(company_data['Year'], company_data['Revenue ($M)'], marker='o', label='Revenue')
    plt.title(f'{company} Revenue and Net Income Over Time')
    plt.ylabel('Revenue ($M)')
    plt.legend()

    plt.subplot(2, 1, 2)
    plt.plot(company_data['Year'], company_data['Net Income ($M)'], marker='o', color='orange', label='Net Income')
    plt.xlabel('Year')
    plt.ylabel('Net Income ($M)')
    plt.legend()

    plt.tight_layout()
    plt.show()

# Analyze assets, liabilities, and equity trends
for company in data['Company'].unique():
    company_data = data[data['Company'] == company]
    plt.figure(figsize=(14, 7))

    plt.plot(company_data['Year'], company_data['Total Assets ($M)'], marker='o', label='Total Assets')
    plt.plot(company_data['Year'], company_data['Total Liabilities ($M)'], marker='o', label='Total Liabilities')
    plt.plot(company_data['Year'], company_data['Shareholders\' Equity ($M)'], marker='o', label='Shareholders\' Equity')
    plt.title(f'{company} Financial Position Over Time')
    plt.xlabel('Year')
    plt.ylabel('$M')
    plt.legend()

    plt.tight_layout()
    plt.show()
```

## Chatbot Prototype

The chatbot prototype is a simplified version of an AI chatbot for financial analysis. It uses predefined intents and responses to answer basic financial queries. The chatbot is trained using a simple JSON-based intent file.

### Key Components

- `chatbot.py`: The main script to run the chatbot.
- `intents.json`: Contains predefined intents and responses for the chatbot.

### Chatbot Script

```python
import pandas as pd

# Load the financial data from the Excel file
file_path = 'data_extraction/financial_data.xlsx'
financial_data = pd.read_excel(file_path)

def get_total_revenue(company):
    data = financial_data[financial_data['Company'] == company]
    total_revenue = data['Revenue ($M)'].sum()
    return total_revenue

def get_net_income_change(company):
    data = financial_data[financial_data['Company'] == company]
    net_income = data['Net Income ($M)'].values
    if len(net_income) >= 2:
        change = net_income[-1] - net_income[-2]
        return change
    return None

def get_total_assets(company):
    data = financial_data[financial_data['Company'] == company]
    total_assets = data['Total Assets ($M)'].sum()
    return total_assets

def get_total_liabilities(company):
    data = financial_data[financial_data['Company'] == company]
    total_liabilities = data['Total Liabilities ($M)'].sum()
    return total_liabilities

def get_eps(company):
    data = financial_data[financial_data['Company'] == company]
    eps = data['EPS ($)'].values[-1] if len(data) > 0 else None
    return eps

def simple_chatbot(user_query, company):
    if user_query == "What is the total revenue?":
        total_revenue = get_total_revenue(company)
        return f"The total revenue for {company} is ${total_revenue} million."
    elif user_query == "How has net income changed over the last year?":
        net_income_change = get_net_income_change(company)
        if net_income_change is not None:
            change_direction = "increased" if net_income_change > 0 else "decreased"
            return f"The net income for {company} has {change_direction} by ${abs(net_income_change)} million over the last year."
        else:
            return "Insufficient data to determine the net income change."
    elif user_query == "What are the total assets?":
        total_assets = get_total_assets(company)
        return f"The total assets for {company} are ${total_assets} million."
    elif user_query == "What are the total liabilities?":
        total_liabilities = get_total_liabilities(company)
        return f"The total liabilities for {company} are ${total_liabilities} million."
    elif user_query == "What is the EPS (Earnings Per Share)?":
        eps = get_eps(company)
        if eps is not None:
            return f"The EPS (Earnings Per Share) for {company} is ${eps}."
        else:
            return "Insufficient data to determine the EPS."
    else:
        return "Sorry, I can only provide information on predefined queries."

# Example usage
company = "Microsoft"
print(simple_chatbot("What is the total revenue?", company))
print(simple_chatbot("How has net income changed over the last year?", company))
print(simple_chatbot("What are the total assets?", company))
print(simple_chatbot("What are the total liabilities?", company))
print(simple_chatbot("What is the EPS (Earnings Per Share)?", company))
```

## Requirements

- Python 3.8+
- pandas
- matplotlib

## Installation

Clone the repository and install the required dependencies:

```bash
git clone https://github.com/yourusername/ai-financial-chatbot.git
cd ai-financial-chatbot
pip install -r requirements.txt
```

## Usage

### Data Analysis

Run the analysis script:

```bash
python analysis/financial_analysis.py
```

### Chatbot

Run the chatbot script:

```bash
python chatbot/chatbot.py
```

## Contributing

Contributions are welcome! Please fork the repository and submit a pull request with your changes.
