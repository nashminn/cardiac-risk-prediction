# Talking Points — Interpretable Cardiac Risk Prediction
**Course:** CSE710 – Advanced Artificial Intelligence
**Duration:** ~8 minutes total | 3 speakers
**Format:** Each person's section ends with a smooth handover line (marked ↪)

---

## Slide Assignments

| Person | Slides | Approx. Time |
|--------|--------|--------------|
| Person 1 | Title, Outline, Problem Statement, Dataset, Preprocessing | ~2.5 min |
| Person 2 | EDA, MLP Architecture, Training, Evaluation Metrics, Evaluation Visuals | ~2.5 min |
| Person 3 | SHAP Overview, SHAP Global, SHAP Local, Key Findings, References, Thank You | ~3 min |

---

## PERSON 1

### Slide 1 — Title (~20 sec)
Good [morning/afternoon]. Our project is titled *Interpretable Cardiac Risk Prediction* — a system that not only detects cardiac disease risk but explains its decisions. My name is [Person 1], and I'll be walking you through our motivation and the data we worked with.

---

### Slide 2 — Outline (~20 sec)
Here's a quick roadmap of our presentation. We'll start with the problem, move through our deep learning pipeline, and then focus on what makes this project distinct: the explainability layer using SHAP. The second half of the talk is really about building trust in AI — not just accuracy.

---

### Slide 3 — Problem Statement (1/12) (~60 sec)
Cardiovascular disease kills more people each year than any other cause globally — over 17 million deaths according to the WHO. The clinical data to detect it early — blood pressure, cholesterol, ECG results — is already routinely collected. The question is whether we can extract a reliable risk signal from it.

Now, deep learning can absolutely do that. But the problem in a medical setting is trust. If a model tells a doctor "this patient is at risk," the doctor needs to know *why*. Without an explanation, the recommendation is practically useless — or worse, it gets ignored entirely.

Regulatory pressure makes this even more urgent. Frameworks like the FDA's AI/ML guidance and the EU AI Act are pushing for mandatory explainability in high-risk AI systems, which certainly includes clinical decision support.

Our response to this is to pair a neural network with SHAP — giving us both the predictive power of deep learning and the transparency of game-theoretic feature attribution.

---

### Slide 4 — Dataset Overview (2/12) (~30 sec)
We used the UCI Heart Disease dataset, specifically the Cleveland subset, which is a benchmark in clinical ML. After removing one duplicate record, we had 302 patients described by 13 clinical features — age, sex, chest pain type, cholesterol, resting blood pressure, maximum heart rate, and more. The class balance is nearly even: 54% positive for cardiac risk, 46% negative, which is helpful for training.

The seven features highlighted in the table are the most clinically interpretable — the full 13 are used in the model.

---

### Slide 5 — Data Preprocessing (3/12) (~30 sec)
The data came in quite clean. No missing values, one duplicate removed. We split features from the target, then divided the data 80/20 — 241 samples for training, 61 for testing — with stratification to maintain class balance in both splits. Finally, we standardized all features using `StandardScaler`, which is fitted on training data only to prevent data leakage into the test set.

↪ *"With the data prepared, I'll now hand over to [Person 2], who'll walk you through our exploratory analysis and the model we built."*

---

---

## PERSON 2

### Slide 6 — Exploratory Data Analysis (4/12) (~45 sec)
Thank you, [Person 1]. Before building the model, we explored the data to understand which features carry the most signal.

The correlation heatmap on the left shows that several features correlate meaningfully with the target: *Oldpeak* (ST depression) and *ExerciseAngina* show strong positive correlation with cardiac risk, while *MaxHR* — maximum heart rate — is negatively correlated. Interestingly, patients with cardiac risk tend to have a lower maximum heart rate, which aligns with clinical literature.

The class distribution plot confirms there's no severe class imbalance, which means we don't need specialized sampling techniques.

---

### Slide 7 — MLP Architecture (5/12) (~45 sec)
For the model, we chose a Multi-Layer Perceptron. The architecture is relatively compact given the dataset size: 13 inputs, three hidden layers with 64, 32, and 16 neurons using ReLU activation, and a single sigmoid output node giving us a probability of cardiac risk.

We added Dropout at 30% after the first two hidden layers. This is critical here — with only 302 samples, the model would memorize the training data without regularization. Dropout forces the network to learn distributed, redundant representations by randomly disabling neurons during each training step.

The optimizer is Adam with binary cross-entropy loss, which is the natural choice for binary classification. Total parameter count is just 3,521 — a deliberately lean model.

---

### Slide 8 — Training & Optimization (6/12) (~30 sec)
We trained for up to 200 epochs but used early stopping with a patience of 15, monitoring validation loss. As you can see in the training curves, the model converged around epoch 33. Validation loss tracked training loss reasonably well — no dramatic overfitting. Early stopping then restored the best weights from approximately epoch 14, where validation loss was minimized.

The 15% validation split carved out about 36 samples from the training set for monitoring generalization during training.

---

### Slide 9 — Evaluation Metrics (7/12) (~40 sec)
Now for results. On the 61-sample test set, the model achieved:

- **Accuracy: 80.33%** — 49 out of 61 predictions correct.
- **Precision: 76.92%** — of all patients the model flagged as high risk, 77% actually were.
- **Recall: 90.91%** — of all actual cardiac risk patients, the model caught 91%.
- **F1-Score: 83.33%** — the harmonic mean, balancing both concerns.
- **ROC-AUC: 86.90%** — strong discrimination ability across all classification thresholds.

The clinically most important metric here is recall. A false negative — missing a patient who is actually at risk — is far more costly than a false alarm. Our model missed only 3 out of 33 true positive cases.

---

### Slide 10 — Evaluation Visuals (8/12) (~20 sec)
The confusion matrix on the left gives the concrete breakdown: 30 true positives, 19 true negatives, 9 false positives, and 3 false negatives. The ROC curve on the right shows the model well above the diagonal across all thresholds, with AUC of 0.87 confirming solid discriminative ability.

↪ *"The model performs well — but performance numbers alone don't explain what it's actually learning. I'll hand over to [Person 3], who'll show you the explainability layer."*

---

---

## PERSON 3

### Slide 11 — SHAP Overview (9/12) (~55 sec)
Thank you, [Person 2]. A model that says "this patient is at 90% cardiac risk" is only clinically useful if we can say *why*. That's where SHAP comes in — SHapley Additive exPlanations.

SHAP is grounded in cooperative game theory. The original Shapley value (1953) asked: in a team effort, how do we fairly credit each player's contribution to the outcome? Lundberg and Lee (NeurIPS 2017) applied this idea to machine learning: each feature in a prediction gets a "SHAP value" representing its fair share of the difference between the model's output and a baseline expectation.

Three mathematical guarantees make SHAP uniquely trustworthy:
- **Local accuracy** — the SHAP values always sum to the exact model prediction. Nothing is approximated away.
- **Missingness** — absent features contribute nothing.
- **Consistency** — if a feature becomes more influential, its SHAP value cannot decrease.

We used **KernelExplainer** because our MLP is a black box — it has no tree structure or gradient that SHAP can exploit directly. KernelExplainer works by sampling subsets of features, observing the model's response, and fitting a local linear model. It's slower but model-agnostic.

---

### Slide 12 — SHAP Global Importance (10/12) (~40 sec)
Here we see global explanations — averaged across all 50 test patients analyzed.

The bar chart on the left ranks features by mean absolute SHAP value. **Thalassemia** and **ChestPainType** are the dominant predictors, followed by **MaxHR**, **Oldpeak**, and **MajorVessels**.

The beeswarm plot on the right adds directionality. Each dot is one patient. Red dots represent high feature values, blue represent low. For **MaxHR**, we see that *low* values (blue) cluster on the right — meaning low max heart rate *increases* predicted risk. Conversely, *high* MaxHR (red) pushes toward No Risk. This is medically coherent: a higher heart rate capacity is a sign of cardiovascular fitness.

Thalassemia and ChestPainType show similar directional consistency, which cross-validates the model's learned associations against clinical knowledge.

---

### Slide 13 — SHAP Local Explanation (11/12) (~45 sec)
Global explanations tell us which features matter on average. Local explanations tell us what mattered for a *specific patient* — which is what a clinician actually needs at the bedside.

This waterfall plot is for Patient 1 in our test set. The model predicted **No Risk** with a probability of 0.094 — and the patient was indeed No Risk, so the prediction was correct.

The baseline expected value is approximately 0.54 — that's the model's average output across the training data. Each horizontal bar shows how much one feature shifted the prediction up or down from that baseline:

- **MaxHR** pushes the prediction strongly downward — this patient had a high max heart rate, which the model associates with low risk.
- **Thalassemia** also pulls the prediction downward.
- **ChestPainType** offers a small upward push toward risk, but it's outweighed by the protective factors.

The final output is 0.094 — well below the 0.5 decision threshold. This kind of per-patient waterfall makes it possible for a doctor to audit, question, or override the model's reasoning on a case-by-case basis.

---

### Slide 14 — Key Findings (12/12) (~35 sec)
To summarize what we found:

The MLP achieves strong performance for a 302-sample dataset — particularly the 90.91% recall, which is the metric that matters most for screening. SHAP reveals that Thalassemia, Chest Pain Type, and MaxHR are the most predictive features globally, with clinically sensible directions of influence.

The main limitations are the small dataset and the Cleveland-only source, which may not generalize well across populations. KernelExplainer is also computationally expensive at scale.

For future work, the most impactful extensions would be a larger multi-centre dataset, faster SHAP variants like GradientSHAP, and packaging the pipeline into a real-time clinical decision-support system with integrated explainability.

---

### Slide 15 — References (~5 sec)
The key references for this work are listed here — spanning the original Cleveland dataset, the SHAP framework, and the TensorFlow/Keras ecosystem. Full citations are in our report.

---

### Slide 16 — Thank You (~5 sec)
Thank you for your time. We're happy to take any questions.

---

---

## Timing Guidance

| Section | Target | Buffer |
|---------|--------|--------|
| Person 1 (5 slides) | 2 min 20 sec | +10 sec |
| Person 2 (5 slides) | 2 min 40 sec | +10 sec |
| Person 3 (6 slides) | 2 min 40 sec | +10 sec |
| **Total** | **~8 min** | |

**Tips:**
- The preprocessing slide (P1, slide 5) and references slide (P3, slide 15) can be compressed or skimmed if running over time.
- The SHAP local explanation slide (P3, slide 13) is the richest — give it the full 45 seconds.
- The handover lines are natural pauses — use them to pass the clicker and let the audience reset.

---

## Handover Lines (verbatim suggestions)

**Person 1 → Person 2:**
> "With the data prepared, I'll hand over to [Person 2], who'll take you through our exploratory analysis and the model architecture."

**Person 2 → Person 3:**
> "The numbers look solid — but performance alone doesn't tell the full story. I'll pass it to [Person 3], who'll show you what the model is actually learning through SHAP explanations."

## Slide link
https://docs.google.com/presentation/d/11LbDQuOZDE9Claf0JCnGPhdLdHHMBZ2IR1GxQMdY1WE/edit?usp=sharing