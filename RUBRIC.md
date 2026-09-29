# CAP 6606 Final Project Rubric

## Structured Entity Extraction with Supervised Fine-Tuning

**Total: 100 points**

This project evaluates whether students understand how to adapt an instruction-tuned causal language model to a structured prediction task with supervised fine-tuning (SFT). Grading emphasizes the learning objectives of the project rather than dataset plumbing, environment setup, or matching an instructor reference score exactly.

The expected experimental arc is:

```text
pretrained instruction-tuned model
        ↓
baseline evaluation
        ↓
construct supervised prompt/response examples
        ↓
mask prompt tokens from the causal-LM loss
        ↓
full supervised fine-tuning
        ↓
post-SFT evaluation
        ↓
error analysis
```

The primary evaluation metric is **micro-F1 over exact `(category, surface string)` entity mentions** on the public development set.

---

# 1. Prompt and Target Construction — 10 points

Students must correctly express the extraction task as an instruction-following conversation and serialize the gold response in the required six-field JSON format.

### Full credit — 10 points

- Uses the provided system/task specification correctly.
- Includes the source passage in the user message.
- Produces the required six target fields:
  - `person`
  - `location`
  - `group`
  - `creative_work`
  - `product`
  - `medical`
- Serializes each target value as a JSON array of exact surface strings.
- Uses empty arrays when a category is absent.
- Passes the provided prompt/target construction checks.

### Partial credit

- **7–9:** Minor formatting or serialization error that does not change the intended task substantially.
- **4–6:** Prompt or target construction is incomplete or inconsistent, but the intended task is recognizable.
- **1–3:** Major misunderstanding of the required conversation or target format.
- **0:** No functional prompt/target implementation.

---

# 2. Response-Only Causal-LM Supervision — 25 points

This is a central learning objective of the project. Students must demonstrate that they understand how supervised instruction tuning is implemented with a causal language-model objective.

### Full credit — 25 points

- Constructs a full token sequence containing both the prompt and the assistant response.
- Constructs a label sequence with the same length as the input sequence.
- Masks prompt/context tokens with `-100` so they do not contribute to the loss.
- Leaves assistant-response tokens supervised.
- Does not silently truncate the gold response.
- Passes the provided response-masking assertions/tests.
- Correctly explains, in the notebook, why prompt tokens are masked and what would change if they were included in the supervised objective.

### Partial credit

- **20–24:** Correct masking with a minor implementation or explanation issue.
- **13–19:** Mostly correct idea, but some prompt tokens or response tokens are supervised incorrectly.
- **6–12:** Training labels exist but do not implement response-only supervision reliably.
- **1–5:** Minimal attempt without a correct causal-LM supervision construction.
- **0:** No functional training labels.

---

# 3. Supervised Fine-Tuning Implementation — 30 points

Students must complete a functioning PyTorch SFT loop using the provided Colab-compatible numerical-stability scaffold.

### Full credit — 30 points

The implementation correctly performs the essential optimization sequence:

1. moves each batch to the GPU;
2. executes the forward pass under the provided mixed-precision context;
3. obtains the causal-LM loss;
4. accounts correctly for gradient accumulation;
5. performs backpropagation through the scaled loss;
6. unscales gradients before clipping when required by the provided scaffold;
7. clips gradients using the provided bound;
8. performs optimizer updates at the correct accumulation interval;
9. advances the learning-rate scheduler with optimizer updates;
10. clears gradients correctly between updates.

The submitted run must also:

- start from the specified pretrained checkpoint rather than a previously fine-tuned checkpoint;
- complete the prescribed one-epoch training run;
- maintain finite training loss;
- produce a saved fine-tuned checkpoint/artifact as directed by the instructor.

### Partial credit

- **24–29:** Training is correct overall with a minor implementation issue.
- **16–23:** Training completes and learns, but one or more optimization details are incorrect or missing.
- **8–15:** Partial training loop; substantial instructor repair would be needed for a valid experiment.
- **1–7:** Minimal implementation that does not produce meaningful SFT.
- **0:** No functioning training procedure.

---

# 4. Baseline and Post-SFT Evaluation — 15 points

Students must run the same deterministic evaluator before and after fine-tuning and interpret the comparison correctly.

### Full credit — 15 points

- Runs the frozen pretrained baseline on the assigned public-development set.
- Runs the fine-tuned model on the same public-development set.
- Reports baseline and post-SFT micro-F1 clearly.
- Uses the provided deterministic evaluator without modifying its matching rules.
- Uses deterministic generation settings provided by the notebook.
- Shows that the fine-tuned model produces structured outputs compatible with the required schema.
- Reports and discusses the change from baseline to post-SFT performance.

### Performance policy

Students are **not graded on reproducing an exact instructor reference score**. Normal hardware/software variation and small implementation differences may change the final metric.

For full experimental credit, the result should nevertheless be consistent with a functioning SFT pipeline: the post-SFT model should show a substantial improvement over the pretrained baseline and should reliably produce the required structured output format. A weak result is not automatically penalized if the implementation is correct and the student diagnoses the failure convincingly.

### Partial credit

- **12–14:** Correct evaluation with a minor reporting or interpretation issue.
- **8–11:** Baseline and post-SFT results are present, but the comparison is incomplete or evaluation procedure is partly incorrect.
- **4–7:** Only one valid evaluation is completed, or the provided evaluator is modified in a way that changes the task.
- **1–3:** Minimal or unreliable evaluation evidence.
- **0:** No usable evaluation.

---

# 5. Error Analysis and Interpretation — 15 points

Students must analyze approximately five representative post-SFT errors. The purpose is to reason about model behavior rather than simply display incorrect examples.

### Full credit — 15 points

For approximately five examples, the student:

- shows the source passage;
- compares gold and predicted structured outputs;
- identifies the error type where applicable, such as:
  - false negative;
  - false positive;
  - wrong coarse category;
  - entity-boundary error;
  - formatting/schema error;
  - another clearly explained failure mode;
- explains why the prediction is incorrect under the exact evaluation contract;
- identifies at least one recurring error pattern or limitation of the fine-tuned model;
- distinguishes formatting failures from semantic/extraction failures.

### Partial credit

- **12–14:** Five useful examples with generally correct analysis but limited synthesis.
- **8–11:** Examples are provided, but explanations are shallow or some classifications are incorrect.
- **4–7:** Fewer than the requested examples or primarily descriptive rather than analytical discussion.
- **1–3:** Minimal error inspection.
- **0:** No error analysis.

---

# 6. Reproducibility, Completeness, and Notebook Quality — 5 points

### Full credit — 5 points

- Notebook runs in order in the intended Google Colab environment.
- Required TODOs are completed.
- Provided assertions/checks pass.
- Results shown in the submitted notebook correspond to the submitted implementation.
- Required model/checkpoint artifact is saved as directed.
- Notebook is readable and contains enough output to verify the experiment without unnecessary debugging clutter.
- Student does not alter frozen course data or hidden-evaluation machinery.

### Partial credit

- **3–4:** Minor reproducibility, organization, or submission issue.
- **1–2:** Significant missing output or manual intervention is required to understand/reproduce the work.
- **0:** Submission is not runnable or does not correspond to the claimed experiment.

---

# Point Summary

| Component | Points |
| --- | ---: |
| Prompt and target construction | 10 |
| Response-only causal-LM supervision | 25 |
| Supervised fine-tuning implementation | 30 |
| Baseline and post-SFT evaluation | 15 |
| Error analysis and interpretation | 15 |
| Reproducibility, completeness, and notebook quality | 5 |
| **Total** | **100** |
