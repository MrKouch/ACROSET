# ACROSET: A Benchmark for Novel Acronym Expansion from Context

This repository contains **ACROSET**, a benchmark for evaluating dictionary-independent acronym expansion from context.

In standard acronym disambiguation, models are usually asked to select the correct expansion from a predefined candidate inventory. ACROSET instead evaluates a harder setting: the model must infer the expansion directly from context, without access to a fixed acronym dictionary.

## Task Overview

Each ACROSET example is constructed from naturally occurring text.

Given a source context, such as a paragraph, article, or dialogue, we select a contiguous span of `k` words, where `2 <= k <= 6`. This span is treated as the gold expansion. We then form an acronym by concatenating the initial letters of the words in the span and replace the original span with this acronym in the context.

The task is to recover the original expansion from the acronym and the provided context.

For example, if the original span is:

```text
long term memory
```

the constructed acronym is:

```text
LTM
```

The model is then asked to expand `LTM` using only the available context.

## Dataset Sources

ACROSET includes examples from three sources:

| Source | Description |
|---|---|
| `gutenberg` | Literary text from Project Gutenberg |
| `cc_news` | News articles collected from Common Crawl news sources |
| `daily_dialog` | Short conversational dialogues from DailyDialog |

These sources are included to test acronym expansion across different registers: literary prose, news text, and casual dialogue.

## Dataset Size and Splits

The full released dataset contains **129,623 unique examples**, after duplicate removal.

The data is split into train, validation, and test partitions using a **70/10/20 split** with random seed `42`.

| Split | Total | Gutenberg | CC News | DailyDialog |
|---|---:|---:|---:|---:|
| Train | 90,736 | 29,524 | 31,563 | 29,649 |
| Validation | 12,962 | 4,215 | 4,405 | 4,342 |
| Test | 25,925 | 8,415 | 9,026 | 8,484 |
| Total | 129,623 | 42,154 | 44,994 | 42,475 |

The experiments reported in the paper were conducted **only on the held-out test split**.

The full dataset, including train and validation splits, is released here to support future work on supervised, fine-tuned, or task-adapted acronym expansion models.

## Span Selection Modes

ACROSET includes two span selection modes:

| Mode | Description |
|---|---|
| `constituent` | The selected span is a syntactic constituent. These examples are intended to produce more linguistically coherent expansions. |
| `random_span` | The selected span is a random contiguous word sequence, without requiring syntactic coherence. These examples provide a harder stress-test setting. |

This distinction allows researchers to evaluate whether models benefit from syntactic coherence when recovering acronym expansions.

## Context Variants

Each acronym-expansion pair is released with multiple context variants.

For `gutenberg` and `cc_news`, the context variants are:

| Context type | Description |
|---|---|
| `none` | No surrounding context |
| `window` | A local window of five words on each side of the acronym |
| `sentence` | The sentence containing the acronym |
| `paragraph` / `article` | The full paragraph or article context |
| `paragraph_wo_sentence` / `article_wo_sentence` | The paragraph/article context with the target sentence removed |

For `daily_dialog`, the context variants are:

| Context type | Description |
|---|---|
| `none` | No surrounding context |
| `utterance` | The utterance containing the acronym |
| `dialogue` | The full dialogue |

The paper’s main experiments focus on local and full-context settings, while the released dataset includes additional context granularities for future analysis.

## Repository Structure

The dataset is organized by:

```text
split / source / span_selection_mode / acronym_length / context_type
```

A typical structure is:

```text
data/
  train/
    gutenberg/
    cc_news/
    daily_dialog/
  validation/
    gutenberg/
    cc_news/
    daily_dialog/
  test/
    gutenberg/
    cc_news/
    daily_dialog/
```

Within each source directory, files are further organized by span selection mode, acronym length, and context type.

## File Format

Dataset files are provided in JSONL format. Each line corresponds to one acronym expansion example.

Each example contains the information needed to evaluate acronym expansion from context, including:

| Field | Description |
|---|---|
| `acronym` | The constructed acronym |
| `gold_expansion` | The original span that should be recovered |
| `context` | The context shown to the model |
| `source` | The source dataset |
| `split` | Train, validation, or test |
| `span_mode` | Constituent or random-span |
| `context_type` | Context variant |
| `words_count` | Number of words in the gold expansion |

Depending on preprocessing version, some files may include additional metadata fields.

## Evaluation Setting

To reproduce the experiments reported in the paper, use only the `test` split.

The primary metric is **exact expansion accuracy**: a prediction is correct only if the full predicted expansion exactly matches the gold expansion after normalization.

Additional metrics used in the paper include:

| Metric | Description |
|---|---|
| Exact expansion accuracy | Full predicted expansion exactly matches the gold expansion |
| Per-word accuracy | Fraction of gold words recovered in the correct positions |
| Per-lemma accuracy | Lemma-level version of per-word accuracy |
| Clean-output rate | Whether the predicted words begin with the corresponding acronym letters |
| Token-level diagnostics | Metrics such as gold-token probability and mean reciprocal rank, for models that expose token probabilities |

## Intended Use

ACROSET can be used for:

- zero-shot acronym expansion from context
- supervised acronym expansion using the train split
- validation-based prompt or hyperparameter tuning
- evaluation on the held-out test split
- analysis by source, context type, span selection mode, acronym length, stop-word presence, and tokenization properties

## Notes on the Benchmark

ACROSET is a synthetic benchmark. The expansions are real spans from naturally occurring text, but the acronyms are automatically constructed from those spans.

This design is intentional: it reduces reliance on memorized acronym-expansion dictionaries and creates a controlled setting for testing whether models can infer novel acronym expansions from context.

Strong performance on ACROSET should therefore be interpreted as evidence of context-based acronym recovery ability, not as direct evidence of performance on all real-world acronym phenomena.

## Citation

Citation information will be added after publication.

## License

License information will be added.