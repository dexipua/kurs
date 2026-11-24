# Data model

All documents live in the tracker's database (`ArtifactData`, `url` = the tracker link).

| Collection / doc | Fields |
|---|---|
| `settings/main` | `name`, `about`, `tone` (`soft` \| `balanced` \| `strict`), `examMode` (bool), `threshold` (% of daily habits for the day to count), `areas` [`{id, name, color}`], `reminders` (text shown in Settings) |
| `habits/<id>` | `title`, `area`, `freq` (`daily` \| `weekly`), `target` (times a week for weekly), `minutes`, `keepInExams` (bool), `active` (bool), `order` |
| `schedule/<date>_<HHMM>_<hash>` | `title`, `place`, `date`, `start`, `end` ("HH:MM", empty for all-day), `kind` (`class` or `exam`), `link` (https join URL for Zoom/Meet/Teams from the event; shown as a «🎥 Зайти» button, non-https ignored), `shelter` (air-raid shelter from «Укриття: …» in the event description, e.g. "S05, S06"; empty if none), `source` (`ics` from the page's import, `gcal` when Claude syncs Google Calendar) |
| `days/<YYYY-MM-DD>` | `date`, `done` {`<habitId>`: true, `penalty`: true}, `focus` (minutes), `light` (true = only core habits count) |
| `journal/<YYYY-MM-DD>` | `date`, `text`, `mood` (1–5 or null), `at` (ISO time). Private diary: read it only to coach (evening/weekly), never quote it outside the page. |
| `tasks/<id>` | `title`, `area`, `due` (`YYYY-MM-DD` or null), `priority` (`high` \| `normal`), `notes`, `done`, `doneAt`, `createdAt` |
| `goals/<id>` | `title`, `area`, `deadline`, `why`, `steps` [`{t, done}`] |
| `notes/<id>` | `at` (ISO time), `kind` (`morning` \| `evening` \| `weekly` \| `page`), `text` |

Reminders write notes with ids `morning-<date>`, `evening-<date>`, `weekly-<date>`; an existing id means that run already happened.

Exam mode keeps only habits with `area: "study"` or `keepInExams: true`, and only study tasks on the Today view.

## Default areas

```json
[{"id":"career","name":"Кар'єра","color":"#3A6EA5"},{"id":"study","name":"Навчання","color":"#7A5CC4"},
 {"id":"books","name":"Книги","color":"#B7841B"},{"id":"body","name":"Тіло","color":"#2F7D4F"},
 {"id":"mind","name":"Розум","color":"#1D6F80"},{"id":"life","name":"Життя","color":"#B4471F"}]
```

## Starter ideas (pick and adapt, never all of them)

- Daily: повторити матеріал пар 30 хв (study, keepInExams), англійська 15 хв (career), 10 сторінок книги (books), прогулянка або 7000 кроків (body, keepInExams), лягти до 00:00 (body, keepInExams), одна швидка партія в шахи 10 хв (mind).
- Weekly: тренування 3× 45 хв (body), задача на LeetCode 2× (career), 10 хв без телефона 3× (mind), поспілкуватися з другом 2× (life), приготувати щось нове 1× (life).
- Goals: one big goal for the next 6–9 months split into 5–8 steps; a reading goal listing the actual books; a 12-week body goal; «сильна сесія» for students.
- Tasks: small first steps with due dates in the next 1–3 weeks, so the first days already have something to close.

## roadmap/<id>

Optional internship map. `{order: number, title, when: string, why: string, items: [{t, done}]}`. Stages render in `order`; the first stage with an unfinished item is the current one.

## books/<id>

`{title, author, status: "read"|"reading"|"want", theme, rating: 1-5|null, idea, finishedAt: ISO|null, before: bool}`. `before: true` marks books read before the tracker (no XP).
