# Prompt Engineering for SQL on an Unfamiliar Database

**Scenario:** Prairie Outfitters, a fictional outdoor retailer with five stores and an online shop. Leadership has business questions. The answers are in a SQLite database that no AI model has seen. The goal was to use an AI assistant to write the SQL, and to prove every answer is right.

**Tools:** Python, SQLite, pandas, Google Gemini (`gemini-3.5-flash-lite`)

## 1. The context experiment

I asked for **2025 net revenue by channel** three ways:

| Prompt | Ran? | Problem | Result |
|---|---|---|---|
| No context | No | Made up tables and columns that do not exist (`OrderItems`, `Channels`) | Error |
| Schema only | Yes | Did not exclude cancelled orders | About $17,700 (4.5%) too high |
| Schema + data dictionary | Yes | None | Matched my verified query |

**Verified answer:** in-store net revenue **$238,579.11** and online **$155,336.65**.

**Lesson:** business definitions, such as "cancelled orders are not revenue", are not in the schema. The AI can only follow rules it is given.

## 2. Six business questions, six prompting techniques

| Question | Technique | Answer | How I verified it |
|---|---|---|---|
| Top 5 products by units sold, Q2 2025 | Few-shot | Jig Assortment and Walleye Pro Rod (48 units each) lead | pandas recompute; row counts before and after joins; tie check |
| Customers with a completed order each month, 2025 | Chain-of-thought | 86 to 109 per month (peak in April) | pandas `nunique()`; hand check of January |
| Return rate by category | Chain-of-thought + fan-out check | Footwear highest at 12.0%; Camping lowest at 2.7% | Unit totals identical before and after joins (5,166) |
| Customers who bought online first, then in store | Tree-of-thought | **169** customers | Two approaches agreed; pandas recompute; tie check |
| Highest average order value by store, 2025 | Self-consistency | Omaha Trailhead, $390.89 | Two formulations matched exactly; pandas recompute |
| Days between orders by loyalty tier | Prompt chaining (plan → SQL → review) | Platinum 72 days, Gold 99, Silver 140, Bronze 173 | pandas recompute; gap total check; hand check of one customer |

## 3. AI failures I caught

| Where | Type | How I caught it | Fix |
|---|---|---|---|
| No-context query | Made-up tables and columns | SQLite error | Added the schema and dictionary |
| Schema-only query | Wrong business logic (cancelled orders counted) | Independent verification query | Added the data dictionary |
| Return-rate query | Invalid SQL (leftover draft code) | Syntax error | Repair prompt showing the model its own error |
| Online-then-in-store query | Wrong filter (`>=` instead of `>`) | Reading the SQL against the question | Manual edit (the answer stayed 169) |

## 4. Recommendation memo (summary)

Analysts should report numbers from AI-written SQL only when:

1. The model had the full schema and business definitions.
2. The result was checked in an independent way.
3. The prompt, SQL and check are logged.
4. Any definition choices are stated next to the number.

The main risk that remains is that the analyst and the AI can share the same wrong reading of a question. The fix is to confirm definitions with the business owner.
