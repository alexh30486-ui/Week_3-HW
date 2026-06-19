# TakeMeter Project Planning: NBA Discourse Quality

## 1. Community
**Community chosen:** r/NYKnicks (and broader NBA Reddit community). 
**Why it fits:** Sports subreddits, particularly in the wake of a major event (like a championship parade or a controversial front-office move), feature highly active, text-heavy discourse. The community is a perfect fit for this classification task because the quality of discourse varies wildly—from highly structured historical and political analysis (e.g., assessing front-office tenure or mayoral policies) to completely groundless assertions and pure emotional venting. This allows for clear distinctions between different types of "takes."

## 2. Labels
My taxonomy measures the rhetorical substance and structure of a comment, dividing the discourse into three mutually exclusive categories:

*   **`analysis`:** The post makes a structured argument backed by specific evidence, historical context, verifiable facts, or tactical observation. 
    *   *Example 1:* "Before this Championship, Linsanity was the single biggest moment the Knicks have had in the 2000's."
    *   *Example 2:* "Its only been 6 months. Fixed tons of pot holes. Delt with 2 monster winter storms. Got expanded 3k. Got people on the rent control board..."
*   **`hot_take`:** A bold, confident, or highly speculative opinion stated without detailed supporting evidence. The claim might be true or false, but the post asserts rather than argues.
    *   *Example 1:* "Mamdani could be dictator of the world just based on oratory skills lmao."
    *   *Example 2:* "Old ppl should not run or remain in office."
*   **`reaction`:** An immediate emotional response, joke, quote drop, or short burst of hype/disgust with little to no analytical reasoning.
    *   *Example 1:* "He's such a fucking bum."
    *   *Example 2:* "Linsanity got a huge ovation lol."

## 3. Hard Edge Cases
**The Ambiguous Post:** *"The cadence of his speech is very Obama-like / For sure, he's the go to guy you want to mimic. Although Mamdani has his own vibe which makes it less immediately obvious..."*

**Decision Rule:** This post genuinely sits at the boundary of `hot_take` (a subjective opinion on speaking style) and `analysis`. The rule I established is that if a comment provides a structured, multi-sentence rationale for its comparison (e.g., breaking down rhetorical techniques and comparing them to specific figures like Josh Shapiro), it crosses into `analysis`. If it is just a standalone aesthetic judgment ("He sounds like Obama"), it is a `reaction` or `hot_take`.

## 4. Data Collection Plan
*   **Source:** Manually scraped 200+ comments from a highly active Reddit thread discussing the Knicks championship parade, Mayor Mamdani's speech, and team owner James Dolan.
*   **Method:** Copied text and hand-labeled into a CSV format. 
*   **Imbalance Handling:** Monitored label counts during collection to ensure the `reaction` class did not exceed 60-70% of the total dataset, actively seeking out longer paragraph comments to bolster the `analysis` and `hot_take` categories.

## 5. Evaluation Metrics
*   **Overall Accuracy:** To compare general performance against the zero-shot LLM baseline.
*   **Per-Class Precision, Recall, and F1-Score:** Accuracy alone is insufficient because the dataset is slightly imbalanced towards `reaction`. I need to monitor F1-scores specifically for the `hot_take` and `analysis` classes to ensure the model isn't just lazily predicting the majority class.
*   **Confusion Matrix:** Crucial for visually diagnosing whether the model is collapsing the nuanced `hot_take` class into the broader `reaction` or `analysis` classes.

## 6. Definition of Success
The classifier will be considered successful if it achieves an overall accuracy of **65% or higher** (meaningfully beating a 33% random guess) AND maintains an F1-score greater than 0.0 on the hardest class (`hot_take`). For real-world deployment in a community tool, the model needs to demonstrate that it isn't relying purely on text length or the presence of profanity to make its classifications.

## 7. AI Tool Plan
*   **Label Stress-Testing:** Used AI to help format and refine the initial label definitions by reviewing raw text dumps from the thread and helping establish strict decision boundaries for edge cases.
*   **Annotation Assistance:** Data was manually annotated to ensure high fidelity to the specific community context, without AI pre-labeling. 
*   **Failure Analysis:** I plan to feed the list of wrong predictions (from the evaluation step) into an AI assistant to help identify systemic linguistic patterns in the errors (e.g., discovering the model's bias towards classifying all short texts or text with profanity as `reaction`).
