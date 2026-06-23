# TakeMeter: Fine-Tuning a Football Discourse Classifier

AI201 Project 3

## Overview

This project builds a text classification system for online football (soccer) communities. The goal is to automatically classify comments into one of four discourse categories:

- NEWS
- ANALYSIS
- HOT_TAKE
- REACTION

The project compares two approaches:

1. Zero-shot classification using Groq's Llama 3.3 70B model
2. Fine-tuned DistilBERT trained on an annotated football discourse dataset

The objective is not only to achieve high accuracy, but also to understand how different models interpret football community discussions and where they fail.

---

## Dataset

The dataset contains 202 manually annotated football community comments collected from football-related online discussions.

### Labels

| Label | Description |
|---------|-------------|
| NEWS | Factual reports, transfer news, injury updates, statistics, announcements, quotes, or verified information |
| ANALYSIS | Reasoning, explanation, comparison, evidence, tactical discussion, or cause-effect logic |
| HOT_TAKE | Strong opinion or judgment with little supporting reasoning |
| REACTION | Emotional response, celebration, disappointment, surprise, or meme-like response |

### Dataset Distribution

| Label | Count |
|---------|---------|
| ANALYSIS | 74 |
| NEWS | 61 |
| HOT_TAKE | 42 |
| REACTION | 25 |
| **Total** | **202** |

After splitting:

- Training: 141 examples
- Validation: 30 examples
- Test: 31 examples

---

## Model Architecture

### Baseline Model

- Model: Llama 3.3 70B Versatile (Groq)
- Method: Zero-shot classification
- Prompt-based classification using label definitions and examples

### Fine-Tuned Model

- Model: DistilBERT Base Uncased
- Epochs: 3
- Learning Rate: 2e-5
- Batch Size: 16
- Weight Decay: 0.01

---

# Evaluation Report

## Overall Accuracy

| Model | Accuracy |
|---------|---------|
| Groq Zero-Shot Baseline | 0.645 |
| Fine-Tuned DistilBERT | 0.516 |

Fine-tuning resulted in a regression of 0.129 (12.9 percentage points) relative to the Groq baseline.

---

## Per-Class Metrics

### Groq Baseline

| Label | Precision | Recall | F1 |
|---------|---------|---------|---------|
| NEWS | 1.00 | 1.00 | 1.00 |
| ANALYSIS | 0.86 | 0.55 | 0.67 |
| HOT_TAKE | 0.29 | 0.29 | 0.29 |
| REACTION | 0.38 | 0.75 | 0.50 |

Overall Accuracy: **64.5%**

---

### Fine-Tuned DistilBERT

| Label | Precision | Recall | F1 |
|---------|---------|---------|---------|
| NEWS | 0.60 | 1.00 | 0.75 |
| ANALYSIS | 0.44 | 0.64 | 0.52 |
| HOT_TAKE | 0.00 | 0.00 | 0.00 |
| REACTION | 0.00 | 0.00 | 0.00 |

Overall Accuracy: **51.6%**

---

## Confusion Matrix

Fine-Tuned DistilBERT Test Results

| True \ Predicted | NEWS | ANALYSIS | HOT_TAKE | REACTION |
|------------------|------|----------|----------|-----------|
| NEWS | 9 | 0 | 0 | 0 |
| ANALYSIS | 4 | 7 | 0 | 0 |
| HOT_TAKE | 2 | 5 | 0 | 0 |
| REACTION | 0 | 4 | 0 | 0 |

### Key Pattern

The most important pattern is that the model completely failed to learn the HOT_TAKE and REACTION classes.

Instead, it collapsed most predictions into NEWS and ANALYSIS. Every REACTION comment was classified as ANALYSIS, while most HOT_TAKE comments were classified as ANALYSIS or NEWS.

This suggests the model learned a broad distinction between:

- factual/information-oriented content (NEWS)
- discussion-oriented content (ANALYSIS)

but never learned the finer boundaries separating opinions and emotional reactions.

---

## Error Analysis

To identify patterns, I reviewed the model's incorrect predictions and grouped them by failure type.

### Error 1

**Comment**

> France remain tournament favorites whenever Mbappe plays.

**True Label:** HOT_TAKE

**Predicted:** NEWS

### Why it failed

This comment expresses an opinion, but it is written as a factual statement.

The model appears to rely heavily on surface structure. Since the comment resembles a news headline and contains no emotional language, it was classified as NEWS even though it is fundamentally a judgment.

---

### Error 2

**Comment**

> Ronaldo WC 2030. Here we go!

**True Label:** REACTION

**Predicted:** ANALYSIS

### Why it failed

This is an emotional reaction expressing excitement.

However, the comment is extremely short and lacks obvious reaction indicators such as emojis, repeated punctuation, or expressive words.

The model likely focused on football entities ("Ronaldo", "2030") and treated it as normal football discussion.

---

### Error 3

**Comment**

> Reminder to everyone that the only reason FIFA did this was so Saudi Arabia's bid couldn't get competition.

**True Label:** ANALYSIS

**Predicted:** NEWS

### Why it failed

This comment contains causal reasoning ("the only reason FIFA did this was..."), which fits the ANALYSIS definition.

However, because it references a real football event and is written confidently, the model interpreted it as reporting information rather than explaining a cause.

---

## Common Failure Patterns

Several patterns appeared repeatedly:

### 1. Short Comments

Many misclassified examples were extremely short.

Examples:

- "I'd say 3."
- "Henry is absolutely right."
- "I'm sure he's losing sleep over this."

These comments contain too little information for DistilBERT to reliably identify intent.

---

### 2. Opinion vs Analysis

The largest source of confusion was between HOT_TAKE and ANALYSIS.

The distinction is subtle:

- HOT_TAKE = unsupported opinion
- ANALYSIS = reasoning or explanation

Many football comments sit directly on this boundary.

---

### 3. Emotion vs Discussion

REACTION comments were often classified as ANALYSIS.

The model appears to focus on football topics rather than emotional intent.

Because REACTION was the smallest class in the dataset, the model had very few examples from which to learn emotional language patterns.

---

## Is This a Labeling Problem or a Data Problem?

After reviewing the errors, I believe the primary issue is data size rather than annotation inconsistency.

The labels were applied consistently throughout the dataset.

The main challenge is that:

1. The dataset is small (202 examples total).
2. REACTION contains only 25 examples.
3. HOT_TAKE contains only 42 examples.
4. Many examples are very short and ambiguous.

The model simply did not observe enough examples to learn the boundaries separating these categories.

---

## How Could This Be Improved?

Several improvements would likely increase performance:

### More Training Data

The most important improvement would be collecting significantly more examples, especially:

- REACTION posts
- HOT_TAKE posts

### Hard-Negative Examples

Include more examples that explicitly contrast:

- ANALYSIS vs HOT_TAKE
- REACTION vs ANALYSIS

These boundaries account for most observed errors.

### Refined Label Definitions

HOT_TAKE and ANALYSIS overlap substantially.

A more precise annotation guideline could improve consistency and make the distinction easier for the model to learn.

---

## Sample Classifications

### Example 1

**Comment**

> [Fabrizio Romano] Liverpool are prepared to bid again for Yan Diomandé.

**Prediction:** NEWS

**Confidence:** 1.00

This prediction is reasonable because the comment is reporting transfer information from a recognized source.

---

### Example 2

**Comment**

> Portugal are playing for Ronaldo instead of playing football.

**Prediction:** ANALYSIS

**Confidence:** 0.64

The comment provides criticism supported by an explanation, making ANALYSIS an appropriate classification.

---

### Example 3

**Comment**

> HE DID IT AGAIN!

**Prediction:** REACTION

**Confidence:** 0.75

The statement is purely emotional and contains no factual information or reasoning.

---

## Reflection

The intended goal was to teach the model to distinguish four discourse styles.

However, the learned decision boundary was different.

Instead of learning all four categories, the model primarily learned:

- informational content (NEWS)
- discussion content (ANALYSIS)

The model effectively ignored HOT_TAKE and REACTION.

This suggests it learned topic-oriented patterns rather than discourse-oriented patterns. It focused on whether a comment looked like information or discussion instead of identifying emotional reactions and unsupported opinions.

The project demonstrates that a model can achieve moderate accuracy while still failing to capture the distinctions that matter most for the task.

---

## Spec Reflection

### How the Spec Helped

The requirement to compare a zero-shot baseline against a fine-tuned model provided a useful benchmark.

Without the baseline, I would have assumed the fine-tuned model was successful simply because training completed correctly.

The comparison revealed that fine-tuning actually reduced performance.

### How My Implementation Diverged

The original expectation was that fine-tuning would outperform the baseline.

Instead, the zero-shot model performed substantially better.

Rather than treating this as a failure, I focused on understanding why it happened and what it reveals about small datasets and class imbalance.

---

## AI Usage

AI Usage

Example 1

I used ChatGPT as a debugging and learning assistant while implementing the project. When I encountered issues with the Groq 
baseline classifier, label parsing, and the fine-tuning notebook, I asked for explanations of the errors and possible fixes. 
I tested the suggested solutions myself and only applied changes after verifying that they worked correctly.

Example 2

After evaluating both models, I used ChatGPT to discuss the results and explore possible explanations for the observed errors.
I reviewed the confusion matrix, per-class metrics, and misclassified examples myself, then used those observations to write
the final error analysis and reflection sections of this report.

Example 3

I used ChatGPT to review drafts of my README and evaluation report. The AI helped improve organization and clarity, but all
conclusions, interpretations of the results, and final written content were reviewed and edited by me before submission.

---

## Repository Contents

- soccer_discourse_dataset.csv
- planning.md
- README.md
- evaluation_results.json
- confusion_matrix.png
- Fine-tuning notebook (.ipynb)
