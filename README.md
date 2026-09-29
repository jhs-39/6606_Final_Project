# CAP 6606 Final Project — Structured Entity Extraction with Supervised Fine-Tuning

In this project, you will adapt an instruction-tuned causal language model to a structured entity-extraction task using supervised fine-tuning (SFT).

You will fine-tune **`HuggingFaceTB/SmolLM2-360M-Instruct`** to convert short English passages into a fixed six-field JSON representation derived from **MultiCoNER v2**.

The project is designed for **Google Colab with a GPU runtime**. The course data are already prepared. You do **not** need to download MultiCoNER, parse BIO/CoNLL annotations, create splits, or implement preprocessing.

## Repository contents

```text
6606_Final_Project/
├── README.md
├── RUBRIC.md
├── student_project.ipynb
└── data/
    ├── train.jsonl
    ├── dev_public.jsonl
    └── schema.json
```

## Task

For every input passage, your model must produce exactly these six fields:

```json
{
  "person": [],
  "location": [],
  "group": [],
  "creative_work": [],
  "product": [],
  "medical": []
}
```

Each value is an array of **exact surface strings copied from the input passage**, in order of appearance. Use an empty array when a category is absent. Exact entity boundaries matter.

## What you will implement

Work through `student_project.ipynb` in order. The notebook provides the environment setup, data loading, deterministic evaluator, mixed-precision scaffold, generation helpers, and checkpoint saving.

Your required work is to:

1. complete prompt and target formatting;
2. implement response-only causal-LM supervision by masking prompt tokens from the loss;
3. complete the core supervised fine-tuning optimization steps;
4. run the pretrained baseline and post-SFT evaluation;
5. answer the checkpoint questions;
6. analyze approximately five representative post-SFT errors;
7. save the fine-tuned checkpoint and results artifact.

The primary task metric is **coarse entity micro-F1** over exact `(category, surface string)` matches.

## Data

- `data/train.jsonl`: 6,000 training examples.
- `data/dev_public.jsonl`: 1,000 public-development examples.
- `data/schema.json`: fixed task schema and label metadata.

Do not modify the frozen course data.

The data are derived from **MultiCoNER v2**. Source dataset: https://multiconer.github.io/dataset. MultiCoNER v2 is distributed under **CC BY 4.0**.

## Environment

Use a Google Colab GPU runtime. The reference workflow was tested on an NVIDIA Tesla T4.

Open the notebook, select **Runtime → Change runtime type → GPU**, and run cells in order.

No GitHub token is required; the notebook clones this public repository directly.

## Submission checklist

Before submitting, verify that:

- [ ] all TODO code sections are complete;
- [ ] all checkpoint questions are answered;
- [ ] the pretrained baseline was evaluated before training;
- [ ] the model was trained for the prescribed one epoch;
- [ ] baseline and post-SFT micro-F1 are shown;
- [ ] approximately five post-SFT errors are analyzed;
- [ ] the fine-tuned checkpoint and `student_results.json` are saved as directed;
- [ ] the notebook runs in order in the intended Colab environment.

See **`RUBRIC.md`** for grading details.
