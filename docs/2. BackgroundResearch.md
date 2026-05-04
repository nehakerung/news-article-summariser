## 2. Background Research 

Abstractive and extractive summarisation are the main approaches used to summarise text.

### 2.1 Extractive Text Summarisation Methods 

Extractive text summarisation selects important sentences from source documents based on statistical (including TF-IDF, sentence position, length, and numerical data presence) and linguistic features (capture meaning through cue phrases, part-of-speech information, sentence embeddings, and semantic clustering) which inform ranking algorithms determining sentence relevance. 

Extractive methods have supervised and unsupervised approaches (Moratanch and Chitrakala, 2017). Unsupervised techniques use algorithmic heuristics and graph-based models like LexRank, which measures sentence similarity and centrality. Other methods combine fuzzy logic with features like term weighting and named entities. Supervised techniques treat summarisation as sentence-level classification, using models like RankNet and Conditional Random Fields with labelled examples to identify structurally and semantically significant content. 

### 2.2 Abstractive Text Summarisation Methods 

Abstractive approaches generate paraphrased summaries from large texts using language generation, compression techniques, and deep learning to construct new, human-like summaries. Initially, there were two main methods: structured and semantic (Andhale and Bewoor, 2016). The structured approach relied on linguistic patterns and predefined frameworks to condense content systematically, whereas semantic-based approaches focused on understanding textual meaning to generate entirely new sentences that capture essential information.  

More recently, abstractive summarisation commonly employs Sequence-to-Sequence (Seq2Seq) models which use encoder-decoder structures. Other contemporary approaches include pre-trained Large Language Models (LLMs), Reinforcement Learning (RL) approaches where models learn through trial and feedback, Hierarchical approaches which focus on how information is structured and arranged, and Multi-modal Summarisation which combines different types of information such as text and images (Shakil, Farooq and Kalita, 2024).  

Abstractive summarisation moves beyond extraction, creating summaries that interpret and reformulate information rather than simply selecting existing sentences. 
 