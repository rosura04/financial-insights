**Agentic Financial Insights Workflow Using AWS Bedrock**

This project implements an Agentic AI financial analysis system powered by AWS Bedrock and modern LLM workflows. The system processes raw financial transactions and generates categorized data, financial KPIs, personalized summaries, and refinement feedback using a multi-stage agentic loop (Plan → Categorize → KPI Compute → Summarize → Reflect).

The project demonstrates:
* Real-world Bedrock integration using `boto3`
* Strong prompt engineering using RAFT/RISEN formats
* Schema-validated JSON outputs
* Automated agentic workflows capable of reasoning, acting, observing, correcting, and improving
* Reproducible financial insights built from structured data
---

**Built With**
* Python 3.10+
* AWS Bedrock
* boto3
* Pandas
* JSONSchema
* Jupyter Notebook / Python Scripts
---

**Getting Started**
This section explains how to set up the project locally and run the full agentic pipeline.

**Prerequisites**
You must have:
* An AWS account with Bedrock access enabled
* AWS credentials configured locally:
  ```bash
  aws configure
  ```
* Python installed:
  ```bash
  python --version
  ```

**Python Dependencies**
Install required packages:
```bash
pip install boto3 pandas jsonschema python-dotenv
```

---

**Installation**
### 1. Clone the repository
```bash
git clone https://github.com/<your-username>/<your-repo>.git
```

### 2. Install dependencies
```bash
pip install -r requirements.txt
```

### 3. Add AWS credentials
Update the **.env** file:
```
AWS_ACCESS_KEY_ID=YOUR_KEY
AWS_SECRET_ACCESS_KEY=YOUR_SECRET
AWS_REGION=us-east-1
BEDROCK_MODEL_ID=anthropic.claude-3-sonnet-v1
```

### 4. (Optional) Prevent accidental pushes to class base repo

```bash
git remote set-url origin <your-username>/<your-repo>
git remote -v
```
---

**Usage**
Run the full financial analysis workflow:
```bash
python src/main.py
```
This script has the steps:

1. PLAN – Generates analysis plan
2. CATEGORIZE – Classifies every transaction
3. COMPUTE KPIs – Income, expenses, ratios, summaries
4. SUMMARIZE – User-friendly financial report
5. REFLECT – Model self-evaluates and improves

**Prompts Used**

categorizationPrompt.txt:
```
[ROLE]
You are a financial transaction categorization assistant for a small business analytics system.

[AUDIENCE]
Your output is consumed by an automated analytics pipeline. It must be machine-readable.

[FORMAT]
You must ONLY return valid JSON using this exact structure:
{
  "categorized": [
    {
      "date": "",
      "merchant": "",
      "amount": 0,
      "category": ""
    }
  ]
}
Do not include comments or explanations outside the JSON.

[TASK]
Given a batch of transactions, assign each to one category:
- Shopping
- Dining
- Utilities
- Income
- Other

Rules:
- Dining: restaurants, cafes, bars, food delivery.
- Shopping: retail, groceries, online stores, general merchandise.
- Utilities: electricity, gas, internet, phone, water.
- Income: paychecks, employer transfers, refunds, credits.
- Other: anything that does not clearly match the above.

Preserve date, merchant, and amount exactly as given. If unsure, choose the closest reasonable category.
```
kpiPrompt.txt
```
[ROLE]
You are a financial KPI analysis assistant. Your job is to compute accurate and consistent
financial metrics from a set of categorized transactions.

[AUDIENCE]
Your output will be consumed by a downstream analytics pipeline that requires strict JSON
formatting and numeric accuracy. Do not include explanations or commentary.

[FORMAT]
Return ONLY valid JSON in the following structure:

{
  "kpis": {
    "total_spend": 0,
    "total_income": 0,
    "average_expense": 0,
    "top_merchants": [
      { "merchant": "", "count": 0 }
    ]
  }
}

Rules:
- total_spend: Sum of all POSITIVE amounts.
- total_income: Sum of ABSOLUTE VALUE of all NEGATIVE amounts.
- average_expense: Average of all POSITIVE amounts. If no expenses exist, return 0.
- top_merchants: Sorted by frequency (descending), return up to 3 merchants.

[TASK]
Given a list of categorized financial transactions, compute the above KPIs.
Preserve numeric precision and ensure all fields are returned, even if zero.
Do not exceed the JSON structure or add fields.
```
planningPrompt
```
[ROLE]
You are a financial analysis planning assistant designing an agentic workflow.

[AUDIENCE]
Your output will be used by a downstream pipeline that will call an LLM for each step.

[FORMAT]
You must ONLY return valid JSON in the following structure:
{
  "plan": [
    {"step": 1, "goal": "", "action": ""},
    ...
  ]
}

[TASK]
Define a 5-step plan to analyze one month of financial transactions including:
1 understanding the dataset,
2 categorizing transactions,
3 computing KPIs,
4 summarizing results,
5 reflecting on quality and improvements.
```
reflectionPrompt.txt
```
[ROLE]
You are a reflective financial analysis auditor.

[AUDIENCE]
Data engineers and analysts improving this workflow.

[FORMAT]
Return plain text with short bullet points under headings:
- Suggested Improvements
- Next Iteration Focus

[TASK]
“Review all your outputs. Identify at least two possible categorization or computation errors.
Suggest improvements or better rules for next time.”
```
summaryPrompt.txt
```
[ROLE]
You are a financial summarization assistant.

[AUDIENCE]
A small business owner who wants a quick, high-level view of their monthly finances.

[FORMAT]
Output must be plain text, at most 100 words. No JSON.

[TASK]
Using provided KPIs, write a concise, neutral summary of spending and income patterns.
Highlight major categories, top merchants, or notable trends if present.
```
Outputs are saved to:
```
outputs/
    plan.json
    categorized.json
    kpis.json
    summary.txt
    reflection.txt
```
### Example: Calling AWS Bedrock

```python
from bedrock_client import call_bedrock

response = call_bedrock("Your prompt here")
print(response["output"])
```
### Example: Validating JSON Against Schema

```python
from validators import validate_json
validate_json(response, "schemas/plan_schema.json")
```
**JSON Troubleshooting Notes**

Throughout development, we encountered several JSON-related issues:
Common Problems:
- LLM adding commentary instead of raw JSON
- Missing commas or mismatched braces
- Mis-typed field names
- Arrays not properly closed
- Categories incorrectly capitalized

Fixes Implemented:
- Stronger “return only JSON” constraints
- Added regex sanitization for stray characters
- JSON schema validation with automatic retries
- Used smaller, simpler field names
- Added reflection to detect errors

**Team Roles:**
- Mohammad - Prompt Engineer
- Rusayla - Financial Analyst
- Rasha - Data Engineer

As a team, we learned that:
- LLMs require very explicit structure to output valid JSON
- Adding schemas + retries solves ~90% of formatting issues
- Reflection dramatically improves accuracy
- FinTech automation benefits most from agentic design
- AWS Bedrock is reliable but requires careful prompt tuning
- The agentic loop is significantly more powerful than single-pass prompting
