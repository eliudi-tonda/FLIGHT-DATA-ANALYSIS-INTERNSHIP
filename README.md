
# Flight Data Analysis: Parameter Study and Approach Screening

Internship project on flight parameter study using public NASA flight data. The work covers building a parameter dictionary, checking data quality, labelling flight phases, computing baselines, and screening approaches for unusual events. All steps are scripted so the results can be reproduced from the original download.

> Status  :  Week 1 (planning) complete. Weeks 2 to 4 are in progress. 


Author: Eliudi Elphace Tonda
Role:    Flight Data Analysis Intern
Period  :  2/10/2026 to 30/102026

---

## 1. Project overview

| Week | Focus | Main output |
|---|---|---|
| 1 | Planning and parameter study | Planning document (research, scope, standards, plan) |
| 2 | Data processing | Parameter dictionary, quality checks, phase labels, baselines |
| 3 | Anomaly detection and validation | Event list, comparison with references, error analysis, change log |
| 4 | Final report and presentation | Final report and a presentation of under ten minutes |

**Question being studied:** how well can a set of core flight parameters from an open dataset be understood, validated and used to describe normal behaviour by phase of flight, and how effectively can that baseline flag unstable or unusual approaches?

## 2. Data

| Item | Detail |
|---|---|
| Source | NASA DASHlink, *Flight Data For Tail 670* |
| File used | `Tail_670_1.zip` (part 1 of this tail's flights) |
| Page | https://c3.nasa.gov/dashlink/resources/648/ |
| Format | MATLAB `.mat` files, one per flight |
| Content | 186 parameters per flight; each has sensor recordings, sampling rate, units, description and ID |
| Licence | The catalogue lists the data as public, with no licence information provided |

**The data is not stored in this repository** (it is too large for GitHub). To get it:

1. Download `Tail_670_1.zip` from the page above.
2. Place it in `data/raw/`.
3. Extract it (see the run order below).

**Subset used:** the first [40] `.mat` files in sorted file-name order. The list of files used is in `docs/flight_file_list.csv`.

> If you use a different tail or part, change the names above and in the scripts.

## 3. Repository structure

```
project/
  data/
    raw/                  original download and extracted flights (not in Git)
    processed/            cleaned data written by the scripts (not in Git)
  docs/
    parameter_dictionary.csv
    cleaning_log.csv
    flight_file_list.csv
    change_log.md
  notebooks/              exploration and plots (outputs cleared before commit)
  results/                tables, figures and event lists made by the pipeline
  src/                    Python scripts and functions
  README.md
  requirements.txt
  .gitignore
```

## 4. Setup

Requires Python 3.10 or newer.

```bash
git clone https://github.com/[username]/[repository].git
cd [repository]
python -m venv venv
# Windows: venv\Scripts\activate     Mac/Linux: source venv/bin/activate
pip install -r requirements.txt
```

Example `requirements.txt` (pin the versions you actually used):

```
numpy
pandas
scipy
matplotlib
scikit-learn
pyarrow
jupyter
```

Add this to `.gitignore` so large data never reaches GitHub:

```
data/raw/
data/processed/
venv/
.ipynb_checkpoints/
```

## 5. How to reproduce the results

Run the scripts in this order from the project folder. File names are examples, so rename them to match your own scripts.

| Step | Script | What it does | Output |
|---|---|---|---|
| 1 | `src/01_extract_subset.py` | Extracts the chosen flights from the ZIP and writes the file list | `data/raw/Tail_670_1/`, `docs/flight_file_list.csv` |
| 2 | `src/02_build_parameter_dictionary.py` | Reads one flight and records each parameter's name, unit, rate and description | `docs/parameter_dictionary.csv` |
| 3 | `src/03_quality_checks.py` | Range, spike, flat-line, gap, time and cross-parameter checks | `docs/cleaning_log.csv` |
| 4 | `src/04_preprocess_and_phases.py` | Converts to a common time base and labels flight phases | `data/processed/` |
| 5 | `src/05_baselines.py` | Baseline statistics and plots by phase | `results/` |
| 6 | `src/06_detect_events.py` | Rule-based approach screening and Isolation Forest | `results/events.csv` |
| 7 | `src/07_validate.py` | Compares results with independent calculations and reference values | `results/validation_table.csv` |

Random seeds are fixed in the scripts. Thresholds are kept in one configuration file, `src/config.py`.

## 6. Method summary

1. **Parameter dictionary:** every parameter used is documented with unit, sampling rate, plausible range and source.
2. **Quality checks:** range, rate-of-change, flat-line, gaps, time order, and cross-parameter consistency. Each check records how many samples it flags.
3. **Flight phases:** rule-based labels (ground, take-off, climb, cruise, descent, approach, landing) from altitude, speed and configuration, based on CICTT phase-of-flight definitions.
4. **Baselines:** statistics by phase for each working parameter.
5. **Detection:** threshold rules for the approach phase, plus Isolation Forest for statistical screening. Flagged flights are reviewed by hand.
6. **Validation:** results are compared with independent calculations (for example, vertical speed derived from altitude) and with published stabilised-approach criteria.

## 7. Results

Add the key figures and tables here as they are produced. For example:

- Number of flights used: [ ]
- Parameters in the working set: [ ]
- Percentage of samples flagged by each quality check: [ ]
- Number of approach events flagged by the rules and by Isolation Forest: [ ]

Figures are stored in `results/`.

## 8. Assumptions and limitations

- Only one aircraft (Tail 670) and one part of its data are used, so findings describe this dataset and cannot be generalised to a fleet or an airline.
- The dataset description does not state the aircraft type, operator or routes, so none is assumed.
- There are no confirmed event labels, so detection results are checked by manual review rather than scored against ground truth.
- Thresholds taken from public guidance may not suit this aircraft and are adjustable in `src/config.py`.
- Parameter names, units and sampling rates are taken from the files themselves. Typical values in the planning document are indicative only.

## 9. References

- ICAO Doc 10000, *Manual on Flight Data Analysis Programmes (FDAP)*
- ICAO Annex 6, Part I (flight recorders) and Annex 19 (Safety Management)
- FAA Advisory Circular 120-82, *Flight Operational Quality Assurance*
- UK CAA CAP 739, *Flight Data Monitoring*
- CAST/ICAO Common Taxonomy Team, phase-of-flight definitions
- Flight Safety Foundation, ALAR Toolkit: stabilised approach criteria
- Liu, F. T., Ting, K. M. and Zhou, Z.-H. (2008), *Isolation Forest*, IEEE ICDM
- Das, S., Matthews, B. L., Srivastava, A. N. and Oza, N. C. (2010), multiple kernel anomaly detection, ACM KDD
- Basora, L., Olive, X. and Dubot, T. (2019), recent advances in anomaly detection in aviation, *Aerospace*
- NASA DASHlink, *Flight Data For Tail 670*: https://c3.nasa.gov/dashlink/resources/648/



## 10. Licence and acknowledgements

Code in this repository: [MIT license].

Data: NASA DASHlink flight data, listed in the catalogue as public with no licence information provided. Credit NASA as the source.
