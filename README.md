# Materials_and_ML_Colab

# Normalized_compositions.ipynb

This notebook processes a JSON file containing alloy compositions and normalizes each composition to atomic percentages.

## 📄 Overview

The JSON file contains alloy entries with a `"composition"` field where each element has a numeric value. This value could be:

* `1` (implying equal weight or molar presence),
* `< 1` (indicating a fractional presence),
* Or a mix of both.

The notebook ensures all compositions sum up to 100% by handling two cases:

### 🔧 Normalization Logic

#### **Case A: All Elements Have Value 1**

* Distribute 100% equally across all elements.
* Example: `{ "Fe": 1, "Ni": 1, "Cr": 1 }` → `{ "Fe": 33.33, "Ni": 33.33, "Cr": 33.33 }`

#### **Case B: Some Elements < 1, Others = 1**

* Compute the deficit from values < 1.
* Distribute this deficit equally among elements with value = 1.
* Normalize the final values to sum up to 100.

### 🗂️ Input

* JSON file path: `/content/admin.materials_cleaned_2.json`
* Format:

```json
{
  "composition": {
    "Fe": 1,
    "Ni": 0.3,
    ...
  }
}
```

### 💾 Output

* Normalized compositions written back into the original `entry['composition']` dictionary.

# Utils.py

# Extracting Data from Image-based PDF Research Papers (Materials Science)

This notebook automates the process of extracting useful keywords and data (e.g., "stress", "strain") from PDF research papers in Materials Science, especially when the content is embedded as images (scanned PDFs).

## 🔍 Purpose

The goal is to:

* Extract text from image-based PDFs using OCR (Optical Character Recognition).
* Process the extracted text with Natural Language Processing (NLP).
* Identify and highlight domain-specific keywords related to **mechanical properties** and **manufacturing processes** (e.g., casting, forging, rolling).

## 📦 Libraries Used

* `PyPDF2`, `pdf2image`, `pytesseract` – for reading and converting PDFs
* `NLTK` – for tokenization, lemmatization, and keyword filtering
* `matplotlib`, `scipy` – (later steps) for potential graph interpretation
* `multiprocessing` – for handling parallel PDF extraction tasks

## 🛠️ Steps in the Pipeline

1. **PDF Preprocessing**:

   * Convert PDF pages into images using `pdf2image`.
   * Apply `pytesseract` OCR to extract raw text from each page image.

2. **Text Cleaning & Tokenization**:

   * Tokenize the OCR text using `nltk.word_tokenize`.
   * Remove stopwords, punctuation, and apply lemmatization.

3. **Keyword Extraction**:

   * Match and highlight domain-specific keywords like `stress`, `strain`, `forging`, `rolling`, etc.

## 📂 Expected Input

* One or more PDF files (image-based or scanned).
* Each file is processed page-by-page for text extraction and keyword spotting.

## 💡 Notes

* The notebook uses `nltk.download()` commands to ensure required NLP datasets are available.
* Keywords are defined in a Python `set` and can be extended for more precise filtering.


