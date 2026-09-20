# AI-Powered Exam Grading System Using multi models

An AI-powered automated examination evaluation system that uses **Vision Language Models (VLMs), Large Language Models (LLMs), and a multi-agent processing pipeline** to evaluate handwritten descriptive answers.

The system converts handwritten answer sheets into machine-readable text using **Qwen2.5-VL**, generates a structured marking scheme using **Llama 3**, evaluates student answers against the marking scheme, validates the generated evaluation, calculates the final score, and stores the results in a **MySQL database**.

---

## 🚀 Project Overview

Manual evaluation of descriptive and handwritten examination papers is time-consuming and requires significant human effort.

This project aims to automate the evaluation workflow using Generative AI.

The system follows the pipeline:

```text
Upload Answer Sheet + Answer Key
              ↓
        PDF/Image Processing
              ↓
          Qwen2.5-VL
              ↓
   Handwritten Answer Extraction
              ↓
         Llama 3 / Scheme Builder
              ↓
       Structured Marking Scheme
              ↓
         Llama 3 / Evaluator
              ↓
       Question-wise Evaluation
              ↓
        Validation Agent
              ↓
       Confidence Level
       (High / Medium / Low)
              ↓
        Score Calculation
              ↓
       SQLAlchemy + MySQL
              ↓
          Final Result
```

---

## 🎯 Objectives

The main objectives of the project are:

- Automate descriptive examination evaluation.
- Recognize handwritten answers using a Vision Language Model.
- Convert answer sheets into machine-readable text.
- Automatically generate a structured marking scheme from the answer key.
- Compare student answers with expected answers using an LLM.
- Support full, partial, and zero marks.
- Validate the generated AI evaluation.
- Calculate the final examination score automatically.
- Store examination results in a MySQL database.
- Reduce the manual effort involved in evaluating large numbers of answer sheets.

---

## 🧠 Key Concept

The project uses multiple AI components, where each component performs a specific task.

### 1. Vision Language Model — Qwen2.5-VL

Qwen2.5-VL is used for understanding the scanned/photographed answer sheet.

The PDF is first converted into page images using **PyMuPDF**.

```text
Answer Sheet PDF
       ↓
    PyMuPDF
       ↓
   Page Images
       ↓
 Qwen2.5-VL
       ↓
Extracted Handwritten Text
```

Qwen2.5-VL performs vision-based text recognition and extracts the student's handwritten answer content.

> The project does not use a separate traditional OCR engine such as Tesseract or EasyOCR for the student answer sheet.

---

### 2. Large Language Model — Llama 3

Llama 3 is used for the language-based reasoning stages of the system.

It is executed locally using **Ollama**.

The LLM is used for:

- Generating the marking scheme.
- Evaluating student answers.
- Assigning marks.
- Validating the generated evaluation.

---

## 🤖 Multi-Agent Architecture

The system divides the LLM workflow into multiple specialized agents.

### Extractor Agent

Responsible for processing the output from the vision model and extracting the student's answer content.

```text
Answer Sheet Image
        ↓
   Qwen2.5-VL
        ↓
Student Answer Text
```

---

### Scheme Builder Agent

The Scheme Builder Agent processes the answer key and creates a structured marking scheme.

```text
Answer Key
    ↓
Scheme Builder Agent
    ↓
Structured Marking Scheme
```

The scheme can contain information such as:

```text
Question Number
Expected Answer
Key Concepts
Allocated Marks
```

Example:

```json
{
  "question_number": "1",
  "expected_answer": "Machine learning is a subset of artificial intelligence...",
  "allocated_marks": 5
}
```

---

### Evaluator Agent

The Evaluator Agent compares the student's extracted answer with the structured marking scheme.

```text
Student Answer
      +
Marking Scheme
      ↓
Evaluator Agent
      ↓
Question-wise Evaluation
```

The evaluation can assign:

- Full marks
- Partial marks
- Zero marks

The evaluator considers the semantic meaning of the student's answer rather than relying only on exact text matching.

---

### Validator Agent

The Validator Agent checks the generated evaluation and produces a qualitative confidence level.

Possible outputs:

```text
High
Medium
Low
```

The confidence value represents the AI system's assessment of the generated evaluation and should not be interpreted as a statistically calibrated probability.

---

## 🏗️ System Architecture

```text
                         ┌───────────────────┐
                         │       User        │
                         └─────────┬─────────┘
                                   │
                                   ▼
                    ┌──────────────────────────┐
                    │      Web Interface       │
                    │      HTML/CSS/JS         │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │         FastAPI          │
                    │       Backend API        │
                    └────────────┬─────────────┘
                                 │
                  ┌──────────────┴──────────────┐
                  │                             │
                  ▼                             ▼
          Student Answer Sheet              Answer Key
                  │                             │
                  ▼                             ▼
             PyMuPDF                     Text Extraction
                  │                             │
                  ▼                             │
             Page Images                        │
                  │                             │
                  ▼                             ▼
           Qwen2.5-VL                   Scheme Builder
                  │                             │
                  ▼                             ▼
       Handwritten Answer Text          Marking Scheme
                  │                             │
                  └──────────────┬──────────────┘
                                 │
                                 ▼
                           Evaluator Agent
                                 │
                                 ▼
                       Question-wise Evaluation
                                 │
                                 ▼
                           Validator Agent
                                 │
                                 ▼
                        Confidence Level
                      High / Medium / Low
                                 │
                                 ▼
                          Score Calculation
                                 │
                                 ▼
                           SQLAlchemy
                                 │
                                 ▼
                              MySQL
```

---

## 🔄 Complete Workflow

### Step 1 — Upload

The user uploads:

- Student answer sheet
- Answer key
- Student roll number
- Maximum examination marks

---

### Step 2 — Answer Sheet Processing

If the answer sheet is a PDF:

```text
PDF
 ↓
PyMuPDF
 ↓
Individual Page Images
```

The images are prepared for the vision model.

---

### Step 3 — Handwriting Recognition

The page images are sent to Qwen2.5-VL.

```text
Page Image
    ↓
Qwen2.5-VL
    ↓
Extracted Student Answer
```

---

### Step 4 — Answer Key Processing

The answer key is extracted and passed to the Scheme Builder Agent.

```text
Raw Answer Key
      ↓
Llama 3
      ↓
Structured Marking Scheme
```

---

### Step 5 — Answer Evaluation

The student's extracted answer and marking scheme are given to the Evaluator Agent.

```text
Student Answer
      +
Expected Answer
      +
Allocated Marks
      ↓
    Llama 3
      ↓
Evaluation
```

Example:

```text
Question 1
Expected: ...
Student Answer: ...
Assessment: Mostly correct
Marks: 4/5
```

---

### Step 6 — Confidence Validation

The generated evaluation is passed to the Validator Agent.

```text
Evaluation
    ↓
Validator
    ↓
High / Medium / Low
```

---

### Step 7 — Score Calculation

The system extracts:

```text
Obtained Marks
Possible Marks
```

and calculates the final score:

```text
Final Score =
(Obtained Marks / Possible Marks) × Maximum Marks
```

Example:

```text
Obtained Marks = 42
Possible Marks = 50
Maximum Marks = 100

Final Score = (42 / 50) × 100

Final Score = 84 / 100
```

---

### Step 8 — Database Storage

The final result is stored in MySQL.

The application uses:

```text
SQLAlchemy
     ↓
PyMySQL
     ↓
MySQL
```

Stored information can include:

- Student roll number
- Extracted answers
- AI evaluation
- Confidence level
- Final score
- Timestamp

---

## 🛠️ Technology Stack

| Category | Technology |
|---|---|
| Programming Language | Python |
| Backend | FastAPI |
| Frontend | HTML, CSS, JavaScript |
| Vision Language Model | Qwen2.5-VL |
| Large Language Model | Llama 3 |
| Local AI Runtime | Ollama |
| PDF Processing | PyMuPDF |
| Image Processing | Pillow |
| Database | MySQL |
| ORM | SQLAlchemy |
| MySQL Driver | PyMySQL |
| Async Processing | AsyncIO / aiohttp / httpx |
| Templates | Jinja2 |

---

## 📂 Project Structure

```text
ai-powered-handwritten-exam-evaluation-system/
│
├── ai_exam_system/
│   ├── main.py
│   ├── crew.py
│   ├── models.py
│   ├── database.py
│   │
│   └── agents/
│       ├── extractor.py
│       ├── scheme_builder.py
│       ├── evaluator.py
│       └── validator.py
│
├── utils/
│   ├── ollama_client.py
│   └── pdf_utils.py
│
├── templates/
│   └── index.html
│
├── static/
│   └── script.js
│
├── data/
│   └── input/
│
├── requirements.txt
├── README.md
└── .env
```

---

## 💻 Installation

### 1. Clone the repository

```bash
git clone https://github.com/sarvaji18rolex/AI-Powered Exam Grading System Using multi models.git
```

```bash
cd ai-powered-handwritten-exam-evaluation-system
```

---

### 2. Create a virtual environment

```bash
python -m venv venv
```

Activate it on Windows:

```bash
venv\Scripts\activate
```

On Linux/macOS:

```bash
source venv/bin/activate
```

---

### 3. Install Python dependencies

```bash
pip install -r requirements.txt
```

---

## 🦙 Ollama Setup

Install Ollama on your system and make sure the Ollama service is running.

Pull the required models.

Example:

```bash
ollama pull llama3
```

For the vision model, use the Qwen2.5-VL model configured by the project.

Verify the installed models:

```bash
ollama list
```

The application communicates with Ollama locally through its API.

Typical local endpoint:

```text
http://localhost:11434
```

---

## 🗄️ MySQL Setup

Create a MySQL database for the project.

Example:

```sql
CREATE DATABASE ai_exam_system;
```

Configure the database connection in the project's environment/configuration.

Example structure:

```text
MYSQL_HOST=localhost
MYSQL_PORT=3306
MYSQL_USER=root
MYSQL_PASSWORD=your_password
MYSQL_DATABASE=ai_exam_system
```

> Use your actual configuration format from the project when setting environment variables.

---

## ▶️ Running the Application

Start the FastAPI application using Uvicorn.

Example:

```bash
uvicorn ai_exam_system.main:app --reload
```

The application will normally be available at:

```text
http://127.0.0.1:8000
```

Open the address in your browser.

---

## 📋 Example Input

### Student Information

```text
Roll Number: 21IT001
Maximum Marks: 100
```

### Answer Sheet

```text
student_answer.pdf
```

### Answer Key

```text
answer_key.pdf
```

---

## 📊 Example Output

```text
Roll Number: 21IT001

Obtained Marks: 42
Possible Marks: 50

Final Score: 84 / 100

Confidence: High
```

The system also maintains the generated evaluation for each question.

---

## 🔬 AI Pipeline

The complete AI pipeline is:

```text
              INPUT
                │
                ▼
        Student Answer Sheet
                │
                ▼
        PDF/Image Processing
                │
                ▼
          Qwen2.5-VL
                │
                ▼
      Handwritten Text Extraction
                │
                ▼
        ┌───────────────┐
        │   Llama 3     │
        └───────┬───────┘
                │
        ┌───────┴────────┐
        │                │
        ▼                ▼
 Scheme Builder      Evaluator
        │                │
        ▼                │
 Marking Scheme ────────►│
                         ▼
                    Evaluation
                         │
                         ▼
                     Validator
                         │
                         ▼
                  Confidence Level
                         │
                         ▼
                   Score Calculation
                         │
                         ▼
                  SQLAlchemy / MySQL
```

---

## ⭐ Key Features

- ✅ Automated handwritten exam evaluation
- ✅ Vision Language Model-based handwriting recognition
- ✅ Multi-model AI architecture
- ✅ LLM-based marking scheme generation
- ✅ Semantic answer evaluation
- ✅ Partial-mark support
- ✅ Confidence validation
- ✅ Automatic score calculation
- ✅ FastAPI backend
- ✅ MySQL result storage
- ✅ SQLAlchemy ORM
- ✅ Local LLM inference using Ollama
- ✅ PDF and image processing

---

## 🔐 Privacy

Because the project uses locally hosted models through Ollama, the AI inference pipeline can be operated locally without requiring student answer sheets to be sent to a third-party cloud LLM API.

However, database and server security should still be configured appropriately before deploying the system in a real educational environment.

---

## ⚠️ Limitations

The system is an AI-assisted evaluation system and should not automatically be treated as a perfect replacement for human examiners.

Potential limitations include:

- Handwriting recognition errors
- Poor-quality scanned images
- Difficult or ambiguous handwriting
- LLM evaluation variability
- Incorrect or incomplete answer keys
- Domain-specific terminology
- Possible inconsistency in partial-mark decisions
- LLM-generated confidence is not a calibrated probability
- Human review is recommended for disputed or low-confidence evaluations

---

## 🚀 Future Enhancements

Possible improvements include:

### 1. Human-in-the-loop evaluation

Add a teacher review interface:

```text
AI Suggested Mark
       ↓
Teacher Review
       ↓
Approve / Modify
       ↓
Final Mark
```

### 2. Advanced rubric-based evaluation

Instead of evaluating only against a general expected answer:

```text
Question
 ├── Definition       → 1 mark
 ├── Key concept      → 2 marks
 ├── Explanation      → 2 marks
 └── Example          → 1 mark
```

This can make grading more transparent.

### 3. Multi-language handwriting recognition

Support answers written in:

- English
- Tamil
- Hindi
- Telugu
- Other regional languages

depending on model capabilities.

### 4. Teacher dashboard

Add:

- Student result dashboard
- Class statistics
- Question-wise analysis
- Average marks
- Low-confidence answers
- Manual correction interface

### 5. Result analytics

Generate:

```text
Average Score
Highest Score
Lowest Score
Question-wise Performance
Topic-wise Performance
```

### 6. Human verification for low confidence

Automatically send:

```text
Confidence = Low
```

answers to the teacher for manual review.

---

## 📈 Future Architecture

A more advanced version can use:

```text
Student Answer Sheet
        ↓
Vision-Language Model
        ↓
Question Segmentation
        ↓
Answer Extraction
        ↓
Rubric Generation
        ↓
Semantic Evaluation
        ↓
AI Suggested Marks
        ↓
Confidence Estimation
        ↓
     ┌─────────────┐
     │ Low         │ → Teacher Review
     │ Confidence  │
     └─────────────┘
        ↓
Final Verified Marks
        ↓
MySQL
        ↓
Analytics Dashboard
```

---

## 📚 Academic Classification

This project can be categorized under:

- Generative Artificial Intelligence
- Large Language Models
- Vision Language Models
- Natural Language Processing
- Computer Vision
- Intelligent Automation
- Automated Assessment
- Multi-Agent AI Systems
- Educational Technology

---

## 👨‍💻 Project Purpose

This project demonstrates how multiple AI technologies can be combined into a single intelligent application to automate a real-world educational workflow.

The key architecture is:

> **Vision → Extraction → Structured Rubric → LLM Evaluation → Validation → Score Calculation → Database Storage**

---

## 📜 Disclaimer

This project is intended for educational, research, and demonstration purposes. AI-generated evaluation should be reviewed by a qualified examiner before being used for official high-stakes examination results.
