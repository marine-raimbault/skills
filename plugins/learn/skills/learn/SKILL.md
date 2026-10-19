---
name: learn
description: Learn from a plan, a design, finished work or a plain topic — one plain-language question at a time, the user answers before seeing yours, each answer is tied to an idea from the classic software reliability books, and what was hard becomes Anki cards. `ready <PR>` checks the user can defend their own change before asking for review. Use when the user runs /learn on an issue, plan, PR, piece of code or topic, /learn ready on a PR they will ask a review for, or /learn review to go through due Anki cards.
disable-model-invocation: true
---

Work through the topic with the user until they understand it — a plan still to be decided, work already done, or a plain topic ("vue routing"). The user is here to learn; when the topic is a plan, they also decide. Learning comes from the user committing to an answer before seeing yours, and from ideas taken from the canonical books — never from a concept label stuck on a question. What the user learned is kept in Anki, so it lasts.

`/learn review` skips sections 1–5: go straight to [Review due cards](#7-review-due-cards).

`/learn handoff <text>` follows [Catch up on a handoff](#8-catch-up-on-a-handoff) instead of sections 2–5.

`/learn ready <PR>` follows [Ready check](#9-ready-check) instead of sections 2–6.

`/learn` with no argument, or `help`, prints this card and the [Anki setup](#anki-setup) and stops — no fact gathering, no question:

```
/learn <issue | PR | plan | code>   learn from work, decide on plans
/learn <topic>                      e.g. /learn vue routing
/learn handoff <text>               catch up on a teammate's handoff, ~20 min
/learn ready <PR>                   can I defend this change before I ask for review?
/learn review                       due Anki cards, then new learn cards
/learn help                         this card, and how to set up Anki

During a session:
  "I don't get it"   explain until clear, then continue
  "skip"             next question, no answer revealed
  "stop"             end now, cards are still proposed
  "ready?"           on someone else's open PR: can I approve it yet?

In review, like Anki:
  type your answer, or "show" to just reveal
  then 1 Again · 2 Hard · 3 Good · 4 Easy ("ok" = my suggestion)
```

The same words work at any point of a session: "skip" moves on without revealing, "stop" jumps to section 5 or 6, "help" prints the card again, "ready?" gives the [approval verdict](#approval-verdict).

Every code reference you write in chat — briefing, questions, reveals, explanations, ready check — is the short file name with its line, `topic.ts:8`: enough to find it with Cmd+P. Never write the full path, absolute or relative; it is not clickable in every terminal, and the session's working directory can change. When two files in the topic share a name, add the parent folder: `routes/topic.ts:8`.

## 1. Gather facts first

Finding facts is your job, never the user's. Read the issue, the code, the diff, related tickets before asking anything. Dispatch a sub-agent for broad searches. Never ask the user something you could look up.

When the argument is a PR the user opened themselves (with GitHub: its author equals `gh api user -q .login`) and it is not merged yet, ask once before the briefing: *"This is your own open PR — run the [Ready check](#9-ready-check) instead, to make sure you can defend it before the review?"* Go on with the session if they say no.

When the argument is, or links to, an open PR someone else opened, list for yourself the changes its diff makes, one line each, and track which ones the user explains correctly during the session.

## 2. Open with a briefing

Before the first question, write a **Briefing** the way you would explain it to a junior colleague: three short paragraphs in plain words.

- **Before:** how it worked until now, and why that hurt.
- **Now:** what changes, as one mechanism they can picture; for a plan, also what is already done and what is left.
- **In practice:** the one line they will use or remember, e.g. "mount, await the helper, assert".

Name the one or two things they will meet in the code, such as a function or a command; keep the three paragraphs free of file paths and other jargon. Add an everyday analogy only when the topic stays abstract.

For a PR or code change — not a plan, not a plain topic — add a **Core file** block after the three paragraphs: the name of the one file where the mechanism from **Now** lives, then its 3–5 central lines, each quoted as `file:line` and one plain sentence on what it does. Pick the file whose lines carry that mechanism, not the biggest file in the diff. One file only, never a file list: the learner needs one place to stand on before the first question. This block is the only place in the briefing where a path appears.

When the topic changes behaviour — a PR, a regex, a rule, a config — add a **Before/after table**: 3–5 real inputs taken from the code or its tests, never invented, so the learner can find them again. Columns `input | before | now`; the rows whose result changed in bold. Under the table, two lines: `Rule before:` and `Rule now:`, one plain sentence each. Lay it out for a phone, as the card tables in [section 6](#6-save-cards-to-anki) are. A plan with no "before", a plain topic or a pure refactor gets no table.

## 3. One question at a time

Map the plan as a **decision tree**: every decision branches into decisions that depend on it. Ask only a question whose prerequisites are settled, most important first.

**Ask exactly one question, then stop and wait.** Never show your pick, a hint, or the book idea before the user answers — the user tries first.

The question:

```
**Q1 — <short title> (<issue id>)**

**The situation:** <one or two plain sentences: what exists, what we must decide>

**Options:**
- (a) <option> — <what happens next month if we pick it>
- (b) <option> — <what happens next month if we pick it>

❓ **Your turn:** <the one question to answer>

💡 *Don't get it? First write down what you do understand.*
```

When the question is about a tracked issue (e.g. GitHub, GitLab, Linear), put its id in the title, e.g. `(ABC-123)`; leave the parentheses out when there is none.

Describe options neutrally: the consequence of each, no loaded wording that gives the answer away.

The ❓ line is the only question in the block, so the user knows exactly what to answer; only the 💡 tip comes after it, always as the block's last line. For a plan it asks for the pick and points at the concrete failure or tradeoff that separates the options ("Which one — and with the other, what does the user see when some code still asks for the page by name?"). It must be answerable from the situation and options alone, never a quiz on terminology.

When a technical term first appears (a file, a macro, a tool), add a **Glossary** line under the question: `term — plain meaning`.

For a plain topic there is no decision to make: drop **Options**, make the ❓ line a question the user can reason out from the situation — predict a failure, spot a hidden assumption — and in the reveal write **➡️ The answer:** instead of **My pick**. Build the questions from the real code, not from textbook examples.

For a PR or code change, the questions start from the core file in the briefing. Other files — specs, removed code, hosts, callers — come in only once the user has explained the core correctly, and then one file per question.

Whenever the user says they don't follow, stop the round and explain until they do, starting from a [before/after table](#2-open-with-a-briefing) when the confusion is about what changed — chat as long as they need, then resume with the next question.

After the third question's reveal — or earlier, when the tree runs out — ask for a recap instead of the next question: `❓ **Recap:** in your own words, what did we settle and why?` Correct what is off, fill what is missing, then carry on with section 5 or 6.

## 4. Reveal, then the next question

After the user answers, reply in this shape, then ask the next question in the same message:

```
**➡️ My pick:** <option> — <the reason, one or two sentences>

**Feedback:** <one or two lines on the user's reasoning — confirm what was right, correct what was off, add the piece they missed>

**📖 From the books:** *<idea>* — <Author>, *<Book>*. <the idea, one or two sentences>. **Here:** <how it decided this question>.

**🔗 Docs:** [<page title>](<url>) — <what to read there, a few words>
```

If the user picked differently and their reasoning holds, say so and take their decision — the decision is theirs. Keep the reveal short. When the answer is about changed behaviour, the reveal shows it as a [before/after table](#2-open-with-a-briefing).

Include **From the books** only when the book idea is the reason one option wins; otherwise omit it. Never cite a chapter, page, or quote unless certain of it; describe the idea instead. Draw on the canonical reliability and maintainability references, e.g.:

- Feathers, *Working Effectively with Legacy Code* — characterization tests, seams, sensing vs separation, the legacy code change algorithm
- Fowler, *Refactoring* — behaviour-preserving small steps, the two hats (refactor or add behaviour, never both at once)
- Nygard, *Release It!* — stability patterns and antipatterns, fail fast, bulkheads, circuit breakers, integration points as the top killer
- Beyer et al., *Site Reliability Engineering* — SLOs, error budgets, toil, blameless postmortems
- Winters, Manshreck, Wright, *Software Engineering at Google* — Hyrum's Law, the Beyoncé Rule, test size and scope, brittle tests
- Kleppmann, *Designing Data-Intensive Applications* — idempotency, ordering, consistency, faults vs failures
- Meszaros, *xUnit Test Patterns* — fragile test, obscure test, test oracle
- Ousterhout, *A Philosophy of Software Design* — deep modules, information leakage, complexity as dependencies plus obscurity
- Hunt & Thomas, *The Pragmatic Programmer* — DRY as knowledge not text, broken windows, tracer bullets
- Forsgren, Humble, Kim, *Accelerate* — small batches, change failure rate

**Docs** links the official reference of the stack in question, on the page about that concept — never a site's front page, never a tutorial mill or a copy site. At most one link, and only in the reveal: never in the question or a **Glossary** line, so the user tries first. Omit it when unsure the page exists. The link stays in the chat: a card never carries it ([card rule 8](#card-rules)). For example, MDN for the browser, HTML and JavaScript; a language's own reference docs or package index for its libraries; the database's own manual.

## 5. Close with a decision record

When the topic is a plan and no questions remain — every branch visited, nothing silently assumed — write a mini **ADR** (Architecture Decision Record):

| Decision | Why | Book idea behind it | Rejected alternative |
|---|---|---|---|

Do not act on it until the user confirms the shared understanding.

## 6. Save cards to Anki

### Approval verdict

On someone else's open PR, before the cards, and whenever the user says "ready?": **Ready to approve**, or **Not yet — missing:** the changes from the list in section 1 the user has not explained yet. After "Not yet", ask about the missing ones only, one at a time in the shape of section 3.

End every session, plan or topic, by proposing 1–3 cards: one fact or reason per card, aimed at where the user's answer was wrong or they needed the explanation — not at what they already knew. Add them once the check below passes, then list the deck and each added front. A card the user rewords afterwards is updated with `updateNoteFields`.

Every card follows the [card rules](#card-rules), whether it is made here or anywhere else. When two stacks or options can be contrasted, put a side-by-side HTML table on the back — e.g. OS threads vs async tasks: unit, how many per process, cost to start, memory. Cards are read on a phone: two or three columns, cells of one to three words, a border and padding; code and full sentences go below the table, never in a cell. The same goes for explanations mid-session when the user is confused.

Before adding the cards, have a fresh sub-agent check each one against the card rules and verify every claim on its back — run the code, read the source — and list the concepts the cards lean on that no card in the user's decks covers yet. Fix what it finds and add the missing cards first.

### Card rules

Drawn from Wozniak's *Twenty rules of formulating knowledge*, Matuschak's *How to write good prompts*, and the research on retrieval practice, spacing, concrete examples and elaboration.

Before the card:

1. **Prerequisites are your job, not the user's.** When a card leans on a concept no existing card covers, add a card for that concept too. Never ask the user what background they lack.
2. **Worth remembering.** Mechanisms, reasons, tradeoffs, failure modes. Not what a book contains, not trivia, not facts one greps for.

Front:

3. **One question, one answer** — a sentence, or a list of at most three.
4. **Precise.** Exactly one right answer, and the reader knows what kind of answer is expected.
5. **Effortful.** Ask why, how, or what breaks if. Never yes/no or mere recognition.

Back:

6. **Always correct.** Verify before writing. Unverified means no card, or the uncertain part said plainly.
7. **Answer first, in bold.** Below a line, at most two short lines of context: why it matters, or where it runs.
8. **Self-contained, front included.** No file paths, no "see X", no internal name without its plain meaning. Every term the user may not know — on the front as much as the back — gets a few plain words inline or its own card (rule 1): "plan node" with neither leaves the question unreadable. Shared decks hold nothing personal.
9. **An example only when it makes the idea concrete**, and inside the card. No example beats a useless one.
10. **Name where a runtime component lives** — which process, container or machine, in plain words.
11. **No open question left.** A question the card raises is answered in its context line or becomes its own card.

The set:

12. **Look-alike cards get a contrast card**, so they don't interfere.
13. **Few cards.** One to three per session.
14. **Rewrite a bad card; don't suspend it.**

Cards go through AnkiConnect on `localhost:8765`, and each card's deck follows from what it holds:

- **Personal** — what the user got wrong, their predictions, their trial and error: `<LEARN_LOG_DECK>::<topic-slug>`.
- **A general concept** a colleague should know too (webhooks, HMAC, OTP supervisors …): when `LEARN_CONCEPTS_DECK` is set, the subdeck of it that fits the topic, picked from `deckNames` (`<LEARN_CONCEPTS_DECK>::<area>::<topic>`); a new subdeck only when none fits. Unset: the personal deck.

Never Anki's `Default` deck, and never a deck outside these two.

```bash
curl -s localhost:8765 -d '{"action":"createDeck","version":6,"params":{"deck":"Learning Log::vue-routing"}}'
curl -s localhost:8765 -d '{"action":"addNotes","version":6,"params":{"notes":[
  {"deckName":"Learning Log::vue-routing","modelName":"Basic","tags":["learn"],
   "fields":{"Front":"<question>","Back":"<answer>"}}]}}'
```

Every response carries `"error"`; a non-null one, or `null` in the `addNotes` result list (a duplicate), is reported to the user, never skipped.

When `curl` cannot connect, Anki is closed or AnkiConnect is not installed — the two look the same. Keep the checked cards on screen as text and say once: "Anki isn't reachable — open it and say *save* to add these cards, or *skip*." Without Anki, `/learn review` has nothing to review: say so and point at the [Anki setup](#anki-setup).

## 7. Review due cards

```bash
curl -s localhost:8765 -d '{"action":"findCards","version":6,"params":{"query":"(deck:\"<LEARN_LOG_DECK>\" OR deck:\"<LEARN_CONCEPTS_DECK>\") is:due"}}'
curl -s localhost:8765 -d '{"action":"findCards","version":6,"params":{"query":"(deck:\"<LEARN_LOG_DECK>\" OR deck:\"<LEARN_CONCEPTS_DECK>\") is:new tag:learn"}}'
curl -s localhost:8765 -d '{"action":"cardsInfo","version":6,"params":{"cards":[<ids>]}}'
```

With `LEARN_CONCEPTS_DECK` empty, query the log deck alone. Due cards come first, then new cards this skill added. Only `tag:learn` new cards: the deck may hold older unstudied ones, and those stay with Anki's daily new-card limit. Say how many of each there are, ask how many to do, then go one card at a time:

1. Ask the card's `fields.Front.value` and stop. Never show the back first.
2. The user either types an answer or says "show". After an answer, give **➡️ The answer:** (the back) and **Feedback:** on their answer, in the shape of section 4. After "show", give the back only. Chat for as long as they want.
3. End the reveal with the grade row, the suggestion marked, and wait:

   `1 Again · 2 Hard · [3 Good] · 4 Easy` — <why that grade, one line>

   A bare number or grade name is the grade; "ok" takes the suggestion. The user's grade wins. After "show" there is no answer to judge: mark no suggestion and wait for a number.
4. Record it, then ask the next card:

```bash
curl -s localhost:8765 -d '{"action":"answerCards","version":6,"params":{"answers":[{"cardId":<id>,"ease":3}]}}'
```

When a card's back turned out wrong or unclear, propose a fix and apply it with `updateNoteFields` once the user agrees. Stop when no card is due or the user says so, and say how many were reviewed.

## 8. Catch up on a handoff

A teammate's handoff (a plan, a status note, a session's notes for its successor) describes work the user did not follow. The goal is **just enough**: the user can follow the pairing and talk about the open decisions — not understand everything. Budget about 20 minutes; when it is up, say so and go to the cards.

1. **Gather facts** as in section 1, limited to what the steps below need: what merged, what the open decisions point at.
2. **Map**, at most ten plain lines, no paths: what is done, what is next, what waits on a decision, and whose.
3. **The ideas the decisions rest on — at most three.** Pick the concepts without which the open decisions make no sense. One question each, in the shape of section 3, then the reveal of section 4. Skip every other concept, however interesting.
4. **Open decisions as practice.** Ask each one in the shape of section 3. Unless the handoff says the user owns it, the decision is the teammate's: reveal the handoff's own pick and its reason as **➡️ Their pick**, then yours only where you disagree. No decision record.
5. **One sidequest.** Propose at most two open items that fit a free slot and do not touch the files the critical path is about to change, each in one line with why it fits. The user picks one or none.
6. **Cards** as in section 6, aimed at the ideas from step 3.

## 9. Ready check

Before the user asks for a review of a change they answer for — whoever or whatever wrote it — check that they could defend it as its reviewer.

1. **Gather facts** as in section 1: the diff, the PR body, the code the change touches.
2. Open with the line `🎯 **Ready check**` on its own. Ask about three questions a reviewer would have to answer, numbered together: where the behaviour lives now and how it runs, what happens on failure or at the edges, and the known risk or trade-off. Ask, then stop; never answer them in the same turn.
3. Compare the answers with the code: say what was right, correct what was off with a `file:line`, and fill any gap in one line per question. When the user doesn't know, the whole answer is at most three lines, one per question.
4. End with **Ready to ask for review** or **Not yet** and what is missing. Gaps → offer a normal session on them (sections 3–6), which ends in cards.

"skip" skips the check.

## Anki setup

Once per machine; everything else in this skill works without it.

1. Install the Anki desktop app (`brew install --cask anki` on macOS). An AnkiWeb account syncs cards to the phone (optional).
2. Add AnkiConnect: Tools → Add-ons → Get Add-ons → code `2055492159`, then restart Anki.
3. Turn on FSRS once for the whole collection: Deck options → FSRS → *Optimize all presets* → Save.

Anki must be open while the skill saves or reviews cards.

## Configuration

Set in the environment, for example in the `env` block of `~/.claude/settings.json`:

| Variable | Default | Meaning |
|----------|---------|---------|
| `LEARN_LOG_DECK` | `Learning Log` | Deck for personal cards: mistakes, predictions, trial and error |
| `LEARN_CONCEPTS_DECK` | empty | Deck tree for general concept cards; empty → they go to `LEARN_LOG_DECK` too |
