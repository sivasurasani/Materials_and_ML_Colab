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


Function `calculate_min_max_slope`:

---

# 📈 Function: `calculate_min_max_slope`

This function analyzes experimental hardening rate data (e.g., from tensile testing) and calculates the **minimum and maximum slope values** from a **cubic spline fit** of the data. These slope bounds are later used to optimize material modeling parameters like **D₁**, a critical slope parameter in stress-strain curve equations.

---

## ✅ Purpose

To:

* Smooth stress-strain (or strain-hardening rate) data using cubic splines.
* Estimate valid slope (first derivative) ranges from positive-slope regions.
* Return a narrow range of slope values (`min_d1`, `max_d1`) for downstream model fitting.

---

## 📥 Parameters

| Parameter   | Type  | Description                                            |
| ----------- | ----- | ------------------------------------------------------ |
| `file_path` | `str` | Path to Excel file with strain and hardening rate data |
| `x_axis`    | `str` | Name of the strain column                              |
| `y2_axis`   | `str` | Name of the hardening rate column                      |

---

## 📤 Returns

* `min_d1` (`int`): Minimum positive slope value from early-stage hardening
* `max_d1` (`int`): Maximum positive slope value (capped at `min_d1 + 10000` for stability)

---

## 🧠 How It Works

1. **Load and Clean Data**

   * Reads specified columns from the Excel file.
   * Removes any rows with missing or non-numeric values.

2. **Detect and Adjust Flat Regions**

   * Optionally modifies values that show unusually flat (constant) segments in the data.

3. **Spline Fitting**

   * Fits a **cubic spline** to the data.
   * Calculates the **first derivative** (slope) at each data point.

4. **Slope Analysis**

   * Filters out negative slopes.
   * Focuses on the first few positive slope values (up to 5).
   * Computes the **difference between consecutive slopes** to find early behavior changes.

5. **Annotated Plotting**

   * Displays original and spline-fitted data.
   * Annotates selected points on the curve with index labels.

6. **Return Range**

   * Returns minimum and maximum slope values from printed slope list.
   * If the difference between max and min is too large (> 10,000), the max is clamped.

---

## 📊 Visual Output

* Spline curve vs. raw data
* Indexed slope points shown directly on the graph
* Helps in manually validating slope segments

---

## 🔍 Use Case

This function is typically used in the context of:

* **Materials deformation modeling**
* Estimating valid ranges for **D₁**, the initial slope parameter in constitutive equations
* Feeding the output into another routine like `find_best_parameters(...)` for optimization

---

## 🛠 Dependencies

* `pandas`, `numpy` – for data handling
* `matplotlib` – for graph plotting
* `scipy.interpolate.CubicSpline` – for curve fitting

---


Function `find_best_parameters` that explains its purpose, usage, and internal logic:

---

# 🔍 Function: `find_best_parameters`

This function performs a **grid search optimization** to find the best-fit parameters (`B₀`, `D₁`, `D₂`, `Ei`) for a custom mathematical model used in **material deformation analysis**, particularly for modeling hardening rate curves in stress-strain data.

---

## 🎯 Purpose

To:

* Evaluate combinations of model parameters (`b0`, `d1`, `d2`, `ei`) over defined ranges.
* Minimize the **Euclidean distance** (error) between the predicted and actual hardening rate values.
* Return the parameter set with the **lowest fitting error**.

---

## 📥 Parameters

| Parameter   | Type    | Description                                                          |
| ----------- | ------- | -------------------------------------------------------------------- |
| `file_path` | `str`   | Path to Excel file with experimental data                            |
| `x_axis`    | `str`   | Name of the column representing true strain (εₚ)                     |
| `y2_axis`   | `str`   | Name of the column representing target output (e.g., hardening rate) |
| `from_b0`   | `int`   | Lower bound for parameter B₀ (initial offset stress)                 |
| `to_b0`     | `int`   | Upper bound for B₀                                                   |
| `min_d1`    | `int`   | Minimum slope value (D₁)                                             |
| `max_d1`    | `int`   | Maximum slope value (D₁)                                             |
| `ee`        | `float` | Global end strain (used in the model equation)                       |

---

## 📤 Returns

* `best_b0` – Best fit value for B₀
* `best_d1` – Best fit value for D₁
* `best_d2` – Best fit value for D₂
* `best_ei` – Best fit value for inflection strain Ei
* `min_mse` – Minimum Euclidean distance (error) between prediction and actual data

---

## 🧮 Model Equation

The function evaluates the following model for a given set of parameters:

$$
y = B₀ - D₁ \left( \left| \frac{εₚ - Eᵢ}{Eᵢ + Eₑ} \right| \cdot \text{arctanh}\left(\frac{εₚ - Eᵢ}{Eᵢ + Eₑ}\right) - D₂ \cdot \left( \frac{εₚ - Eᵢ}{Eᵢ + Eₑ} \right) \right)
$$

Where:

* `εₚ` is the true strain,
* `Eᵢ` is the inflection strain,
* `Eₑ` is the end strain (constant `ee`),
* `B₀`, `D₁`, `D₂` are the parameters to optimize.

---

## ⚙️ Optimization Strategy

* Grid search using `itertools.product()` to test all combinations.
* Evaluates Euclidean distance (L2 norm) between predicted and actual data.
* Selects the combination with the lowest error.

---

## 📦 Parameter Search Ranges

* `b0`: from `from_b0` to `to_b0` (step = 10)
* `d1`: from `min_d1` to `max_d1` (step = 5)
* `d2`: from `0.2` to `0.5` (step = 0.05)
* `ei`: from `0.1` to `0.285` (step = 0.05)

---

## ✅ Example Output

```
Best Parameters:
B0: 1150
D1: 7200
D2: 0.35
Ei: 0.15
```

---

## 📊 Use Case

This function is typically used in:

* **Material model calibration**
* **Work hardening curve fitting**

---


# Loop through each folder in the data directory CELL. This is the implementation for storing all the json information.

---

# 🗂️ Folder-Based Batch Processor for Material Science Data

This script automates the processing of folders containing experimental data and research papers. It reads each folder, extracts relevant files, analyzes mechanical behavior and textual content, and compiles results into a structured JSON format.

---

## 🎯 Purpose

To automate the extraction of:

* **Stress-strain curve modeling parameters** (`b₀`, `d₁`, `d₂`, `eᵢ`, `eₑ`)
* **Textual keywords** and **chemical compositions** from PDFs
* **Hollomon model constants** (`n`, `k`)
* **Source metadata** (via `.txt` file)

And store them in a consolidated JSON file: `data.json`

---

## 📁 Folder Structure (Per Sample)

Each folder in the `data_directory` must contain:

| File         | Required | Purpose                                                        |
| ------------ | -------- | -------------------------------------------------------------- |
| `*_cal.xlsx` | ✅        | Excel sheet with three columns: strain, stress, hardening rate |
| `.pdf`       | ✅        | Research paper (for keyword/composition extraction)            |
| `.txt`       | ❌        | Optional: contains the source link or DOI                      |

---

## 🧠 What This Script Does

1. **Iterates over folders** inside a specified directory.
2. **Identifies and verifies** required files.
3. **Reads Excel sheet** to determine column names dynamically:

   * First column = strain
   * Second = stress
   * Third = hardening rate
4. **Calls** `analyze_pdf_data()` to:

   * Compute modeling parameters from stress-strain curves
   * Extract keywords using NLP
   * Extract chemical compositions using regex/OCR
   * Calculate `n` and `k` values (Hollomon constants)
5. **Builds a metadata dictionary** and appends it to `data.json` via `update_json_data()`.

---

## 📤 Output (JSON Format)

Each folder results in an entry like:

```json
{
  "title": "Folder123",
  "b0": 1145,
  "d1": 7000,
  "d2": 0.35,
  "ei": 0.18,
  "ee": 0.2,
  "lemma": ["strain", "rolling", "powder"],
  "composition": {"Fe": 1, "Ni": 1, "Al": 0.3},
  "link": "https://doi.org/example-link",
  "n": 0.29,
  "k": 950.5
}
```

---

## 📦 Dependencies

Make sure your environment has the following:

* Python libraries: `pandas`, `numpy`, `matplotlib`, `nltk`, `PyPDF2`, `pytesseract`, `pdf2image`
* System tools: `poppler-utils`, `tesseract-ocr`

---

## ✅ Use Case

This script is ideal for:

* Batch-processing a dataset of mechanical testing results + papers
* Creating a knowledge base of material properties
* Feeding AI/ML models for materials property prediction

---
