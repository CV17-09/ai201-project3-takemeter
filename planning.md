# DiscernAI: House of the Dragon Community Discourse Classifier

## Community

I selected the House of the Dragon fan community, focusing primarily on discussion threads and comments from Reddit communities such as r/HouseOfTheDragon and related Game of Thrones forums. This community is a good fit for a classification task because discussions vary widely in style and quality. Some posts contain emotional reactions to episodes, others provide detailed analysis of characters and storylines, many speculate about future events, and some simply express personal opinions. These differences create meaningful categories that can be learned by a machine learning model.

---

# Labels

## Reaction

Definition:

A post that primarily expresses an emotional response to an episode, scene, character, or event without substantial supporting reasoning.

Examples:

* "I can't believe what happened in the final scene. That was heartbreaking."
* "That dragon battle was absolutely incredible."

---

## Analysis

Definition:

A post that explains, interprets, or evaluates events using evidence from the show, dialogue, character actions, or source material.

Examples:

* "Alicent's behavior reflects years of pressure from Otto, which explains many of her decisions."
* "The writers foreshadowed this conflict several episodes earlier through conversations between the characters."

---

## Theory

Definition:

A post that predicts future events, character decisions, or plot developments based on speculation.

Examples:

* "I think Daemon will eventually betray Rhaenyra."
* "The show may combine several book storylines into a single plot next season."

---

## Opinion

Definition:

A post that expresses a personal judgment or preference about a character, episode, actor, or storyline without substantial supporting evidence.

Examples:

* "Alicent is the most interesting character in the series."
* "Season 2 is weaker than Season 1."

---

# Hard Edge Cases

One difficult category boundary exists between Analysis and Opinion.

Example:

"Alicent is the best character because she consistently acts according to her values."

Possible Labels:

* Analysis
* Opinion

Decision Rule:

If the post mainly expresses a personal preference, it will be labeled Opinion.

If the post provides multiple pieces of evidence and reasoning to support the claim, it will be labeled Analysis.

Another difficult category boundary exists between Reaction and Opinion.

Example:

"That episode was terrible."

Decision Rule:

If the post is primarily an emotional response, it will be labeled Reaction.

If the post expresses a broader judgment about quality or preference, it will be labeled Opinion.

---

# Data Collection Plan

Data will be collected from public House of the Dragon discussion threads and comment sections on Reddit.

Target Distribution:

* Reaction: 50 examples
* Analysis: 50 examples
* Theory: 50 examples
* Opinion: 50 examples

Total Dataset Size:

* 200 labeled examples

Dataset Split:

* Training Set: 140 examples
* Validation Set: 30 examples
* Test Set: 30 examples

If one label is underrepresented after collecting 200 examples, I will intentionally search for additional discussion threads where that category appears more frequently and collect extra examples until the dataset is reasonably balanced.

---

# Evaluation Metrics

Accuracy will be reported because it provides an overall measure of classification performance.

However, accuracy alone is insufficient because some labels may be more difficult to classify than others.

Additional metrics will include:

* Precision
* Recall
* F1 Score
* Confusion Matrix

Precision will show how often predictions for a label are correct.

Recall will show how many true examples of a label were successfully identified.

F1 Score balances precision and recall and provides a better measure of overall classification quality.

The confusion matrix will help identify which labels are commonly confused with one another.

---

# Definition of Success

The classifier will be considered successful if it achieves:

* Overall Accuracy ≥ 75%
* Average F1 Score ≥ 0.70
* No individual class F1 Score below 0.60

For comparison, performance will also be measured against a zero-shot Llama 3.3 70B baseline.

The model will be considered useful if it performs better than the baseline and can reliably distinguish between the four discourse categories.

For deployment in a real community tool, an accuracy of approximately 80% and an average F1 score above 0.75 would be considered good enough for practical use.

---

# AI Tool Plan

## Label Stress-Testing

Before annotation begins, an AI tool will be used to generate 5–10 examples that intentionally sit between two labels.

These examples will be used to test whether the label definitions are sufficiently precise.

If examples cannot be consistently classified, the definitions and decision rules will be revised before data annotation begins.

---

## Annotation Assistance

ChatGPT may be used to suggest preliminary labels for some examples.

All labels will be manually reviewed and corrected by the project author before inclusion in the final dataset.

Examples that were initially AI-labeled will be tracked separately for transparency and disclosure.

---

## Failure Analysis

After evaluation, incorrectly classified examples will be provided to an AI tool to identify possible error patterns.

Potential patterns include:

* Confusion between Analysis and Opinion
* Confusion between Reaction and Opinion
* Short comments lacking context
* Posts containing both speculation and analysis

Any patterns suggested by the AI tool will be manually verified by reviewing the original examples and confusion matrix before reporting conclusions.

---

# Success Criteria Review

The success criteria are specific and measurable.

At the conclusion of the project, the classifier's performance can be objectively evaluated using accuracy, precision, recall, F1 score, and comparison against the zero-shot baseline. These metrics provide a clear determination of whether the project achieved its intended goals.
