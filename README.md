# Authorship Attribution Beyond Accuracy: Testing Lexical Profile Stability

This repository accompanies a long paper submitted to [Digital Humanities 2027 Conference (DH2027)](https://dh2027.adho.org/).

We test whether authorship attribution models produce stable rankings of lemma contributions when training samples or vocabulary size change. Across three corpora, Cosine Delta combined good accuracy with stable rankings, while high accuracy alone did not guarantee stability.

## Reproduce the figures

Use Python 3.12 from the repository root. Create and activate a virtual environment, install the requirements and start Jupyter Notebook.

```bash
python -m venv .venv
# Windows PowerShell: .venv\Scripts\Activate.ps1
# macOS/Linux: source .venv/bin/activate
python -m pip install -r requirements.txt
python -m notebook
```

Run [03_figures.ipynb](03_figures.ipynb) against the supplied CSVs in [results/](results/) to regenerate the four [paper figures](figures/) at 600 dpi. Figure 1 uses Arial Narrow when available and falls back to DejaVu Sans.

## Recompute the experiments

1. Download the original IMDb62 ZIP into the checkout. The other two original archives are already in [data/raw/](data/raw/).

   ```bash
   curl -fL "https://www.dropbox.com/s/np1u1hl343gd73m/imdb62.zip?dl=1" -o data/raw/imdb62.zip
   ```

   Use curl.exe on Windows PowerShell. The expected SHA-256 is 88091d873ca90876304b3f11dd8bf27547fe238ef84ab526f065eedda5fb3d4e.
2. Run [01_preprocessing.ipynb](01_preprocessing.ipynb) to build the IMDb62 lemma counts and validate the supplied FreDraCor and RusLit9 matrices. Set REBUILD to True to regenerate all matrices. The fixed selection preserves source order and group membership. For IMDb62, the notebook reads the review and author identifiers locally from the original ZIP.
3. Run [02_experiments.ipynb](02_experiments.ipynb) to recompute both tests and write the six result CSVs. Full lemmatization and training can take considerable CPU time.
4. Run [03_figures.ipynb](03_figures.ipynb) to plot the recomputed results.

The notebooks use five outer folds, ten paired training samples per fold, six vocabulary selectors and four classifiers. Test 1 replaces approximately 10% of a 90% training sample. Test 2 compares nested vocabularies of 400, 500 and 600 lemmas. RBO uses depth 50 and persistence 0.95. The seed is 20261019. Feature selection and standardization use the training texts in each fit.

RusLit9 contains 259 observations in 254 evaluation groups. Overlapping observations stay in the same outer fold. The IMDb62/C20 combination has too few eligible features to complete the vocabulary comparison and is flagged in the metrics table. Rank displacement is measured on shared lemmas at their original ranks. The supplied tables combine the original large-corpus FP64 runs and the grouped RusLit9 run. The portable CPU implementation can change the last digits of the results and the ordering of nearly tied features.

## Corpora and attribution

- **IMDb62.** Yanir Seroussi, Ingrid Zukerman and Fabian Bohnert (2011), *Authorship Attribution with Latent Dirichlet Allocation*. The study uses 62,000 reviews, combining the title and review text. Obtain the archive from the authors' original distribution. [Paper](https://aclanthology.org/W11-0321/) · [Original archive](https://www.dropbox.com/s/np1u1hl343gd73m/imdb62.zip?dl=1)
- **FreDraCor.** Carsten Milling, Frank Fischer and Mathias Göbel (eds), *French Drama Corpus*, based on Paul Fièvre's Théâtre Classique. The repository includes the unmodified ZIP from commit b7900acea0a6474d972a8e49445ed2a9f10b6911 and lemma-count matrices for 1,191 plays. The matrices use spoken text and are shared under the corpus's CC BY-NC-SA 4.0 terms. Source TEI notices are preserved in the ZIP. [Corpus](https://dracor.org/fre) · [Frozen source](https://github.com/dracor-org/fredracor/tree/b7900acea0a6474d972a8e49445ed2a9f10b6911) · [License](https://creativecommons.org/licenses/by-nc-sa/4.0/)
- **RusLit9.** A nine-author selection from d0rj's *RusLit* at commit 4509680428729da225a73a22008133660f92bb7e. The repository includes the unmodified source ZIP. The source identifies its literary works as public domain. The selection includes plays and essays alongside prose fiction. [Frozen source](https://github.com/d0rj/RusLit/tree/4509680428729da225a73a22008133660f92bb7e)
