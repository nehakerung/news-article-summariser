# Further Notes and Reasoning
## 📁 1. Preparing data

### 1.1 Data cleaning

Text summarisation generally requires the original text to remain as is because it provides essential context and meaning. For example, removing punctuation and stop words would grammatically break the data, and nuances could be lost (for example, with "not"). Keeping data grammatically correct ensures the output reads similarly to natural language; it is important summarisations are coherent and read well. Hence, the text pre-processing function is kept minimal, removing only unwanted characters and formatting.

### 1.2 Reduce text length to fit models token input limit

The max_position_embedding for `facebook/bart-large-cnn` is `1024` tokens, but some data we use may exceed this limit; my dataset definitely does. If not dealt with, excess tokens would be truncated, preventing the model from accessing the full context of the news articles and resulting in an inaccurate summary.

My method to avoid harsh truncation is by ranking sentences by importance and keeping up to `1024` tokens from the most important ones. While this means that information is still lost, the context will be more meaningful and meet the max_position_embeddings limit - leading to better summaries than if this was not applied. The original sentence ordering is also preserved, as the order of sentences affects the meaning of text.

A tokenizer instance using the pre-trained (`facebook/bart-large-cnn`) model is loaded. This ensures that the tokenizer has the same vocabulary and tokenization rules as the summarization model that will be used.

The function `reduce_by_importance` takes input text and a default maximum token limit. `TfidfVectorizer` converts the sentences into `TF-IDF` vectors, calculating an importance score for each sentence based on word frequency and rarity. The function encodes sentences into tokens using the `BART` tokenizer and counts how many tokens each contains.
Finally, the sentences are ranked by their `TF-IDF` importance scores, and the most important sentences are selected until the token limit is reached. These selected sentences are then reordered to match their original position in the text before being returned.

### 1.3 Load dataset

This function loads the dataset by reading all article files and their matching summary files from the given directory. Each text is passed through the `pre_process_txt` function so the content is cleaned before further processing. Articles also undergo an additional length check, which trims overly long inputs to stay within the model’s token limits.
After processing, each article–summary pair and their additional information is stored as a dictionary entry. 

There is also print statements to highlight information regarding the dataset. This can help define our parameters or refine the method; for example, I found my dataset's maximum token length in articles were higher than the maximum input allowed by the model choice leading me to add a function to combat this problem.

A try except block is added to handle any errors that may occur when trying to access and read the files.

### 1.4 Organise cleaned data into distinct groups the model will use

From the data, I will need to split to get the training set, validation set and test set.
This will also convert our dictionary data to be Dataset compatible to use with HuggingFace transformers.

* The dataset was shuffled with a fixed random seed (42) to ensure reproducibility while preventing any ordering biases in the data. 
* Training set, `train_dataset`, will be used to teach the model on how to perform the text summarization task, this will be 80% of the dataset.
* Validation set, `val_dataset`, will be used in the training phase to tune the model, this is 10%.
* Finally, the test set, `test_dataset` will be used to test the trained model developed, this is also 10%.

The training set is the highest portion as it needs to be large enough to achieve meaningful results, maximising model performance. 

### 3.2 Evaluation Metrics

#### 3.2.1 ROUGE (Recall-Oriented Understudy for Gisting Evaluation) Score

ROUGE compares overlapping units such as n-grams, word sequences, and word pairs between the generated summary and the reference. It is widely used for summarization evaluation but its recall-focus, it may mean abstractive text summaries are rated lower because of their use of different words, despite it having the same context. As a result, high-quality paraphrased summaries can receive lower ROUGE scores despite capturing the correct content.