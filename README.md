# 💬 The Impact of Code Comments on Issue Resolution Time

Welcome to the official repository for the study **"The impact of code comments on issue resolution time"**. This repository provides the empirical dataset, analysis pipeline, comment classifier, and figures associated with the research paper:

> 📄 **"The impact of code comments on issue resolution time"**
>
> 🏛️ Published in the *Software Quality Journal* (Springer Nature).
>
> 🔗 [Official Journal Page & DOI](https://link.springer.com/article/10.1007/s11219-026-09802-2)

## 📖 About the Project

Source code comments play an important role in program comprehension, maintenance, and issue resolution. Yet their impact on how quickly issues get resolved has been inadequately examined, especially across programming languages and across different *kinds* of comments.

This project analyzes **75 popular open-source GitHub repositories** (25 Python, 25 Java, 25 C++) and studies how comments relate to issue resolution time. A **BERT-based classifier** categorizes every comment into one of four types, so the impact of each type can be measured separately. The study focuses on issues closed via pull requests, so that resolution time reflects real code changes.

This work extends our earlier conference study on Java repositories:
> Hamza, A. A., El-Ramly, M., & ElKady, M. (2023). *Investigating the relation between code comments and issue resolution time in Java repositories.* ICICIS 2023.
[Read the Conference Paper](https://ieeexplore.ieee.org/document/10391201/)
-- [DOI](https://doi.org/10.1109/ICICIS58388.2023.10391201)


## ❓ Research Questions

* **RQ1:** To what extent do source code comments contribute to accelerating software maintenance, as measured by issue resolution time?
* **RQ2:** How do different types of code comments (Explanatory, Exploratory, Evolutionary, Noise) vary in their impact on issue resolution time?
* **RQ3:** How does the impact of code comments and their types differ across Python, Java, and C++?

## 🏷️ Comment Taxonomy

The four categories form a unified taxonomy that reconciles the heterogeneous comment taxonomies of prior work (Pascarella & Bacchelli, Steidl et al., Misra et al., Rani et al.).

| Category | Description |
|---|---|
| 🧠 **Explanatory** | Functionality explanation of a class, a method, a step, or an algorithm. |
| 🔍 **Exploratory** | Side information or guidance: warnings, notes, usage, links, pointers, exceptions. |
| 🔮 **Evolutionary** | Future work in the code, such as TODO and FIXME comments. |
| 🗑️ **Noise** | Useless comments: commented-out code, author/license/copyright/version info, auto-generated comments. |

Explanatory and Exploratory comments are treated as *useful*; Evolutionary and Noise comments as *not useful*.

## 🔬 Methodology

### Experiment 1 — Comment Percentage vs. Issue Resolution Time

1. 🏁 **Repositories filtration:** select 25 repositories per language (highly starred, non-educational, actively maintained, at least 85% in the target language).
2. 🔗 **Extract issues related to PRs** using the `is:issue linked:pr` filter; exclude issues tied to non-code files, removed files, or multiple/cross-repository PRs.
3. 📅 **Extract open and close dates** for each issue.
4. ⏱️ **Calculate the average time** required to close issues in each repository.
5. 🧾 **Extract commits related to PRs** and identify the modified classes.
6. 💬 **Extract comments** from the modified classes.
7. 📈 **Calculate correlations** between comment percentage and average issue resolution time for each language.

### Experiment 2 — Comment Types vs. Normalized Resolution Time

1. 🤖 Fine-tune a BERT classifier on labeled comments.
2. 🏷️ Classify the comments extracted in Experiment 1.
3. 📊 Compute the percentage of each comment type per repository.
4. ⚖️ Normalize resolution time by dividing days open by the number of modified classes.
5. 📈 Run Pearson correlation and linear regression (t-test, R²) per comment type and language.

Normality of the variables was checked with the Kolmogorov–Smirnov test before applying Pearson correlation.

## 🤖 Comment Classifier

* **Training data:** 11,922 labeled comments (11,322 from prior studies plus 600 annotated Python comments from Misra et al.), split 70/30 (8,345 train / 3,577 test).
* **Hyperparameters:** learning rate 0.00002, batch size 6.
* **Performance:** 94.91% accuracy; weighted precision/recall/F1 of 94.92% / 94.91% / 94.91%.

| Class | Precision | Recall | F1-Score | Support |
|---|---|---|---|---|
| Evolutionary | 92.54% | 84.93% | 88.57% | 73 |
| Explanatory | 94.96% | 95.07% | 95.01% | 1,684 |
| Exploratory | 93.45% | 94.15% | 93.80% | 1,333 |
| Noise | 99.17% | 97.95% | 98.55% | 487 |

## 📊 Key Findings

Pearson correlation with average issue resolution time (negative = more comments, faster resolution):

| Language | All comments | Explanatory | Exploratory | Evolutionary | Noise |
|---|---|---|---|---|---|
| 🐍 Python | -0.26 | -0.77 | +0.86 | +0.73 | +0.38 |
| ☕ Java | -0.40 | -0.56 | -0.136 | +0.55 | +0.19 |
| ⚙️ C++ | -0.61 | -0.71 | -0.131 | +0.18 | +0.60 |

* 📉 Comments are associated with faster resolution in all three languages, with the strongest effect in C++ and the weakest in Python. In Python the overall effect is not statistically significant (p = 0.165).
* ✅ **Explanatory** comments show a significant negative relationship with resolution time in all three languages.
* ⚠️ **Evolutionary** and **Noise** comments are associated with longer resolution times. Significance varies by language (see regression tables in the paper).
* 🔍 **Exploratory** comments show mixed results and are significant only in Python, where they make up just ~10% of comments, so that result is not conclusive.

> ℹ️ These are correlations across 25 repositories per language, not causal claims. See the *Threats to Validity* section of the paper for confounders and limitations.

## 🗂️ Repository Contents

```
.
├── data/
│   ├── Repositories/        # The 75 selected repositories (25 per language)
│   ├── StatisticalAnalysis/ # Correlation Results and Regerssion Summary and Normality Tests
│   └── ModelTraining/       # BERT Classification Report and Confusion Matrix
├── src/
│   ├── collect_data.py      # Issue, commit, and comment extraction scripts
│   ├── evaluate_model.py    # BERT evaluation notebook
│   ├── comments_analysis.py # Correlation, regression scripts
│   └── analyze_comments.py  # Correlation, regression scripts
├── results_figures/         # Workflow diagrams, scatter plots, confusion matrix
└── README.md
```

* 📁 **Selected repositories:** The 75 repositories with stars, comment percentage, average days to close PR-related issues, share of issues resolved by PR, and total issues (retrieved February 2023).
* 🔍 **Extracted data:** Issues, resolution times, modified classes, and comments per repository.
* 🤖 **Classifier:** Training notebook and evaluation for the BERT model.
* 📈 **Results:** Correlation and regression outputs, plus the scatter plots for all three languages.


## 🔗 Data Availability

The repository metadata, extracted issue and comment data, and analysis outputs are provided in this repository. The underlying source code comes from public GitHub repositories listed in [`data/repositories/`](data/repositories/).

## 📜 Citation

If you use this dataset, classifier, or find this research helpful in your work, please cite our paper:

**BibTeX:**

```
@article{AshrafHamza2026,
  author    = {Ashraf Hamza, Ahmed and El-Ramly, Mohammad and ElKady, Manar},
  title     = {The impact of code comments on issue resolution time},
  journal   = {Software Quality Journal},
  publisher = {Springer Nature},
  volume    = {34},
  number    = {4},
  pages     = {66},
  issn      = {1573-1367},
  year      = {2026},
  doi       = {10.1007/s11219-026-09802-2},
  url       = {https://doi.org/10.1007/s11219-026-09802-2}
}
```

## 👨‍🏫 Authors

* 🎓 **Ahmed Ashraf Hamza** - *Faculty of Computing and Artificial Intelligence, Cairo University* (Corresponding Author) - [✉️](mailto:ahmed.ashraf.hamza136@gmail.com)

* 🎓 **Dr. Mohammad El-Ramly** - *Cairo University / University of London in the European Universities in Egypt* (Corresponding Author) - [✉️](mailto:m.elramly@fci-cu.edu.eg)

* 🎓 **Dr. Manar ElKady** - *Faculty of Computing and Artificial Intelligence, Cairo University* - [✉️](mailto:m.elkady@fci-cu.edu.eg)

## ⚖️ License

The code and data in this repository are open-source under the terms of the MIT License.
