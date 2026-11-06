---
name: ste-simplify
description: Use when the user invokes `/ste-simplify`, asks for plain or simple English or Simplified Technical English (STE), or asks to rewrite text to remove jargon or ambiguity, or to write unambiguous technical instructions. Applies to natural-language text only, not to simplifying or refactoring code.
---

# STE Simplify

## Overview

Produces clear, unambiguous explanations and instructions with a practical subset of **ASD-STE100 Simplified Technical English (STE)**.

**Core principle:** One word, one meaning; one sentence, one thought; active voice, simple tense.

This is a subset of STE, not the full specification. Do not claim ASD-STE100 compliance in the output.

Do NOT use for creative, marketing, or conversational prose where style matters.

## Preserve Unchanged

Copy these exactly. Apply the rules only to the prose around them:
- Code, commands, file paths, and values in quotes (for example `'localhost'`)
- Error messages and log strings
- API, function, parameter, and variable names
- Product names, proper nouns, and version numbers
- Quoted text and legal text
- Markdown structure when you rewrite a document: headings, links, tables, and code blocks. Rewrite the prose inside headings, list items, and table cells, but keep the same structure.

## Keep the Meaning

- Keep the action and the object of every sentence. Do not add advice, causes, or steps that the source does not give.
- The only content you may add is a one-sentence definition of a technical term (see Words).
- To show a possibility, write *if*, *sometimes*, *probably*, or *may*. Never turn a possibility into a fact.
- If a simple word loses precision, keep the original word and define it.
- If the source has an odd or wrong term, keep the source wording. Add one line after the text that starts with **Note:** and names the problem. Do not fix it silently.
- If the source has a dangling *etc.* with no list before it, delete it.

## Rules

### 1. Words
- **One word, one meaning.** Use the same word for the same thing in the whole text. Do not rename a thing.
- **Choose the simple word:**

  | Use | Instead of |
  |---|---|
  | use | utilize, employ, leverage |
  | start | commence, initiate |
  | stop | cease, terminate, discontinue |
  | show | demonstrate, indicate, illustrate |
  | change | modify, alter |
  | make sure | ensure, ascertain |
  | help | facilitate, assist |
  | need, must | require, necessitate |
  | if | provided that, in the event that |
  | because | due to the fact that, since, as (when they give a reason; *since* is correct for time) |

- **No idioms, slang, metaphors, or analogies.** Explain with the thing itself.
- **No contractions.** Write *does not*, not *doesn't*.
- **Write *for example*, *that is*, and *and so on*** instead of *e.g.*, *i.e.*, and *etc.*
- **Define a technical term in one sentence on first use** if a reader new to the topic would not know it (for example *daemon*, *write-ahead log*). Do not define names that you must preserve.

### 2. Nouns
- **Maximum 3 nouns in a cluster.** Break longer stacks with prepositions.
  - *Bad:* "Run the production database connection pool health check."
  - *Good:* "Check the health of the connection pool for the production database."
- **Keep the articles** (*the*, *a*, *an*).

### 3. Verbs
- **Active voice.** Name who or what does the action.
  - *Bad:* "The configuration file is read by the server."
  - *Good:* "The server reads the configuration file."
- **Imperative mood for instructions.**
  - *Bad:* "You should restart the service."
  - *Good:* "Restart the service."
- **Simple tenses.** Use simple present, simple past, and simple future (*will read*). Do not use progressive (*is reading*) or perfect (*has read*) forms. *Has* and *have* are correct as main verbs (*the account has a balance*).
- **Modals:** Do not use *would*, *could*, or *might*. Use *may* only to show a possibility. Use *can* only to show ability. Change *should* to an imperative, or to *must* if the action is mandatory.
  - *Bad:* "You should back up the database before the upgrade."
  - *Good:* "Back up the database before the upgrade."
- **No gerund as a noun.** A participle as an adjective is correct (*the running process*).
  - *Bad:* "Logging errors helps with debugging."
  - *Good:* "Record errors in the log to help you find bugs."

### 4. Sentences and paragraphs
- **Descriptions:** Maximum 25 words per sentence. One idea per sentence.
- **Instructions:** Maximum 20 words per sentence. One instruction per sentence, unless two actions happen at the same time. This limit also applies to each item in a numbered list of steps.
- **Mixed text:** Apply the 20-word limit to every instruction that tells the reader to act. Apply the 25-word limit to everything else, including the steps in a description of how a system works.
- **Condition first.**
  - *Bad:* "Delete the lock file if the process stops."
  - *Good:* "If the process stops, delete the lock file."
- **Maximum 6 sentences per paragraph.** One topic per paragraph.
- **Use a vertical list** for 3 or more items or for sequential steps.

### 5. Warnings
- If a step can cause injury, data loss, or damage, put a short **Warning:** line *before* that step. State the risk.
- If one risk applies to several steps, write one **Warning:** line before the first of those steps.

## Output Structure

**To explain a concept** (use these headings in this order; omit an optional section as its rule says):
1. **Definition:** 1–2 sentences. State what it is and what it does.
2. **How It Works:** Include only if the concept has an ordered process. Use a numbered list of short sentences.
3. **Key Terms:** List each technical term in the text that a new reader would not know, except the main term that the Definition already defines. Define each in one sentence. Omit this section if the list is empty.
4. **Example:** One concrete, minimal example. Maximum 5 sentences or steps.

**To rewrite existing text:** Output only the rewritten text, plus a **Note:** line if the Keep the Meaning rule requires one. Use no headings. Use a vertical list for steps. Do not add a preface or a list of changes unless the user asks.

**Reader level:** If the user says the reader is a newcomer or beginner, define every technical term, and use the Key Terms section. If the user gives no level, assume the reader knows the basics of the field. Explain the specific idea, not the whole field.

## Examples

### Rewrite existing text

Source:

> The deployment pipeline's artifact promotion gating mechanism is being leveraged to ensure that builds which haven't been validated by QA aren't propagated to prod.

Rewrite:

> The pipeline blocks builds that QA did not validate. Only validated builds go to production.

### Explain a concept

Request: "Explain what a database index is."

> **Definition:** A database index is a data structure that helps the database find rows quickly. The database keeps the index in addition to the table.
>
> **How It Works:**
> 1. The database copies the values of one or more columns into the index.
> 2. The database sorts these values and stores a pointer to each row.
> 3. A query searches the sorted values instead of every row in the table.
>
> **Key Terms:**
> - **Column:** One named field in each row of a table.
> - **Pointer:** A value that gives the location of a row.
>
> **Example:** A `users` table has one million rows. You add an index on the `email` column. A query for one email address now reads only a few index entries. Without the index, the query reads all one million rows.

## Self-Check Before Responding

Read your draft once and fix each violation:
- [ ] A description over 25 words, or an instruction or list step over 20 words? Split it.
- [ ] Passive voice, progressive or perfect tense, *would*/*could*/*might*, or *should*? Rewrite it.
- [ ] A gerund used as a noun? Use a simple verb.
- [ ] A noun cluster over 3 nouns? Unpack it with *of*, *for*, *in*, or *to*.
- [ ] A contraction, an idiom, an analogy, or *e.g.*, *i.e.*, *etc.*? Replace it.
- [ ] A word from the *Instead of* column of the table? Use the word in the *Use* column.
- [ ] A fact, step, or cause that the source does not contain? Remove it.
- [ ] Protected text (code, names, errors, quoted values, Markdown structure) changed? Restore it exactly.
- [ ] One thing with two names? Pick one.
