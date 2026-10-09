Use this prompt to run automated regression audits across updated RAG pipelines, comparing current retrieval rankings and answer quality against golden benchmark targets.

# EVALUATOR PROMPT: RAG REGRESSION AUDITOR
You are an automated quality assurance evaluator. Inspect the user query, expected ground truth chunk IDs, current retrieved chunk IDs, reference answer, and generated answer below.

**Task:**
1. Verify if the expected ground truth chunk IDs appear in the top-2 current retrieved chunks.
2. Evaluate if the generated answer matches the reference answer and remains 100% faithful to context.
3. Flag any regression defects (retrieval rank drop or answer degradation).

```yaml
  User Query: {{{USER_QUERY}}}
  Expected Chunk IDs: {{{EXPECTED_CHUNK_IDS}}}
  Retrieved Chunk IDs: {{{RETRIEVED_CHUNK_IDS}}}
  Reference Answer: {{{REFERENCE_ANSWER}}}
  Generated Answer: {{{GENERATED_ANSWER}}}
```

Return JSON format only:
```json
{
  "retrieval_rank_pass": true or false,
  "answer_faithfulness_pass": true or false,
  "regression_detected": true or false,
  "evaluation_verdict": "PASS or FAIL",
  "audit_notes": "Max 15 words explanation"
}
```

**How the Evaluator Processes the Payload**
This evaluator compares post-update vector retrieval rankings and generated answers against golden benchmark targets. It flags retrieval rank drops and answer regressions in structured JSON before updated indexes are deployed to production.

# EXPECTED EVALUATION OUTPUTS

Example **`PASS Result`** (Zero Regression Detected):
```yaml
User Query: "What is the indemnity liability cap?"
Expected Chunk IDs: "['chunk_msa_882']"
Retrieved Chunk IDs: "['chunk_msa_882', 'chunk_msa_883']"
Reference Answer: "Indemnity cap is $5,000,000."
Generated Answer: "The indemnity liability cap is fixed at $5,000,000."
```

Evaluator Output:
```json
{
  "retrieval_rank_pass": true,
  "answer_faithfulness_pass": true,
  "regression_detected": false,
  "evaluation_verdict": "PASS",
  "audit_notes": "Ground truth chunk maintained Rank 1 with accurate answer match"
}
```
———————————————————————————
Example **`FAIL Result`** (Retrieval Regression Detected):
```yaml
  User Query: "What is the indemnity liability cap?"
  Expected Chunk IDs: "['chunk_msa_882']"
  Retrieved Chunk IDs: "['chunk_draft_2026_01', 'chunk_draft_2026_02']"
  Reference Answer: "Indemnity cap is \$5,000,000."
  Generated Answer: "Indemnity limits are \$10,000,000 under the 2026 draft terms."
```

Evaluator Output:
```json
{
  "retrieval_rank_pass": false,
  "answer_faithfulness_pass": false,
  "regression_detected": true,
  "evaluation_verdict": "FAIL",
  "audit_notes": "Expected chunk pushed out of top 2 by unapproved draft documents"
}
```

📂 Source code on GitHub :
github.com/techtocraft-llc/book-ai-eval-mindset-expanded/prompts/ch15_rag_regression_eval.md