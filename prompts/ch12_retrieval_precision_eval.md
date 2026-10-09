Use this prompt to evaluate vector search results across benchmark query sets, calculating Precision@K, Reciprocal Rank, and retrieval pass rates to eliminate context noise.

# EVALUATOR PROMPT: RETRIEVAL PRECISION & RECALL AUDITOR
You are an automated quality assurance evaluator. Inspect the user query, ground truth criteria, and retrieved context chunks payload.

**Task:**
1. Determine if each retrieved chunk directly contains information relevant to the user query and ground truth criteria.
2. Identify the rank position (1 to K) of the first relevant chunk and calculate Precision@K.

```yaml
  User Query: {{{USER_QUERY}}}
  Ground Truth Criteria: {{{GROUND_TRUTH_CRITERIA}}}
  Retrieved Chunks Payload: {{{RETRIEVED_CHUNKS_PAYLOAD}}}
```

Return JSON format only:
```json
{
  "total_retrieved_k": 3,
  "relevant_chunks_count": "number",
  "first_relevant_rank_position": "1 to K",
  "precision_at_k": "0.0 to 1.0",
  "reciprocal_rank": "0.0 to 1.0",
  "retrieval_verdict": "PASS or FAIL",
  "audit_summary": "Max 15 words explanation"
}
```

**How the Evaluator Processes the Payload**
This evaluator computes Precision@K and Reciprocal Rank metrics across retrieved candidate sets. It flags low-precision search payloads and buried context before irrelevance dilutes the generator prompt.

# EXPECTED EVALUATION OUTPUTS

Example **`PASS Result`** (High Precision & Top Rank):
```yaml
User Query: "What are battery thermal cutoff limits for Class II devices?"
Ground Truth Criteria: "Class II thermal cutoff temp max 42C"
Retrieved Chunks Payload:
  - Rank 1: "Doc_A: Class II battery cutoff limits set to 42C max."
  - Rank 2: "Doc_B: Thermal safety cutoff testing procedures."
  - Rank 3: "Doc_C: Unrelated casing material guidelines."
```

Evaluator Output:
```json
{
  "total_retrieved_k": 3,
  "relevant_chunks_count": 2,
  "first_relevant_rank_position": 1,
  "precision_at_k": 0.67,
  "reciprocal_rank": 1.0,
  "retrieval_verdict": "PASS",
  "audit_summary": "Relevant context positioned at Rank 1 with high precision"
}
```
———————————————————————————
Example **`FAIL Result`** (Low Precision & Buried Rank):
```yaml
  User Query: "What are battery thermal cutoff limits for Class II devices?"
  Ground Truth Criteria: "Class II thermal cutoff temp max 42C"
  Retrieved Chunks Payload:
    - Rank 1: "Doc_X: Storage room ambient temperature limits."
    - Rank 2: "Doc_Y: Shipping container heat tolerances."
    - Rank 3: "Doc_A: Class II battery cutoff limits set to 42C max."
```

Evaluator Output:
```json
{
  "total_retrieved_k": 3,
  "relevant_chunks_count": 1,
  "first_relevant_rank_position": 3,
  "precision_at_k": 0.33,
  "reciprocal_rank": 0.33,
  "retrieval_verdict": "FAIL",
  "audit_summary": "First relevant chunk buried at Rank 3 with low precision"
}
```

📂 Source code on GitHub :
github.com/techtocraft-llc/book-ai-eval-mindset-expanded/prompts/ch12_retrieval_precision_eval.md