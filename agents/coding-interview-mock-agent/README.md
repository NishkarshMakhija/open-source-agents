# Coding Interview Mock Agent

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue.svg)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.110%2B-009688.svg)](https://fastapi.tiangolo.com/)
[![Gemini](https://img.shields.io/badge/Gemini%20API-Google%20GenAI-4285F4.svg)](https://aistudio.google.com/)
[![React](https://img.shields.io/badge/React-18-61DAFB.svg)](https://react.dev/)
[![TailwindCSS](https://img.shields.io/badge/TailwindCSS-3.4-38B2AC.svg)](https://tailwindcss.com/)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)

An AI-powered technical coding interview simulator that mimics an authentic Google Software Engineering (SWE) Data Structures and Algorithms (DSA) interview.

Unlike superficial Q&A bots, this agent acts as an experienced **Google Interviewer and Hiring Committee Bar-Raiser**: it enforces the Socratic method, withholds hidden constraints until the candidate proactively asks clarifying questions, guides algorithmic optimization from brute force to optimal Big-O, tests corner cases, executes candidate code in an isolated runner, and generates a structured hiring evaluation report.

---

## 📋 Table of Contents
- [Overview](#overview)
- [Key Features](#features)
- [Architecture & Design](#architecture)
- [Project Structure](#project-structure)
- [Setup Instructions](#setup-instructions)
- [Environment Variables](#environment-variables)
- [Running Locally](#running-locally)
  - [Backend (FastAPI)](#1-start-the-backend-server)
  - [Frontend (React + Tailwind)](#2-start-the-frontend-ui)
  - [Terminal CLI Mode](#3-interactive-cli-terminal-mode)
- [Running Tests](#running-tests)
- [Example Interview Walkthrough](#example-interview)
- [Docker Sandbox Migration (Security)](#docker-sandbox-migration)
- [Future Improvements](#future-improvements)

---

## 🌟 Overview

The **Coding Interview Mock Agent** bridges the gap between passive algorithmic practice (LeetCode) and real live technical interviews. It evaluates not only whether your code compiles, but how you:
- Communicate your thought process and think aloud
- Deconstruct ambiguous problem statements and inquire about input bounds
- Formulate baseline approaches before jumping into code
- Optimize asymptotic time and space complexities
- Test edge cases and handle unexpected inputs
- Respond to Google-scale follow-up scenarios (e.g., 100x data scale, distributed memory, streams)

---

## ⚡ Features

### 1. Difficulty & Topic Selection
Supports **Easy**, **Medium**, and **Hard** DSA challenges curated across Google SWE interview categories:
- **Arrays & Strings** (e.g., Two Sum, Valid Palindrome, Trapping Rain Water)
- **Linked Lists** (e.g., Reverse Linked List, Merge K Sorted Lists)
- **Trees & Graphs** (e.g., Max Depth, LCA, Number of Islands, Longest Increasing Path)
- **Dynamic Programming** (e.g., Word Break, Edit Distance)
- **Sliding Window & Two Pointers** (e.g., Longest Substring Without Repeating Characters, Sliding Window Maximum)
- **Heaps & Priority Queues** (e.g., Top K Frequent Elements)
- **Trie & Prefix Trees** (e.g., Implement Trie, Word Search II)
- **Backtracking** (e.g., Subsets)

### 2. Authentic 7-Stage Interview Progression
1. **Stage 1: Introduction** — Sets expectations, explains the interview rubric, invites candidate readiness.
2. **Stage 2: Problem Statement** — Minimal initial description with basic examples; hides edge constraints until queried.
3. **Stage 3: Approach Discussion** — Evaluates problem decomposition, brute force reasoning, and Big-O targets.
4. **Stage 4: Implementation** — Code editor supporting **Python**, **JavaScript**, **C++**, and **Java**.
5. **Stage 5: Code Review** — Dry-run tracing, edge-case probing, syntax verification.
6. **Stage 6: Google Follow-up** — Real-world scale-up challenges (distributed data, memory bounds, real-time streams).
7. **Stage 7: Hiring Committee Report** — Comprehensive evaluation scorecard with score out of 10 and hiring recommendations.

### 3. Socratic Interviewer Persona
- Never leaks the optimal solution upfront.
- Answers candidate questions about input bounds, nulls, negative numbers, and duplicates naturally.
- Provides subtle nudges when the candidate is blocked.

### 4. Multi-Language Code Execution
- Built-in runner with process isolation, strict timeouts (default 5s), and memory bounds.
- Captures `stdout`, `stderr`, execution time in milliseconds, and exit codes.

### 5. Hiring Committee Evaluation
Scores candidates from 1 to 10 across 6 Google SWE competencies:
1. **Communication**
2. **Problem Solving**
3. **Code Quality**
4. **Complexity Analysis**
5. **Edge Cases**
6. **Optimization**
Maps to Google Hiring recommendations: `Strong Hire`, `Hire`, `Leaning Hire`, `Leaning No Hire`, and `No Hire`.

---

## 🏗️ Architecture

```
                       +----------------------------------+
                       |   React + Tailwind Frontend UI   |
                       | (Chat, Code Editor, Console, Rep)|
                       +-----------------+----------------+
                                         | REST / JSON
                                         v
                       +----------------------------------+
                       |       FastAPI Application        |
                       |            (main.py)             |
                       +-----------------+----------------+
                                         |
     +-------------------+---------------+-------------------+
     |                   |                                   |
     v                   v                                   v
+------------+  +------------------+                +------------------+
| Interview  |  |  Code Execution  |                | Evaluator & Rep  |
|  Engine    |  |  Service (Runner)|                |     Services     |
+-----+------+  +--------+---------+                +--------+---------+
      |                  |                                   |
      v                  v                                   v
+------------+  +------------------+                +------------------+
|  Question  |  | Subprocess Layer |                | Google Gemini    |
|    Bank    |  | (Timeout Bounds) |                |   GenAI SDK      |
| easy/med/hd|  | [Docker TODO]    |                | (gemini-2.5-flsh)|
+------------+  +------------------+                +------------------+
```

---

## 📁 Project Structure

```
agents/coding-interview-mock-agent/
├── README.md                      # Comprehensive documentation
├── requirements.txt               # Python package dependencies
├── .env.example                   # Template environment variables
├── main.py                        # FastAPI application & CLI entrypoint
│
├── prompts/                       # LLM System Prompts
│   ├── interviewer.txt            # Google SWE Interviewer Persona & Rules
│   ├── evaluator.txt              # Hiring Committee 6-dimension Rubric Prompt
│   └── followup.txt               # Scale & Systems Follow-up Generator
│
├── question_bank/                 # Curated DSA Questions (>= 5 per difficulty)
│   ├── easy.json                  # Arrays, Strings, Trees, Linked List
│   ├── medium.json                # Graphs, Sliding Window, Heap, Trie, DP
│   └── hard.json                  # Two Pointers, K-Way Merge, Trie+DFS, DP
│
├── services/                      # Decoupled Business Logic
│   ├── interview.py               # Interview State Machine & Session Storage
│   ├── evaluator.py               # Gemini Evaluation & Offline Heuristics
│   ├── code_runner.py             # Subprocess Runner (Python, JS, C++, Java)
│   └── report.py                  # Score calculation & Markdown Report Builder
│
├── models/                        # Pydantic Schemas & Data Contracts
│   └── schemas.py                 # Question, Session, Submission, Report models
│
├── utils/                         # Helper Utilities
│   └── loader.py                  # Question & Prompt disk loader
│
├── tests/                         # Pytest Suite
│   ├── test_questions.py          # Schema validation & Question counts
│   └── test_evaluator.py          # Evaluator, Parser, Runner, Report tests
│
└── ui/                            # React + Tailwind CSS Web Application
    ├── README.md                  # Frontend documentation
    ├── package.json               # NPM package manifest
    ├── vite.config.js             # Vite configuration with API proxy
    ├── index.html                 # HTML entrypoint
    └── src/
        ├── App.jsx                # Main workspace container
        ├── main.jsx               # React mount root
        ├── index.css              # Tailwind directives & dark scrollbars
        └── components/
            ├── Navbar.jsx         # Header & Stage Stepper
            ├── ProblemDrawer.jsx  # Problem statement & discovered bounds
            ├── ChatFeed.jsx       # Multi-turn conversation feed
            ├── CodeEditor.jsx     # Editor with line numbers & run console
            └── ReportModal.jsx    # Google Hiring Committee Report modal
```

---

## 🛠️ Setup Instructions

### Prerequisites
- Python 3.10 or higher
- Node.js 18+ and npm (for frontend UI)
- Google Gemini API Key ([Get one free at Google AI Studio](https://aistudio.google.com/))

### 1. Create Virtual Environment
Open a terminal in `agents/coding-interview-mock-agent`:

```bash
# On Linux/macOS
python3 -m venv venv
source venv/bin/activate

# On Windows (PowerShell)
python -m venv venv
.\venv\Scripts\Activate.ps1
```

### 2. Install Backend Dependencies
```bash
pip install -r requirements.txt
```

### 3. Configure Environment Variables
Copy the `.env.example` file to `.env`:
```bash
cp .env.example .env
```
Open `.env` and set your Gemini API key:
```ini
GEMINI_API_KEY=AIzaSyYourActualGeminiKeyHere
GEMINI_MODEL=gemini-2.5-flash
PORT=8000
HOST=127.0.0.1
```

> **Note:** Even without a Gemini API key, the system includes an **intelligent offline fallback simulator** so you can develop, test, and run mock interviews completely offline!

---

## 🚀 Running Locally

### 1. Start the Backend Server
```bash
python main.py
```
*Backend API documentation (Swagger UI) is available at: [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs)*

### 2. Start the Frontend UI
In a separate terminal:
```bash
cd ui
npm install
npm run dev
```
*Open your browser at: [http://localhost:5173](http://localhost:5173)*

### 3. Interactive CLI Terminal Mode
If you prefer practicing directly inside your terminal without opening a browser:
```bash
python main.py --cli
```
- Type messages to chat with the interviewer.
- Type `:code` to paste multi-line code.
- Type `exit` or `finish` to receive your evaluation scorecard.

---

## 🧪 Running Tests

The test suite validates question counts, schema compliance, heuristic evaluation, JSON extraction from LLMs, and code execution.

Run tests using `pytest`:
```bash
pytest tests/ -v
```

---

## 💬 Example Interview

### Stage 1: Introduction
> **Interviewer:** "Hi Candidate, welcome to today's Google coding interview! Over the next 45 minutes, we'll collaborate on a coding problem. I'm especially interested in how you think and communicate, so please think aloud. Feel free to ask clarification questions before coding. Whenever you're ready, let me know!"
>
> **Candidate:** "Hi! I am ready to begin."

### Stage 2: Problem Statement & Clarifications
> **Interviewer:** "Great! Here is the problem: Given an array of integers nums and an integer target, return indices of the two numbers such that they add up to target."
>
> **Candidate:** "Can the input array contain negative numbers or duplicates? Also, what if no pair exists?"
>
> **Interviewer:** "Great questions! Yes, numbers can be negative. Each input has exactly one valid solution, and you may not use the same element twice."

### Stage 3: Approach Discussion
> **Candidate:** "A naive approach would check all pairs with nested loops in O(N^2) time. To optimize, I can use a Hash Map storing value-to-index mappings in a single pass, bringing time to O(N) and space to O(N)."
>
> **Interviewer:** "That sounds like a solid approach! What would be the trade-off? Go ahead and write the implementation."

### Stage 4 & 5: Coding & Review
Candidate submits code in Python:
```python
class Solution:
    def two_sum(self, nums, target):
        seen = {}
        for i, num in enumerate(nums):
            complement = target - num
            if complement in seen:
                return [seen[complement], i]
            seen[num] = i
        return []
```
> **Candidate:** "I've implemented the hash map solution. Let's review it."
>
> **Interviewer:** "Clean and readable! Let's trace through: what happens if the array has length 2 and the target is the sum of those two elements? How do you prevent using the same index twice?"

### Stage 6: Follow-up
> **Interviewer:** "If the input array is already sorted, can you achieve O(1) auxiliary space without using a hash map?"
>
> **Candidate:** "Yes! With a sorted array, we can use Two Pointers starting at the left and right ends, advancing based on whether the sum is less than or greater than target."

### Stage 7: Evaluation Report
```markdown
# 🏛️ Google SWE Coding Interview Feedback Report
**Candidate:** Candidate | **Overall Score:** 8.6 / 10
### Hiring Recommendation: 🟢 STRONG HIRE

| Competency | Score | Rating |
| :--- | :---: | :--- |
| Communication | 9/10 | Exceptional |
| Problem Solving | 9/10 | Exceptional |
| Code Quality | 9/10 | Exceptional |
| Complexity Analysis | 9/10 | Exceptional |
| Edge Cases | 8/10 | Good |
| Optimization | 9/10 | Exceptional |
```

---

## 🔒 Docker Sandbox Migration

The MVP currently executes code via Python's `subprocess` with timeout and process isolation.
For production deployments, follow the `TODO` markers in [`services/code_runner.py`](services/code_runner.py) to migrate to an isolated Docker container runner:

```dockerfile
# Sandbox Container
FROM python:3.11-alpine
RUN adduser -D -u 1000 sandboxuser
USER sandboxuser
WORKDIR /sandbox
```

Execution flags to enforce in production:
```bash
docker run --rm \
  --network none \
  --memory 128m \
  --cpus 0.5 \
  --pids-limit 64 \
  --read-only \
  --tmpfs /tmp:rw,noexec,nosuid,size=16m \
  sandbox-runner
```

---

## 🚀 Future Improvements

- [ ] **Gemini Multimodal Live API (Voice)**: Add real-time speech-to-speech audio streaming for 100% verbal interview practice.
- [ ] **AST Static Analysis**: Parse candidate code abstract syntax trees to detect recursion depth, variable scope leaks, and style linting automatically.
- [ ] **Collaborative Whiteboard**: Diagramming canvas for graph/tree traversals and system architecture.
- [ ] **Resume & Level Customization**: Tailor question difficulty and follow-up depth dynamically based on candidate target level (L3, L4, L5, L6).

---

## 📄 License
This project is open-source under the [Apache 2.0 License](LICENSE).

