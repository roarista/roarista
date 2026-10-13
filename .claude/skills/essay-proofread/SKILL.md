---
description: Proofread and correct essays for grammar, punctuation, spelling, and tense. Outputs a formatted .docx file and runs a sub-agent review against assignment requirements. Only use with Opus 4.6 — do not run this skill on any other model.
model: claude-opus-4-6
---

# Essay Proofread & Format

**Model requirement:** This skill must only be used with `claude-opus-4-6`. If the session is running a different model, tell the user and stop — do not proceed.

## When to use

When the user asks to proofread, correct, or format an essay. Trigger phrases: "essay writing skill", "proofread", "correct this essay", "check my essay", "fix my essay".

## Core principle

**Keep the student's words verbatim.** Only fix what is mechanically wrong. Never rewrite sentences, change arguments, add new ideas, or alter the student's voice. The goal is error correction, not revision.

## What to correct

Work through the essay systematically, fixing only these categories:

### 1. Spelling and possessives
- Misspellings and typos ("Misters" → "Mr.", "Brad Birds" → "Brad Bird's")
- Missing possessive apostrophes ("Woods theory" → "Wood's theory", "societys" → "society's")
- Their/there/they're confusion
- Compound words that should be one word ("super heroes" → "superheroes", "side kick" → "sidekick", "super powers" → "superpowers")

### 2. Punctuation
- Missing commas after introductory/subordinate clauses ("Although X I think" → "Although X, I think")
- Missing commas before coordinating conjunctions joining independent clauses ("X but Y" → "X, but Y" when both are full clauses)
- Run-on sentences and comma splices — fix with semicolons or periods
- Periods after abbreviations ("Mr Incredible" → "Mr. Incredible")
- Quotation mark punctuation placement (periods and commas inside quotes per American/MLA style)
- En dashes in ranges (timestamps, page numbers): use – not -

### 3. Grammar
- Wrong prepositions ("sued from" → "sued by", "repression on" → "repression of")
- Sentence fragments — attach to the preceding sentence or restructure minimally
- Adjective/adverb confusion ("act selfish" → "act selfishly")
- Awkward constructions that are clearly ungrammatical ("denying him of being" → "refusing to let him be")

### 4. Verb tense (for academic/film essays)
- If the assignment requires present tense when discussing films, essays, or works of art (standard MLA convention), fix past tense to present tense throughout
- "Bob did not enjoy" → "Bob does not enjoy"
- "Syndrome came back" → "Syndrome comes back"
- "was told no" → "is told no"
- Only change tense for descriptions of what happens in the source material, not for the student's own meta-commentary about their essay

### 5. Formatting (in the .docx output)
- Film titles: italicized
- Book/essay titles in quotes: use proper curly quotation marks
- Proper nouns: capitalize (character names, program names, etc.)

## What NOT to change
- The student's arguments, ideas, or analytical points
- Word choice (unless it creates a clear grammatical error)
- Sentence structure (unless it's a fragment or run-on)
- The order or organization of paragraphs
- Content — never add or remove ideas

## Output process

### Step 1: Generate the .docx
Use `python-docx` to create a properly formatted document:
- **Font:** Times New Roman, 12pt throughout
- **Spacing:** Double-spaced (line_spacing = 2.0), no extra space before/after paragraphs
- **Margins:** 1 inch all around
- **MLA header:** Student name, professor, class, date (preserve from original or template)
- **Title:** Centered, bold, 13pt
- **Section headings:** Bold, 13pt (preserve student's heading style)
- **Body paragraphs:** 0.5 inch first-line indent
- **Works Cited:** Hanging indent (0.5 inch left indent, -0.5 inch first-line indent)
- **Film titles:** Italicized in running text and Works Cited
- **Journal/publication titles:** Italicized in Works Cited
- **Smart quotes and apostrophes:** Use Unicode curly quotes (U+2018/2019, U+201C/201D)

Install `python-docx` if needed: `pip install python-docx`

### Step 2: Count words
Count body text words excluding:
- Section headings
- Parenthetical in-text citations
- Works Cited entries

Report the count and flag if it falls outside the assignment's required range.

### Step 3: Report corrections
List every correction made, organized by category. Use a table or grouped list so the student can see exactly what changed.

### Step 4: Sub-agent review
Launch an Agent (model: opus) to review the corrected essay against the assignment requirements. The agent should verify:
1. Word count within required range
2. Present tense consistency (if required)
3. No remaining sentence fragments
4. Citation format compliance (MLA or as specified)
5. Whether the essay adequately addresses the assignment prompt
6. Any remaining mechanical errors

Report the agent's findings to the user.

### Step 5: Flag (don't fix) content issues
If the word count is too low/high, or if there are content-level issues the assignment requires (e.g., missing framework introduction, incomplete arguments), **flag them** — do not fix them by adding or removing the student's content. Tell the user what needs attention and offer to help if they want to expand.

## Example invocation

User provides:
- Essay text (pasted or in a file)
- Optionally: assignment requirements (PDF, docx, or pasted)
- Optionally: a template document for formatting reference

Claude:
1. Reads the essay and requirements
2. Identifies all mechanical errors
3. Generates corrected .docx preserving the student's words
4. Sends the file
5. Lists all corrections made
6. Runs sub-agent review
7. Reports findings and flags any content-level issues
