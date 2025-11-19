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
Create a **.env** file:
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
This script automatically performs:

1. PLAN – Generates analysis plan
2. CATEGORIZE – Classifies every transaction
3. COMPUTE KPIs – Income, expenses, ratios, summaries
4. SUMMARIZE – User-friendly financial report
5. REFLECT – Model self-evaluates and improves

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
