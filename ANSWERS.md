# Midterm 1: answers

GitHub username: kaelowz
Session code: kettle-62
Fingerprint: 3f5ba607

> Fill in the three lines above from `my_data/DATA.md`. Do not rename headings or table columns: a script reads the numbers.
> Write only in the empty cells and lines. Percentages as 0.78 or 78 %, money as plain numbers.

## AI-use declaration

I used one AI assistant during this exam: Anthropic's Claude. I used it to plan the workflow, to write and check Python code for the statistics (recall, MRR@10, Wilson 95 % intervals) and the cost models (retrieval candidates, pipelines H and L), to draft the wording of my answers within the word limits, and to point out errors in my earlier drafts (for example a wrong number in Q3). The code was run on my own data set, generated with make_my_data.py from my GitHub username and the session code. The AI drafted most of the text of the decision, the memo and the Part 3 explanations; I read, checked and edited them, I compared the results with my own output files, and I can explain every number and sentence. I take responsibility for everything submitted here.

## Part 1: Retrieval and cost decision

### 1a. Metrics (all 60 questions)

| config | recall@1 | recall@3 | recall@5 | MRR@10 |
|---|---|---|---|---|
| A | 0.533 | 0.650 | 0.683 | 0.5869 |
| B | 0.483 | 0.617 | 0.700 | 0.5739 |

### 1b. Recall and 95 % Wilson interval of each candidate (recall at its own k)

| candidate | recall@k | lower | upper |
|---|---|---|---|
| A, k=3 | 0.650 | 0.524 | 0.758 |
| A, k=5 | 0.683 | 0.558 | 0.787 |
| B, k=3 | 0.617 | 0.490 | 0.729 |
| B, k=5 | 0.700 | 0.575 | 0.801 |

### 1c. Annual model cost in USD

| candidate | annual cost |
|---|---|
| A, k=3 | 37577 |
| A, k=5 | 48973 |
| B, k=3 | 24742 |
| B, k=5 | 27581 |

### 1d. Decision (at most 150 words)

Chosen candidate: B, k=3

Candidates (A, 3) ($37,577) and (A, 5) ($48,973) exceed the $32,600 budget and are rejected. Both B candidates fit: (B, 3) costs $24,742 and (B, 5) $27,581. Recall of (B, 5) is higher (0.700 vs 0.617), but the 95 % Wilson intervals, [0.575, 0.801] and [0.490, 0.729], overlap, so the data do not show that k=5 is better. I claim no winner and choose the cheaper option, (B, 3), which leaves $7,858 of budget.

Recall of (B, 3) by language: EN 0.700 (14/20), RU 0.600 (12/20), KK 0.550 (11/20).

Kazakh users are served worst: 11 of 20 questions found, and with only 20 questions per language the interval [0.342, 0.742] is wide. These data do not show why.

### 1e. Next measurement (at most 60 words)

Run a larger test set, at least 200 questions per language, mainly Kazakh, for B,3 and B,5. If the 8-point recall gap holds and the intervals no longer overlap, switch to B,5, which costs $2,839 more per year and stays within budget.

---

## Part 2: A document pipeline you can defend

### 2a. Numbers

| pipeline | overall accuracy | weakest cell (language, condition) | accuracy there | lower | upper | annual cost |
|---|---|---|---|---|---|---|
| H | 0.867 | KK, degraded | 0.800 | 0.627 | 0.905 | 25568 |
| L | 0.822 | KK, clean | 0.700 | 0.521 | 0.833 | 5400 |

Personal-data pages per year sent abroad under H: 292000 (5,000 * 365 * 0.16 = 292000)

### 2b. Memo (at most 300 words)

**To:** Head of Operations. **Recommendation: pipeline L, with human review of Kazakh invoices.**

**Who is served worst.** Kazakh scans, under both pipelines. H reads Kazakh at 0.817 [0.701, 0.894]; its weakest cell is KK degraded, 0.800 [0.627, 0.905]. L reads Kazakh at 0.717 [0.592, 0.815]; its weakest cell is KK clean, 0.700 [0.521, 0.833]. The overall figures (H 0.867, L 0.822) hide this. Their intervals overlap ([0.809, 0.909] vs [0.760, 0.871]), so I am not sure H is truly more accurate; with 30 invoices per cell the test is small.

**Personal data.** In 16 % of pages a name and ИИН is printed; Law No. 94-V covers the ИИН. Under H: the scan goes to a data centre abroad (known: 292,000 pages a year); the vendor stores it (to be confirmed); for how long is unknown. I would have to obtain the contract terms and the basis for the transfer; a vendor's statement is not a compliance review. Under L: the scan stays on our server in Kazakhstan; the bank stores it; retention is ours to set, so we must define it.

**Safeguards anyone can verify.** (1) Firewall logs show no outbound traffic from the L server. (2) Scans are deleted automatically after a fixed period; an audit checks the folder. (3) An access log records who opened ИИН fields, reviewed monthly. (4) Before launch, re-test L on at least 200 Kazakh invoices.

**Choice.** L: $5,400 a year against $25,568, data stays in Kazakhstan, and H's accuracy advantage is not proven. **Residual risk:** about 3 in 10 Kazakh invoices may be misread; human review of Kazakh and low-confidence invoices must catch them, at extra labour cost.

---

## Part 3: Explain your own numbers

### Q1

Number: 1.72  
Why (at most 40 words): The tokenizer, not the encoding. Bytes grow only 2.14× for Kazakh versus English, but tokens grow 3.67×; the extra 1.72× comes from a vocabulary that splits Kazakh words into many small pieces.

### Q2

Number: 35.1 %  
Why (at most 40 words): Only English is cut: 768 cl100k tokens are about 789 e5 tokens, above 512. Russian (about 384) and Kazakh (about 228) fit. The cut tail is never embedded, no error appears, and that text cannot be retrieved.

### Q3

Number: 31.8 %  
Why (at most 40 words): Dividing scores by T does not change the ranking; Monday stays first. It flattens the distribution (top token 42.8 % at T=1, 31.8 % at T=2). Every token keeps non-zero probability, so the sampleable set is unchanged.

### Q4

Number: 16  
Why (at most 40 words): The leaderboard prefers X (71 vs 65), because "I don't know" scores 0, the same as a wrong answer. This rewards guessing. With a penalty, Y wins (29 vs 13), so models learn to guess rather than abstain.

### Q5

Number: 2.45  
Why (at most 40 words): Over-trained by this criterion: 49 tokens per parameter versus Chinchilla's 20, so 2.45 times more. Chinchilla argued that models such as GPT-3 were under-trained: too many parameters for too few tokens.
