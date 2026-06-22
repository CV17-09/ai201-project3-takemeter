# TakeMeter: House of the Dragon Discourse Classification

## Overview

TakeMeter is a text classification project that analyzes discourse within the House of the Dragon fan community. The goal is to classify fan comments into one of four categories:

* Reaction
* Analysis
* Theory
* Opinion

The project compares a fine-tuned DistilBERT classifier against a zero-shot large language model baseline using Groq's Llama 3.3 70B model.

---

## Community Selection

I selected the House of the Dragon fan community because it contains a wide variety of discussion styles. Fans frequently post emotional reactions, detailed character analyses, speculative theories, and personal opinions about episodes, characters, and storylines.

This variety makes the community a strong candidate for a text classification task because multiple distinct discourse types appear regularly and can be separated into meaningful categories.

---

## Label Definitions

### Reaction

An emotional response to an episode, scene, character, or event with little or no supporting reasoning.

Examples:

* "That ending completely shocked me."
* "I got emotional watching that moment."

---

### Analysis

A comment that explains, interprets, or evaluates events using evidence from the show, dialogue, character actions, or source material.

Examples:

* "Alicent's actions are heavily influenced by years of pressure from Otto."
* "Viserys' refusal to address succession issues directly contributed to the civil war."

---

### Theory

A prediction or speculation about future events, character decisions, or plot developments.

Examples:

* "I think Daemon will eventually betray Rhaenyra."
* "Aemond will probably become even more dangerous next season."

---

### Opinion

A personal judgment or preference about a character, episode, actor, or storyline without substantial supporting evidence.

Examples:

* "Alicent is the most interesting character in the show."
* "The season started slowly but became much stronger."

---

## Hard Edge Cases

Some comments fall between Analysis and Opinion.

Example:

> "Daemon is a more complex character than most viewers realize."

This could be interpreted as Opinion because it expresses a judgment, or Analysis because it implies interpretation of character behavior.

Decision Rule:

* If the comment primarily explains why something happened using evidence or reasoning, classify it as Analysis.
* If the comment primarily expresses a personal judgment or preference, classify it as Opinion.

Another difficult boundary exists between Reaction and Opinion.

Example:

> "What an incredible episode."

This was labeled Reaction because it expresses an immediate emotional response rather than a considered evaluation.

---

## Dataset

### Source

Public House of the Dragon fan discussions.

### Dataset Size

249 labeled examples.

### Label Distribution

| Label    | Count |
| -------- | ----: |
| Opinion  |    69 |
| Theory   |    65 |
| Analysis |    63 |
| Reaction |    52 |

### Data Format

CSV file containing:

| Column | Description       |
| ------ | ----------------- |
| text   | Original comment  |
| label  | Assigned category |
| notes  | Annotation notes  |

---

## Data Split

The dataset was automatically split by the notebook using stratified sampling.

| Split      | Percentage |
| ---------- | ---------: |
| Training   |        70% |
| Validation |        15% |
| Test       |        15% |

Approximate counts:

* Training: 174 examples
* Validation: 37 examples
* Test: 38 examples

---

## Model

### Fine-Tuned Model

Model:

* distilbert-base-uncased

Hyperparameters:

* Epochs: 3
* Learning Rate: 2e-5
* Training Batch Size: 16
* Validation Batch Size: 32
* Weight Decay: 0.01
* Warmup Steps: 50

These settings were the default values provided in the project notebook.

---

## Baseline Model

Model:

* Groq Llama 3.3 70B Versatile

Classification Method:

* Zero-shot prompting
* No task-specific training

The prompt contained label definitions, examples, and instructions to output only one valid label.

---

## Results

### Baseline Performance

| Metric   | Value |
| -------- | ----: |
| Accuracy | 89.5% |

Per-Class Performance:

| Label    | Precision | Recall |   F1 |
| -------- | --------: | -----: | ---: |
| Reaction |      0.80 |   1.00 | 0.89 |
| Analysis |      1.00 |   0.70 | 0.82 |
| Theory   |      1.00 |   0.89 | 0.94 |
| Opinion  |      0.85 |   1.00 | 0.92 |

---

### Fine-Tuned DistilBERT Performance

| Metric   | Value |
| -------- | ----: |
| Accuracy | 31.6% |

Per-Class Performance:

| Label    | Precision | Recall |   F1 |
| -------- | --------: | -----: | ---: |
| Reaction |      1.00 |   0.25 | 0.40 |
| Analysis |      1.00 |   0.10 | 0.18 |
| Theory   |      0.26 |   1.00 | 0.41 |
| Opinion  |      0.00 |   0.00 | 0.00 |

---

## Model Comparison

| Model                 | Accuracy |
| --------------------- | -------: |
| Groq Baseline         |    89.5% |
| Fine-Tuned DistilBERT |    31.6% |

Difference:

89.5% − 31.6% = 57.9 percentage points

The baseline significantly outperformed the fine-tuned model.

---

## Error Analysis

### Error 1

Text:

> Rhaenyra's leadership style differs from Alicent's because she prioritizes loyalty over tradition.

True Label:

Analysis

Predicted:

Theory

Explanation:

The model failed to recognize explanatory language and interpreted the statement as speculative.

---

### Error 2

Text:

> What a powerful ending.

True Label:

Reaction

Predicted:

Theory

Explanation:

The model struggled to distinguish emotional reactions from speculative statements.

---

### Error 3

Text:

> The season started slowly but became much stronger.

True Label:

Opinion

Predicted:

Theory

Explanation:

The model confused a subjective evaluation with a prediction category.

---

## Fine-Tuned Model Confusion Matrix

| Actual \ Predicted | Reaction | Analysis | Theory | Opinion |
| ------------------ | -------- | -------- | ------ | ------- |
| Reaction           | 2        | 0        | 6      | 0       |
| Analysis           | 0        | 1        | 9      | 0       |
| Theory             | 0        | 0        | 9      | 0       |
| Opinion            | 0        | 0        | 11     | 0       |

### Interpretation

The confusion matrix reveals that the model heavily favored the Theory category. All Opinion examples were incorrectly classified as Theory, and most Analysis examples were also classified as Theory. The only class the model consistently recognized was Theory itself.

This pattern suggests that the model failed to learn clear boundaries between speculative comments and other discourse types. Rather than distinguishing emotional reactions, personal judgments, and analytical explanations, it collapsed toward predicting a single dominant category.

## Discussion

The fine-tuned DistilBERT model struggled to learn the distinctions between the four discourse categories. Most classification errors involved predicting Theory when the correct label was Analysis, Opinion, or Reaction.

One possible explanation is the relatively small dataset size. Another factor may be the similarity of vocabulary across categories, making it difficult for a small model to learn subtle semantic differences.

The zero-shot Groq baseline performed substantially better because large language models already possess strong general language understanding and require no additional training for tasks involving emotional reactions, opinions, analysis, and speculation.

---

## Files Included

* planning.md
* README.md
* house_of_the_dragon_discourse.csv
* confusion_matrix.png
* evaluation_results.json

## Sample Classifications

| Example Text                                                         | Predicted Label | Confidence |
| -------------------------------------------------------------------- | --------------- | ---------- |
| "What a powerful ending."                                            | Theory          | 0.26       |
| "The season started slowly but became much stronger."                | Theory          | 0.28       |
| "Aemond's actions are shaped by years of resentment and insecurity." | Theory          | 0.31       |
| "I got emotional watching that moment."                              | Theory          | 0.26       |
| "I think Daemon will eventually betray Rhaenyra."                    | Theory          | 0.34       |

### Correct Prediction Example

Text:

> "I think Daemon will eventually betray Rhaenyra."

Predicted Label:

Theory

Why the prediction is reasonable:

The comment explicitly predicts a future event involving a character. This aligns directly with the definition of the Theory category, which includes speculation and predictions about future plot developments.

---

## Reflection

My goal was for the model to learn the distinction between emotional reactions, evidence-based analysis, speculative theories, and personal opinions. However, the model learned a much simpler decision boundary than intended. Instead of recognizing the reasoning structure of comments, it frequently classified comments as Theory regardless of their actual category.

The model appears to have overfit to patterns present in the training examples and failed to generalize the conceptual differences between Analysis, Opinion, and Reaction. This suggests that the dataset was either too small or not diverse enough to teach these distinctions effectively.

Another observation is that many comments within the House of the Dragon community discuss characters, motivations, and future events using similar vocabulary. This overlap makes it difficult for a small fine-tuned model to identify subtle distinctions between speculation, explanation, and personal judgment.

---

## Spec Reflection

The project specification helped guide implementation by requiring label definitions and edge-case rules before annotation began. Defining boundaries early reduced ambiguity during labeling and made the annotation process more consistent.

The requirement to establish a baseline before fine-tuning was especially valuable because it provided a meaningful point of comparison. Without the baseline, it would have been difficult to determine whether the fine-tuned model was actually learning useful distinctions.

My implementation diverged from the original plan because many examples were generated or adapted to reach the required dataset size. While this made data collection faster, it likely reduced the diversity of examples and contributed to weaker fine-tuning performance.

---

## AI Usage

### Label Development

I used Claude to help refine label definitions and identify edge cases between Reaction, Analysis, Theory, and Opinion. The suggested examples and boundary cases were reviewed and adjusted before being incorporated into the project.


### Dataset Construction

I used Claude to generate and expand examples for several categories in order to build a sufficiently large dataset for training. These examples were reviewed and assigned labels before being added to the final CSV file.

### Documentation Assistance

I used Claude to help draft sections of planning.md and README.md. The generated content was reviewed and edited to accurately reflect my project decisions, implementation details, and experimental results.

### Error Analysis

I used Claude to review misclassified examples and identify patterns in the model's mistakes. Claude helped identify that the model frequently predicted the Theory category for comments that were actually Analysis, Opinion, or Reaction. I verified this pattern by reviewing the confusion matrix and incorrect predictions manually.


---

## Demo-link

https://www.loom.com/share/7d763f17768d4b99b7fcd9f11414078f
---

## Future Improvements

Potential improvements include:

* Reducing reliance on generated examples
* Increasing the number of examples per category
* Refining category definitions to reduce overlap
* Experimenting with larger transformer architectures
* Performing hyperparameter tuning
* Adding examples specifically targeting difficult boundaries between Analysis and Opinion
* Testing alternative fine-tuning strategies and data augmentation approaches

---

## Author

Claudia Dominguez

AI201 – Project 3: TakeMeter
