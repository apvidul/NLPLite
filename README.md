# NLPLite

Lightweight NLP library for **fast AhoCorasick term extraction** and **enhanced NegEx engine for negation and uncertainty detection**.

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
dictionary = {"headache": "R51", "fever": "R50.9"}

extractor = flash_extractor(dictionary)
text = "The patient denies headache but reports fever."

print(extractor.run(text, negation_check=True))
```

**Output:**
```python
[('headache:N', 'R51:N', 18, 25), ('fever:Y', 'R50.9:Y', 40, 44)]
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
    ["headache:N", "R51:N", 18, 25],
    ["fever:Y", "R50.9:Y", 40, 44]
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
fever,R50.9
headache,R51
```

**Example TSV:**
```tsv
term	code
fever	R50.9
headache	R51
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
