# Read Me — Option 1 (Coding):

**Author:** Khushi Desai  
**Date:** 2025-09-23  
**Language:** Python (3.10+)  
**Files submitted:**  
1) `hajin_code.py` (code)  
2) `hajin_coding.ipynb` (jupyter notebook which shows code chunks and output side by side)
2) `well_to_producing_entity_mapping.parquet` (output)  
3) `README.md` (this memo)

---

## Objective
Produce a final dataframe using three provided parquet files:

- `API_Well_Data.parquet` — columns: `Unformatted_API_UWI_12`, `StateProvince`  
- `API_Cross_Reference.parquet` — columns: `ProducingEntityID`, `Unformatted_API_UWI_12`  
- `API_Producing_Entity.parquet` — columns: `ProducingEntityID`, `API_10_UWI`, `StateProvince`

**Target output schema (unique pairs):**
- `Unformatted_API_UWI_12`
- `ProducingEntityID`
- `StateProvince`

---

## What the code does -

1. **Load & Inspect**
   - Reads all three parquet files with pandas.
   - Prints schemas and shapes for quick sanity checks.

2. **Harmonizing data structures**
   - Converts `ProducingEntityID` and `Unformatted_API_UWI_12` to **string** in `API_Cross_Reference` to ensure join compatibility.

3. **Attempt 1 — Direct 12-digit Match**
   - `API_Well_Data` ⟷ `API_Cross_Reference` on `Unformatted_API_UWI_12` (inner join).  
   - **Result in this run:** `Direct matches found: 0`.

4. **Attempt 2 — 10-digit Match via API_10_UWI**
   - Creates `API_10_UWI` in `API_Well_Data` from the first 10 chars of `Unformatted_API_UWI_12`.  
   - Joins `API_Well_Data` ⟷ `API_Producing_Entity` on `API_10_UWI` (inner).  
   - Selects final columns and renames `StateProvince_x` to `StateProvince` (source of truth = well table).

5. **Save Output**
   - Writes `well_to_producing_entity_mapping.parquet` (no index).

---

## Results (from the sample run)
- `API_Well_Data` shape: **(42,771, 2)**
- `API_Cross_Reference` shape: **(4,103,634, 2)**  
  (noticed as int types on read; converted to string before use)
- `API_Producing_Entity` shape: **(44,392, 3)**

**Direct 12-digit matches:** `0`  
**10-digit `API_10_UWI` matches:** `46,266`  

**Final output shape:** **(46,266, 3)**  
Columns: `Unformatted_API_UWI_12`, `ProducingEntityID`, `StateProvince`  

**Requested report:**  
> “Wells matched using identifiers other than `Unformatted_API_UWI_12`” - **46,266**

---

## Reproducibility — How to Run
**Requirements**
```bash
pip install pandas numpy 
# advent-of-code-2025
