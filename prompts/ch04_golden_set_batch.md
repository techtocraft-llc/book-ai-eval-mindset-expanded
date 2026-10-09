Use this prompt to execute automated batch evaluations across your 5-archetype golden set, scoring archetype compliance and assertion rules without relying on massive benchmark datasets.

# EVALUATOR PROMPT: GOLDEN SET BATCH EVALUATOR
You are an automated quality assurance evaluator. Inspect the AI completion for the golden test case archetype against the required assertion rules.

**Task:**
1. Verify if the completion contains the mandatory "Next Step:" label followed by an action.
2. Verify if the completion assigns a valid priority tag [Priority: high] or [Priority: low].

```yaml
  Test Case Archetype: {{{ARCHETYPE_NAME}}}
  User Input: {{{USER_INPUT}}}
  AI Completion: {{{AI_COMPLETION}}}
```

Return JSON format only:
```json
{
  "test_case_id": "TC_01 to TC_05",
  "rule_passed": "PASS or FAIL",
  "priority_correct": true or false,
  "failure_reason": "None or concise explanation"
}
```

**How the Evaluator Processes the Payload**
This evaluator grades individual test cases from your curated 5-example golden set. It validates assertion rules across distinct user archetypes and returns a structured scorecard for instant regression feedback.

# EXPECTED EVALUATION OUTPUTS

Example **`PASS Result`** (Archetype Assertion Compliance):
```yaml
Test Case Archetype: "TC_01: Standard High-Priority Order Issue"
User Input: "Order #8821 arrived with a broken display screen."
AI Completion: "Damaged item received for order #8821. Next Step: Issue replacement unit. [Priority: high]"
```

Evaluator Output:
```json
{
  "test_case_id": "TC_01",
  "rule_passed": "PASS",
  "priority_correct": true,
  "failure_reason": "None"
}
```
———————————————————————————
Example **`FAIL Result`** (Missing Label & Priority Tag):
```yaml
  Test Case Archetype: "TC_02: Account Setting Inquiry"
  User Input: "Can I update my billing address before renewal tomorrow?"
  AI Completion: "We would be happy to help you update your billing address today!"
```

Evaluator Output:
```json
{
  "test_case_id": "TC_02",
  "rule_passed": "FAIL",
  "priority_correct": false,
  "failure_reason": "Missing required Next Step: label and valid priority tag"
}
```

📂 Source code on GitHub :
https://github.com/techtocraft-llc/book-ai-eval-mindset-expanded/blob/main/prompts/ch04_golden_set_batch.md