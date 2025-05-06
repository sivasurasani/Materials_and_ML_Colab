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

# Main file

Function `calculate_b0_range`:

---

# 📐 Function: `calculate_b0_range`

This function estimates a suitable range for the material parameter **b₀** used in stress-strain modeling. It analyzes the hardening rate data from a mechanical test (like a tensile test), fits a polynomial curve, evaluates its derivative, and extracts candidate **b₀** values based on turning points in the data.

---

## ✅ Purpose

To:

* Fit a polynomial to experimental hardening rate data.
* Calculate the derivative and its roots (i.e., critical points).
* Select the two closest root values and compute a bounded range around their corresponding **y** values (i.e., estimated b₀ range).

---

## 📥 Parameters

| Parameter   | Type  | Description                                                             |
| ----------- | ----- | ----------------------------------------------------------------------- |
| `file_path` | `str` | Path to an Excel file containing strain and hardening rate columns      |
| `x_axis`    | `str` | Column name for the x-axis (typically true strain)                      |
| `y2_axis`   | `str` | Column name for the y-axis (typically hardening rate)                   |
| `k_value`   | `int` | Degree of the polynomial used for curve fitting and derivative analysis |

---

## 📤 Returns

* `from_b0` (`int`) – lower bound of the estimated b₀ range
* `to_b0` (`int`) – upper bound of the estimated b₀ range

---

## ⚙️ How It Works

1. **Load and Clean Data**
   Reads the Excel file and drops trailing rows that might be metadata or noise.

2. **Smooth and Interpolate**

   * Uses cubic spline interpolation for smoothing.
   * Fits a `k_value`-degree polynomial to the data.

3. **Detect Flat Regions (Optional Adjustment)**
   Identifies sections where the hardening rate is almost constant and adjusts them for better fitting (if the curve is monotonically decreasing).

4. **Print Curve Equations**
   Dynamically constructs and prints:

   * The polynomial equation.
   * Its derivative.

5. **Find Derivative Roots**

   * Calculates where the slope of the curve becomes zero or changes direction.
   * These roots are critical points (turning points) in the hardening curve.

6. **Evaluate Candidate b₀**

   * Evaluates the original polynomial at each root.
   * Selects the two closest `y` values.
   * Calculates a bounding range: `from_b0` and `to_b0`.

7. **Visualize Curve Fitting**

   * Plots the original, interpolated, and polynomial curves.
   * Helps visualize turning points and fitting quality.

---

## 📈 Example Output

```
Cubic Polynomial Coefficients: [...]
Polynomial Equation: a x^3 + b x^2 + c x + d
Derivative Equation: 3a x^2 + 2b x + c
Roots (Zeros): [x1, x2, ...]
Original Y Values: [y1, y2, ...]
Minimum b0: 1130.5
Maximum b0: 1145.2
From_b0: 1000
To_b0: 1300
```

---

## 📊 Dependencies

* `pandas`, `numpy` – data manipulation
* `matplotlib` – plotting
* `scipy.interpolate` – spline fitting

---
