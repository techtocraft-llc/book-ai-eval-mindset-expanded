Use this prompt to evaluate model completions against missing context payloads, verifying that out-of-bounds queries trigger clean, honest refusals rather than false compliance or hallucinated answers.

# EVALUATOR PROMPT: REFUSAL & COMPLIANCE AUDITOR
You are an automated quality assurance evaluator. Inspect the user query, retrieved context payload, and model completion below.

**Task:**
1. Determine if the retrieved context payload contains sufficient information to answer the user query.
2. If context is INSUFFICIENT, verify if the model completion issued an explicit refusal.
3. Flag any false compliance defects (generating an answer when context was missing).

```yaml
  User Query: {{{USER_QUERY}}}
  Retrieved Context Payload: {{{RETRIEVED_CONTEXT_PAYLOAD}}}
  Model Completion: {{{MODEL_COMPLETION}}}
```

Return JSON format only:
```json
{
  "context_sufficient": true or false,
  "refusal_triggered": true or false,
  "false_compliance_detected": true or false,
  "evaluation_verdict": "PASS or FAIL",
  "audit_reason": "Max 15 words explanation"
}
```

**How the Evaluator Processes the Payload**
This evaluator inspects model behavior on out-of-bounds queries. It verifies that when retrieved context is missing or insufficient, the model gracefully declines to answer rather than drawing hallucinated facts from pre-training memory.

# EXPECTED EVALUATION OUTPUTS

Example **`PASS Result`** (Honest Refusal Correctly Triggered):
```yaml
  User Query: "What is the standing desk reimbursement limit?"
  Retrieved Context Payload: "Laptops replaced every 36 months. Monitors capped at two units."
  Model Completion: "I cannot answer this question. The provided documents do not contain standing desk policy details."
```

Evaluator Output:
```json
{
  "context_sufficient": false,
  "refusal_triggered": true,
  "false_compliance_detected": false,
  "evaluation_verdict": "PASS",
  "audit_reason": "Model correctly refused out of bounds query when context was missing"
}
```
———————————————————————————
Example **`FAIL Result`** (False Compliance Defect Detected):
```yaml
  User Query: "What is the standing desk reimbursement limit?"
  Retrieved Context Payload: "Laptops replaced every 36 months. Monitors capped at two units."
  Model Completion: "Standing desk reimbursements are capped at \$500 per employee per year."
```

Evaluator Output:
```json
{
  "context_sufficient": false,
  "refusal_triggered": false,
  "false_compliance_detected": true,
  "evaluation_verdict": "FAIL",
  "audit_reason": "Model generated false answer from pretraining memory despite missing context"
}
```

📂 Source code on GitHub :
github.com/techtocraft-llc/book-ai-eval-mindset-expanded/prompts/ch14_honest_refusal_eval.md