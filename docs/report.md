# Summarisation of News Articles 

## 1. Introduction 

News articles are long and daunting. Text summarisation condenses a large amount of text into shorter versions while retaining the overall information of the original document. It provides a productive solution of meaningful access to information that would otherwise be avoided or not read to its entirety.    

This report investigates current text summarisation methods and details the development of a custom abstractive summarisation system tailored for news articles. Using the pre-trained BART (Bidirectional and Auto-Regressive Transformers) model facebook/bart-large-cnn, the fine-tuned model aims to generate coherent, concise summaries through a carefully designed pipeline encompassing data preprocessing and model training, with comprehensive evaluation. 

## 2. Background Research 

Abstractive and extractive summarisation are the main approaches used to summarise text.

### 2.1 Extractive Text Summarisation Methods 

Extractive text summarisation selects important sentences from source documents based on statistical (including TF-IDF, sentence position, length, and numerical data presence) and linguistic features (capture meaning through cue phrases, part-of-speech information, sentence embeddings, and semantic clustering) which inform ranking algorithms determining sentence relevance. 

Extractive methods have supervised and unsupervised approaches (Moratanch and Chitrakala, 2017). Unsupervised techniques use algorithmic heuristics and graph-based models like LexRank, which measures sentence similarity and centrality. Other methods combine fuzzy logic with features like term weighting and named entities. Supervised techniques treat summarisation as sentence-level classification, using models like RankNet and Conditional Random Fields with labelled examples to identify structurally and semantically significant content. 

### 2.2 Abstractive Text Summarisation Methods 

Abstractive approaches generate paraphrased summaries from large texts using language generation, compression techniques, and deep learning to construct new, human-like summaries. Initially, there were two main methods: structured and semantic (Andhale and Bewoor, 2016). The structured approach relied on linguistic patterns and predefined frameworks to condense content systematically, whereas semantic-based approaches focused on understanding textual meaning to generate entirely new sentences that capture essential information.  

More recently, abstractive summarisation commonly employs Sequence-to-Sequence (Seq2Seq) models which use encoder-decoder structures. Other contemporary approaches include pre-trained Large Language Models (LLMs), Reinforcement Learning (RL) approaches where models learn through trial and feedback, Hierarchical approaches which focus on how information is structured and arranged, and Multi-modal Summarisation which combines different types of information such as text and images (Shakil, Farooq and Kalita, 2024).  

Abstractive summarisation moves beyond extraction, creating summaries that interpret and reformulate information rather than simply selecting existing sentences. 

## 3. Design and Implementation 

### 3.1 Requirements 

The developed model is required to generate accurate, human-like short summaries from news articles using a dataset comprising of 511 article and human-written summary pairs. Multiple evaluation metrics, rating different qualities of text summarisation, will be used to assess the model's performance, aiming to achieve scores closer to the ideal value of 1.0, to indicate high-quality summary generations that closely aligns with human-produced summaries 

### 3.2 Model Selection and Justification 

The BART model (facebook/bart-large-cnn) was selected as it was pre-trained on English and fine-tuned specifically on news articles (CNN/Daily Mail), aligning directly with this project's requirements. As an abstractive summarisation method, it produces fluent summaries, unlike extractive methods which often result in disjointed or unreadable output.  

Larger models such as T5 and PEGASUS were considered. Pegasus generates fluent, high-quality summaries and can handle longer token sequences; however, its attention mechanisms make it more resource-intensive and slower at inference. While T5 is also computationally demanding, it is highly versatile and has outperformed other models for news summarisation (Singh et al., 2021) and variants like LongT5 support longer sequences. But the base models were trained on shorter contexts (512 tokens), making them less suitable for long articles without additional adaptation.  

BART model’s domain-specific fine-tuning on news content made it the most effective choice for this project, and the limitations from my physical device and small dataset meant BART offers strong summarisation performance without excessive computational overhead. 

### 3.3 Model 
![diagram of model](images/model_diagram.png)

#### 3.3.1 Data Pre-processing 

The preprocessing pipeline transforms raw articles into data while preserving linguistic integrity. It removes only disruptive elements (URLs, excess whitespace, and non-essential characters), while retaining punctuation critical for meaning and essential for summarisation models to train with. TF-IDF-based sentence ranking system identifies the most semantically important sentences to ensure articles are within the BART model’s maximum input limit of 1024 tokens. Pre-processed data is converted to Dataset format and split into 80% training, 10% validation, and 10% test sets using a fixed random seed for reproducibility. The BART tokenizer handles encoding with parameters aligned to model constraints: 1024 tokens for inputs and 660 (max length from dataset summaries) for summary targets. 

#### 3.3.2 Hyperparameter Tuning 

For the trainer set up, the Learning Rate and total number of training epochs is utilised for deep learning, to give the model more exposure to the limited data and improve its ability to learn meaningful patterns without excessive risk of overfitting. 


When generating the summaries, a length_penalty of 1.0 was used to allow the model to generate longer, more detailed summaries without discouraging length, and specific parameters for output lengths were defined to match the reference dataset summaries, improving evaluation scores. Furthermore, beam search was employed to explore multiple candidate summaries, select the most coherent output, and ensure stable results. 

#### 3.3.3 Evaluation Metrics 

Multiple metrices were used to evaluate the model's performance because no single metric fully captures summarisation quality. ROUGE (Recall-Oriented Understudy for Gisting Evaluation) is a widely used evaluation method with a focus on recall, comparing the overlap of units such as n-grams, word sequence and word pairs between generated and reference summaries. Similarly, BLEU (Bilingual Evaluation Understudy), originally designed for machine translations, assesses precision of n-gram matches. However, both metrics measure lexical similarity hence struggle to recognise semantically equivalent paraphrases, which are common in abstractive summarisation. BERTScore uses contextual embeddings from pre-trained language models, to find semantic similarity (Zhang et al., 2020). Including BERTScore addresses evaluation to recognise paraphrased content which preserves meaning while altering surface-level text, giving an insightful appraisal on how well a summarisation model performs. 

## 4. Results 

### 4.1 Model Performance 

 

Figure 6. Screenshot of evaluation metric results from model built and trained on provided dataset (Author’s Own) 

This image shows the full evaluation metrics assessing the fine-tuned BART model's performance. Strong semantic understanding is demonstrated, with a high BERTScore F1 of 0.92. However, moderate ROUGE scores and BLEU scores (0.57) reveal lower lexical similarity between generated and reference summaries. 

### 4.2 Evaluation Metrics Analysis 

The model’s strength is its semantic correctness with a high BERTScore precision (0.92) even when paraphrasing. This indicates the training hyperparameters has avoided overfitting the small data set and TF-IDF-based sentence ranking in preprocessing has effectively preserved contextual relevance within token limits. 

However, the BLEU’s length ratio (0.88) and brevity penalty (0.88) show generated summaries averaged only 88% of reference lengths, suggesting generated texts are under the target due to fixed length constraints. 

To improve the model, I would revisit how length is defined. A dynamic length calibration could be implemented where min/max length is determined by a percentage of the article length based on dataset statistics, or the length penalty could be adjusted to encourage longer outputs. Expanding the dataset will train a more robust model allowing for an increase of model parameters without the risk of overfitting as with the current dataset. Additionally, using a hybrid evaluation that combines human judgment with automated metrics could offer insights that go beyond simple lexical-overlap measures, as metrics like ROUGE and BLEU often miss importance of actual human understanding. Finally, without the computational limitations of my personal device and dataset constraints, a much larger and proven model, T5, could be used to enhance summary quality and handle longer input texts more effectively. 

# 5. Conclusion 

Text summarisation can be achieved through numerous approaches, particularly as Natural Language Processing continues to develop rapidly. This solution performed satisfactorily, with the BERTScore demonstrating strong semantic understanding and human-like summary generation for lengthy news articles. While extractive methods may achieve high ROUGE and BLEU scores through text repetition, this investigation revealed their limitations in capturing semantic quality, as human language extends beyond lexical matching and requires deeper comprehension of meaning and context. Although this model shows promising results, substantial contextual and semantic nuances in human communication remain challenging for current NLP systems to fully capture. Future developments must continue bridging this gap between computational understanding and the complexity of human language. 

 

# 6. References 

Andhale, N. and Bewoor, L.A. (2016) 'An overview of text summarization techniques', in 2016 International Conference on Computing Communication Control and Automation (ICCUBEA), Pune, India, 1–2 December. IEEE, pp. 1–7. doi:10.1109/ICCUBEA.2016.7860024. 

Mazumdar, S. (2024) Exploring the extractive method of text summarization. Analytics Vidhya. Available at: https://www.analyticsvidhya.com/blog/2023/03/exploring-the-extractive-method-of-text-summarization/ (Accessed: 12 December 2025). 

Moratanch, N. and Chitrakala, S. (2017) 'A survey on extractive text summarization', in 2017 International Conference on Computer, Communication and Signal Processing (ICCCSP), Chennai, India, 10–11 January. IEEE, pp. 1–6. doi:10.1109/ICCCSP.2017.7944061. 

Shakil, H., Farooq, A. and Kalita, J. (2024) 'Abstractive text summarization: state of the art, challenges, and improvements', Neurocomputing, 603, 128255. 

Singh, P., Kashetty, P., Reddy, A.S.R.T., Teja, G.S. and Anusha, V. (2024) 'Automated news summarization using transformers', in 2024 Intelligent Systems and Machine Learning Conference (ISML), Hyderabad, India. IEEE, pp. 693–697. doi:10.1109/ISML60050.2024.10074399. 

Zhang, T., Kishore, V., Wu, F., Weinberger, K.Q. and Artzi, Y. (2020) 'BERTScore: evaluating text generation with BERT', in International Conference on Learning Representations (ICLR) 2020, Addis Ababa, Ethiopia. Available at: https://arxiv.org/abs/1904.09675 (Accessed: 5 December 2025). 

# 7. Use of Ai 

Artificial intelligence was used to check grammar and clarity of text I wrote myself. 
Also further explaining hyperparameters and their effects to help my comprehension. 
 