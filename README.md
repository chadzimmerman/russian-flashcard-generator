# Anki Gap Finder

A desktop app that analyzes a Russian source text (PDF, EPUB, TXT, or HTML), compares its vocabulary against your existing Anki deck, and automatically generates new flashcards for every word you haven't studied yet — complete with stress marks, audio pronunciation, example sentences from your own text, English translations, and grammar notes.

---

## The Problem

Building Anki flashcards by hand is slow. When working through a Russian textbook or novel, you already know many of the words — but identifying which ones you're missing, looking up their definitions, finding example sentences, and recording pronunciation one by one takes hours per chapter.

This tool automates the entire process.

---

## Time Saved

Here's what making a single card by hand actually looks like:

| Step | Time |
|---|---|
| Navigate to a dictionary, search the word | ~20 sec |
| Copy stressed form, paste into Anki front field | ~10 sec |
| Find audio, download, drag into card | ~35 sec |
| Copy/paste example sentence | ~15 sec |
| Copy/paste meaning | ~10 sec |
| Save card | ~5 sec |
| **Total per card** | **~2 minutes** |

At 10 new cards per day, that adds up fast:

| Timeframe | Cards | Manual time | With this tool | Saved |
|---|---|---|---|---|
| Daily | 10 | ~20 min | ~0 min | ~20 min |
| Weekly | 70 | ~2.5 hrs | ~0 min | ~2.5 hrs |
| Monthly | ~300 | ~10 hrs | ~0 min | ~10 hrs |
| Yearly | ~3,650 | **~122 hrs** | ~0 min | **~122 hrs** |

---

## What It Does

1. **Extracts vocabulary from your source file** — Supports PDF, plain text (.txt/.md), EPUB, and HTML. Uses `pdfplumber` and `ebooklib` to pull all Russian text, then reduces every word to its base (dictionary) form using `pymorphy3`. This means "читал", "читала", "читаете" all resolve to "читать" before comparison.

2. **Reads your existing Anki deck** — Directly opens your `.apkg` file (including the modern zstd-compressed `.anki21b` format used by Anki 23+), reads all card fields, and builds a set of lemmas you already know.

3. **Finds the gaps** — Set subtraction: words in your text that are not in your deck.

4. **Adds stress marks** — Uses StressRNN (offline, Zaliznyak dictionary + BiLSTM model) to predict the stressed vowel in each word. Produces носи́ть, not носить.

5. **Pulls example sentences from your text** — For every gap word, finds the first complete sentence in your source text that contains it. Examples are always contextually relevant to what you're actually reading.

6. **Translates via Google Translate** — Uses `deep-translator` (no API key required) to get an English definition for each word.

7. **Generates audio** — Uses Google Text-to-Speech (`gTTS`) to produce an MP3 pronunciation file for each word. Files are cached so re-runs are fast. Audio auto-plays on the front of the card in Anki.

8. **Adds grammar notes** — Uses `pymorphy3` tags to produce a readable grammar summary for each word: part of speech, verb aspect (perfective/imperfective), gender, animacy, transitivity. Shown on the back of each card.

9. **Review before export** — A clean table lets you inspect every word, delete anything that looks wrong, and then export. No garbage cards.

10. **Builds a ready-to-import `.apkg`** — Uses `genanki` to produce an Anki package file with all cards and audio bundled. Also exports CSV and JSON if you want to use the data elsewhere.

---

## Card Format

Each generated card contains:

| Field | Content |
|---|---|
| **Front** | Russian word with stress mark (e.g. `носи́ть`) + audio |
| **Front (below word)** | Example sentence from your source text |
| **Back** | English translation |
| **Back (below translation)** | Grammar notes (e.g. `verb · imperfective · transitive`) |

---

## Freemium

- **Free:** process up to 10 pages per file
- **$5 lifetime:** unlimited pages, all future updates, no subscription

License keys are validated locally via HMAC — no internet connection required after activation.

---

## Technical Stack

| Library | Purpose |
|---|---|
| `customtkinter` | Desktop GUI (modern tkinter wrapper) |
| `pdfplumber` | PDF text extraction |
| `ebooklib` + `beautifulsoup4` | EPUB and HTML parsing |
| `pymorphy3` | Russian morphological analysis, lemmatization, grammar tags |
| `StressRNN` | Offline stress mark prediction (Zaliznyak + BiLSTM) |
| `deep-translator` | Google Translate (no API key) |
| `gTTS` | Google Text-to-Speech audio generation |
| `genanki` | Anki `.apkg` package generation |
| `zstandard` | Decompression of modern Anki `.anki21b` database format |
| `sqlite3` | Reading the Anki deck database (stdlib) |

---

## Project Structure

```
anki-python-russian-card-script/
├── run_app.py              # GUI launcher
├── main.py                 # CLI launcher (for scripting)
├── app/
│   ├── pipeline.py         # Core processing pipeline
│   ├── extractors.py       # PDF, TXT, EPUB, HTML input parsers + Anki loader
│   ├── exporters.py        # .apkg, CSV, JSON output writers
│   ├── nlp.py              # Lemmatization, stress marks, grammar info
│   ├── translate.py        # Translation and audio generation
│   ├── licensing.py        # HMAC license key validation, freemium gate
│   └── gui/
│       ├── main_window.py  # Main CustomTkinter window
│       ├── review.py       # Results table (ttk.Treeview)
│       └── license_dialog.py  # Upgrade banner and activation dialog
├── site/
│   └── index.html          # GitHub Pages landing page
└── requirements.txt
```

---

## Installation

**Requirements:** Python 3.10+

```bash
git clone https://github.com/yourusername/anki-gap-finder.git
cd anki-gap-finder
pip3 install -r requirements.txt
```

> **Note:** StressRNN is not on PyPI and installs directly from GitHub. This is handled automatically by `requirements.txt`.

---

## Usage

### Desktop app

```bash
python3 run_app.py
```

1. Select your source file (PDF, TXT, EPUB, or HTML)
2. Optionally select your existing Anki deck (.apkg) to filter out known words
3. Choose sort order (alphabetical or by frequency in your text)
4. Choose output format (.apkg, CSV, or JSON)
5. Click **Find Gaps & Generate Cards**
6. Review the results table — delete any rows you don't want
7. Click **Export**
8. Drag the `.apkg` into Anki to import

### CLI

Edit the config at the top of `main.py`, then:

```bash
python3 main.py
```

---

## Supported Input Formats

| Format | Notes |
|---|---|
| PDF (`.pdf`) | Must have selectable text — scanned PDFs without OCR won't work |
| Plain text (`.txt`, `.md`) | Any UTF-8 text file |
| EPUB (`.epub`) | Project Gutenberg and other ebook sources |
| HTML (`.html`, `.htm`) | Web pages, Project Gutenberg HTML downloads |

---

## Output Formats

| Format | Use case |
|---|---|
| `.apkg` | Direct import into Anki with audio |
| `.csv` | Review in Excel/Numbers, import into other tools |
| `.json` | Programmatic use, custom pipelines |

---

## Design Decisions

**Why lemmatize both sides?**
Russian is a highly inflected language — a single verb can appear in dozens of forms. Comparing raw words would miss matches like "читал" (your deck) vs "читаете" (the book). Reducing both sides to their dictionary form before comparing ensures accurate gap detection.

**Why use StressRNN instead of a dictionary lookup?**
Online sources like Wiktionary impose rate limits that break on large vocabularies (hundreds of 429 errors per run in testing). StressRNN runs fully offline using a bundled Zaliznyak dictionary and a BiLSTM model — no network required, no rate limiting, consistent speed.

**Why pull example sentences from the source text?**
Wiktionary example sentences are often literary or archaic. Pulling sentences directly from your textbook or novel means every example is in the exact vocabulary register you're trying to learn, and is more likely to be a sentence you've already encountered.

**Why not use a server for licensing?**
License keys are HMAC signatures of the buyer's email — validated locally without any network call. A small serverless webhook (Vercel free tier) generates and emails the key at purchase time. No database, no monthly costs, no server to maintain.

**Why pause for review?**
Automatic translations are imperfect, especially for words with multiple meanings. The review step catches translation errors and removes any garbage fragments before they become cards. At the quality level this tool produces, review takes 5–10 minutes for a full book run.

---

## Limitations

- **PDF quality dependent** — Scanned PDFs without OCR will produce no output. The text must be selectable.
- **Translation quality** — Single-word translation is inherently ambiguous. Words with multiple meanings (e.g. `мир` = "world" or "peace") get one translation. The review step is the opportunity to fix these.
- **Rate limiting** — Google Translate and gTTS are free services with no API key, so very large runs (1000+ words) may occasionally hit limits. The script includes a delay between calls to mitigate this.
- **Stress accuracy** — StressRNN is highly accurate but not perfect, especially on rare or very short words. Incorrect stress marks are visible in the review table before export.

---

## License

MIT
