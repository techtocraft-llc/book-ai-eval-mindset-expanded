Use this prompt to evaluate two completions generated from identical inputs to detect non-deterministic variance and formatting drops across parallel runs.

# EVALUATOR PROMPT: MULTI-RUN CONSISTENCY CHECK

You are an automated quality assurance evaluator. Inspect and compare
Output A and Output B below, which were generated from identical inputs.

**Task:**
1. Verify if both outputs preserve core semantic meaning.
2. Verify if both outputs strictly adhere to the 20-word length limit.

```yaml
Output A: {{{OUTPUT_A}}}
Output B: {{{OUTPUT_B}}}
```

Return JSON format only:
```json
{
  "meaning_match": "PASS or FAIL",
  "length_match": "PASS or FAIL",
  "failure_reason": "None or concise explanation"
}
```

**How the Evaluator Processes the Payload**
This evaluator compares paired completions across parallel runs. Instead of requiring manual review, it inspects semantic consistency and flags subtle word-count or formatting drops in structured JSON for your test runner.

# EXPECTED EVALUATION OUTPUTS

Example **`PASS Result`** (Consistent Quality):
```yaml
Output A: "Evals measure AI quality; guardrails enforce live rules before users are affected."
Output B: "Evals assess AI quality; guardrails enforce real-time safety rules before release."
```

Evaluator Output:
```json
{
  "meaning_match": "PASS",
  "length_match": "PASS",
  "failure_reason": "None"
}
```
———————————————————————————
Example **`FAIL Result`** (Variance & Formatting Drop Detected):
```yaml
Output A: "Evals measure AI quality."
Output B: "Here is your definition: Evals measure quality while guardrails act as live safety filters."
```

Evaluator Output:
```json
{
  "meaning_match": "PASS",
  "length_match": "FAIL",
  "failure_reason": "Output B exceeded 20 words and introduced preamble fluff"
}
```

📂 Source code on GitHub :
https://github.com/techtocraft-llc/book-ai-eval-mindset-expanded/blob/main/prompts/ch01_consistency.md