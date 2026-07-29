# Structured Outputs — Clean JSON from Any Prompt

**Internship:** Generative AI & Prompt Engineering — Neurofive Solutions

## Objective
To force an LLM to always respond in valid, schema-conformant JSON — with no extra text — so its output can be dropped directly into an application, instead of returning a paragraph a developer would have to manually parse.

## Use Case
**Scenario:** Extracting structured data from customer support messages.

**JSON Schema:**
```json
{
  "name": "string",
  "email": "string",
  "issue_type": "string",
  "urgency": "low | medium | high"
}
```

## Prompt Used

> You are a strict data extraction system. Extract information from the support message below and respond with ONLY valid JSON — no extra text, no explanation, no markdown formatting. Follow this exact schema:
> {"name": "string", "email": "string", "issue_type": "string", "urgency": "low | medium | high"}
>
> Support message: "[message here]"

## Test Results — 5 Sample Inputs

| # | Input Summary | Output | Valid JSON? |
|---|---|---|---|
| 1 | Password reset, urgent tone, client meeting | `{"name":"Ahmed Raza","email":"ahmed.raza99@gmail.com","issue_type":"password reset","urgency":"high"}` | ✅ |
| 2 | Billing double-charge, casual tone | `{"name":"Sara Khan","email":"sarakhan_finance@company.com","issue_type":"billing","urgency":"low"}` | ✅ |
| 3 | Account locked, informal name placement | `{"name":"Muhammad Hassan","email":"m.hassan@outlook.com","issue_type":"account locked","urgency":"high"}` | ✅ |
| 4 | Billing address update, formal tone | `{"name":"Fatima Sheikh","email":"fatima.sheikh@corpmail.com","issue_type":"billing address update","urgency":"low"}` | ✅ |
| 5 | Very casual/slang message | `{"name":"Jake","email":"jake_m@yahoo.com","issue_type":"billing","urgency":"low"}` | ✅ |

All 5 samples parsed correctly with no formatting errors, no extra text, and accurate field extraction — including correctly inferring urgency from tone/context even when not explicitly stated.

## "Break It" Test — Deliberately Messy Input

**Input:**
> "asdkjaskdj I DONT KNOW WHAT'S HAPPENING with my thing??? someone said email but idk if that's right, maybe try jake@ or contact my brother instead, he handles this stuff. anyway URGENT!!! or not idk anymore. forget it."

**Output:**
```json
{"name":"","email":"","issue_type":"unknown","urgency":"medium"}
```

**Observation:** The schema did **not** break. Instead of hallucinating a fake name/email or producing malformed JSON, the model:
- Left `name` and `email` as empty strings since no valid data was present
- Marked `issue_type` as `"unknown"` rather than guessing
- Resolved the contradictory urgency signals ("URGENT!!!" vs. "forget it") into a balanced `"medium"`

No fix was needed — the strict schema + "ONLY valid JSON" constraint held up even under a deliberately confusing, low-signal input.

## Summary

Testing the JSON extraction prompt on 5 realistic support messages plus one deliberately messy/contradictory input showed that all 6 responses returned valid, parseable JSON with no extra text. Even the "break" attempt didn't cause malformed output — the model gracefully defaulted to empty strings and "unknown"/"medium" labels for missing or ambiguous data rather than hallucinating fake values. This demonstrates that a well-constrained prompt with a strict schema and an explicit "ONLY valid JSON" instruction produces reliable, production-ready structured output even under edge-case inputs — exactly what real applications need to safely parse AI responses into code.

## Concepts Covered
- Structured Outputs / JSON mode
- Schema design for LLM extraction tasks
- API response parsing
- Prompt constraints for reliability
- Edge-case / adversarial input testing# Structured-JSON-Outputs-Task.
