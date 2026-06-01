# SentiArab-LEV

SentiArab-LEV is an Arabic sentiment analysis project that compares classical machine learning, Arabic transformer fine-tuning, and GPT-based prompting on a three-class Arabic reviews dataset.

The project classifies reviews as:

- `Positive`
- `Negative`
- `Mixed`

It includes a complete notebook workflow for Arabic preprocessing, linguistic feature extraction, model training, evaluation, visualizations, a final report, and a presentation.

## Project Overview

Arabic sentiment analysis is challenging because Arabic text often contains rich morphology, dialect variation, inconsistent spelling, emojis, code-switching, and informal writing. This project builds a sentiment analysis pipeline that handles those issues through Arabic-specific preprocessing and then compares several modeling approaches.

The main goals are to:

- Clean and normalize noisy Arabic review text.
- Extract Arabic NLP features such as stems, lemmas, POS tags, and named entities.
- Train classical machine learning baselines using TF-IDF plus linguistic features.
- Fine-tune a CAMeL-BERT sentiment model on Arabic review text.
- Compare the trained models with a GPT few-shot sentiment classifier.

## Dataset

The dataset is stored at:

```text
dataset/ar_reviews_100k.tsv
```

It contains 99,999 Arabic review samples with two columns:

| Column | Description |
| --- | --- |
| `label` | Sentiment class: `Positive`, `Negative`, or `Mixed` |
| `text` | Raw Arabic review text |

The classes are perfectly balanced:

| Label | Count |
| --- | ---: |
| `Positive` | 33,333 |
| `Mixed` | 33,333 |
| `Negative` | 33,333 |

The report and presentation refer to this as a 100K review dataset.

## Pipeline

The main workflow is implemented in:

```text
notebooks/SentiArab_LEV_Project-2.ipynb
```

The notebook follows these stages:

1. Data loading
2. Arabic text preprocessing
3. Stemming and lemmatization
4. POS tagging and sentiment-oriented POS features
5. Named Entity Recognition and entity flags
6. TF-IDF feature extraction
7. Classical ML model training
8. CAMeL-BERT fine-tuning
9. GPT few-shot comparison
10. Evaluation and visualization

### Arabic Preprocessing

The preprocessing pipeline:

- Removes tashkeel and normalizes Arabic letter variants.
- Normalizes Alef, Ya, Hamza variants, Teh Marbuta, and related characters.
- Removes emojis, punctuation, numbers, and English characters.
- Tokenizes text with NLTK.
- Removes Arabic stopwords.
- Produces a cleaned text field used by downstream models.

### Linguistic Features

The notebook extracts additional Arabic NLP features:

- ISRI stemming
- CAMeL Tools lemmatization
- POS tags
- Adjective and adverb counts
- Named entity lists
- Binary flags for person, location, and organization mentions

For classical ML models, the feature matrix combines:

- TF-IDF unigrams and bigrams from lemmatized text
- `adj_count`
- `adv_count`
- `has_person`
- `has_location`
- `has_organization`

## Models

The project evaluates five models:

| Model | Approach |
| --- | --- |
| Naive Bayes | Classical ML baseline |
| Logistic Regression | Linear TF-IDF classifier |
| Linear SVM | Sparse high-dimensional classifier |
| CAMeL-BERT | Arabic transformer fine-tuned on a 50K subset |
| GPT-5.5 | Few-shot prompted classifier on a 150-sample subset |

The CAMeL-BERT model used in the notebook is:

```text
CAMeL-Lab/bert-base-arabic-camelbert-da-sentiment
```

## Results

The notebook reports the following final model comparison:

| Model | Accuracy | Macro F1 | Evaluation Size |
| --- | ---: | ---: | ---: |
| CAMeL-BERT | 0.6991 | 0.6973 | 10,000 |
| GPT-5.5 | 0.5867 | 0.5930 | 150 |
| Logistic Regression | 0.5467 | 0.5472 | 20,000 |
| Linear SVM | 0.5421 | 0.5420 | 20,000 |
| Naive Bayes | 0.5311 | 0.5312 | 20,000 |

CAMeL-BERT achieved the strongest overall performance. GPT-5.5 ranked above the classical ML baselines, but it was evaluated on only 150 samples due to API cost, so its result is not directly comparable to the full test-set evaluations.

Across models, the `Mixed` class is the hardest class to classify because many reviews contain both positive and negative sentiment cues.

## Repository Structure

```text

|-- dataset/
|   |-- ar_reviews_100k.tsv
|   |-- ar_reviews_ml_ready.tsv
|-- figures/
|   |-- allModels-comparison.png
|   |-- camel-bert-cm.png
|   |-- classicalML.png
|   |-- gpt-cm.png
|   |-- log-reg-cm.png
|   |-- naive-bayes-cm.png
|   |-- sentiment-distribution.png
|   |-- svm-cm.png
|-- notebooks/
|   |-- SentiArab_LEV_Project-2.ipynb
|-- presentation/
|   |-- SentiArab_LEV_Presentation.pptx
|-- report/
|   |-- SentiArab_LEV_Report_Final.docx
|-- README.md
|-- requirements.txt
```

## Setup

Use Python 3.10, 3.11, or 3.12. CAMeL Tools currently requires Python `<3.13`, so Python 3.13 is not recommended for this project.

Create and activate a virtual environment:

```bash
python3.12 -m venv .venv
source .venv/bin/activate
```

Install dependencies:

```bash
pip install --upgrade pip
pip install -r requirements.txt
```

Download the CAMeL Tools data packages used by the notebook:

```bash
camel_data -i morphology-db-msa-r13
camel_data -i pos-msa
camel_data -i ner-named-entity-recognition
camel_data -i disambig-mle-calima-msa-r13
camel_data -i ner-arabert
```

The notebook also downloads NLTK resources such as `punkt`, `punkt_tab`, and `stopwords`.

## Running the Notebook

Start Jupyter:

```bash
jupyter notebook notebooks/SentiArab_LEV_Project-2.ipynb
```

The notebook was originally written for Google Colab, so if you run it locally:

- Skip or adjust the Google Drive mount cells.
- Update the dataset path from `data/ar_reviews_100k.tsv` to `dataset/ar_reviews_100k.tsv`.
- Create a local `results/` directory before saving processed datasets or the fine-tuned model.
- Run the sections in order, because later stages depend on columns created earlier.

To run the GPT comparison section, set an OpenAI API key before launching the notebook:

```bash
export OPENAI_API_KEY="your_api_key_here"
```

## Generated Outputs

The repository includes final visualization artifacts in `figures/`:

- Sentiment distribution chart
- Classical ML comparison chart
- Confusion matrices for Naive Bayes, Logistic Regression, SVM, CAMeL-BERT, and GPT
- Full model comparison chart

The final written outputs are:

- `report/SentiArab_LEV_Report_Final.docx`
- `presentation/SentiArab_LEV_Presentation.pptx`

The notebook also saves intermediate processed datasets and the fine-tuned CAMeL-BERT model under `results/` when run end to end.

## Notes and Limitations

- The dataset is balanced, but it does not include dialect labels.
- GPT-5.5 was evaluated on a small 150-sample subset, so it should be treated as a limited comparison.
- The `Mixed` sentiment class remains difficult because it often contains genuinely conflicting sentiment signals.
- Sarcasm, dialect ambiguity, and short reviews are major sources of model error.
- Some notebook paths are Colab-specific and should be adjusted for local execution.

## License

No license file is currently included in this repository.
