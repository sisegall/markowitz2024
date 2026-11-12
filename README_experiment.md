# AI Explanations and Perceptions of Scientists: A Replication Study

A jsPsych version of Study 2 from Markowitz (2024, *PNAS Nexus*), "From complexity to clarity: How AI enhances perceptions of scientists and the public's understanding of science."

## Files

| File | What it does |
|---|---|
| `index.html` | The experiment (jsPsych 8, loaded from unpkg, like the course template). |
| `stimuli.js` | The five stimulus pairs: the published PNAS significance statement and the GPT-4 significance statement for each paper. |

## Design

- Each participant is randomly assigned to either the no-label or AI-labeled condition.
- After random assignment to condition, each participant reads *5* summaries in a random order. 
- Of the five summaries, two are AI generated and two are actual article summaries from the journal PNAS, and the fifth summary is random where it could either be an AI or PNAS summary.
- After each summary, participants answer 8 questions on 7-point scales, with one question per screen and the summary shown above each question:
  - manipulation checks: clear, complex, understand
  - author perceptions: intelligent, credible, trustworthy
  - author identity: human, AI
- After that, participants answer one multiple choice question about each summary and write an open-ended explanation to articulate the summaries they read. There is one multiple choice question and free-response question for each of the five summaries.
- Then come demographics (age, gender, race/ethnicity, political ideology), a debrief, and a thank-you screen.

## Run it locally

From this folder:

```
python3 -m http.server 8000
```

Then open http://localhost:8000 in Chrome. 

## Where the data go

The DataPipe extension saves one CSV per participant when the experiment ends in Google Drive. The file is named `<subject_id>.csv`.

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

Every question requires an answer before Continue works, so `response` is never empty on question rows.

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
| `honeypot_triggered` | `true` if the answer contains the hidden code word "periwinkle". The `ai_example_coursework` prompt has white, invisible text telling AI agents to include that word. |

The `debrief` row's `blur_count` is the number of times the participant left the tab or window over the whole study.

[note to self:] to get original wide format (one row per participant x summary), filter out the `read` rows and other non-rating rows, then pivot `measure` to columns by `subject_id` and `stimulus_id`.
