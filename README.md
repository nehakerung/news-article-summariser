# News Articles Summariser

> [!CAUTION]
> WORK IN PROGRESS

This project creates concise and accurate summaries of news articles.
It explores transformer based models BART and DistilBART to generate accurate summaries. It includes full preprocessing, dataset handling, model fine-tuning, and evaluation of models.

## Contents

## Instructions to run code
All instructions and code paths depend on whether you're running locally or in a cloud notebook environment such as Google Colab.

1. Local Notebook / IDE

    * Make sure Python and pip are installed
    * Install required libraries: `pip install -r requirements.txt`
    * Open and follow the notebook, main.ipynb, sequentially

2. Google Colab

    * Open and follow the notebook, main.ipynb, sequentially

### About Dataset
The original dataset is included on the repositry.

If you would like to use your own dataset, a compatible folder layout would look like the following; where the number of articles coresponds with the same number summary.
```
dataset
├── Articles
│   └── 001.txt
|   ├── 002.txt
|   ...
|   └── 511.txt
└── Summary
    └── 001.txt
    ├── 002.txt
    ...
    └── 511.txt
```
Please bear in mind, in colab you will have to take several extra steps:
* Save the dataset folder as a zip file
* Upload to the current colab workspace as a zip file
* Unzip via following

```
!unzip /content/dataset.zip
```
## Model choice
Abstract summarisation is used. Specifically a BART model trained on news articles.

### Links

These are websites I used to throughout this project:
* [facebook/bart-large-cnn](https://huggingface.co/facebook/bart-large-cnn)
*
*