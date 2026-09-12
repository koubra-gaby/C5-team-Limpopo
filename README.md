# Agriculture & Climate SLM Challenge

## Project Overview

This project was developed for the **Agriculture & Climate SLM Challenge**. Our goal is to build a lightweight language model capable of answering agriculture and climate-related questions using the provided knowledge documents and supervised question-answer examples.

The proposed approach combines **knowledge-grounded prompting** with **Supervised Fine-Tuning (SFT)** of a pretrained Small Language Model (SLM) using **LoRA (Low-Rank Adaptation)**. The final system generates answers for the unseen questions in the test set and produces a `submission.csv` file.

---

## Dataset

The challenge provides three main data sources:

* **`documents.csv`**: a collection of agriculture and climate-related knowledge documents.
* **`train_qa.csv`**: supervised training examples containing questions, reference answers, and metadata such as crop, agro-ecological zone, topic, and document identifiers.
* **`test_questions.csv`**: unseen questions for which the system must generate answers.

For each training example, the associated document was identified using its `document_id`. Relevant metadata, including **crop**, **agro zone**, and **topic**, was preserved to provide additional context to the model.

The training data was transformed into instruction-style examples following this structure:

```text
Crop: ...
Zone: ...
Topic: ...
Question: ...
Context: ...
Answer: reference answer
```

The knowledge document provides grounding information, while the question-answer pairs provide supervised examples of the desired answering behavior.

---

## Training Pipeline

### 1. Data Collection and Preparation

All data used in this project was provided as part of the challenge. No external agricultural dataset was required.

The preprocessing pipeline:

1. Loads `documents.csv`, `train_qa.csv`, and `test_questions.csv`.
2. Matches each training question with its corresponding knowledge document using `document_id`.
3. Extracts a relevant portion of the document as contextual information.
4. Combines metadata, question, context, and reference answer into an instruction-following training example.
5. Formats the resulting examples for Supervised Fine-Tuning.

### 2. Model Design

We use a pretrained **Small Language Model (SLM)** rather than training a model from scratch.

The model is adapted using **LoRA**, a parameter-efficient fine-tuning technique. Instead of updating all pretrained model parameters, LoRA trains a small number of additional parameters while keeping most of the original model frozen.

This approach was selected because it:

* reduces GPU memory requirements;
* enables efficient fine-tuning on Kaggle GPUs;
* is suitable for small supervised datasets;
* reduces the computational cost compared with full fine-tuning.

The model receives structured information including the crop, agro zone, topic, question, and agricultural context, and learns to generate the corresponding answer.

### 3. Hyperparameter Selection

The training configuration is designed for efficient experimentation on limited GPU resources. The main parameters explored include:

* learning rate;
* number of training epochs;
* LoRA rank (`r`);
* LoRA scaling factor (`alpha`);
* batch size;
* maximum sequence length.

Because the supervised dataset is relatively small, the main objective is to avoid overfitting while allowing the model to learn the required answer format and domain-specific knowledge.

---

## Evaluation

The system is evaluated by generating answers for unseen questions and comparing the generated outputs with the expected answers according to the challenge evaluation procedure.

During development, we also verify the pipeline through:

* inspection of formatted training examples;
* validation of document-to-question matching;
* checking generated answers for relevance and factual consistency;
* comparison with a simple retrieval baseline based on TF-IDF and cosine similarity.

The baseline retrieves the most similar training question and reuses its associated answer. The fine-tuned SLM is expected to provide more flexible, context-aware answers.

---

## Reproduction

To reproduce the project, run the notebooks or scripts in the following order:

### Step 1 — Load the data

Load:

```text
documents.csv
train_qa.csv
test_questions.csv
```

### Step 2 — Prepare the training data

Run the preprocessing code to:

* match training questions with their documents;
* construct contextual prompts;
* create the SFT dataset.

### Step 3 — Load the pretrained SLM

Attach the pretrained model through **Kaggle Models** and configure:

```text
MODEL_PATH
```

The model should be loaded from the local Kaggle input directory.

### Step 4 — Fine-tune with LoRA

Run the LoRA/SFT training pipeline to adapt the pretrained SLM to the agriculture and climate question-answering task.

### Step 5 — Generate answers

Use the fine-tuned model to generate answers for all questions in:

```text
test_questions.csv
```

### Step 6 — Create the submission

Save the generated predictions in the required format:

```text
submission.csv
```

The output should contain the required question identifier and generated answer columns.

---

## Appendix

### Contributors / Team Members

* **[Name — Role]**
* **[Name — Role]**
* **[Name — Role]**

### Mentors

* **[Mentor Name]**
* **[Mentor Name]**

---

## References

* Hu et al. (2021), *LoRA: Low-Rank Adaptation of Large Language Models*.
* Hugging Face Transformers Documentation.
* Hugging Face PEFT Documentation.
* Kaggle Competition and Dataset Documentation.
