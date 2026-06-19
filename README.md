# TakeMeter: NBA Discourse Quality Classifier

## 🏀 Project Overview
TakeMeter is a fine-tuned NLP classification model designed to evaluate the rhetorical quality of community discourse in the **r/NYKnicks** and broader NBA Reddit community. 

Rather than judging whether a comment is "positive" or "negative," this model attempts to measure the *substance* of a take, separating evidence-based arguments from groundless opinions and pure emotional reactions.

## 🏷️ Label Taxonomy
The dataset uses a custom, mutually exclusive 3-label taxonomy grounded in how sports fans actually converse:
* **`analysis`:** The post makes a structured argument backed by specific evidence, historical context, verifiable facts, or tactical observation.
* **`hot_take`:** A bold, confident, or highly speculative opinion stated without detailed supporting evidence. It asserts rather than argues.
* **`reaction`:** An immediate emotional response, joke, quote drop, or short burst of hype/disgust with little to no analytical reasoning.

## 📊 Dataset & Annotation
* **Source:** 200+ public comments manually collected from highly active Reddit threads.
* **Annotation:** Hand-labeled by the repository owner based on strict decision rules.
* **Distribution:** The dataset was slightly imbalanced by nature of the platform, leaning heavily toward the `reaction` class, which actively influenced the model's learning behavior.

## ⚙️ Fine-Tuning Pipeline
* **Base Model:** `distilbert-base-uncased`
* **Compute:** Google Colab (T4 GPU)
* **Key Hyperparameters:**
  * **Epochs:** 3
  * **Batch Size:** 8
  * **Learning Rate:** 2e-5

---

## 📈 Evaluation Results

### Baseline vs. Fine-Tuned Performance
*(Data pulled from `evaluation_results.json`)*

Before fine-tuning, a zero-shot baseline was attempted using Groq's `llama-3.3-70b-versatile`. However, the LLM failed to output strictly formatted labels, resulting in unparseable data.

* **Baseline Accuracy:** N/A (`NaN` - Parsing Error)
* **Fine-Tuned Model Accuracy:** **45.45%** * **Test Set Size:** 11 examples

### Fine-Tuned Per-Class Metrics
| Label | Precision | Recall | F1-Score | Support |
| :--- | :--- | :--- | :--- | :--- |
| **analysis** (Class 0) | 0.33 | 0.33 | 0.33 | 3 |
| **hot_take** (Class 1) | 0.00 | 0.00 | 0.00 | 3 |
| **reaction** (Class 2) | 0.50 | 0.80 | 0.62 | 5 |

### Confusion Matrix
![Confusion Matrix Output](confusion_matrix.png)

---

## 🧠 Reflection & Failure Analysis

While an accuracy of 45.45% seems low, it provides a perfect window into how a model processes a subjective, slightly imbalanced dataset. 

**What I intended the model to learn:**
I intended the model to evaluate the *logical substance* of a post—learning the semantic difference between citing evidence (`analysis`) and making a groundless assertion (`hot_take`).

**What the model actually learned:**
Instead of learning logic, the model learned **surface-level linguistic triggers and dataset frequencies.**
1. **The Majority Class Trap:** Because `reaction` was the dominant class in the training data, the model heavily over-predicted it (guessing `reaction` for 8 out of 11 test cases). It learned that predicting `reaction` was a statistically safe bet to lower its training loss.
2. **Length & Tone Bias:** The model associated short text, conversational phrasing ("Tbf", "I think"), and profanity exclusively with the `reaction` class. When it encountered a highly analytical post that happened to use colorful language to describe a team owner, it misclassified it as a `reaction`.
3. **The Invisible `hot_take`:** The model predicted `hot_take` exactly 0 times. Because `hot_take` comments share the formal sentence length of `analysis` but the subjectivity of a `reaction`, the model failed to isolate its unique boundaries in just 3 epochs. It consistently folded `hot_take` examples into the other two categories based purely on text length.

**Next Steps:** To improve this model for deployment, the dataset requires strict class balancing (e.g., exactly 66 examples per label) and potentially a larger context window or more epochs to allow the model to move past surface-level triggers and learn structural syntax.
