Use this prompt to evaluate model completions across your golden set, verifying multi-constraint compliance and catching instruction collisions when prompt rules conflict.

# EVALUATOR PROMPT: GOLDEN SET CONSTRAINT EVALUATION
You are an automated quality assurance evaluator. Inspect the model response against the golden set scenario rules and user query.

**Task:**
1. Verify if the response adheres to all specific business rules defined for this scenario.
2. Detect whether prompt bloat caused an instruction collision where one rule overrode another.

```yaml
  Scenario Rules: {{{GOLDEN_SCENARIO_RULES}}}
  Customer Query: {{{CUSTOMER_INPUT}}}
  Model Response: {{{GENERATED_RESPONSE}}}
```

Return JSON format only:
```json
{
  "rule_compliance": "PASS or FAIL",
  "instruction_collision": true or false,
  "violated_rules": ["list of violated rules or Empty"],
  "failure_reason": "None or concise explanation"
}
```


**How the Evaluator Processes the Payload**
This evaluator tests model responses against explicit golden set scenario constraints. It flags instruction collisions where bloated system prompts cause the model to silently ignore lower-priority business rules.

# EXPECTED EVALUATION OUTPUTS

Example **`PASS Result`** (Multi-Constraint Compliance):
```yaml
Scenario Rules: "Weather delay policy: Fee waived. Cancellation permitted within 24 hours of flight."
Customer Query: "Flight canceled due to storm, can I get a full refund without fees?"
Model Response: "Yes, under our weather delay policy, your cancellation fee is waived and a full refund will be processed."
```

Evaluator Output:
```json
{
  "rule_compliance": "PASS",
  "instruction_collision": false,
  "violated_rules": [],
  "failure_reason": "None"
}
```
———————————————————————————
Example **`FAIL Result`** (Instruction Collision Detected):
```yaml
  Scenario Rules: "Weather delay policy: Fee waived. Cancellation permitted within 24 hours of flight."
  Customer Query: "Flight canceled due to storm, can I get a full refund without fees?"
  Model Response: "Standard tickets are non-refundable. A \$75 fee applies to all cancellations."
```

Evaluator Output:
```json
{
  "rule_compliance": "FAIL",
  "instruction_collision": true,
  "violated_rules": ["Weather delay policy fee waiver"],
  "failure_reason": "General non-refundable ticket rule collided with and overrode the weather delay fee waiver policy"
}
```

📂 Source code on GitHub :
github.com/eaccmk/book-ai-eval-mindset-expanded/prompts/ch03_golden_set_eval.md