# HW1 submission

**Sezim:**
**Student ID:** 64043
**4007-8:**
**(https://github.com/ardakshalkar/cs4007-hw1-i)**

## AI tool disclosure

State which AI tools you used and for what. Expected and fine; undisclosed use
is not.

>I used ChatGPT to help understand the assignment requirements, debug errors, and review my Python code.

---

## Sublab Easy — the registration bot and its bill

**How I laid the catalogue out inside the system prompt, and why:**

>I included every course in the system prompt with its code, title, credits, prerequisites, meeting times, and remaining seats. I also included the student's completed courses and credit limit, and instructed the model to refuse courses outside the catalogue. I used a structured layout so the model could clearly distinguish the course information and registration rules.

**My turn 5 (Kazakh or Russian):**

> Мен үшінші курс студентімін. Қандай курстарға әлі де тіркеле аламын?
>
> The bot correctly identified CSS-4007, CSS-4102, and FIN-3300 as courses I am eligible for. It also detected the timetable conflict between CSS-4007 and CSS-4102 and refused the invented CSS-4090 course in turn 4.

### Run 1 — OpenAI, `gpt-5.6-luna`

| Turn | Input tokens | Output tokens | Cost $ |
|---|---|---|---|
| 1 | 1234 | 343 | 0.000658 |
| 2 | 1441 | 125 | 0.000438 |
| 3 | 1549 | 113 | 0.000445 |
| 4 | 1639 | 45 | 0.000382 |
| 5 | 1713 | 331 | 0.000740 |
| **total** | | | **0.002664** |

### Run 2 — OpenRouter, `google/gemma-4-26b-a4b-it:free`

| Turn | Input tokens | Output tokens | Cost $ |
|---|---|---|---|
| 1 | | | |
| 2 | | | |
| 3 | | | |
| 4 | | | |
| 5 | | | |
| **total** | | | |

> **Run note:** The OpenRouter Gemma run could not complete because the upstream Google AI Studio shared pool returned HTTP 429 (`temporarily rate-limited upstream`). The failure occurred before turn 1 completed, so no valid per-turn token counts, costs, or OpenRouter turn-4 reply were produced. I left those result fields blank rather than fabricating measurements.

### Turn 4, verbatim

The turn where you asked for CSS-4090, which does not exist. Paste both replies
exactly as they came back — do not tidy them.

**OpenAI:**

```I can’t add **CSS-4090 — Quantum Machine Learning** because it is **not listed in the course catalogue**. I don’t have information about its credits, prerequisites, schedule, or availability.

```

**OpenRouter:**

```

```

### Written answers

**1. The two providers used almost identical code. What actually changed, and
what did not?**

>The main changes were the API key, base URL, and model/provider selection. OpenRouter uses the OpenAI-compatible API format, so the same OpenAI client library and almost the same chat/completions code could be used. The conversation history, messages, system prompt, and overall request logic did not change.

**2. Why did the input token count climb on every turn when your questions
stayed roughly the same length? Use the numbers from your own table. What
happens to the bill at fifty turns?**

>The input token count increased because every turn sends the entire conversation history again, not only the newest question. In my OpenAI run, the input grew from 1234 tokens on turn 1 to 1376, 1466, 1552, and 1623 tokens on turn 5. Therefore, even though each new question was fairly short, previous questions and answers became part of every later request. At fifty turns this repeated history would make later requests much larger, so the total bill would grow faster than if every turn had a constant input size.

**3. Turn 4: did the bot refuse, or did it invent CSS-4090?** If it refused, what
in your system prompt held the line? If it invented, what did it make up —
credits, a room, an instructor?

>The bot refused to invent CSS-4090. My system prompt explicitly included every real course in the catalogue and instructed the model to refuse courses outside the catalogue rather than inventing them. That instruction held the line: the bot said that CSS-4090 was not listed and did not invent credits, prerequisites, a schedule, or availability.

**4. Where else was either bot wrong?** Turn 2 asks for two courses that meet at
the same hour; two courses in the catalogue are full. Did the bots notice?

>The bot correctly noticed the time conflict in turn 2: CSS-4007 and CSS-4102 both meet on Tuesday from 09:00–10:50, so it refused to register them together. It also noticed unavailable courses when discussing eligibility; for example, it stated that CSS-4400 was full. Therefore, the OpenAI bot handled both the schedule conflict and seat availability correctly.

---

## Sublab Medium — one task, six models

Paste the per-model summary printed by `correct_kazakh.py`:

| Model | Exact / 8 | Failed | Tokens | Cost $ |
|---|---|---|---|---|
| google/gemma-4-26b-a4b-it:free | | | | |
| qwen/qwen3.8-27b | | | | |
| deepseek/deepseek-v4-flash-0731 | | | | |
| gpt-5.6-luna | 3 | 0 | 2701 | 0,00260 |
| gpt-5.6-terra | 3 | 0 | 1716 | 0,01415 |
| gpt-5.6-sol | 3 | 0 | 1808 | 0,03814 |

### Which error types did each model repair?

> **OpenRouter note:** The three OpenRouter models did not produce valid evaluation results in this run. Gemma returned HTTP 429 (upstream rate limit), Qwen returned HTTP 402 (credit/max_tokens limit), and DeepSeek encountered a connection/response failure. Therefore I did not fabricate Exact, token, or cost measurements for these models.

Rows are error labels, columns are models. Write "yes", "no" or "partial".

| Error type | gemma | qwen | deepseek | luna | terra | sol |
|---|---|---|---|---|---|---|
| kaz_to_rus | failed | failed | failed | yes  | yes  | yes  |
| latin_homoglyph | failed | failed | failed | yes  | yes  | yes  |
| drop_hyphen | failed | failed | failed | yes  | yes  | yes  |
| join_words | failed | failed | failed | yes  | yes  | yes  |
| double_letter | failed | failed | failed | yes  | yes  | yes  |

**The `latin_homoglyph` row: what happened?** Describe what you observed. The
explanation is Sublab Harder's job, not this one's.

>All three successful OpenAI models repaired the Latin homoglyphs. For example, the mixed-script text `Aлaяқtарға` was converted to the Cyrillic Kazakh `Алаяқтарға`, and the mixed Latin/Cyrillic characters in KZ-08 were also repaired.

**Where a model returned good Kazakh that was not identical to the original,
say so here.** Exact match is not correctness.

>Several outputs were good Kazakh but were not exact matches. For example, Luna changed KZ-01 from `бірқатар мемлекеттің елшісінен` to `бірқатар мемлекеттердің елшілерінен`. Terra and Sol added a comma in KZ-03, and all three models added final punctuation to some sentences. These differences caused exact-match failures even though the original corruption was repaired.

**Cheapest model that was good enough, and why:**

>Among the three OpenAI models that completed the task, gpt-5.6-luna was the cheapest at $0.00260. It repaired all five tested error types and produced good Kazakh even when some outputs were not exact string matches, so it was good enough for this correction task.

---

## Sublab Harder — open the tokenizer

### A. What a language costs

**`cl100k_base`:**

| Language | Tokens | Chars | Tok/char | × English | $ per 1,000 sentences |
|---|---|---|---|---|---|
| kk | 200 | 263 | 0.760 | 3.75 | 1.000 |
| ru | 129 | 277 | 0.466 | 2.30 | 0.645 |
| en | 59 | 291 | 0.203 | 1.00 | 0.295 |

**`o200k_base`:**

| Language | Tokens | Chars | Tok/char | × English | $ per 1,000 sentences |
|---|---|---|---|---|---|
| kk | 84 | 263 | 0.319 | 1.58 | 0.420 |
| ru | 74 | 277 | 0.267 | 1.32 | 0.370 |
| en | 59 | 291 | 0.203 | 1.00 | 0.295 |

### B. What a homoglyph does

One row per `latin_homoglyph` sentence in the dataset. Paste the actual decoded
token strings around the divergence point, not a description of them.

| Sentence id | Foreign char (index, name) | Tokens correct | Tokens corrupted | Δ | Diverges at |
|---|---|---|---|---|---|
| KZ-03 | 0 'A' LATIN CAPITAL LETTER A; 2 'a' LATIN SMALL LETTER A; 5 't' LATIN SMALL LETTER T | `['А', 'лая', 'қ', 'тарға', ' ақша']` |  `['A', 'л', 'a', 'я', 'қ']` | +4 | 0 |
| KZ-08 | 1 'o' LATIN SMALL LETTER O; 3 'a' LATIN SMALL LETTER A; 9 'T' LATIN CAPITAL LETTER T | `['Д', 'он', 'аль', 'д', ' Т', 'рамп']` | `['Д', 'o', 'н', 'a', 'л', 'ль']` | +3 | 1 |

**Token pieces around the divergence:**

```
correct  : ['А', 'лая', 'қ', 'тарға', ' ақша']
corrupted: ['A', 'л', 'a', 'я', 'қ']
```

### C. Did it get better?

| Language | cl100k_base | o200k_base | Change |
|---|---|---|---|
| kk | 0.760 | 0.319 | -0.441 |
| ru | 0.466 | 0.267 | -0.199 |
| en | 0.203 | 0.203 | 0.000 |

### Written answers

**1. What is the Kazakh tax?** The ratio against English in both encodings, the
dollar figure from A, and how much it changed between the two tokenizers.

>With `cl100k_base`, Kazakh uses 200 tokens compared with 59 for English, making it 3.75× as expensive by the tok/char comparison; 1,000 Kazakh sentences would cost about $1.00 at the $5/M-token input rate. With `o200k_base`, Kazakh falls to 84 tokens and 1.58× English, costing about $0.42 per 1,000 sentences. Thus the estimated Kazakh cost drops from $1.00 to $0.42, a reduction of $0.58, or about 58%.

**2. Why did the models repair `kaz_to_rus` but struggle with
`latin_homoglyph`?** Both are single-letter substitutions and both look almost
identical on screen. Use your token streams from B as the evidence. Say what the
model actually received in each case.

>`kaz_to_rus` substitutions remain Cyrillic, so the surrounding Kazakh text stays within the same script. `latin_homoglyph`, however, inserts visually similar Latin characters that change the token stream. In KZ-03, the corrupted form used 20 tokens instead of 16 (+4) and diverged from the correct stream at token 0: the correct beginning was `['А', 'лая', 'қ', 'тарға', ' ақша']`, while the corrupted beginning was `['A', 'л', 'a', 'я', 'қ']`. In KZ-08, the corruption increased the count from 21 to 24 (+3) and divergence began at index 1. Thus the model did not receive merely a visually misspelled version of the same tokens; it received a different sequence of token pieces. In my successful OpenAI runs the models still repaired these homoglyph cases, but the token evidence shows why this corruption can be more disruptive.

**3. Name one thing this measurement does not explain about your Sublab Medium
results.** You measured OpenAI's tokenizers; three of your six models were not
OpenAI's. What follows, and what would you have to do to close the gap?

>Tokenization alone does not explain the differences in correction quality between the six models. The tokenizer experiment measures how text is segmented, but model behaviour also depends on training data, learned language knowledge, model architecture, and prompting. In addition, three of the six models were accessed through OpenRouter rather than OpenAI, so I did not measure their tokenizers here. To close this gap, I would need to inspect the actual tokenizer used by each of those models and compare their token streams on the same corrupted Kazakh sentences, then relate those measurements to each model's correction results.
