# 🧠 Deep Research Scoping

A **LangGraph + LangChain + LangSmith** powered system for **automated research scoping and evaluation**.  
This project transforms natural user queries into **structured research briefs** by:  

1. Assessing whether clarification is required from the user.  
2. Generating detailed and assumption-free research questions.  
3. Evaluating the quality of research briefs against explicit user criteria.  
4. Ensuring that no unwarranted assumptions or hallucinations creep into the scope.  

---

## 🚀 Project Objectives

- Build a **deterministic scoping agent** that turns vague user input into **clear, structured research briefs**.  
- Ensure **fidelity to user requirements** while avoiding hallucinations or hidden assumptions.  
- Provide an **evaluation framework** using **LangSmith** to measure quality, coverage, and assumption avoidance.  
- Enable **scalable and repeatable research pipelines** that can plug into downstream research agents.

---

## 🏗️ System Design

### 1. Conversation Processing
- Captures user input as `messages` (`HumanMessage`, `AIMessage`)  
- Uses **LangGraph** `StateGraph` to maintain state:
  - `AgentInputState`: Raw user messages.  
  - `AgentState`: Extended with research brief, supervisor messages, raw notes, structured notes, and final report.

### 2. Clarification Workflow
- Node `clarify_with_user`:
  - Determines if clarification is needed.  
  - If yes → returns a clarifying question.  
  - If no → acknowledges and proceeds.  
- Uses structured schema `ClarifyWithUser`.

### 3. Research Brief Generation
- Node `write_research_brief`:  
  - Transforms user conversation into a **detailed research brief**.  
  - Enforces structured schema `ResearchQuestion`.  
  - Outputs brief to downstream supervisor for research coordination.  

### 4. Evaluation Layer
- Two evaluators ensure research brief quality:
  - **Success Criteria Evaluator** (`evaluate_success_criteria`)  
    - Checks if **all user-specified criteria** are **captured** in the brief.  
    - Returns granular capture status + overall score.  
  - **No-Assumptions Evaluator** (`evaluate_no_assumptions`)  
    - Ensures no unwarranted assumptions were added.  
    - Returns a pass/fail score with reasoning.  

### 5. LangSmith Integration
- Dataset: `deep_research_scoping`  
- Evaluates conversations → generated briefs against **reference criteria**.  
- Metrics tracked:
  - ✅ Criteria coverage score  
  - 🚫 Assumptions made (boolean pass/fail)  

---

## 📊 Example Workflows

### Example 1 – Investment Research
**Conversation**:  
- User: "What's the best way to invest $50,000 for retirement?"  
- Clarification: Age, risk tolerance, investment type, account type.  
- User: "I'm 25, want to retire by 45, high risk now but lower later, prefer stocks/ETFs, brokerage account."  

**Criteria Extracted**:  
- Age 25, retirement 45, high→lower risk, stocks/ETFs preference, brokerage account.  

**Evaluation**:  
- Success Criteria Score = ✅ Captured: 5/5 → **1.0**  
- No Assumptions = **PASS**  

---

### Example 2 – NYC Apartment Search
**Conversation**:  
- User: "I want an apartment in NYC."  
- Clarification: Neighborhood, rooms, budget, amenities, move-in date.  
- User: "Chelsea/Flatiron/West Village, 2 bed 2 bath, under 7k, doorman required, W/D preferred, gym optional, September 2025."  

**Criteria Extracted**:  
- Location, size, budget, doorman required, amenities optional, move-in date.  

**Evaluation**:  
- Success Criteria Score = ✅ Captured: 6/6 → **1.0**  
- No Assumptions = **PASS**  

---

## 🛠️ Tech Stack

- **LangChain** – Message abstraction + ChatOpenAI model API  
- **LangGraph** – StateGraph orchestration of research workflow  
- **LangSmith** – Dataset creation + evaluation harness  
- **OpenAI GPT-4o-mini** – Reasoning + structured output generation  
- **Pydantic** – Strongly-typed schemas for structured outputs  
- **Rich / IPython Display** – Pretty-printing results & visualizing workflows  

