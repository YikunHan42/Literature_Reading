## **1. Motivation**

- Current **medical QA benchmarks** (e.g., MEDQA, PubMedQA) evaluate models in a **static, single-turn** setting where all relevant patient information is given upfront.
- **Real-world clinical consultations** are interactive: patients often provide **incomplete information** initially, requiring doctors to ask **follow-up questions**.
- Existing LLMs (e.g., GPT-4, Llama-3) tend to **answer prematurely** without eliciting more data, which can lead to unreliable or unsafe decisions.
- **Goal:** Shift the paradigm from static QA to **interactive, information-seeking clinical reasoning** and evaluate LLMs in this setting.

------

## **2. MEDIQ Framework**

**Key Idea:** Benchmark LLMs on their ability to **ask questions to fill knowledge gaps** before making medical decisions.

### **Components**

1. **Patient System**
   - Simulates a patient by responding to the Expert’s questions using a full patient record.
   - Evaluated on:
     - **Factuality** – stays faithful to patient record.
     - **Relevance** – answers the specific question asked.
   - Three variants tested:
     - **Direct:** naive response → prone to hallucination.
     - **Instruct:** constrained to context but sometimes infers extra.
     - **Fact-Select:** retrieves atomic facts from record → best factuality (89.1%) and relevance (79.9%)
2. **Expert System**
   - Simulates a doctor deciding whether to ask further questions or provide a diagnosis.
   - Modular pipeline:
     1. **Initial Assessment:** identifies missing info.
     2. **Abstention:** decides whether to answer or ask another question.
     3. **Question Generation:** forms atomic follow-ups.
     4. **Information Integration:** aggregates responses.
     5. **Decision Making:** final diagnosis.
3. **Datasets**
   - **iMEDQA** and **iCRAFT-MD:** derived from existing QA benchmarks by **revealing only age, gender, and chief complaint initially**, forcing the Expert to ask for missing details.

------

## **3. Main Challenges**

- LLMs often **default to answering immediately**, even with insufficient context.
- Deciding **when to abstain and ask a question** is nontrivial.
- Need for reliable confidence estimation to avoid premature or hallucinated answers.

------

## **4. Experiments & Results**

### **4.1 Patient System**

- **Fact-Select** improved factuality by 0.33 and relevance by 0.04 over Direct/Instruct.
- Demonstrates the importance of grounding responses in atomic facts to avoid hallucination.

------

### **4.2 Expert System with Limited Information**

- Benchmarked **Llama-3 (8B, 70B), GPT-3.5, GPT-4** in both **non-interactive** (static QA) and **interactive** settings.
- **Performance drops sharply** as initial information is reduced:
  - With full records (upper bound), GPT-4 reached **79.7%** on iMEDQA.
  - With only initial info (age, gender, chief complaint), GPT-4 accuracy dropped to **54.5%**
- **Naive interactive prompting (BASIC)** worsened results further:
  - GPT-4 fell to **55.4% → 11.3% relative drop vs. non-interactive baseline** because models often didn’t ask useful follow-ups
  - Many conversations were short with few or no questions asked.

------

### **4.3 Improving Question-Asking with Abstention**

- Introduced **MEDIQ-Expert** with abstention strategies:
  - **Numerical, Binary, Scale (Likert-based)** confidence thresholds.
  - **Rationale Generation (RG)** improved calibration of confidence.
  - **Self-Consistency (SC)** stabilized abstention decisions.
- **Best setup: Scale + RG + SC**
  - Closed **22.3% of the performance gap** vs. BASIC interactive baseline.
  - Closed **51.2% of the gap** between interactive setting and full-information upper bound.
  - Larger models (≥70B) benefited most.

------

### **4.4 Additional Findings**

- **Filtering irrelevant/repeated questions** and **reformatting conversations into paragraphs** improved performance by ~5.7 percentage points.
- **Rationale Generation** reduced Expected Calibration Error (ECE) from **2.9 → 2.1**, showing better confidence estimation and more targeted questioning
- Nonetheless, even the best interactive setup still fell short of performance achievable with full information.

------

## **5. Contributions**

1. Identified the **information-seeking gap** as a critical factor for LLM reliability in high-stakes domains.
2. Developed **MEDIQ benchmark** to evaluate interactive clinical reasoning.
3. Designed **MEDIQ-Expert** to improve confidence-aware questioning.
4. Demonstrated that **current SOTA LLMs struggle in interactive, information-scarce scenarios**, highlighting the need for specialized strategies beyond static QA.

------

## **6. Limitations & Ethics**

- Limited by available datasets (only MEDQA and CRAFT-MD had sufficient detail).
- Current Patient system relies on paid APIs; open-source alternatives needed.
- Benchmark still limited to multiple-choice tasks; extension to open-ended settings is encouraged.
- Deployment risks: privacy, bias, over-reliance on imperfect models.

------

## **7. Key Takeaways**

- **Static QA benchmarks overestimate LLM reliability** for real-world clinical settings.
- **Interactive reasoning with abstention and rationale generation improves—but doesn’t solve—LLM gaps** in clinical question-asking.
- **Future work:** better confidence calibration, integration of external knowledge, multi-agent collaboration, and more realistic open-ended benchmarks.