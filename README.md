# 🥗 NutriBot — Personalized Nutrition Agentic AI

[![Python](https://img.shields.io/badge/Python-3.9%2B-blue?logo=python)](https://www.python.org/)
[![Flask](https://img.shields.io/badge/Flask-3.0-black?logo=flask)](https://flask.palletsprojects.com/)
[![IBM watsonx.ai](https://img.shields.io/badge/IBM-watsonx.ai-054ADA?logo=ibm)](https://www.ibm.com/watsonx)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

> A **web-based Agentic AI Nutrition Assistant** powered by **Meta LLaMA 3.3 70B Instruct** (via **IBM watsonx.ai**) that generates fully personalised, evidence-based nutrition plans — with built-in BMI calculator, TDEE estimator, healthy recipe finder, and a contextual Q&A chatbot.

---

## 📸 Features at a Glance

| Tab | Feature |
|---|---|
| 📋 **Nutrition Plan** | Fill in your profile → get a personalised, 7-section AI-generated nutrition plan |
| 💬 **Ask NutriBot** | Contextual Q&A chatbot with suggested question chips |
| 🍽️ **Recipe Finder** | Enter available ingredients → get healthy, condition-aware recipes |
| ⚖️ **BMI Tool** | Visual BMI calculator with animated colour bar and health tips |
| ℹ️ **About** | Feature overview, ethics summary, and legal disclaimer |

---

## 🏗️ Project Structure

```
nutrition_ai/
├── app.py                  # Flask backend + IBM watsonx.ai agent logic
├── requirements.txt        # Python dependencies
├── .gitignore              # Git ignore rules
├── README.md               # This file
└── templates/
    └── index.html          # Single-page web UI (5 tabs, fully responsive)
```

---

## 🧠 Architecture

```
Browser (index.html)
      │
      │  HTTP POST (JSON)
      ▼
Flask Backend (app.py)
      │
      ├── /api/generate_plan  ──► Agentic Tool: BMI + TDEE ──► LLM Prompt ──► Nutrition Plan
      ├── /api/query          ──► Context-aware Q&A prompt  ──► LLM Answer
      ├── /api/recipe         ──► Recipe prompt             ──► Healthy Recipes
      └── /api/bmi            ──► BMI Calculator (local)
                                          │
                                          ▼
                              IBM watsonx.ai
                              meta-llama/llama-3-3-70b-instruct
```

---

## ⚙️ Prerequisites

- Python **3.9** or higher
- `pip` package manager
- An active **IBM watsonx.ai** account with:
  - A valid **Project ID**
  - A valid **API Key**
  - Access to `meta-llama/llama-3-3-70b-instruct`

---

## 🚀 Installation & Setup

### 1. Clone the repository

```bash
git clone https://github.com/swatik31-hub/Project_Nutriant.git
cd Project_Nutriant
```

### 2. (Optional) Create a virtual environment

```bash
python -m venv venv

# Windows
venv\Scripts\activate

# macOS / Linux
source venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure IBM watsonx.ai credentials

Open [`app.py`](app.py) and update the credentials section (lines 15–18):

```python
PROJECT_ID   = "your-project-id"
API_KEY      = "your-api-key"
ENDPOINT_URL = "https://au-syd.ml.cloud.ibm.com"   # change region if needed
MODEL_ID     = "meta-llama/llama-3-3-70b-instruct"
```

> ⚠️ **Security tip:** For production, use environment variables instead of hardcoding credentials.  
> Set `WATSONX_API_KEY`, `WATSONX_PROJECT_ID` as env vars and read them with `os.getenv()`.

### 5. Run the application

```bash
python app.py
```

### 6. Open in browser

```
http://localhost:5000
```

---

## 🔑 IBM watsonx.ai Configuration

| Setting | Value |
|---|---|
| Endpoint URL | `https://au-syd.ml.cloud.ibm.com` |
| Model | `meta-llama/llama-3-3-70b-instruct` |
| Max New Tokens | 1200 |
| Temperature | 0.65 |
| Top-P | 0.9 |
| Repetition Penalty | 1.15 |

---

## 🛡️ AI Ethics & Guardrails

NutriBot is built with responsible AI principles at its core:

| Guardrail | Implementation |
|---|---|
| **Input Safety** | Regex-based scan of all user inputs before any model call |
| **Output Safety** | Regex-based scan of all model outputs before sending to browser |
| **HAM Principle** | Every prompt instructs the model: **H**elpful, **A**ccurate, **M**indful |
| **No Medication Advice** | Explicit prompt instruction: never tell users to stop prescribed medication |
| **Mandatory Disclaimer** | Medical disclaimer auto-appended to every generated nutrition plan |
| **Scope Limiting** | Off-topic or harmful questions are politely redirected |
| **Input Validation** | Server-side field validation and type checking on all API endpoints |

---

## 🤖 Agentic Tools

| Tool | Function | Endpoint |
|---|---|---|
| **BMI Calculator** | Computes Body Mass Index + WHO category | Auto-runs on plan generation & `/api/bmi` |
| **TDEE Estimator** | Mifflin-St Jeor formula for daily calorie needs | Auto-runs on plan generation |
| **Nutrition Planner** | 7-section personalised plan via LLM | `POST /api/generate_plan` |
| **Q&A Agent** | Context-aware nutrition chatbot | `POST /api/query` |
| **Recipe Finder** | Healthy, condition-aware recipes from ingredients | `POST /api/recipe` |

---

## 🗂️ API Reference

### `POST /api/generate_plan`
Generates a personalised nutrition plan.

**Request body:**
```json
{
  "name": "Priya Sharma",
  "age": 34,
  "gender": "female",
  "weight": 65,
  "height": 162,
  "diet": "Vegetarian",
  "activity": "moderate",
  "goal": "Weight loss",
  "medical_history": "Type 2 Diabetes",
  "allergies": "Lactose intolerant",
  "city": "Mumbai",
  "country": "India"
}
```

**Response:**
```json
{
  "plan": "## Personalized Nutrition Plan for Priya Sharma\n...",
  "bmi": 24.8,
  "bmi_cat": "Normal weight",
  "tdee": 1842
}
```

---

### `POST /api/query`
Answers a nutrition question with optional user context.

**Request body:**
```json
{
  "query": "What foods are good for managing blood pressure?",
  "user_context": { "name": "Priya", "diet": "Vegetarian", ... }
}
```

---

### `POST /api/recipe`
Suggests healthy recipes from available ingredients.

**Request body:**
```json
{
  "ingredients": "spinach, chickpeas, tomatoes, olive oil",
  "diet": "Vegan",
  "condition": "Diabetes"
}
```

---

### `POST /api/bmi`
Calculates BMI and returns WHO category.

**Request body:**
```json
{ "weight": 70, "height": 170 }
```

**Response:**
```json
{ "bmi": 24.2, "category": "Normal weight" }
```

---

## ⚠️ Medical Disclaimer

> This application is powered by AI and is intended for **general informational purposes only**.  
> The nutrition plans and advice generated do **NOT** constitute medical advice, diagnosis, or treatment.  
> Always consult a qualified healthcare professional or registered dietitian before making significant changes  
> to your diet, especially if you have pre-existing medical conditions.  
> **Never disregard professional medical advice based on AI-generated content.**

---

## 📦 Tech Stack

| Layer | Technology |
|---|---|
| Backend | Python 3.9+, Flask 3.x |
| AI / LLM | IBM watsonx.ai — `meta-llama/llama-3-3-70b-instruct` |
| SDK | `ibm-watsonx-ai` |
| Frontend | HTML5, CSS3, Vanilla JavaScript (no framework) |
| Deployment | Local / Any Python-compatible host |

---

## 📄 License

This project is licensed under the **MIT License**.

---

## 🙏 Acknowledgements

- [IBM watsonx.ai](https://www.ibm.com/watsonx) for the LLM platform
- [Meta LLaMA 3](https://ai.meta.com/llama/) for the language model
- [Flask](https://flask.palletsprojects.com/) for the web framework
