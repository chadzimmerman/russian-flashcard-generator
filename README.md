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

1. **Extracts vocabulary from your source file** — Supports PDF, plain text (.txt/.md), EPUB, and HTML. Reduces every word to its base (dictionary) form, so "читал", "читала", and "читаете" all resolve to "читать" before comparison.

2. **Reads your existing Anki deck** — Opens your `.apkg` file and builds a set of every word you already know.

3. **Finds the gaps** — Every word in your text that isn't in your deck.

4. **Adds stress marks** — Offline stress prediction using a Zaliznyak dictionary model. Produces носи́ть, not носить.

5. **Pulls example sentences from your text** — For every gap word, finds the first complete sentence in your source that contains it. Examples are always relevant to what you're actually reading.

6. **Translates via Google Translate** — No API key required.

7. **Generates audio** — Google Text-to-Speech produces an MP3 for every word. Auto-plays on the card front in Anki.

8. **Adds grammar notes** — Part of speech, verb aspect (perfective/imperfective), gender, animacy. Shown on the back of each card.

9. **Review before export** — A clean table lets you inspect every word and delete anything that looks wrong before generating cards.

10. **Exports to Anki** — Produces an `.apkg` file with all cards and audio bundled, ready to drag into Anki. Also exports CSV and JSON.

---

## Card Format

| Field | Content |
|---|---|
| **Front** | Russian word with stress mark (e.g. `носи́ть`) + audio |
| **Front (below word)** | Example sentence from your source text |
| **Back** | English translation |
| **Back (below translation)** | Grammar notes (e.g. `verb · imperfective · transitive`) |

---

## Download & Install

### macOS

1. Download `AnkiGapFinder.dmg` from the [website](https://yourusername.github.io/anki-gap-finder)
2. Open the `.dmg` file
3. Drag **Anki Gap Finder** into your Applications folder
4. Open it from Applications or Spotlight

> **First launch:** macOS may show a security warning because the app isn't from the App Store. Go to **System Settings → Privacy & Security** and click **Open Anyway**.

### Windows

1. Download `AnkiGapFinder-Setup.exe` from the [website](https://yourusername.github.io/anki-gap-finder)
2. Run the installer and follow the prompts
3. Launch **Anki Gap Finder** from the Start menu or desktop shortcut

### Linux

1. Download `AnkiGapFinder.AppImage` from the [website](https://yourusername.github.io/anki-gap-finder)
2. Make it executable: `chmod +x AnkiGapFinder.AppImage`
3. Run it: `./AnkiGapFinder.AppImage`

---

## How to Use

### Step 1 — Prepare your files

- **Source file:** your Russian textbook, novel, or article in PDF, EPUB, TXT, or HTML format
- **Anki deck:** export your existing Russian deck from Anki via **File → Export → Anki Deck Package (.apkg)**

### Step 2 — Run the app

1. Open **Anki Gap Finder**
2. Click **Browse** next to "Source file" and select your file
3. Click **Browse** next to "Anki deck" and select your `.apkg` export *(optional — skip this to get all words in the text)*
4. Choose your sort order: **Alphabetical** or **Frequency** (most common words first)
5. Choose your output format: **apkg**, **csv**, or **json**
6. If exporting to Anki, enter a name for your new deck
7. Click **Find Gaps & Generate Cards**

### Step 3 — Review the results

When processing finishes, a table shows every gap word with its stressed form, translation, grammar notes, and example sentence.

- Click a row to select it
- Press **Delete** or **Backspace** to remove selected rows
- **Cmd+A** (Mac) / **Ctrl+A** (Windows) to select all

Remove anything that looks wrong before exporting.

### Step 4 — Export

Click **Export**. The output file is saved next to your source file.

- **For .apkg:** drag the file into Anki to import the deck
- **For .csv:** open in Excel, Numbers, or Google Sheets
- **For .json:** use programmatically or import into other tools

---

## Supported Formats

| Input | Notes |
|---|---|
| PDF (`.pdf`) | Text must be selectable — scanned PDFs without OCR won't work |
| Plain text (`.txt`, `.md`) | Any UTF-8 text file |
| EPUB (`.epub`) | Project Gutenberg and other ebook sources |
| HTML (`.html`, `.htm`) | Web pages, Project Gutenberg HTML downloads |

---

## Pricing

| Plan | Price | Limit |
|---|---|---|
| Free | $0 | Up to 10 pages per file |
| Lifetime | $5 once | Unlimited pages, all future updates |

To unlock the full version, click **Buy — $5 lifetime** in the app or on the [website](https://yourusername.github.io/anki-gap-finder). After purchase you'll receive a license key by email. Enter it under **Activate license** in the app.

---

## Tips

- **First run is slower** — the stress model loads on startup. Subsequent runs are faster.
- **Audio is cached** — if you re-run on the same text, existing audio files are reused instantly.
- **Network required for translation and audio** — if your internet drops mid-run, affected words will have blank translations. You can fill these in manually in the review table before exporting.
- **Re-run after editing your deck** — as your Anki deck grows, re-running on the same book will find fewer and fewer gaps.

---

## Limitations

- Scanned PDFs without OCR will produce no output
- Translation is single-word and may be ambiguous for words with multiple meanings (e.g. `мир` = "world" or "peace") — use the review step to correct these
- Stress prediction is highly accurate but not perfect on rare or very short words

---

## License

MIT
