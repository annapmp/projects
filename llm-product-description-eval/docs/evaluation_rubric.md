# Evaluation Rubric

Each generated product description is scored as **good / ok / bad** on five per-description criteria. Two system-level metrics are measured across the whole run. Human raters and the LLM judge use the same rubric.

## Per-description criteria

### Fluency: how naturally the description reads as prose
| Rating | Definition |
|---|---|
| good | Natural, flowing prose. Sentences connect logically, with no awkward phrasing, abrupt stops or repetition. |
| ok | Mostly readable, but has 1–2 awkward phrases or slightly choppy transitions. Feels a little mechanical. |
| bad | Multiple unnatural constructions, repetitive phrasing, run-on sentences, or obvious template output. |

### Grammar: spelling, punctuation and syntax
| Rating | Definition |
|---|---|
| good | No errors. Capitalisation is consistent. |
| ok | 1–2 minor errors that do not distort meaning. |
| bad | 3 or more errors, or any error that changes the meaning. |

### Tone: friendly, credible and sales-oriented without being spammy
| Rating | Definition |
|---|---|
| good | Warm, confident and persuasive, like a reputable retailer. No hyperbole and no clinical language. |
| ok | Slightly flat (reads like a spec sheet) or slightly over-hyped. Publishable with minor edits. |
| bad | Purely technical, or aggressively spammy with unsupported superlatives. Needs a full rewrite. |

### Length: word count of the description only
| Rating | Definition |
|---|---|
| good | 50–90 words |
| ok | 40–49 or 91–110 words |
| bad | 39 words or fewer, or 111 words or more |

### Grounding: every factual claim can be traced to the input data
| Rating | Definition |
|---|---|
| good | Every specific claim (material, feature, warranty and so on) is in the input. Generic praise is fine only if it is consistent with the attributes. |
| ok | One minor, low-risk inference, such as "easy to clean" for stainless steel. No fabricated facts. |
| bad | At least one fabricated or unverifiable specific claim. **One fabricated fact is enough for bad.** |

## System-level metrics (averaged across all calls)

| Metric | good | ok | bad |
|---|---|---|---|
| Latency per call | ≤ 3,000 ms | 3,001–6,000 ms | > 6,000 ms |
| Cost per call | < $0.0005 | $0.0005–$0.002 | > $0.002 |

## Pass / fail rules

```text
final_score = "fail"  if grounding == "bad"              # go/no-go: fabricated facts are a legal and trust risk
final_score = "fail"  if length    == "bad"              # go/no-go: unusable output, regenerate
final_score = "fail"  if count(bad)  > 0                 # across the 5 criteria
final_score = "fail"  if count(good) < 3
final_score = "pass"  otherwise
```

In plain terms, a description passes with **at least 3 goods, no bads, and neither hard-fail rule triggered**.
