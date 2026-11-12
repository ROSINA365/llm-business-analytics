# Support Ticket Triage with an Evaluated LLM Step

**Scenario:** LedgerLoop, a fictional invoicing app for small businesses, gets support tickets by email, chat and web form. The goal was to tag each ticket with **category, urgency and sentiment**, and to pull **invoice numbers and phone numbers** out of the message text.

**Data:** 78 tickets in nested JSON. 20 tickets were labeled as a dev set.
**Tools:** Python, pandas, regex, Google Gemini (`gemini-3.5-flash-lite`)

## What I built

1. **Flattened nested JSON** into a 78 × 14 table and standardized phone numbers.
2. **Regex extractors written test-first.** I wrote 20 tests, including tricky cases that should not match: ticket numbers, dollar amounts, "501(c)(3)" and a 14-digit tracking number. Accuracy on the dev set was **100%**.
3. **LLM classifier** returning JSON at temperature 0. Any label outside the allowed list is set to empty instead of accepted.
4. **Three prompt versions,** each scored on the dev set.

## Results (dev set, 20 tickets)

| Prompt version | Category | Urgency | Sentiment | Average |
|---|---|---|---|---|
| v1 – label names only | 95% | 65% | 90% | 83% |
| v2 – + labeling guide | 85% | 75% | 95% | 85% |
| v3 – + decision rules and 7 written examples | 90% | **85%** | 95% | **90%** |

- **Consistency:** running v3 twice gave identical labels on 20 of 20 tickets. The improvement came from the prompt, not from chance.
- **Cost:** about $0.00043 per ticket, or about **$8.66 per month** for 20,000 tickets. The free tier is too small for this volume.

## Recommendations

- **Automate now, with spot checks:** invoice IDs, phone numbers, category and sentiment.
- **Keep a human on urgency** for tickets labeled Low or Medium. Calm customers describing real bugs were often rated too low.
- **Send Critical tickets straight to the top** of the queue.
- **Next step:** add the ticket's received date to the prompt, so that cancellations dated months ahead are not marked urgent.
