---
title: "Authorship Classification via Stylometric Features"
excerpt: "A classification pipeline that uses TF-IDF, cosine similarity, and gradient boosting to determine whether two text spans share an author."
collection: portfolio
---

This project involved building a sentiment- and style-based classification system to determine whether two spans of English text were written by the same author. The task—part of a digital stylometry challenge—was a binary classification problem requiring the model to assign either label `0` (different authors) or `1` (same author).

## Task Summary

The objective of the task was to identify whether two pieces of English text were written by the same person. This type of task is known as **authorship identification** and has applications in forensic linguistics, authorship attribution in literary analysis, and online behavior tracking. The challenge was structured as a binary classification problem, where the input consisted of two spans of text separated by a delimiter (`[SNIPPET]`), and the model had to predict whether the spans were authored by the same individual (label `1`) or not (label `0`).

A key complexity of this task was the absence of author labels—i.e., no metadata about authorship beyond the span pairs themselves. Therefore, the model needed to rely entirely on linguistic features and patterns in the text to infer authorship, which requires both semantic and stylistic analysis.

## Exploratory Data Analysis

The training data contained 899 samples. Each sample had a single text field that consisted of two concatenated spans of English text, separated by the `[SNIPPET]` token. These spans varied in length and structure but generally ranged from 2 to 4 sentences each. The data was highly imbalanced:

- **Label 0 (Different authors):** 826 samples
- **Label 1 (Same author):** 73 samples

This imbalance presented a challenge for training, particularly for recall on the minority class. In order to address this, I later incorporated class-balancing techniques during model development.

An initial inspection of text pairs revealed substantial stylistic variation between authors. However, many pairs that were semantically similar—due to common topics, genres, or even quotation—did not share authorship. As a result, naive semantic similarity was not sufficient for classification, and deeper stylistic analysis was necessary.

One illustrative example of a text pair from the dataset was:
> Span 1: "It mounted now above that line of metal stages in the distance. And as Grantline gestured, I saw from Venus the same sword-like beam streaming off almost to cross our own."
> 
> Span 2: "Grantline and I, with a mutual thought, ran around the balcony and gazed to where Mars had set. A narrow radiance was streaming up among the stars off there."

While these spans clearly relate thematically, the model had to decide whether these similarities indicated a shared author or not.

## Preprocessing and Feature Engineering

The preprocessing pipeline was designed to clean and normalize the input text. I used the following steps:

1. **Lowercasing**: Convert all text to lowercase to reduce vocabulary size.
2. **Punctuation removal**: Strip punctuation using Python’s `string.punctuation`.
3. **Tokenization**: Tokenize each span using NLTK’s `TreebankWordTokenizer`.
4. **Stopword removal**: Remove common English stopwords.
5. **Lemmatization**: Lemmatize all tokens using NLTK’s `WordNetLemmatizer`.

The text was then split into two columns—`SPAN1` and `SPAN2`—based on the `[SNIPPET]` delimiter. Each span was preprocessed independently, and I used both the individual spans and their combination to build features.

## Feature Extraction

To convert text into numerical representations, I used the **TF-IDF vectorizer** from `scikit-learn`, configured with the following parameters:

- `ngram_range=(1, 2)`: Included unigrams and bigrams to capture short phrases
- `max_features=5000`: Capped feature size to reduce sparsity and overfitting
- `min_df=2`: Ignored terms that appeared only once
- `max_df=0.8`: Removed overly common terms that might dilute specificity

TF-IDF vectors were generated separately for each span and their combination. To enhance performance, I also calculated the **cosine similarity** between the two spans’ TF-IDF vectors. This additional feature captured overall semantic similarity and was particularly helpful for distinguishing paraphrased content.

The final feature matrix was constructed by horizontally stacking the TF-IDF vectors of the combined span and the cosine similarity values using `scipy.sparse.hstack`.

## Modeling Approach

Initially, I used **Logistic Regression** as a baseline model due to its speed and interpretability. To address the class imbalance, I used the `class_weight='balanced'` option, which penalizes misclassifications from the minority class more heavily.

Once the baseline was established, I implemented a **Gradient Boosting Classifier (GBC)** to improve performance. GBC is an ensemble method that builds sequential decision trees, each one trained to correct the errors of its predecessors. This allowed the model to capture nonlinear relationships and interactions between features.

The full pipeline included:
- Preprocessing and tokenization
- Feature engineering with TF-IDF and cosine similarity
- Classifier training with GBC
- Evaluation on test data

## Evaluation and Results

Model performance was evaluated through a blind test submission. The final Gradient Boosting model achieved a **leaderboard score of 0.57992**, which represented a **7.09% improvement over the baseline** Logistic Regression model.

This result demonstrated that combining stylistic features with semantic similarity, and employing a non-linear model, led to more accurate authorship classification. Although the score reflects a moderately difficult task, it also highlighted the importance of feature engineering and model selection in text classification.

## Error Analysis

Misclassifications were primarily caused by:
- **Semantic similarity without shared authorship**: Some pairs were topically or stylistically alike, which misled the cosine similarity metric.
- **Vocabulary mismatch**: Rare or domain-specific words increased feature sparsity.
- **Class imbalance**: The underrepresentation of the positive class affected recall.

Example errors included cases where span 1 used vivid, poetic language and span 2 was more direct or narrative in tone, despite thematic overlap. In such cases, the classifier often defaulted to predicting different authors.

Future improvements could include:
- **Using contextual embeddings** such as BERT or RoBERTa for richer semantic features
- **Augmenting data** through paraphrasing or synthetic text generation
- **Applying sampling techniques** like SMOTE to balance classes
- **Exploring stylometric markers** such as sentence length, part-of-speech tags, and punctuation frequency

## Learning Outcomes

This project allowed me to demonstrate the following learning outcomes of the MS-HLT program:

1. **Programming proficiency**: I wrote clean, modular code in Python using libraries like `nltk`, `scikit-learn`, and `pandas`.
2. **Applied NLP knowledge**: I applied tokenization, lemmatization, TF-IDF vectorization, and cosine similarity to real-world text data.
3. **Tool integration**: I combined multiple tools and packages into a coherent ML pipeline.
4. **Analytical thinking**: I conducted exploratory data analysis, tuned model parameters, and performed error analysis to guide improvements.
5. **Reproducibility and professionalism**: The project was organized for easy sharing and reproducibility via GitHub.

[View the code on GitHub](#) *https://github.com/uazhlt-ms-program/ling-539-spring-2024-class-competition-code-leliarhodes72.git*
