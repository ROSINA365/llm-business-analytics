# Customer Entity Resolution and an AI-Written Log Parser

**Scenario:** Bluestem Coffee Co. (fictional) runs a web store and a loyalty program that were never linked. Marketing wants one view of each customer. Names have nicknames and typos, people use both work and personal emails, and family members share addresses and phones.

**Data:** 83 web customers, 86 loyalty members, 40 labeled dev pairs, and a 60-line sales register log in three mixed formats.
**Tools:** Python, pandas, Jaro-Winkler and fuzzy string matching, union-find, Google Gemini (`gemini-3.5-flash-lite`)

## Part 1: Entity resolution

1. **Standardize** names, emails, phones and addresses. 42 nicknames were mapped to formal names (for example Bill → William).
2. **Blocking:** only compare records that share a phone, email or zip code. This cut **14,196 possible pairs to 136 candidates (99% fewer)** without losing any true match.
3. **Weighted similarity score** across first name, last name, email, phone, street and zip. Names carry the most weight because family members share phones and addresses.
4. **Three zones:**
   - Score of 0.85 or more: automatic match (39 pairs).
   - Below 0.55: non-match (71 pairs).
   - Between 0.55 and 0.85: the **LLM decides** (26 pairs).
5. **LLM for the gray zone only,** with rules for family members and parent/child pairs. A match needs confidence of 0.6 or more. The model confirmed 21 pairs and correctly rejected 5, including two family members at one address and a parent and child with the same name.

**Results:**

- Dev set: **precision 1.00, recall 1.00, F1 1.00.**
- **113 unique customers:** 50 in both programs, 36 loyalty-only and 27 web-only.
- **Golden records** use clear rules to choose values, for example a personal email over a work email, and the birth year from the loyalty record.

**Design choice:** for a loyalty program, merging two different people is worse than missing a match. A wrong merge mixes up points and exposes private data. The thresholds were set with this in mind.

## Part 2: AI-written log parser

The LLM **wrote a parser** (regex, JSON and datetime, standard library only) from 7 sample lines, instead of reading each line itself.

- Imports in the generated code were checked against an allowlist before it ran.
- **100% accurate** on the 15 labeled lines on the first try, and all 60 lines passed the format checks.
- **Cost:** 1 call to write the parser, compared with 60 calls to parse line by line. At 1 million lines, parsing line by line would take about 663 million tokens.

**Lesson:** use the AI to write and repair code, not to run it. Generated code is fast, repeatable and testable.
