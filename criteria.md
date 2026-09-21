# Acceptance criteria — The Unofficial Guide

Five criteria that say what "working" means for this system, written in unit 1
**before** any results existed.

An acceptance criterion names a target: a number, a count, a rate, or something
a person could plainly observe. *"Retrieval works"* is an opinion. *"For at
least 4 of my 5 test questions, the top results include a chunk containing the
answer"* is a criterion.

Under each one, write a sentence or two on **why that target** and not a
stricter or looser one. A reason that says something about your corpus or your
pipeline earns credit; *"80% seemed reasonable"* does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## 1. Retrieved chunks contain the answer

For at least 4 of my 5 test questions, the retrieved chunks include one that
contains the answer.

**Why this target:**
<!-- e.g. "One of my questions is about a topic only two documents mention, so
     I expect that one to be hard." -->
Three of my five questions (bus tickets, bakery, limited mobility) are answered
by a cross-cutting guide, and the town guides repeat the same topics, so those
are the ones most likely to pull back chunks from the wrong file. The bus
question also uses words ("confused", "visitors") that appear nowhere in the
guide's own wording. I allow one miss for those, but not two, because the other
two questions name a town and a specific detail (Pellew Sands parking, Halden
Bay overflow lot) that each appear in only one guide, so they should be found
reliably. A miss on those would point at the chunking or the embedding, not at
overlapping guides.

---

## 2. Every answer names a source

Every answer the system produces names at least one source document.

**Why this target:**
<!-- Why all five and not four? What about your setup makes that achievable —
     or what would have to go wrong for it not to be? -->
All of them, not four, because naming a source is the point of a guide people
will act on (a wrong opening time is only checkable if you can go and look at
the guide it came from). It's achievable because `generate.py` labels every
retrieved chunk `[from <filename>]` in the prompt and tells the model to name
the file, so the filename is always in front of it. What would have to go wrong
is the model ignoring that instruction, which it can do because the source is
named by the model and not attached by my code. A refusal from the relevance
gate is not counted, since no answer is produced and there is no source to name.

---

## 3. The relevance gate stops out-of-corpus questions

When I ask a question my documents clearly don't cover, the relevance gate
stops it and the system returns "I don't have enough information about that" —
in at least 4 of 5 tries.

<!-- The five questions are the ones in `OUT_OF_SCOPE` at the bottom of
     `questions.py`, and `run_eval.py` puts them through the gate and writes
     what happened into your run log. Swap them for your own if you'd rather —
     just keep five of them, or the "4 of 5" above has nothing to be 4 of. -->

**Why this target:**
<!-- What did your distances look like when you set the cutoff in Milestone 4?
     Was there a clean gap, or did the two groups overlap? -->
Measured with the default chunking (800 characters, 120 overlap, 51 chunks) and
top 5 results. The best-chunk distance for my five out-of-scope questions was
0.829 to 0.903. The best-chunk distance for my five in-corpus questions was
0.319 to 0.695. There is a clean gap between them (0.695 to 0.829), so the two
groups don't overlap. The starting cutoff of 0.6 sits below the weakest
in-corpus question (bus tickets, 0.695), so at 0.6 the gate would refuse that
question as well as all five out-of-scope ones.

---

## 4. Something about your chunks

<!-- YOU WRITE THIS ONE.

     How would you know if your chunks were the right size? Name something
     countable or observable.

     Examples of the right shape — don't copy these, they should come from
     what you actually saw in Milestone 3:
       - "At least 4 of 5 sampled chunks read as a complete thought, with no
          sentence cut in half at either end."
       - "No chunk is shorter than 200 characters, since anything below that
          in my corpus turned out to be a heading with no content under it." -->
At least 4 of 5 sampled chunks contain a complete section or paragraph, with no sentence cut in half at the start or end.


**Why this target:**
My corpus (city_guides) is organized by headings with information spread across full paragraphs, not single-sentence facts. A chunk that cuts a paragraph mid-sentence risks losing the detail needed to answer the question, so I'm checking that chunking respects those natural boundaries.


---

## 5. Your choice

<!-- YOU WRITE THIS ONE TOO.

     Pick something you actually care about getting right. It could be about
     speed, about refusals, about a particular kind of question your corpus
     handles badly, about source attribution being correct rather than merely
     present — anything, as long as it names a number or an observable
     outcome. -->
For at least 4 of 5 test questions that mention a specific town, the source the system names is a guide for that same town, not a different one.


**Why this target:**
My corpus has both town-specific guides and cross-cutting guides (eating, walking, accessibility) that reference multiple towns. It's easy for retrieval to grab a chunk from the wrong town's guide if the wording overlaps, so I want to confirm attribution is actually correct, not just present.


---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 2 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath it, like this:

         ## 1. Retrieved chunks contain the answer

         For at least 4 of my 5 test questions, the retrieved chunks include
         one that contains the answer.

         **Why this target:** ...

         > **Revised in unit 2:** For at least 4 of 5 questions, the top three
         > results contain the answer.
         >
         > **Why revised:** I couldn't judge "the chunks include one that
         > contains the answer" the same way twice — I scored two questions
         > differently on Monday than on Wednesday. The new version is
         > something I can actually check.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "I said 4 of 5 but got 2 of 5, so 2 of 5 is more realistic."

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.

     The whole reason the originals stay visible is so someone can see what you
     said before you knew the answer.
     ───────────────────────────────────────────────────────────────────────── -->
