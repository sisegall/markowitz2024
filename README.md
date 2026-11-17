# Replication of Markowitz (2024), Study 2

A PSYC 201A (UCSD) replication of Study 2 from Markowitz (2024, *PNAS Nexus*), "From complexity to clarity: How AI enhances perceptions of scientists and the public's understanding of science." This repo was created from the PSYCH 201A replication project template.

Participants read short summaries of five real PNAS papers. For each paper they see either the published PNAS significance statement (complex, human-written) or a GPT-4 version (simpler). They then rate the summary and its author. The confirmatory test is whether the scientist is rated as more **trustworthy** after a GPT summary than after a PNAS summary.

- Original paper: `original_paper/Markowitz_2024_PNASNexus_AI_science_summaries.pdf`
- Original data and materials (OSF): <https://osf.io/64am3/>
- Original preregistration (Study 2): <https://aspredicted.org/zq9qv4.pdf>
- This replication's preregistration and archived materials: see the writeup.

## Files

| File | What it does |
|---|---|
| `index.html` | The experiment (jsPsych 8, loaded from unpkg, like the course template). |
| `stimuli.js` | The five stimulus pairs: the published PNAS significance statement and the GPT-4 significance statement for each paper, verbatim from the original OSF materials. |
| `analysis/analyze-ratings.qmd` | The confirmatory analysis and robustness check, with every step explained. |
| `Replication_Report_Template.qmd` | The writeup: introduction, methods, power analysis, analysis plan, and (after data collection) results. |

**Raw data are not in this repo.** They are in the project's Google Drive folder, shared with the project TA and Bria Long. The analysis reads them from a folder next to the repo, `../replication of Markowitz 2024/`, with one subfolder per phase (`pilot A`, `pilot B`, `main`).

## Design

- Within-subjects, one factor (summary version) with two levels: `gpt` (simple) and `pnas` (complex).
- Every participant reads one summary for each of the five papers, in a random order.
- Version is balanced within each participant: two summaries are GPT, two are PNAS, and the fifth is randomly GPT or PNAS. Everyone sees a 3/2 split, never all of one version.
- Each summary first appears on its own screen. Then the participant answers 8 questions on 7-point scales, one question per screen, with the summary shown above each question:
  - manipulation checks: clear, complex, understand
  - author perceptions: intelligent, credible, trustworthy
  - author identity: human, AI
- The order of the three blocks is randomized for each summary, and so is the order of items within each block.
- Next comes an exploratory questionnaire about AI use and attitudes: select-all questions, 1-7 agreement statements (12, plus an attention check mixed in), sliders, and three open-ended questions.
- Then demographics (age, gender, race/ethnicity, year in school, major, native language, political ideology) and a debrief. After the debrief, participants are sent back to SONA for credit.

## Run it locally

From the folder with `index.html`:

```
python3 -m http.server 8000
```

Then open http://localhost:8000 in Chrome.

Before launching, check `study_phase` in `index.html`: `"pilotA"` (classmates), `"pilotB"` (a few SONA participants), or `"main"` (real data).

## Where the data go

The DataPipe extension saves one CSV per participant to Google Drive when the experiment ends. Files are named `<study_phase>_<subject_id>.csv`, e.g. `pilotB_xssfkg4cx7.csv` or `main_xssfkg4cx7.csv`. (Pilot A files, from an earlier version, are named `<subject_id>.csv`.) DataPipe also saves `.partial.json` backups while someone is still in the study; these are from people who had not finished and are not analyzed.

No SONA IDs are saved. The SONA code is used only to send the participant back to SONA for credit.

Each row is one screen. The main columns are:

| Column | Meaning |
|---|---|
| `subject_id` | Random 10-character participant ID (also in the file name). |
| `study_phase` | `pilotA`, `pilotB`, or `main`. |
| `stimulus_id` | PNAS article number. The DOI is 10.1073/pnas.`stimulus_id`. Matches `stim` in the original `experimental_data_OSF.csv`. |
| `version` | `gpt` (simple) or `pnas` (complex). |
| `trial_in_block` | Position of this summary for this participant (1-5). |
| `measure` | What the row is: `read` for a reading screen; `clear`, `complex`, `understand`, `intelligent`, `credible`, `trustworthy`, `human`, `ai` for the ratings; `attention_check`; the AI questionnaire items (names starting with `ai_`); `consent`, `instructions`, the demographics (`age`, `gender`, `ethnicity`, `year_in_school`, `major`, `native_english`, `ideology`), or `debrief`. |
| `response` | The answer: a 1-7 rating, a 0-100 slider value, the chosen option(s) (several choices are joined with "; "), or typed text. |
| `rt` | Time on the screen in ms. On `read` rows, this is reading time. |
| `time_elapsed` | Time since the study started, in ms. On the `debrief` row, this is the total time. |
| `measure_block`, `item_position` | For ratings: which block the item came from, and its position (1-8) among the questions for that summary. |
| `pair_order` | The participant's five pairs and versions, in order. |

### Attention check

`attention_check` asks participants to select "Strongly disagree". A response of `1` passes.

### AI / bot checks

On the open-ended text rows (`ai_example_coursework`, `ai_disasters`, `ai_elaborate`):

| Column | Meaning |
|---|---|
| `keystrokes` | Character keys pressed in the box. Much lower than `response_length` means the text was pasted or inserted, not typed. |
| `backspaces` | Backspace/Delete presses. Real typing almost always has some. |
| `paste_count`, `pasted_chars` | Number of pastes into the box and total characters pasted. |
| `response_length` | Characters in the final answer. |
| `first_key_ms` | Time from the page appearing to the first keypress. |
| `typing_ms` | Time from first to last keypress. |
| `median_key_gap_ms` | Median time between keypresses. Very fast and very even gaps suggest scripted typing. |
| `blur_count` | Times the participant left the tab or window while on this question (e.g. to use a chatbot). |
| `honeypot_triggered` | `TRUE` if the answer contains the hidden code word "periwinkle". The `ai_example_coursework` prompt has white, invisible text telling AI agents to include that word. |

The `debrief` row's `blur_count` is the number of times the participant left the tab or window over the whole study.

## Analysis

`analysis/analyze-ratings.qmd` runs the preregistered analysis:

- **Confirmatory test:** a linear mixed model of trustworthiness on version, with random intercepts for participant and stimulus, using everyone who completed the study (no exclusions, as in the original). If the fit is singular, stimulus is entered as a fixed effect instead.
- **Robustness check:** the same model after excluding participants who failed the attention check, read 3 or more summaries in under 10 seconds, show signs of AI on the open-ended questions, or took under 5 or over 60 minutes.

To run it, open the R project, set the data folder in the `list.files()` line (`"pilot B"` or `"main"`), and run the chunks from the top.
