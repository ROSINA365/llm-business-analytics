# LLMs for Business Analytics

Four projects that use large language models (LLMs) to solve everyday analytics problems. Each one checks the AI's output against code, labeled data, or an independent query, so the numbers can be trusted.

Built for DSCI 519 – Advanced Business Analytics, University of South Dakota (Fall 2026).

**Tools:** Python · pandas · SQLite · regex · Pydantic · Google Gemini API (`gemini-3.5-flash-lite`) · Google Colab

## Projects

| # | Project | Business problem | Key result |
|---|---|---|---|
| 1 | [Prompt Engineering for SQL](01-prompt-engineering-sql/) | Answer leadership questions from a retail database the AI has never seen | Adding the schema and data dictionary turned a query that would not run into the correct answer (2025 net revenue matched a verified query to the cent) |
| 2 | [Support Ticket Triage](02-support-ticket-triage/) | Tag 78 support tickets by category, urgency and sentiment, and extract invoice and phone numbers | Prompt redesign raised average accuracy from 83% to 90%; urgency accuracy rose from 65% to 85%; regex extraction was 100% accurate |
| 3 | [Registration Data Quality](03-registration-data-quality/) | Clean 120 messy conference registrations from three different forms | 100% field accuracy on the labeled dev set, with only 36 AI calls instead of 240 |
| 4 | [Entity Resolution and Log Parsing](04-entity-resolution-log-parsing/) | Match customers across a web store and a loyalty program; parse a mixed-format sales log | F1 of 1.00 on dev pairs with blocking that cut comparisons by 99%; an AI-written parser read all 60 log lines correctly |

## What these projects show

- **Context matters.** The same model gave a broken query, a wrong answer and a correct answer depending on what I put in the prompt.
- **Code first, AI where judgment is needed.** Rules handle what is predictable, such as dates, phone numbers and state codes. The AI handles judgment calls, such as job seniority, unclear dietary notes and borderline customer matches.
- **Every AI answer is verified** with a second query, a pandas recompute, a labeled dev set or a validation contract (Pydantic).
- **Cost is part of the design.** The projects track AI calls and tokens and project monthly cost.

## How to run

1. Open any notebook in Google Colab.
2. Add your API key to **Colab Secrets** (for example `GOOGLE_API_KEY`). Never paste keys into the notebook.
3. Run the setup cell. It installs the packages and writes the course helper `llmkit.py`, which works with Gemini, OpenAI, Anthropic, Groq and other providers.

The datasets are fictional and were provided by the course instructor.

---

**Author:** Sharon Rosina Wamalwa – MS Business Analytics, University of South Dakota (May 2027)
[LinkedIn](https://www.linkedin.com/in/YOUR-PROFILE)
