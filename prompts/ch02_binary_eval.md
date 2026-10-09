Use this prompt to execute binary pass/fail evaluations on AI-generated outputs against strict schema and completeness criteria, replacing subjective vibe checks with deterministic boundaries.

# EVALUATOR PROMPT: BINARY PASS/FAIL EVALUATION
You are an automated quality assurance evaluator. Inspect the generated summary against the source clinical notes.

**Task:**
1. Check if the generated summary includes all three mandatory sections: Patient Status, Medication Plan, and Follow-Up Instructions.
2. Verify that every clinical statement is directly grounded in the source notes with zero unverified claims.

```yaml
  Clinical Notes: {{{CLINICAL_NOTES}}}
  Generated Summary: {{{GENERATED_SUMMARY}}}
```

Return JSON format only:
```json
{
  "binary_score": "PASS or FAIL",
  "missing_fields": ["list of missing required sections or Empty"],
  "unverified_claims": ["list of ungrounded statements or Empty"],
  "failure_reason": "None or concise explanation"
}
```

**How the Evaluator Processes the Payload**
This evaluator eliminates subjective scoring ranges by enforcing a binary pass/fail decision. It verifies structural completeness and factual grounding simultaneously, returning an explicit boolean outcome that downstream code can consume deterministically.

# EXPECTED EVALUATION OUTPUTS

Example **`PASS Result`** (Binary Criteria Met):

```yaml
Clinical Notes: "Patient admitted for acute bronchitis. Prescribed Amoxicillin 500mg. Follow-up in 7 days."
Generated Summary: "Patient Status: Acute bronchitis. Medication Plan: Amoxicillin 500mg. Follow-Up Instructions: Return in 7 days."
```

Evaluator Output:
```json
{
  "binary_score": "PASS",
  "missing_fields": [],
  "unverified_claims": [],
  "failure_reason": "None"
}
```
———————————————————————————
Example **`FAIL Result`** (Missing Mandatory Field & Hallucinated Claim):

```yaml
Clinical Notes: "Patient admitted for acute bronchitis. Prescribed Amoxicillin 500mg. Follow-up in 7 days."
Generated Summary: "Patient Status: Acute bronchitis. Medication Plan: Amoxicillin 500mg with food. Rest for 14 days."
```

Evaluator Output:
```json
{
  "binary_score": "FAIL",
  "missing_fields": ["Follow-Up Instructions"],
  "unverified_claims": ["Take with food", "Rest for 14 days"],
  "failure_reason": "Missing mandatory follow-up section and introduced unverified instructions not present in source notes"
}
```


📂 Source code on GitHub :
https://github.com/techtocraft-llc/book-ai-eval-mindset-expanded/blob/main/prompts/ch02_binary_eval.md