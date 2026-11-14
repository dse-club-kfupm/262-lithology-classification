# Rock Reader: Identifying Rock Types from Well Logs

Data Engineering & Science Club, Applied Projects Committee, KFUPM. Term 262. Prediction case study.

When a well is drilled, instruments lowered into the hole record how the surrounding rock responds to natural radiation, sound and electric current along its depth. Petrophysicists read these measurements, called well logs, to decide which rock lies at each depth: the shale that seals a reservoir, the sandstone or carbonate that may hold oil and gas, and everything in between.

In this project you build a model that predicts the rock type at each depth from five well-log measurements.

## Scope

This is a case study. You will not interpret a real well for drilling decisions. You work with one table of measurements from Norwegian offshore wells, released for the FORCE 2020 machine learning competition, and you show how the data science workflow (understanding, cleaning, exploring, modelling and communicating) answers the question on that table.

## The client and the question

| Item | Description |
| --- | --- |
| Client | A petrophysics team that must label rock types along many new wells faster than it can interpret them by hand |
| Question | Given the log measurements at a depth, which rock group is it? |
| Prediction | `rock_group`: `Shale`, `Sandstone`, `Carbonate` or `Other` |
| Score | Balanced accuracy on the hidden test set (see Grading) |

## The data

`data/train.csv` contains measurements from a subset of the competition's wells. The committee holds whole wells as the hidden test set, so your model is tested on wells it has never seen. Split your own validation set the same way, by well and not by row, because neighbouring rows in one well are almost identical.

The original interpretation has twelve lithology classes. They are grouped into four: Shale (shale, sandstone/shale and marl), Sandstone, Carbonate (limestone, chalk and dolomite) and Other (anhydrite, halite, coal, tuff and basement). Some logs were not recorded in every well, so expect missing values.

| Column | Meaning | Unit |
| --- | --- | --- |
| `well` | Well name, not used as a feature | text |
| `depth_m` | Measured depth along the well | m |
| `GR` | Gamma ray: natural radioactivity, usually high in shale | API units |
| `RHOB` | Bulk density of the rock | g/cm³ |
| `NPHI` | Neutron porosity: an estimate of pore space | fraction |
| `DTC` | Compressional sonic slowness: time for sound to travel one foot | µs/ft |
| `RDEP` | Deep resistivity: resistance to electric current | ohm·m |
| `rock_group` | Target: `Shale`, `Sandstone`, `Carbonate` or `Other` | label |

Test rows contain every column except `rock_group`.

Source: FORCE 2020 well log and lithofacies dataset, Bormann, Aursand, Dilib, Manral and Dischington (2020), DOI 10.5281/zenodo.4351156. Well logs: Norwegian Licence for Open Government Data (NLOD) 2.0. Lithology interpretation: CC BY 4.0.

## Prerequisites

Learn these before you start. Each row gives one source to start with and one to go further.

| Skill | Start with | Go further |
| --- | --- | --- |
| Git and GitHub | [Introduction to GitHub](https://github.com/skills/introduction-to-github), GitHub Skills | [Pro Git](https://git-scm.com/book/en/v2), chapters 1 to 3 |
| Python | [Python](https://www.kaggle.com/learn/python), Kaggle Learn | [The Python Tutorial](https://docs.python.org/3/tutorial/) |
| pandas | [Pandas](https://www.kaggle.com/learn/pandas), Kaggle Learn | [10 minutes to pandas](https://pandas.pydata.org/docs/user_guide/10min.html) |
| Data cleaning | [Data Cleaning](https://www.kaggle.com/learn/data-cleaning), Kaggle Learn | [Working with missing data](https://pandas.pydata.org/docs/user_guide/missing_data.html) |
| Charts | [Data Visualization](https://www.kaggle.com/learn/data-visualization), Kaggle Learn | [Ten Simple Rules for Better Figures](https://doi.org/10.1371/journal.pcbi.1003833) |
| Prediction | [Intro to Machine Learning](https://www.kaggle.com/learn/intro-to-machine-learning), Kaggle Learn | [scikit-learn: Getting started](https://scikit-learn.org/stable/getting_started.html) |
| Animation | [Manim Community quickstart](https://docs.manim.community/en/stable/tutorials/quickstart.html) | [Animations using Matplotlib](https://matplotlib.org/stable/users/explain/animations/animations.html) |
| Slides | [Ten Simple Rules for Making Good Oral Presentations](https://doi.org/10.1371/journal.pcbi.0030077) |  |

Background on the data: the [competition repository](https://github.com/bolgebrygg/Force-2020-Machine-Learning-competition), which describes the wells and the original classes.

## Getting started

```bash
git clone https://github.com/dse-club-kfupm/262-rock-reader.git
cd 262-rock-reader
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter lab
```

Open `notebooks/01_data_understanding.ipynb`. Google Colab is also acceptable; upload `data/train.csv` and keep your notebooks in the repository.

## Repository structure

```text
262-rock-reader/
├── README.md
├── requirements.txt
├── data/
│   ├── train.csv
│   └── README.md               source and licence
├── notebooks/
│   ├── 01_data_understanding.ipynb
│   ├── 02_cleaning_and_exploration.ipynb
│   └── 03_modelling.ipynb
├── src/
│   ├── features.py             cleaning and feature functions
│   └── predict.py              submission entry point
├── models/                     saved model, if predict.py loads one
├── tests/
│   └── check_submission.py     run before submitting
└── visuals/
    ├── slides/                 presentation as PDF
    └── animation/              animation source code and rendered video
```

## What you submit

Part A, prediction (50 points)

- `src/predict.py` containing a function `predict(df)`. It receives a table with the test columns described above and returns one of "Shale", "Sandstone", "Carbonate" or "Other" per row, in the same order.
- The three notebooks showing how you reached the model.
- Run `python tests/check_submission.py` before your final pull request. It confirms that `predict.py` loads and returns predictions in the right format.

Part B, visual communication (50 points)

- Clear, labelled charts in the notebooks.
- Slides: 5 to 7 slides that take the client from the question to the answer, saved as PDF in `visuals/slides/`.
- An animation or a short video of up to 2 minutes made with Python (Manim or matplotlib), with its source code and the rendered file in `visuals/animation/`. A good subject: a virtual well drilled downward, with the logs drawn as curves and the predicted rock group coloured beside them.

## Grading

Your `predict.py` is run once on the hidden test set after the submission deadline.

| Level | Requirement | Points |
| --- | --- | --- |
| Runs | Returns a valid prediction for every test row | 10 |
| Bronze | Scores above the baseline, which always predicts the most common rock group: 25.0% | 30 |
| Silver | Balanced accuracy of at least 42.6% | 40 |
| Gold | Balanced accuracy of at least 60.2%, the committee's reference model | 50 |

Balanced accuracy is the average, over the four rock groups, of the share of rows in that group that your model labels correctly. A model that always gives the same answer scores 25%.

| Part B | Missing | Basic | Good | Excellent |
| --- | --- | --- | --- | --- |
| Charts in the notebooks | 0 | 5 | 10 | 15 |
| Slides, 5 to 7, as PDF | 0 | 5 | 10 | 15 |
| Animation or video, made with Python, up to 2 minutes | 0 | 7 | 14 | 20 |

Any team member may be asked to explain any part of the work. If no one can, the Part A points do not count.

## Milestones

Progress is tracked with four milestones, each with its own issues.

| Milestone | Complete when |
| --- | --- |
| 1. Data understanding | Notebook 01 and the cleaning functions are merged |
| 2. Exploration | Notebook 02, with charts and written findings, is merged |
| 3. Prediction submission | Notebook 03 and `src/predict.py` are merged and pass the format check |
| 4. Visual communication | The slides and the animation or video are merged |

## Rules

- Train only on `data/train.csv`.
- Using other FORCE 2020 files, or any other well data, for training or prediction is not allowed.
- Every change reaches `main` through a pull request reviewed by a teammate.
- AI tools are allowed if declared in the Credits section, and every member must be able to explain the work.
- `predict.py` may use only pandas, NumPy and scikit-learn, so that it runs in the grading environment. Notebooks and visuals may also use matplotlib, seaborn, plotly and Manim.

## Credits

- Project Leads: Name (@github), Name (@github)
- Contributors: Name (@github)
- Organized by: Applied Projects Committee, Data Engineering & Science Club, KFUPM
- Data: FORCE 2020 well log and lithofacies dataset, Bormann, Aursand, Dilib, Manral and Dischington (2020), DOI 10.5281/zenodo.4351156. Well logs: Norwegian Licence for Open Government Data (NLOD) 2.0. Lithology interpretation: CC BY 4.0.
- AI tools used: none, or name the tool and what it was used for
