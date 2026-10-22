# Experiment: Replication of Markowitz (2024), Study 2

A jsPsych version of Study 2 from Markowitz (2024, *PNAS Nexus*), "From complexity to clarity: How AI enhances perceptions of scientists and the public's understanding of science."

## Files

| File | What it does |
|---|---|
| `index.html` | The experiment (jsPsych 8, loaded from unpkg, like the course template). |
| `stimuli.js` | The five stimulus pairs: the published PNAS significance statement and the GPT-4 significance statement for each paper. |

## Design

- Each participant reads **3 of the 5** stimulus pairs, in random order.
- For each pair, the **GPT (simple)** or **PNAS (complex)** version is chosen at random, separately for each pair.
- After each summary, participants answer 8 questions on 7-point scales, one per screen, with the summary shown above each question:
  - manipulation checks: clear, complex, understand
  - author perceptions: intelligent, credible, trustworthy
  - author identity: human, AI
- The order of the three measure blocks, and the order of the items within each block, is randomized for every summary, as in the original.
- Then come demographics (age, gender, race/ethnicity, political ideology), a debrief, and a thank-you screen.

## Run it locally

From this folder:

```
python3 -m http.server 8000
```

Then open http://localhost:8000 in Chrome. Without a real DataPipe experiment ID, the data will not save, and the console will show a DataPipe error at the end. That is expected.

## Where the data go

The DataPipe extension saves one CSV per participant when the experiment ends. The file is named `<subject_id>.csv` and lands in your UCSD Google Drive under `My Drive/DataPipe/<experiment name>/`. Paste your DataPipe experiment ID in place of `PASTE_YOUR_ID_HERE` in `index.html`, and turn on **Accept new data** in the DataPipe dashboard.

Each row is one screen. The main columns are:

| Column | Meaning |
|---|---|
| `subject_id` | Random 10-character participant ID (also the file name). |
| `stimulus_id` | PNAS article number. The DOI is 10.1073/pnas.`stimulus_id`. Matches `stim` in the original `experimental_data_OSF.csv`. |
| `version` | `gpt` (simple) or `pnas` (complex). |
| `trial_in_block` | Position of this summary for this participant (1-3). |
| `measure` | `clear`, `complex`, `understand`, `intelligent`, `credible`, `trustworthy`, `human`, `ai`; `read` for the reading screen; or `consent`, `instructions`, `age`, `gender`, `ethnicity`, `ideology`, `debrief`. |
| `response` | Rating from 1 to 7, or the demographic answer. |
| `rt` | Response time in ms. |
| `measure_block`, `item_position` | Which measure block the item came from, and its position (1-8) among the questions for that summary. |
| `pair_order` | The participant's three pairs and versions, in order. |

To get the original wide format (one row per participant x summary), filter out the `read` rows and other non-rating rows, then pivot `measure` to columns by `subject_id` and `stimulus_id`.

Do not commit participant data to this public repository.
