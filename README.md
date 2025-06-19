````markdown
# Sort Google Scholar — Interactive Notebook Demo

This repository contains a **Jupyter Notebook sandbox** that lets you try the
[`sortgs`](https://pypi.org/project/sortgs/) command-line tool without having to
set up a full local environment.  
It is ideal for quickly ranking Google Scholar results by citations or
citations per year, fetching abstracts, exporting to `.bib`, and visualising
trends — all from a single notebook.

> **First time with Jupyter?**  
> Click into a code cell and press **`⇧ Shift` + `⏎ Enter`** to execute it.
> Work through the notebook top-to-bottom, editing parameters as you go.

---

## What’s inside?

| file / folder | purpose |
|---------------|---------|
| `sortgs.ipynb` | Main notebook (the code excerpt you see below) |
| `requirements.txt`  | Minimal dependencies (`sortgs`, `selenium`, `plotly`, etc.) |
| `.env` (optional)   | Place API keys or proxies here (loaded via `python-dotenv`) |

---

## Quick start

```bash
# 1. clone *this* repo
git clone https://github.com/arenkis/sort-google-scholar-notebook.git
cd sort-google-scholar-notebook

# 2. create a virtual environment
python -m venv .venv
source .venv/bin/activate           # Windows: .venv\Scripts\activate

# 3. install requirements
pip install -r requirements.txt

# 4. launch Jupyter
jupyter lab         # or: jupyter notebook
````

Open **`sortgs.ipynb`** and run the cells.

---

## Running a search

Inside the notebook you will find a parameter block:

```python
search_query = '"green jobs" OR "empleos verdes"'
sortby       = "cit/year"      # "Citations" or "cit/year"
nresults     = 100
startyear    = "2000"
endyear      = None
langfilter   = None            # e.g. ["en", "es"]
```

The notebook constructs and executes the equivalent CLI command

```bash
sortgs "<query>" --sortby "cit/year" --nresults 100 --startyear 2000
```

and saves the results to `<query>.csv`.

### Search-query cheat-sheet

| pattern            | example                                          |
| ------------------ | ------------------------------------------------ |
| General search     | `Large Language Models`                          |
| Exact phrase       | `"Large Language Models"`                        |
| Exclude term       | `Large Language Models -transformer`             |
| Author filter      | `Large Language Models author:"Geoffrey Hinton"` |
| Publication filter | `source:Nature`                                  |
| Boolean logic      | `("LLM" OR "GPT") AND (BERT)`                    |
| Title-only         | `intitle:"Large Language Models"`                |

---

## Fetching abstracts

The notebook attempts to enrich each paper with an abstract using a four-tier
fallback strategy:

1. **Semantic Scholar API** (DOI → abstract, then title → abstract)
2. **CrossRef** (DOI metadata)
3. **Web scraping** (BeautifulSoup, Selenium for JS sites)
4. **PDF text extraction** (fitz / PyMuPDF)

Results are streamed to `abstracts_<query>.csv` as they are found so you can
interrupt and resume safely.

---

## Export to BibTeX

```python
df_to_bib(df_abstracts, "abstracts_<query>.bib")
```

The exporter normalises author strings, generates a Zotero-friendly citation key
(`LastNameYEARTitleSnippet`), and embeds the fetched abstract so you can import
directly into reference managers.

---

## Visualisation

An example Plotly scatter plot shows **Google Scholar rank vs. citations**, with
interactive hover displaying truncated titles, authors and citation metrics.
Feel free to add your own plots.

---

## Robot-checking (CAPTCHA) notice

Google Scholar may trigger CAPTCHA challenges after many automated requests:

* For small queries (≤ 100 results) the plain `requests` backend usually works.
* If blocked, the notebook falls back to **Selenium**.
  You will need **ChromeDriver** in your `PATH` and might have to solve a manual
  CAPTCHA once per session.
* If you keep getting blocked, disconnect the notebook kernel (new IP) or run
  locally behind a VPN.

---

## Development

```bash
# run tests (if you fork sortgs itself)
pip install pytest
pytest
```

Feel free to open issues or PRs for improvements to this demo.

---

## License

This notebook demo is released under the **MIT License**.
`sortgs` itself is MIT-licensed by [@WittmannF](https://github.com/WittmannF).