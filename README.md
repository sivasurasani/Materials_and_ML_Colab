# Materials_and_ML_Colab

# Normalized_compositions.ipynb

Here is a `README.md` based on your notebook **Normalized\_compositions.ipynb**:

---

# Normalize Alloy Compositions

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
