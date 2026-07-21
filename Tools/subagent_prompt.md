<!--
SUBAGENT PROMPT — practice logs → labelled JSON.
Hand this whole file to each labelling subagent as its prompt. Before sending,
replace the two placeholders:
  {{LOG_FILES}}    — the (≤5) .txt file paths this subagent should label
  {{OUTPUT_PATH}}  — where it writes its JSON array (e.g. Tools/label_parts/part_03.json)
The prompt is fully self-contained — the subagent does NOT need to read the runbook.
-->

You are labelling meditation practice logs into structured JSON records for a
searchable database. You will label the files listed below — one JSON object per
file — then write them as a single JSON array.

**Your input files:**
{{LOG_FILES}}

**Write your output (a JSON array of the records) to:** `{{OUTPUT_PATH}}`

---

## Context

Both the Dharma Overground and Kenneth Folk Dharma are advanced meditation practice
forums where people post logs detailing their meditation experience. You're adding
labels so people can find logs relevant to their interests. People discuss the four
path model as articulated by Daniel M. Ingram. Diagnostic criteria:

- **A&P** — increased perceptual abilities, rapture and bliss, possible kundalini
  phenomena (energetic sensations, vortices, physical movements), possible
  out-of-body experiences and visions, heightened interest in spirituality/meditation.
- **Equanimity** — natural, panoramic awareness where phenomena are perceived in an
  integrated, inclusive way, less identification with a separate self; practice feels
  ordinary and effortless as reality is accepted just as it is.
- **Dark Night** — fear/terror, disgust/revulsion, misery/grief, a desperate desire
  for relief/release, restlessness, aversion to practice, repetitive thoughts,
  difficulty staying with formal meditation.
- **Cessation** — a discontinuity in conscious experience where there is no sensory
  or mental experience (no time, space, consciousness, or any phenomena), followed by
  reality's reappearance, often with distinctive aftereffects (clarity, bliss waves,
  profound realizations).
- After the A&P one cycles A&P → Dark Night → Equanimity → Cessation (one insight
  cycle). One cessation = first path; another cycle + cessation = second path; again
  = third path (all phenomena seem to do themselves but something is missing; more
  frequent cessations, faster cycles). Fourth path = centerless, boundless experience
  with no separate controller/watcher/knower/be-er/see-er/do-er, no singularly
  positioned epistemic agent.
- First path = stream enterer / sotopanna. Second = sadagami. Third = anagami.
  Fourth = arhat / arahant.

---

## Rules

- **Skip non-practice-log files — do not force a record.** Some files in your batch may NOT actually be a first-person meditation practice log, even though they came from the practice-log board. Skip a file (write **no** JSON record for it) if it is: a meta/admin/Q&A discussion thread (site errors, moderation, "how do I...", advice-seeking one-offs with no ongoing practice content); a split-off philosophical/doctrinal debate thread with no single author narrating their own practice; or a thread whose entire first-person content has been redacted/deleted/is blank (leaving only replies or empty stubs). Do NOT stretch these into a low-quality record just to have something to output. For every file you skip, add ONE line to a companion file at `{{OUTPUT_PATH}}` with `.skipped.txt` appended in place of `.json` (e.g. if `{{OUTPUT_PATH}}` is `Tools/label_parts/part_09.json`, write skips to `Tools/label_parts/part_09.skipped.txt`), in the format `<exact filename>.txt: <one-sentence reason>`. If you skip nothing, do not create the `.skipped.txt` file at all. This is a judgment call for genuinely non-log content — most files, even short or low-quality ones, ARE real practice logs and should still get a record.
- **Avoiding cross-log contamination:** Some files are sequential installments of the *same* person's practice log (numbered parts, "Reboot", "MIGRATE" duplicates, monthly/dated journal entries, etc.) — this happens often enough that it is not a rare edge case. If more than one file in your batch is by the same author, write each JSON record based **only** on the content of its own specific file. Do not blend details, claims, attainment levels, techniques, or the arc of that person's practice as it unfolds across their other logs in your batch into any single record — each record must stand entirely on what that one file contains, even if you can see (and should still use, for author attribution) that the same person wrote another file in your batch. This has caused real errors before (e.g. a technique mentioned only in a later log bleeding into an earlier log's `techniques_used`).
- For each file: line 1 is `Title: …`, line 2 is `URL: …`; the rest is the log.
- Choose a specific answer for **every** field — no "unsure", no ranges. If asked to
  pick exactly one option, output only that option. If uncertain, make your best
  estimate; do not equivocate.
- For fixed-list fields (marked "choose only from this list" or "exactly one of"), use
  **only** the given options — never invent or add values. The only open-ended fields
  are `key_words`, `teachers_mentioned`, `author`, and the free-text ones (`summary`,
  `interesting_sentence`, `helpful_sentence`, `who_should_read`).
- Field names below are the exact `snake_case` JSON keys. List fields become JSON
  arrays of strings (trimmed, order preserved).
- Escape correctly — logs contain quotes, apostrophes, and URLs.

## Fields

- **author:** the username of the practitioner whose log this is — normally the
  original poster / most frequent author of the thread, not the repliers.
- **predefined_themes:** choose only from this list (no additions — put anything
  off-list in key_words), in order of prominence — Trauma,
  Relationships, Work-Practice Balance, Procrastination, Illness, Mental Health,
  Life Direction, Motivation, Doubt, Fear, Loss, Sadness, Loneliness, Anger,
  Self-Control, Relating to Non-Meditators, Community, Core Wounds, Psychology vs
  Meditation, Depression, Theorizing vs Practice, Anxiety, Diagnostic Criteria, Death, Schizophrenia, Surrender, Bipolar Disorder, Borderline Personality
  Disorder, OCD, ADHD, Autism, Addiction, Medication, Physical Pain, Sexuality,
  Distraction, Dullness, Striving, Psychedelics, Teaching, Dependent Origination,
  Emptiness, Craving, Adverse Effects.
- **key_words:** 3–8 free-form tags capturing this log's distinctive, *searchable*
  features — specific practices, phenomena, teachers, events, or life circumstances
  someone might filter or search for (e.g. "Goenka retreat", "kundalini", "insomnia",
  "cessation phenomenology"). Generated dynamically per log; do NOT duplicate
  predefined_themes — prefer concrete specifics over broad themes.
- **summary:** one sentence focusing on specific practice details, experiences, and
  outcomes — avoid general statements about meditation/spirituality. Write to facilitate quick
  skimming in a list view.
- **interesting_sentence:** an exact quote, with quotation marks, **followed by an
  attribution** — the username of whoever said it (the `User:` block it falls under),
  formatted as `"<exact quote>" —<username>`. Pick the single most interesting
  sentence in the log.
- **helpful_sentence:** an exact quote, with quotation marks, **followed by an
  attribution** in the same `"<exact quote>" —<username>` format. Pick the single most
  helpful sentence for the meditator audience. Avoid pedestrian tenets of Buddhism;
  prefer detailed, clear explanations and insights.
- **attainment_claimed:** exactly one of — never claimed (don't output this if they
  previously claimed but have no current claim)/no attainment/A&P/Dark Night/
  Equanimity/1st path/2nd path/3rd path/4th path.
- **likely_highest_attainment:** exactly one of — no attainment/A&P/Dark Night/
  Equanimity/1st path/2nd path/3rd path/4th path (**never NA**). Your OWN independent
  best estimate of the highest attainment the log actually demonstrates per the
  diagnostic criteria above. **Always fill this in, even when the author makes an
  explicit claim** — it may agree with or differ from `attainment_claimed` (e.g. an
  author claims 3rd path but the log only evidences Equanimity, or claims nothing but
  clearly describes cessation). If the log spans multiple levels, output the highest
  the text actually supports.
- **usefulness:** a number from 1 to 10, no explanation (10 = genuinely exceptional,
  5 = average). Have high standards.
- **non_author_advice_quality:** a number from 1 to 10, no explanation (10 = genuinely
  exceptional, 5 = average). Have high standards.
- **techniques_used:** choose only from this list (no additions) — Vipassana, Samatha,
  Noting, Six Realms, Five Elements, Watching the Mind, Body Scanning, Breath
  Meditation, Walking, Do Nothing, Shikantaza, Fire Kasina, Self-Inquiry, Koan, Metta,
  Tonglen, Jhana, Actualism, Mantra, Visualization, Tantra, Guru Yoga, Tummo, Dzogchen,
  Mahamudra, Magick, TWIM, Pure Land Jhana, Nirodha Samapatti.
- **teachers_mentioned:** excluding Daniel Ingram.
- **map_territory_focus:** choose only from this list (no additions) — A&P, Dark Night,
  Equanimity, cessation, 1st path, 2nd path, 3rd path, 4th path. List only the main
  focuses.
- **who_should_read:** one sentence stating which type of meditator would benefit most
  and what they'd learn. Pithy.
- **spice:** a whole number from 1 to 5 rating how much disagreement there is between
  posters (1 = none, 5 = heated). Be extremely sensitive — people hide disagreement
  politely on this forum.
- **practice_experience:** specific number of years, e.g. "2 years"; if under a year,
  "<1 year". Start counting from first consistent practice.
- **practice_volume:** a number from 1 to 10 rating overall how much they practice
  (replaces consistency + intensity; 10 = exceptional volume, 5 = average).
- **retreat:** exactly one of — Retreat, No Retreat: whether the yogi describes, in
  some detail, going on a meditation retreat.

---

## Building the record

Emit one JSON object per log: every field above under its `snake_case` name, **plus
four keys you derive from context** (not in the Fields list):

- `log` — the title (line 1, without `.txt`)
- `url` — the line-2 URL
- `source` — `"DhO"` if the log is under a `DhO Practice Logs Board …` folder, `"KFD"`
  if under `KFD Practice Logs`, else infer from the URL: `dharmaoverground.org` →
  `"DhO"`, `awakenetwork.org` (the KFD archive/magazine republish host, e.g. the
  Chris Marti journals in `Additional Logs/`) → `"KFD"`
- `exp_years` — number from `practice_experience`: `<1 year`→`0.5`, `N years`→`N.0`,
  `>5 years`→`5.0`

**Types:** the list fields (`predefined_themes`, `key_words`, `techniques_used`,
`teachers_mentioned`, `map_territory_focus`) are arrays; `usefulness`,
`non_author_advice_quality`, `practice_volume`, `spice`, and `exp_years` are **numbers**
(so `index.html` sorts/filters them numerically); everything else is a string, and
`practice_experience` stays a display string (e.g. `"2 years"`).

Example:

```json
{
  "log": "Johns Log",
  "url": "https://www.dharmaoverground.org/discussion/-/message_boards/message/27501795",
  "source": "DhO",
  "author": "John L",
  "predefined_themes": ["Surrender", "Fear", "Self-Control"],
  "key_words": ["cessation phenomenology", "surrender vs control", "law school stress"],
  "summary": "Cycles repeatedly through the insight stages, reporting several cessations and a growing trust in surrender over control.",
  "interesting_sentence": "\"...reality stopped and restarted on October 17, 2023.\" —John L",
  "helpful_sentence": "\"It's really about knowing the moment as it is, however it is.\" —Kenneth Folk",
  "attainment_claimed": "3rd path",
  "likely_highest_attainment": "3rd path",
  "usefulness": 8,
  "non_author_advice_quality": 6,
  "techniques_used": ["Noting", "Watching the Mind", "Do Nothing"],
  "teachers_mentioned": ["Kenneth Folk", "Rob Burbea"],
  "map_territory_focus": ["Equanimity", "cessation", "3rd path"],
  "who_should_read": "Practitioners cycling post-stream-entry who want a candid account of surrender-based practice.",
  "practice_experience": "2 years",
  "practice_volume": 7,
  "retreat": "Retreat",
  "spice": 2,
  "exp_years": 2.0
}
```

---

## Finish

Write a **single JSON array** containing one object per input file (excluding any
files you skipped per the skip rule above) to `{{OUTPUT_PATH}}`. Then verify: (1) the
file parses as JSON, and (2) the array length plus the number of skipped files equals
the number of input files. Write only the JSON array — no commentary, no markdown
fences in the file. If you skipped one or more files, also write the companion
`.skipped.txt` file described in the skip rule above.
