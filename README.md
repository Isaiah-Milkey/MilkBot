# MilkBot - LLM Agent
**Name:** Isaiah Milkey  
**Course:** Introduction to Natrual Language Processing  
**GitHub:** [https://github.com/Isaiah-Milkey/MilkBot](https://github.com/Isaiah-Milkey/MilkBot)  

---

## Testing the Agent
Using the development data, two helper functions are provided: `filter_domain` and `get_batch`. These allow filtering questions by domain and retrieving a batch of n questions. Both return lists that can be passed into `self_evaluate_tests`.  
To test the agent:  
1. Use `load_questions` and `build_answers` methods in the testing cell.  
2. You can optionally filter by domain from the training data to run targeted tests.

---

## How the Agent Works
MilkBot is designed to handle questions across 5 main categories: `math`, `coding`, `future_prediction`, `planning`, and `common_sense`.  

1. **Category Classification:** The agent calls the LLM once to determine the question category or classify it as 'other'.  
2. **Category-Specific Handling:** Depending on the classification, the corresponding function is executed.  
3. **Other Category Handling:** If 'other' is selected:  
   - RAG: Collect 3 relevant documents  
   - self_consistency: Generate 2 candidate outputs  
   - double_check: Combine outputs to produce final prediction  

---

## Inference Method Functions

- **`reasoning_via_planning(prior: str, question: str) → str`**  
  Uses prompt engineering to decompose a problem and generate a plan/solution. Executes 1 LLM call.

- **`tree_of_thought(question: str, n_paths: int, prior: str = None) → List[]`**  
  Creates n distinct reasoning paths. Currently not used due to poor performance. Uses 1 LLM call.

- **`self_consistency(input_question: str, n_paths: int, prior: str = "") → List[]`**  
  Calls the LLM n times for the same question to generate multiple outputs, returning them as a list.

- **`double_check(prior: str, question: str) → str`**  
  Verifies solution accuracy and formatting. Uses 1 LLM call.

---

## Tool Functions

- **`wiki_tool(input_question: str, attempts=1) → str`**  
  Searches for relevant Wikipedia articles using the LLM. Scrapes content via Trafilatura and returns concatenated context. Uses n LLM calls.

- **`RAG_tool(question: str, num_examples: int = 1) → str`**  
  Retrieves similar examples from the training dataset using LangChain + FAISS vector store. Uses 0 LLM calls.

- **`calc_tool(input_question: str) → int/float`**  
  Converts questions to arithmetic expressions via LLM and evaluates them. Uses prompt engineering and 1 LLM call.

---

## Case Functions

- **`case_math(input_question: str)`**  
  - RAG: Collect 2 related documents  
  - reasoning_via_planning: Generates a Python program to solve the math problem  
  - calc_tool: Converts program into a math expression and evaluates  
  - double_check: Verifies correctness via LLM  

- **`case_coding(input_question: str)`**  
  - RAG: Collect 2 examples  
  - reasoning_via_planning: Creates Python script  
  - double_check: LLM verifies accuracy  
  - RAG: Collect 5 additional documents from solution  
  - reasoning_via_planning: Refines solution  
  - call_model_chat_completions: Generates final script  

- **`case_common_sense(input_question: str)`**  
  - RAG: Collect 2 examples  
  - wiki_tool: Adds external knowledge  
  - call_model_chat_completions: Returns final answer  

- **`case_future_prediction(input_question: str)`**  
  - RAG: Collect 4 examples  
  - reasoning_via_planning: Generate prediction  
  - double_check: Validate consistency  
  - RAG: Collect 6 more documents based on prediction  
  - reasoning_via_planning: Refine prediction  
  - call_model_chat_completions: Final prediction in required format  

- **`case_planning(input_question: str)`**  
  - RAG: Collect 4 documents  
  - reasoning_via_planning: Generate initial plan  
  - double_check: LLM judges plan quality  
  - RAG: Collect 8 documents from solution  
  - reasoning_via_planning: Refine plan  
  - self_consistency: Generate 2 candidate plans  
  - call_model_chat_completions: Final plan  

---

## Files
- **`Agent_main.ipynb`**: Main agent code  
- **`cse476_final_project_dev_data.json`**: Training data for RAG and testing  
- JSON outputs and final results are generated using `Agent_main.ipynb` with the provided dataset.
