# NLPLite

Lightweight NLP library for **fast concept extraction** and **enhanced negation and uncertainty detection**.

## Features

- **Fast Aho Corasick** term extraction (C-accelerated via `pyahocorasick`)
- **NegEx Engine** negation & uncertainty detection
- **CLI** for quick runs

---

## Install

```bash
pip install nlplite
```

---

## Quick Start

### Basic Example

```python
from nlplite import flash_extractor

# Term to code dictionary
dictionary = {"headache": "C0018681", "fever": "C0015967"}

extractor = flash_extractor(dictionary)
text = "The patient denies headache but reports fever."

print(extractor.run(text, negation_check=True))
```

**Output:**
```python
[('headache:N', 'C0018681:N', 18, 25), ('fever:Y', 'C0015967:Y', 40, 44)]
```

### Quick CLI 
```bash
printf "term,code\nfever,C0015967\nheadache,C0018681\n" > terms.csv
printf "The patient denies headache but reports fever.\n" > note.txt
flash-extract terms.csv note.txt --sep , --negation
```

### Structured JSONL-Style Record

```python
rec = extractor.extract_note("1234", text, negation_check=True)
print(rec)
```

**Output:**
```json
{
  "note_id": "1234",
  "text": "The patient denies headache but reports fever.",
  "extractions": [
    ["headache:N", "C0018681:N", 18, 25],
    ["fever:Y", "C0015967:Y", 40, 44]
  ]
}
```

---

## Dictionary Formats

### A) Two-Column File (term, code)

Use when you want codes in the output (CUI/SNOMED/ICD etc)

**Example CSV:**
```csv
term,code
fever,C0015967
headache,C0018681
```

**Example TSV:**
```tsv
term	code
fever	C0015967
headache	C0018681
```

**Loader:**
```python
# CSV
extractor = flash_extractor("terms.csv", sep=",", header=True)

# TSV
extractor = flash_extractor("terms.tsv", sep="\t", header=True)
```

### B) Term List (No Codes)

If you **omit** `sep`, the file is treated as a **term list** (one term per line).

**Example:**
```
fever
headache
shortness of breath
```

**Loader:**
```python
extractor = flash_extractor("terms.txt")  # sep omitted → term list mode
```

---

## Notes

- **Term suffix:** `:N` = negated, `:Y` = affirmed, `:U` = uncertain
- **Uncertainty:** Handled via NegEx-style phrase matching
- **Case-insensitive** by default
- Returns tuples: `(term, code, start_pos, end_pos)`
