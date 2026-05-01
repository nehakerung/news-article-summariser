# News articles summariser

A custom abstractive summarisation model fine-tuned to generate concise summaries of news articles. The project uses the pre-trained BART architecture [facebook/bart-large-cnn](https://huggingface.co/facebook/bart-large-cnn), trained on a curated [dataset](training_dataset) of 511 news articles paired with human-written summaries. Performance is reported with ROUGE, BLEU, and BERTScore to capture both lexical overlap and semantic similarity.

A written report with background, design, and results lives in the [docs](docs/1.%20Intro.md) folder.

## Contents

* [Instructions to run](#instructions-to-run)
  * [Prerequisites](#prerequisites)
  * [About the dataset](#about-the-dataset)
* [About the model](#about-the-model)
  * [Model choice](#model-choice)
  * [Method](#method)
    * [Data preprocessing](#1-data-preprocessing)
    * [Training](#2-model-training)
    * [Evaluation](#3-evaluation)
  * [Evaluation results](#evaluation-results)
* [License](#license)
* [Links](#links)

## Instructions to run

Workflows are documented for a local machine or a cloud notebook (for example Google Colab).

1. Clone or download this repository.
2. Open `main.ipynb` in Jupyter, VS Code, or Colab and run the cells in order.

### Prerequisites

* **Python** 3.10 or newer recommended.
* **Dependencies:** `pip install -r requirements.txt`
* **Hardware:** Training is much faster with a GPU; CPU runs are possible but slow. The first run downloads the base model from Hugging Face (internet required).

### About the dataset

The training split used in the notebook is included under `training_dataset/`.

To use your own data, use the same layout: matching numeric IDs in `Articles/` and `Summary/`.

```text
dataset/
├── Articles/
│   ├── 001.txt
│   ├── 002.txt
│   └── ...
└── Summary/
    ├── 001.txt
    ├── 002.txt
    └── ...
```

**Colab:** zip the dataset, upload it, then unzip, for example:

```python
!unzip /content/dataset.zip -d /content/
```

## About the model

### Model choice

Abstractive models tend to produce more fluent summaries than purely extractive ones. `facebook/bart-large-cnn` is pre-trained on English and fine-tuned on CNN/Daily Mail–style news summarisation, which matches this task. It supports inputs up to 1024 tokens and is a practical balance of quality and compute for a modest dataset.

### Method

Pipeline overview:

![diagram of model](images/model_diagram.png)

#### 1. Data preprocessing

* **Text cleaning:** normalise the text while keeping punctuation and structure that summarisation models rely on.
* **Length management:** articles over the 1024-token limit are shortened with TF–IDF sentence ranking so the most informative sentences stay in order.
* **Splits:** 80% train, 10% validation, 10% test (see `main.ipynb` for the random seed and details).

#### 2. Model training

* The pretrained `facebook/bart-large-cnn` checkpoint is fine-tuned on the training and validation splits.
* Hyperparameters and training arguments are documented in `main.ipynb`.

#### 3. Evaluation

* The fine-tuned model generates summaries for the held-out test set.
* Metrics include ROUGE (ROUGE-1 / ROUGE-2 / ROUGE-L / ROUGE-Lsum), BLEU (and related length statistics), and BERTScore (precision / recall / F1). See `main.ipynb` for interpretation.

## Evaluation results

Example metric output from the notebook:

![Results sample](images/sample_results.png)

## License

This project is released under the [MIT License](LICENSE).

## Links

* [facebook/bart-large-cnn](https://huggingface.co/facebook/bart-large-cnn)
* [BERTScore (Zhang et al., 2020)](https://arxiv.org/abs/1904.09675)
