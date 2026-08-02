# Bank Regulatory Data Dictionary

**The most detailed open catalogue of U.S. bank regulatory data — every major information collection and its subschedules, the MDRM code system, the FFIEC NIC institutional-structure data, the identifier crosswalk, cross-form mappings, and reconciliation formulas.**

**Version 10.0** | Updated: 2026-06-11 | **FR Y-9C + Call Report line items** detailed against official field specifications · **UBPR derivation formulas** parsed and empirically validated · **official edit history** across 30 taxonomy cycles · **100% MDRM/UBPR token validity** (11,126/11,126), now **CI-enforced** · **empirically validated against 208 million + 1.9 billion rows of real FR Y-9C and Call Report filings**

> This repository is a **catalogue / mapping reference** — it documents *what the datasets are, how they are structured, and how they relate to each other*. For a data-*access* package (download and query the actual filings), see the companion project **[FreeNIC](https://github.com/andenick/FreeNIC)**.

> **New in v10.0 — Call Report line items, UBPR derivations, edit history, and CI-enforced validity.**
> v10.0 ships **seven Call Report per-schedule line-item CSVs** (RC balance sheet, RI income, RC-B
> securities, RC-C loans, RC-E deposits, RC-N past due, RC-R capital) so the dictionary now details
> line items for **both** report families — each row triple-attested against official 2025-12 grids,
> the CDR XBRL taxonomy presentation linkbase, and the bulk warehouse. It adds the **UBPR**: the
> official taxonomy v181 + User's Guide were acquired, **4,207 derivation formulas parsed** (zero
> silent drops), and **31 headline ratios empirically validated at 99.77–100%** against real
> published UBPR values (ROA, ROE, NIM, efficiency, charge-off, past-due, capital, liquidity). It
> ships the **official edit history** — **15,622 distinct edit labels across 30 taxonomy cycles
> (2001–2026)**, showing the rulebook roughly quintupled. A new **conditional/compound testing
> engine** made 448 previously-documented registry rows testable and drove every row to an explained
> verdict. Token validity is now **CI-enforced** (`scripts/ci_audit.py` fails the build on any invalid
> token). The [Relationship Registry](csv/RELATIONSHIP_REGISTRY.csv) grew to **7,539 relationships**.
> See **[docs/EMPIRICAL_VALIDATION.md](docs/EMPIRICAL_VALIDATION.md)**, **[docs/UBPR_GUIDE.md](docs/UBPR_GUIDE.md)**,
> and **[docs/EDIT_HISTORY.md](docs/EDIT_HISTORY.md)**.

---

## Why This Exists

U.S. bank regulators run dozens of overlapping data collections — FR Y-9C, the Call Report (FFIEC 031/041/051), FFIEC 101/102/002/009, the FR Y-7/Y-10/Y-11/Y-12/Y-14/Y-15 family, FR 2052a, Pillar 3, plus FDIC (SDI/SOD/failures), NCUA 5300, OCC historical returns, and the NIC/NPW structure files — together using **tens of thousands of MDRM variable codes across hundreds of schedules**. Analysts spend hours mapping between forms, deciphering code prefixes, tracing corporate hierarchies, reconciling identifiers, and verifying that balance-sheet components actually add up. This repository puts all of that reference material in one place — as CSV tables for programmatic use, JSON for AI agents, and Markdown guides for human readers.

**What v6.0 adds over the original FR Y-9C/trading focus:** a master [collections catalogue](docs/COLLECTIONS_CATALOG.md) of **42 collections** with a [schedule catalogue](csv/SCHEDULES_CATALOG.csv) of every subschedule; the full [FFIEC NIC institutional-structure layer](docs/NIC_STRUCTURE_GUIDE.md) (entities, relationships, transformations, 40+ code lists); an [identifier crosswalk](docs/IDENTIFIERS.md) (RSSD / FDIC Cert / OCC / NCUA / LEI / ABA / EIN / CIK); the complete [Call Report](docs/CALL_REPORT_GUIDE.md), [foreign/structure](docs/FOREIGN_AND_STRUCTURE_GUIDE.md), and [FDIC/NCUA/OCC/UBPR](docs/FDIC_NCUA_OCC_UBPR_GUIDE.md) guides; and an [MDRM meta-dictionary](docs/MDRM_GUIDE.md) + namespace catalogue.

---

## Get the Repo

```bash
git clone https://github.com/andenick/bank-data-dictionary.git
cd bank-data-dictionary
```

No installation required — all data is in CSV, JSON, and Markdown files ready to use. Start with [`docs/NAVIGATION.md`](docs/NAVIGATION.md) for navigation or [`docs/COLLECTIONS_CATALOG.md`](docs/COLLECTIONS_CATALOG.md) for the full data universe.

---

## What Adds Up to What

```
                    TOTAL ASSETS (BHCT2170)
                           |
    +----------+-----------+-----------+-----------+----------+
    |          |           |           |           |          |
  Cash      Securities    Loans     Trading    Other Assets
 (0081)     (8641*)      (B528)     (3545)      (2160)
              |            |           |           |
           HC-B         HC-C        HC-D        HC-F
              |            |           |
         AFS + HTM    Net Loans   Assets + Deriv
        1773+JJ34+    B529 =       Pos FV 3543
          JA22       B528-3123         |
                                    HC-L
                               (FV by Asset Class)

                    TOTAL LIABILITIES (BHCK2948)
                           |
    +----------+-----------+-----------+-----------+
    |          |           |           |           |
 Deposits   Fed Funds   Trading    Other      Borrowings
  (2200*)   Purchased    Liab      Liab       (3190)
              (B993)    (3548)    (2750)
                          |         |
                        HC-D      HC-G
                          |
                    Shorts + Deriv
                     Neg FV 3547

                    TOTAL EQUITY (BHCT3210)
                           |
         +--------+--------+--------+--------+
         |        |        |        |        |
      Preferred Common  Surplus  Retained  AOCI
       (3283)   (3230)  (3240)   (3247)   (B530)

                REGULATORY CAPITAL
                    (HC-R)
         +----------+----------+
         |                     |
    TIER 1 (8274)       TIER 2 (5311)
         |
    +----+----+
    |         |
  CET1      AT1
 (P859)    (P865)
```

> **Code conventions for the diagram above.** Leaf labels are MDRM *item* codes; on the
> FR Y-9C they take the prefix `BHCK`/`BHCT` (consolidated) for balance-sheet/income items
> and `BHCA` (e.g. `BHCA8274`, `BHCAP859`) for HC-R regulatory-capital items. All codes shown
> are verified against the Federal Reserve MDRM **except** the two marked `*`:
> `8641` (total securities) and `2200` (total deposits) are **Call Report** totals
> (`RCFD8641` / `RCFD2200`) and have **no single consolidated FR Y-9C MDRM** — on the Y-9C,
> total securities = AFS `BHCK1773` + HTM `BHCKJJ34` + equity `BHCKJA22`, and total deposits
> = `BHDM6631` (noninterest) + `BHDM6636` (interest). Capital: Tier 1 = `BHCA8274`,
> Tier 2 = `BHCA5311`, Total = `BHCA3792`, CET1 = `BHCAP859`, AT1 = `BHCAP865`.

---

## Quick Start

### For Analysts

1. **Need MDRM codes?** Start with `csv/MDRM_MASTER_COMPLETE.csv`
2. **Analyzing specific schedules?** See schedule-specific guides in `docs/`
3. **Understanding form relationships?** See `docs/MASTER_REGULATORY_GUIDE.md`
4. **Validating data?** Check `docs/RECONCILIATION_HIERARCHY.md`

### For AI Agents

1. **Programmatic access to schedules** → `json/schedule_schemas.json`
2. **Cross-form concept mapping** → `json/cross_form_mapping.json`
3. **Validation rules for data quality** → `csv/VALIDATION_RULES.csv`
4. **Full regulatory taxonomy** → `json/data_taxonomy.json`

### For Data Engineers

1. **Reconciliation formulas** → `csv/RECONCILIATION_FORMULAS_COMPLETE.csv`
2. **Component hierarchies** → `csv/SCHEDULE_COMPONENT_HIERARCHY.csv`
3. **Form data flow** → `csv/FORM_REPORTING_HIERARCHY.csv`
4. **Validation test cases** → `csv/VALIDATION_TEST_CASES.csv`

---

## Reconciliation Quick Reference

### Top 10 Critical Checks

Every check below carries its **empirical verdict** against 208M rows of real FR Y-9C filings
(the Call Report side is validated against 1.9B rows — see
[docs/EMPIRICAL_VALIDATION.md](docs/EMPIRICAL_VALIDATION.md) and
[`csv/RELATIONSHIP_REGISTRY.csv`](csv/RELATIONSHIP_REGISTRY.csv)).

> **Cross-source check (v9.0):** the same balance-sheet concepts were confirmed against the FDIC's
> independently-collected SDI at 99.5–99.97% over up to 667,551 bank-quarters. Two definitional
> mappings were corrected as a result — FDIC total equity maps to Call item **`3210`** (excl.
> minority interest, not the MI-inclusive `G105`), and FDIC net loans maps to **`2122 − 3123`**
> (loans net of unearned income minus allowance, not the RC-C item-12 total `B529`). See
> [`json/cross_form_mapping.json`](json/cross_form_mapping.json).

| # | Check | Formula | Empirical verdict |
|---|-------|---------|-------------------|
| 1 | Balance Sheet (item 29 = item 12) | BHCK3300 = BHCK2170 | **CONFIRMED** (100.00%, n=187,845) |
| 1b | Balance Sheet (A = L + E) | BHCK2170 = BHCK2948 + BHCK3210 | QUALITY_TOLERANCE (92.6%; `BHCK2948` includes minority interest, so item-29 form is the exact one) |
| 2 | Trading Assets | BHCT3545 (HC-D) = BHCK3545 (HC) | CRITICAL |
| 3 | Trading Liabilities | BHCT3548 (HC-D) = BHCK3548 (HC) | CRITICAL |
| 4 | Loans Net | BHCKB529 = BHCKB528 − BHCK3123 (HFI − allowance) | **CONFIRMED** (100.00%, n=100,102) |
| 5 | Tier 1 Capital | BHCA8274 = BHCAP859 + BHCAP865 | CRITICAL |
| 6 | Total Capital | BHCA3792 = BHCA8274 + BHCA5311 | CRITICAL |
| 7 | Derivatives +FV | BHCT3543 = Sum(HC-L Positive FV) | CRITICAL |
| 8 | Derivatives -FV | BHCT3547 = Sum(HC-L Negative FV) | CRITICAL |
| 9 | Securities | HC item 2 = BHCK1773 (AFS) + BHCKJJ34 (HTM) + BHCKJA22 (equity) † | HIGH |
| 10 | NII | BHCK4074 = BHCK4107 − BHCK4073 | **CONFIRMED** (100.00%, n=187,842) |

† The FR Y-9C has **no single consolidated total-securities MDRM**; the total is the sum of
its AFS + HTM + equity components. `8641` (total securities) is a **Call Report** code (`RCFD8641`).

---

## Repository Structure

```
bank-regulatory-data-dictionary/
│
├── csv/                                    # All CSV data tables
│   ├── MDRM_MASTER_COMPLETE.csv            # Curated cross-form concept spine (~100 concepts)
│   ├── MDRM_CROSSWALK_EXPANDED.csv         # 1,239 MDRM-verified codes w/ cross-scope mapping
│   ├── CODE_VALIDATION_AUDIT.csv           # Every code validated vs MDRM (NEW v6.2)
│   │
│   ├── ALL 22 FR Y-9C Schedule Files (v8.0 — verified vs official March-2026 field spec):
│   │   ├── HI_INCOME_STATEMENT.csv         # Schedule HI income statement
│   │   ├── HI_A_EQUITY_CHANGES.csv         # Schedule HI-A changes in equity (NEW v8.0)
│   │   ├── HI_B_CHARGEOFFS.csv             # Schedule HI-B charge-offs & allowance (NEW v8.0)
│   │   ├── HI_C_ALLOWANCE.csv              # Schedule HI-C ALLL disaggregation (NEW v8.0)
│   │   ├── HC_BALANCE_SHEET.csv            # Schedule HC master balance sheet
│   │   ├── HC_B_SECURITIES.csv             # Schedule HC-B securities
│   │   ├── HC_C_LOANS.csv                  # Schedule HC-C loans
│   │   ├── HC_D_TRADING_ASSETS.csv         # Schedule HC-D trading
│   │   ├── HC_E_DEPOSITS.csv               # Schedule HC-E deposit liabilities (NEW v8.0)
│   │   ├── HC_F_OTHER_ASSETS.csv           # Schedule HC-F other assets
│   │   ├── HC_G_OTHER_LIABILITIES.csv      # Schedule HC-G other liabilities
│   │   ├── HC_H_INTEREST_SENSITIVITY.csv   # Schedule HC-H interest sensitivity
│   │   ├── HC_I_INSURANCE.csv              # Schedule HC-I insurance activities (NEW v8.0)
│   │   ├── HC_K_QUARTERLY_AVERAGES.csv     # Schedule HC-K averages
│   │   ├── HC_L_DERIVATIVES.csv            # Schedule HC-L derivatives
│   │   ├── HC_M_MEMORANDA.csv              # Schedule HC-M memoranda (NEW v8.0)
│   │   ├── HC_N_PAST_DUE.csv               # Schedule HC-N past due
│   │   ├── HC_P_MORTGAGE_BANKING.csv       # Schedule HC-P mortgage banking (NEW v8.0)
│   │   ├── HC_Q_FAIR_VALUE.csv             # Schedule HC-Q fair value
│   │   ├── HC_R_CAPITAL.csv                # Schedule HC-R capital
│   │   ├── HC_S_SECURITIZATION.csv         # Schedule HC-S securitization
│   │   └── HC_V_VIES.csv                   # Schedule HC-V variable interest entities (NEW v8.0)
│   │
│   ├── Call Report per-schedule line items (NEW v10.0 — triple-attested):
│   │   ├── CALL_RC_BALANCE_SHEET.csv       # Schedule RC balance sheet (48 rows)
│   │   ├── CALL_RI_INCOME.csv              # Schedule RI income statement (108 rows)
│   │   ├── CALL_RC_B_SECURITIES.csv        # Schedule RC-B securities (52 rows)
│   │   ├── CALL_RC_C_LOANS.csv             # Schedule RC-C loans (79 rows)
│   │   ├── CALL_RC_E_DEPOSITS.csv          # Schedule RC-E deposits (55 rows)
│   │   ├── CALL_RC_N_PAST_DUE.csv          # Schedule RC-N past due (93 rows)
│   │   └── CALL_RC_R_CAPITAL.csv           # Schedule RC-R capital (361 rows)
│   │
│   ├── UBPR & edit history (NEW v10.0):
│   │   ├── UBPR_CONCEPTS.csv               # 4,099 UBPR concepts (2,186 w/ derivations; 31 validated)
│   │   └── EDIT_HISTORY.csv                # 15,622 official edit labels × 30 taxonomy cycles (2001–2026)
│   │
│   ├── Empirical Validation (v8.0; Call layer v9.0; UBPR + conditional engine v10.0):
│   │   └── RELATIONSHIP_REGISTRY.csv       # 7,539 relationships (y9c+call+cross-source+ubpr); tested vs 208M Y-9C + 1.9B Call rows
│   │
│   ├── Advanced Forms (~5,000 items):
│   │   ├── FFIEC_101_ADVANCED_CAPITAL.csv  # Advanced approaches capital
│   │   ├── FFIEC_102_MARKET_RISK.csv       # Market risk VaR/sVaR
│   │   ├── FR_Y15_SYSTEMIC_RISK.csv        # G-SIB systemic risk
│   │   ├── FR_Y9LP_PARENT_ONLY.csv         # Parent company only
│   │   ├── FFIEC_009_COUNTRY_EXPOSURE.csv  # Country exposure
│   │   └── FR_Y11_FOREIGN_SUBSIDIARY.csv   # Foreign subsidiary
│   │
│   ├── New Files (v4.0-5.0):
│   │   ├── FR_Y14A_SCHEDULES.csv           # Y-14A stress testing
│   │   ├── FR_Y14Q_SCHEDULES.csv           # Y-14Q quarterly
│   │   ├── FR_2052a_PRODUCT_HIERARCHY.csv  # Liquidity monitoring
│   │   ├── PILLAR3_GSIB_DISCLOSURE.csv     # Pillar 3 disclosure
│   │   ├── RECONCILIATION_FORMULAS_COMPLETE.csv  # 60+ reconciliations (NEW v5.0)
│   │   ├── VALIDATION_RULES.csv            # 50 validation rules (NEW v5.0)
│   │   ├── SCHEDULE_COMPONENT_HIERARCHY.csv # Component hierarchies (NEW v5.0)
│   │   └── FORM_REPORTING_HIERARCHY.csv    # Form data flow (NEW v5.0)
│   │
│   ├── Catalogue & Structure (NEW v6.0):
│   │   ├── COLLECTIONS_CATALOG.csv         # All 42 collections
│   │   ├── SCHEDULES_CATALOG.csv           # Every subschedule (265 rows)
│   │   ├── NIC_ATTRIBUTES_SCHEMA.csv       # NIC entity attributes (74 fields)
│   │   ├── NIC_RELATIONSHIPS_SCHEMA.csv    # NIC relationships (22 fields)
│   │   ├── NIC_TRANSFORMATIONS_SCHEMA.csv  # NIC mergers/failures (6 fields)
│   │   ├── NIC_CODE_LISTS.csv              # 40 NIC code lists (274 codes)
│   │   ├── IDENTIFIERS.csv                 # Identifier systems crosswalk
│   │   └── MDRM_NAMESPACES.csv             # Full mnemonic namespace catalogue
│   │
│   └── Reference Files:
│       ├── MDRM_PREFIX_DEFINITIONS.csv     # BHCK (consolidated), RCFD, etc.
│       ├── FORM_HIERARCHY.csv              # Form relationships
│       ├── GSIB_ENTITY_IDENTIFIERS.csv     # Bank identifiers
│       └── HISTORICAL_CODE_TRANSITIONS.csv # Legacy codes
│
├── docs/                                   # Documentation (53 guides incl. all 22 Y-9C schedule guides)
│   ├── index.md                            # Site landing page (GitHub Pages home)
│   ├── NAVIGATION.md                       # Master navigation
│   ├── COLLECTIONS_CATALOG.md              # The full data universe — 42 collections (NEW v6.0)
│   ├── NIC_STRUCTURE_GUIDE.md              # FFIEC NIC entities/relationships/transformations (NEW v6.0)
│   ├── IDENTIFIERS.md                      # RSSD/FDIC/OCC/NCUA/LEI/ABA/EIN/CIK crosswalk (NEW v6.0)
│   ├── CALL_REPORT_GUIDE.md                # Complete FFIEC 031/041/051 (NEW v6.0)
│   ├── FOREIGN_AND_STRUCTURE_GUIDE.md      # FFIEC 002/009, FR Y-7/10/11/12/6/8 (NEW v6.0)
│   ├── FDIC_NCUA_OCC_UBPR_GUIDE.md         # Non-Fed collections + UBPR (NEW v6.0)
│   ├── MDRM_GUIDE.md                       # MDRM meta-dictionary + namespaces (NEW v6.0)
│   ├── COVERAGE_PROVENANCE.md              # Temporal coverage & vintages (NEW v6.0)
│   ├── RECONCILIATION_HIERARCHY.md         # What adds up to what
│   ├── MASTER_REGULATORY_GUIDE.md          # Comprehensive reference
│   ├── RECONCILIATION_MATRIX.md            # Cross-schedule tie-outs
│   │
│   ├── Schedule Guides (all 22 Y-9C + 5 Call Report):
│   │   ├── HC_*_GUIDE.md / HI_*_GUIDE.md   # One guide per Y-9C schedule (HC, HC-B…HC-V, HI, HI-A…HI-C)
│   │   ├── CALL_REPORT_RC_GUIDE.md         # (NEW v5.0)
│   │   ├── CALL_REPORT_RC_C_LOANS_GUIDE.md # (NEW v5.0)
│   │   ├── CALL_REPORT_RC_E_DEPOSITS_GUIDE.md # (NEW v5.0)
│   │   ├── CALL_REPORT_RC_N_PAST_DUE_GUIDE.md # (NEW v5.0)
│   │   └── CALL_REPORT_RI_INCOME_GUIDE.md  # (NEW v5.0)
│   │
│   └── Advanced Form Guides:
│       ├── FR_Y14_CAPITAL_ASSESSMENT_GUIDE.md
│       ├── FR_2052a_LIQUIDITY_GUIDE.md
│       └── PILLAR3_DISCLOSURE_GUIDE.md
│
├── json/                                   # Machine-readable data
│   ├── collections.json                    # All 42 collections (NEW v6.0)
│   ├── identifiers.json                     # Identifier systems (NEW v6.0)
│   ├── schedule_schemas.json               # Schedule structures
│   ├── cross_form_mapping.json             # Concept mapping
│   ├── data_taxonomy.json                  # Full regulatory taxonomy
│   └── schedule_correspondence.json        # Cross-schedule linkages
│
├── scripts/                                # Validation scripts (NEW v5.0)
│   ├── validate_reconciliation.py          # Registry-driven validator; --scope {y9c,call,all,ubpr} (v9.0/v10.0)
│   └── ci_audit.py                         # CI token-validity guard — fails the build on any invalid token (NEW v10.0)
│
├── README.md                               # This file
└── LICENSE                                 # MIT License
```

---

## Key Concepts

### MDRM Codes Explained

```
BHCT3545
│  │ │──│
│  │   │
│  │   └── Item number (3545 = Trading Assets)
│  │
│  └────── Form/Scope code (T = total consolidated)
│
└───────── Prefix (BHC = Bank Holding Company)
```

**Common Prefixes:**

| Prefix | Form | Scope |
|--------|------|-------|
| BHCK | FR Y-9C | **Consolidated** (domestic + foreign) — the primary Y-9C series |
| BHDM | FR Y-9C | Domestic offices only |
| BHFN | FR Y-9C | Foreign offices only |
| BHCP | FR Y-9LP | Parent company only (large) |
| BHSP | FR Y-9SP | Parent company only (small) |
| BHCA | FR Y-9C | Consolidated regulatory capital (HC-R) |
| RCFD | Call Report | Consolidated (domestic + foreign) |
| RCON | Call Report | Domestic offices only |
| RCFN | Call Report | Foreign offices only |
| RIAD | Call Report / FR Y-9C | Income statement (year-to-date) |

> **Correction (v6.0):** earlier releases mislabeled `BHCK` as "domestic only." `BHCK` is the **consolidated** series; domestic-only is `BHDM` and foreign-only is `BHFN` (confirmed against the Federal Reserve MDRM). See [`docs/MDRM_GUIDE.md`](docs/MDRM_GUIDE.md) and [`csv/MDRM_NAMESPACES.csv`](csv/MDRM_NAMESPACES.csv) for the full namespace catalogue.

---

## For AI Agents

### JSON Schema Structure

```python
import json

# Load schedule schemas
with open('json/schedule_schemas.json') as f:
    schemas = json.load(f)

# Access HC balance sheet structure
for item in schemas['HC']['line_items']:
    print(f"{item['item']}: {item['mdrm']} - {item['name']}")
    if item.get('reconciliation'):
        print(f"  Reconciliation: {item['reconciliation']}")
```

### Cross-Form Mapping

```python
# Load cross-form mapping
with open('json/cross_form_mapping.json') as f:
    mapping = json.load(f)

# Find same concept across forms
total_assets = mapping['concepts']['total_assets']
print(f"Y-9C: {total_assets['forms']['FR_Y9C']['mdrm']}")  # BHCT2170
print(f"Call: {total_assets['forms']['Call_Report_031']['mdrm']}")  # RCFD2170
```

### Validation Rules

```python
import pandas as pd

# Load validation rules
rules = pd.read_csv('csv/VALIDATION_RULES.csv', comment='#')

# Get critical balance sheet rules
critical_rules = rules[rules['severity'] == 'CRITICAL']
for _, rule in critical_rules.iterrows():
    print(f"{rule['rule_id']}: {rule['description']}")
```

---

## Common Use Cases

### 1. Bank Financial Model

```python
# Core balance sheet items
balance_sheet = {
    'total_assets': 'BHCT2170',
    'total_loans': 'BHCTB528',
    'securities_afs': 'BHCT1773',   # + HTM 'BHCKJJ34' + equity 'BHCKJA22' (no single Y-9C total; Call: RCFD8641)
    'trading_assets': 'BHCT3545',
    'deposits_noninterest': 'BHDM6631',  # + interest 'BHDM6636' (no single Y-9C total; Call: RCFD2200)
    'total_equity': 'BHCT3210',
}

# Income statement items
income = {
    'interest_income': 'BHCK4107',
    'interest_expense': 'BHCK4073',
    'net_interest_income': 'BHCK4074',   # = 4107 - 4073 (CONFIRMED, 100%, n=187,842)
    'provision': 'BHCKJJ33',             # HI item 4, CECL "provisions for credit losses" (current)
    'net_income': 'BHCK4340',            # HI item 14 (BHCT4340 = BHCK4340, CONFIRMED)
}
```

### 2. Capital Analysis

```python
# Regulatory capital
capital = {
    'cet1': 'BHCAP859',
    'tier1': 'BHCA8274',
    'total_capital': 'BHCA3792',
    'rwa': 'BHCAA223',
}

# Ratios
cet1_ratio = capital['cet1'] / capital['rwa']  # Min 4.5%
tier1_ratio = capital['tier1'] / capital['rwa']  # Min 6.0%
total_ratio = capital['total_capital'] / capital['rwa']  # Min 8.0%
```

### 3. Asset Quality

```python
asset_quality = {
    'npl_total': 'BHCK1403',        # Nonaccrual
    'past_due_30_89': 'BHCK5524',   # 30-89 days
    'past_due_90_plus': 'BHCK5525', # 90+ days
    'allowance': 'BHCT3123',        # ALLL
}

# NPL Ratio = (Nonaccrual + 90+ DPD) / Total Loans
```

---

## Data Sources

| Source | URL | Content |
|--------|-----|---------|
| FFIEC CDR | https://cdr.ffiec.gov/ | Call Reports, FR Y-9C, FFIEC 101/102/002/009 |
| MDRM Dictionary | https://www.federalreserve.gov/apps/mdrm/ | Official variable definitions (~87,000+ codes) |
| Fed Reporting Forms | https://www.federalreserve.gov/apps/reportingforms/ | FR Y-series forms + instructions |
| Fed NIC / NPW | https://www.ffiec.gov/NPW/ | Institutional structure, relationships, transformations |
| FDIC BankFind | https://banks.data.fdic.gov/ | SDI financials, SOD, failures, locations |
| NCUA | https://ncua.gov/analysis/credit-union-corporate-call-report-data | 5300 credit-union Call Reports (FS220) |
| FRASER (OCC) | https://fraser.stlouisfed.org/ | Historical OCC national-bank reports (1863–1941) |

For an annotated directory of every source — what each provides, its coverage, what it is best used for, and citation requirements — see [`docs/DATA_SOURCES.md`](docs/DATA_SOURCES.md).

---

## Companion Project: FreeNIC

This repository is the **catalogue / mapping** product. Its sibling, **[FreeNIC](https://github.com/andenick/FreeNIC)**, is the **data-access** package — it lets you download and query ~4.97 billion rows of the actual filings (1863–2026) via Python/SQL. Use this repo to understand *what the data is and how it fits together*; use FreeNIC to *get and query it*.

---

## Major Trading BHCs

| Entity | RSSD ID | 2024Q4 Assets |
|--------|---------|---------------|
| JPMorgan Chase | 1039502 | $3.9T |
| Bank of America | 1073757 | $3.3T |
| Citigroup | 1951350 | $2.4T |
| Wells Fargo | 1120754 | $1.9T |
| Goldman Sachs | 2380443 | $1.7T |
| Morgan Stanley | 2162966 | $1.2T |

---

## Data Quality & Verification

This repository is verified on two independent axes: **token validity** (does every code exist
in the Fed MDRM?) and **empirical relationship validity** (do the identities actually hold in
real filings?).

**Token validity: 100% — and CI-enforced (v10.0).** Every one of the **11,126** distinct
MDRM/UBPR-code-shaped tokens in the shipped files validates against the full Federal Reserve MDRM
dictionary and the official UBPR taxonomy concept set; results are in
[`csv/CODE_VALIDATION_AUDIT.csv`](csv/CODE_VALIDATION_AUDIT.csv). UBPR-namespace tokens validate
against the official UBPR taxonomy concept set rather than the MDRM (the MDRM's UBPR section is
incomplete, e.g. `UBPR2210`). Validity is no longer just habitual: `scripts/ci_audit.py` runs in CI
on every push and **fails the build on any invalid token**. (The three form-name string literals
"031"/"041"/"051" flagged by the official form-number edit are whitelisted as documented false
positives.)

**Empirical relationship validity (FR Y-9C v8.0; Call Report v9.0; UBPR + conditional engine v10.0).**
The official FR Y-9C edit checklist, the **official FFIEC Call Report edit checks and calculation
linkbases** (distributed in the CDR XBRL taxonomy, cdr.ffiec.gov; forms 031/041/051), the **official
FFIEC UBPR taxonomy v181 + User's Guide** derivations, and the dictionary's curated identities were
structured into a [Relationship Registry](csv/RELATIONSHIP_REGISTRY.csv) of **7,539 relationships**
(y9c 2,330 · call 5,162 · cross-source 16 · ubpr 31). Every machine-testable one was tested against
real filings — **208 million rows** of FR Y-9C bulk data (1986–2025), **1.917 billion rows** of Call
Report bulk data (2001 Q1–2026 Q1), and real published UBPR values — with a `max($1k, 0.1%)`
tolerance. v10.0 added a **conditional/compound testing engine** (IF/THEN→CASE, AND-splitting,
constant coefficients) that made 448 previously-documented rows testable and drove every row to an
explained verdict:

| Status | Count |
|--------|------:|
| CONFIRMED | 2,841 |
| CONFIRMED_CURRENT | 144 |
| LOW_N_PASS | 4 |
| VINTAGE_CONFIRMED | 7 |
| QUALITY_TOLERANCE (documented rate) | 43 |
| DATA_GAP (component splits too sparse) | 122 |
| OFFICIAL_EDIT_UNMET (kept as research finding) | 8 |
| CONDITION_NEVER_MET (confidential supervisory items absent from bulk) | 48 |
| NOT_IN_BULK (text/confidential/discontinued code) | 134 |
| CONDITIONAL_DOC (sub-population / non-arithmetic) | 224 |
| GRAMMAR_DOC (null-logic / ratio-division families, documented) | 2,158 |
| not_testable (documented non-arithmetic classes) | 1,806 |
| **Total** | **7,539** |

**Zero rows are pending or unexplained.** A v9.0 **cross-source concordance** additionally confirmed
16 core concepts between the Call Reports and the FDIC SDI at 99.5–99.97% (up to 667,551
bank-quarters each), correcting two definitional mappings — **FDIC equity ↔ Call `3210`** (not the
MI-inclusive `G105`) and **FDIC net loans ↔ `2122 − 3123`** (not RC-C item-12 `B529`). Full
methodology, showcase identities with pass rates, the seven OFFICIAL_EDIT_UNMET findings, and honest
limitations are in **[docs/EMPIRICAL_VALIDATION.md](docs/EMPIRICAL_VALIDATION.md)**; the
per-relationship verdicts are in
[`csv/RELATIONSHIP_REGISTRY.csv`](csv/RELATIONSHIP_REGISTRY.csv).

**v8.0: all 22 schedules, official-spec verified, empirically validated.** v8.0 took the
dictionary from token-validity to full conceptual and empirical verification. Every FR Y-9C
schedule was rebuilt and checked against the **official March-2026 field specifications**
(Reporting Central Appendix A); **8 schedules were newly added** (HC-E, HC-I, HC-M, HC-P, HC-V,
HI-A, HI-B, HI-C) so the dictionary now details **all 22 FR Y-9C schedules**. A conceptual sweep
fixed hundreds of wrong-concept and wrong-vintage codes. Token validity now stands at
**4,564 / 4,564 (100%)**, and every machine-testable relationship is tested against real filings
(see the verdict table above and [docs/EMPIRICAL_VALIDATION.md](docs/EMPIRICAL_VALIDATION.md)).

**v7.0: 100% code validity.** Every one of the 2,426 distinct MDRM-code tokens in the v7.0 shipped
files now exists in the Federal Reserve MDRM. The three structurally fabricated legacy schedule
CSVs (HC-H, HC-K, HC-Q) were **rebuilt from the official FR Y-9C form** rather than patched —
every code in them is verified both against MDRM *and* against actual reported FR Y-9C (BHCF)
bulk data, 1986–2021. The remaining concepts with no MDRM code (caption totals like Y-9C total
deposits, computed lines like NII-after-provision, retired Basel lines) are stated honestly as
code-less concepts instead of carrying invented codes. Every adjudication — FIX / REMOVED /
DISCONTINUED / FALSE_POSITIVE, with reasoning — is published in
[`csv/CODE_REMEDIATION_LOG.csv`](csv/CODE_REMEDIATION_LOG.csv) (427 entries).

**Correction (v7.0):** v6.3 described Schedule HC-H as "removed ~2001." That was wrong — HC-H
(Interest Sensitivity) is a **current** FR Y-9C schedule of five single-column items
(`BHCK3197/3296/3298/3408/3409`), collected continuously since 1986. What never existed was the
14-row maturity-bucket grid earlier editions printed for it; v7.0 replaces it with the real
schedule.

**Math-verified relationships.** Every asserted reconciliation identity was checked for code
validity **and** definitional correctness against the FR Y-9C form. Corrections include the
capital identities (`BHCA8274 = BHCAP859 + BHCAP865`; AT1 is `BHCAP865`, not the previously-used
`BHCAP856`), and the balance-sheet identity (`BHCK2170 = BHCK2948 + BHCK3210 = BHCK3300` —
`BHCK2948` already includes minority interest, so adding `BHCK3000` separately double-counts it).

The v6.0+ layer — Collections/Schedules catalogue, NIC structure, identifier crosswalk, MDRM
namespace catalogue, and the [1,239-code expanded crosswalk](csv/MDRM_CROSSWALK_EXPANDED.csv) — is
**MDRM-verified by construction.** For guaranteed-valid codes, prefer `MDRM_CROSSWALK_EXPANDED.csv`
and the schedule files; consult `CODE_REMEDIATION_LOG.csv` for the full disposition history.

## Version History

| Version | Date | Changes |
|---------|------|---------|
| **10.0** | 2026-06-11 | **Call Report line items, UBPR derivations, official edit history, and CI-enforced validity.** Shipped **seven Call Report per-schedule line-item CSVs** (`CALL_RC_BALANCE_SHEET` 48 · `CALL_RI_INCOME` 108 · `CALL_RC_B_SECURITIES` 52 · `CALL_RC_C_LOANS` 79 · `CALL_RC_E_DEPOSITS` 55 · `CALL_RC_N_PAST_DUE` 93 · `CALL_RC_R_CAPITAL` 361), each row **triple-attested** against official 2025-12 grids, the CDR XBRL taxonomy presentation linkbase, and the 1.9B-row warehouse — so the dictionary now details line items for **both** report families. Added the **UBPR**: official taxonomy v181 + FFIEC UBPR User's Guide acquired; **4,207 derivation formulas parsed (zero silent drops)**; [`csv/UBPR_CONCEPTS.csv`](csv/UBPR_CONCEPTS.csv) ships **4,099 concepts** (2,186 with human-readable derivations); **31 headline ratios empirically validated at 99.77–100%** against real published UBPR values (~44k bank-quarters each, 2015–2026) — ROA, ROE, NIM, efficiency, charge-off, past-due, capital, liquidity. New [`docs/UBPR_GUIDE.md`](docs/UBPR_GUIDE.md) (documents the unit/annualization/averaging conventions and the User's-Guide-vs-caption corrections, e.g. `UBPR7408` is a growth rate, not a capital ratio). Shipped the **official edit history** — [`csv/EDIT_HISTORY.csv`](csv/EDIT_HISTORY.csv): **15,622 distinct edit labels across 30 taxonomy cycles 2001–2026** (4,759 retired, 1,212 introduced 2020+; the rulebook ~quintupled, ≈2,450→≈11,000); new [`docs/EDIT_HISTORY.md`](docs/EDIT_HISTORY.md); registry call rows carry `first_official_cycle`. New **conditional/compound engine** (IF/THEN→CASE + AND-splitting + constant coefficients) made **448** documented rows testable (rest stay GRAMMAR_DOC); a registry truncation defect was caught and repaired against official text (two rows flipped to confirmed). Registry grew to **7,539 rows** (y9c 2,330 · call 5,162 · cross_source 16 · ubpr 31), **0 unexplained**. Token audit **11,126/11,126 (100%)**, now **CI-enforced** (`scripts/ci_audit.py` fails the build on any invalid token); `_rebuild/quarterly_refresh.py` one-command refresh. **No schema breaks** — additive CSVs + registry growth only. |
| **9.0** | 2026-06-11 | **The Call Report, empirically validated against 1.9 billion filing rows.** Acquired the **official FFIEC Call Report edit checks and calculation linkbases** (distributed in the CDR XBRL taxonomy, cdr.ffiec.gov; forms 031/041/051; 3,615 edits/form + 243 calc identities; current cycle 2026-03-31, 30 historical cycles archived), structured them into [`RELATIONSHIP_REGISTRY.csv`](csv/RELATIONSHIP_REGISTRY.csv) — which grew **2,330 → 7,508 relationships** (y9c 2,330 · call 5,162 · cross-source 16) — and tested every machine-testable Call relationship (**2,464**) against **1.917 billion rows of bulk Call Report filings** (2001 Q1–2026 Q1). Added a **cross-source concordance** confirming 16 core concepts vs FDIC SDI at **99.5–99.97%**, which corrected two definitional mappings (**FDIC equity ↔ Call `3210`** not MI-inclusive `G105`; **FDIC net loans ↔ `2122 − 3123`** not RC-C item-12 `B529`). Registry status distribution: **2,564 CONFIRMED · 83 CONFIRMED_CURRENT · 36 QUALITY_TOLERANCE · 48 DATA_GAP · 134 NOT_IN_BULK · 224 CONDITIONAL_DOC · 7 OFFICIAL_EDIT_UNMET · 4,412 not_testable** (0 pending). Seven OFFICIAL_EDIT_UNMET findings kept deliberately (e.g. a phantom `RCONM288 = RCONL191 + RCONL192` decomposition with zero public observations; an RC-R II securitization bound that holds at only 0.452 among active securitizers). Caught a second parser hazard (dropped multiplication: `X = Y * 0` read as `X = Y`; fixed + guarded). Token validity **6,475/6,475 (100%)**. `validate_reconciliation.py` gains `--scope {y9c,call,all}`. **No schema breaks** — additive registry growth + new statuses only. |
| **8.0** | 2026-06-11 | **All 22 FR Y-9C schedules + empirical validation against 208M filing rows.** Rebuilt and verified every FR Y-9C schedule against the **official March-2026 field specifications** (Reporting Central Appendix A); **added 8 new schedules** — HC-E (deposits), HC-I (insurance), HC-M (memoranda), HC-P (mortgage banking), HC-V (VIEs), HI-A (equity changes), HI-B (charge-offs/allowance), HI-C (ALLL disaggregation) — so the dictionary now details **all 22** Y-9C schedules. Structured the official FR Y-9C edit checklist (2,195 edits) and merged it with curated identities into the new [`RELATIONSHIP_REGISTRY.csv`](csv/RELATIONSHIP_REGISTRY.csv) (**2,330 relationships**); **tested all 332 machine-testable relationships against 208M rows of real FR Y-9C filings** (13,668 holding companies, 1986–2025): **264 CONFIRMED, 34 CONFIRMED_CURRENT, 13 QUALITY_TOLERANCE, 17 NOT_IN_BULK, 3 CONDITIONAL_DOC, 1 DATA_GAP** (298/332 ≥99%). A conceptual sweep fixed hundreds of wrong-concept/wrong-vintage codes; token validity 100% (**4,564/4,564**). New [`docs/EMPIRICAL_VALIDATION.md`](docs/EMPIRICAL_VALIDATION.md). **Breaking:** many schedule CSVs rebuilt to the current form; HC-L / HC-S schema changes; new relationship registry. |
| **7.0** | 2026-06-11 | **Legacy-schedule rebuild + 100% code validity.** Rebuilt the three structurally fabricated schedule CSVs from the official FR Y-9C form: **HC-H** (real 5-item single-column schedule `BHCK3197/3296/3408/3409/3298`, current since 1986 — correcting v6.3's erroneous "discontinued ~2001" claim; the old 14-row × 7-bucket grid never existed), **HC-K** (verified quarterly-average grid incl. historical end-dated items; removed contradictory double-assignments), **HC-Q** (real 5-column G-series layout: total FV / netting / Level 1 / 2 / 3, effective 2009-06-30 — replacing a fabricated 4-column `BHCL` grid). Every code in the rebuilt schedules verified against MDRM **and** observed in actual FR Y-9C (BHCF) bulk data 1986–2021, with per-code provenance. Repo-wide apply campaign cleared every remaining invalid token (87% → **100%** of 2,426 code tokens); remediation log grown to 427 adjudications, 0 unresolved. Crosswalk 984 → **1,239** codes against a fresh June-2026 MDRM snapshot. Rebuilt HC-G to the official 4-line structure. **Breaking:** HC_H/HC_K/HC_Q/HC_G CSV schemas changed. |
| **6.3** | 2026-06-09 | **Per-code remediation + math-verified relationships.** Fixed 139 invalid legacy codes to their verified current MDRM code (validity 81%→87%); full disposition of every flagged code in `CODE_REMEDIATION_LOG.csv`. Math-verified all reconciliation/validation identities against the FR Y-9C form: corrected the AT1 code (`BHCAP856`→`BHCAP865`) and the balance-sheet identity (removed the `BHCK3000` minority-interest double-count → `BHCK2170 = BHCK2948 + BHCK3210`). Remaining flagged codes are documented discontinued/fabricated items, not fixable without fabrication. |
| **6.2** | 2026-06-09 | **Code audit + crosswalk growth + browsable data tables.** Repo-wide MDRM-code validation (`CODE_VALIDATION_AUDIT.csv`; 81% valid) — flags ~412 codes in the legacy per-schedule CSVs (Call-Report items under BHC mnemonics) for remediation; fixed a fabricated Additional-Tier-1 code to the verified `BHCAP865`. Expanded `MDRM_CROSSWALK_EXPANDED.csv` 677→**984** codes (added FFIEC 101/102, FR Y-15/Y-9LP/Y-11, FFIEC 009). Pages site: CSV catalogues rendered as searchable/sortable tables. |
| **6.1** | 2026-06-09 | **Capital-code correction + crosswalk expansion + docs site.** Fixed the non-existent `BHCF`-prefixed capital codes (`BHCF`+`A223/A224/A225`) repo-wide → verified `BHCA8274` (Tier 1) / `BHCA5311` (Tier 2) / `BHCA3792` (Total). Resolved prefix scopes against the full Fed MDRM (BHCT/BHCM/BHCB verified; BHCAP/BHCFA shown to be non-mnemonics = `BHCA`+item). Added `MDRM_CROSSWALK_EXPANDED.csv` (677 MDRM-verified codes with cross-scope mapping). Resolved FFIEC 002/009 Schedule C Part II titles + CIK↔RSSD note. Added a MkDocs Material GitHub Pages site. |
| **6.0** | 2026-06-09 | **Major expansion from FR Y-9C focus to the full U.S. bank-data universe.** Added: master Collections Catalogue (42 collections) + Schedules Catalogue (every subschedule); FFIEC NIC institutional-structure layer (entity/relationship/transformation schemas + 40 code lists, 274 codes); identifier crosswalk (RSSD/FDIC/OCC/NCUA/LEI/ABA/EIN/CIK); complete Call Report (031/041/051) guide; foreign/structure guide (FFIEC 002/009, FR Y-7/10/11/12/6/8); FDIC/NCUA/OCC/UBPR guide; MDRM meta-dictionary + namespace catalogue; coverage/provenance guide. **Fixed:** BHCK mislabel (it is consolidated, not domestic); FR Y-11 and FFIEC 009 schedule lists; cross-form line-item references. |
| 5.0 | 2026-01-29 | Complete reconciliation system (60+ formulas), validation rules (50), schedule schemas JSON, cross-form mapping JSON, component hierarchies, 5 Call Report guides, INDEX.md, RECONCILIATION_HIERARCHY.md |
| 4.0 | 2026-01-29 | Added Y-14A/Q, 2052a hierarchy, Pillar 3 G-SIB disclosure |
| 3.0 | 2026-01-29 | Added FFIEC 101/102, FR Y-15/Y-9LP/Y-11, FFIEC 009 |
| 2.1 | 2026-01-28 | Market risk/trading analysis |
| 2.0 | 2026-01-28 | Comprehensive schedule coverage (14 schedules) |
| 1.0 | 2026-01-28 | Initial release with trading focus |

---

## Contributing

This repository is maintained as a reference resource. Issues and suggestions are welcome.

## Citation

```bibtex
@software{bank_data_dictionary2026,
  title = {Bank Regulatory Data Dictionary},
  author = {Anderson, Nicholas},
  year = {2026},
  url = {https://github.com/andenick/bank-data-dictionary}
}
```

## License

MIT License — See [LICENSE](LICENSE) file for details.
