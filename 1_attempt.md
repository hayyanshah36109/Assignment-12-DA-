# Assignment 2 — The Dirty Ledger
## Initial audit plan

**File under review:** `teaching_prices_messy.csv`  
**Purpose:** Identify data-quality concerns before deciding whether any cleaning is justified.

### What I intend to check

The first pass will establish whether the file is structurally usable and whether its values are internally consistent. In particular, I will examine:

- the number of records, fields, column names, and stored data types;
- missing cells and values that cannot be interpreted as dates or prices;
- exact repeated rows and repeated date–item–city combinations;
- item labels that may differ only because of capitalization or extra spaces;
- whether prices are positive and whether unusually large or small values need verification;
- the number of observations for each item and city;
- consistency of the measurement unit and the dataset's source/disclaimer label.

### Rules for handling findings

| Finding | Initial response | Why this is the safer choice |
|---|---|---|
| Blank value | Record its location; leave it blank | A replacement price would be invented without supporting evidence |
| Price written with punctuation | Test conversion separately from the source field | Formatting should not be mistaken for an invalid observation |
| Exact repeated row | Display the matching records before any removal | A duplicate check should be visible and reviewable |
| Repeated date, item, and city | Flag the whole matching group | A repeated key may have an explanation that the file alone cannot establish |
| Similar item names | Compare normalized labels but retain original spelling | Capitalization alone does not prove that records should be merged |
| Date or price that will not parse | Count and inspect the failed values | Guessing a corrected value could change the evidence |
| Zero, negative, or unusually high price | Flag for investigation | Statistical or logical rules are screening tools, not proof of error |
| Unit or source label | Preserve unless reliable evidence supports a correction | These fields define how the observations should be interpreted |

### Working procedure

1. Read the CSV and retain an untouched raw dataframe.
2. Make a separate working copy for diagnostic fields.
3. Parse dates and prices in new columns; never overwrite the source columns.
4. Produce visible lists of missing values, duplicate groups, key collisions, and unusual prices.
5. Compare the original columns in the raw and working dataframes at the end.
6. Leave all records in place until a correction can be justified and documented.

**Checkpoint label:** `initial-audit-checkpoint`  
**Commit message planned:** `initial-audit-checkpoint`  
**Commit hash:** To be added if a commit is made.
