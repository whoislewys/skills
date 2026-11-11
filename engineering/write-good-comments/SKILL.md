---
name: write-good-comments
description: Write and review code comments using Ousterhout's rule from A Philosophy of Software Design (Ch. 12-13) — a comment must carry what the code next to it cannot. Covers interface vs implementation comments, precision comments on declarations, intuition comments on blocks, and cross-module design notes. Use when writing comments or docstrings, reviewing a diff for comment quality, or documenting a public API.
disable-model-invocation: true
---

A comment earns its place only by describing what is **not obvious from the code next to it**. "Obvious" means obvious to a first-time reader, not to you.

Comments are how you hide complexity. A declaration alone is not an abstraction — callers can only avoid reading the body if the comment tells them what the body would have.

## Comment every declaration

Every class, every class/instance variable, every method or function gets a comment block. Don't spend energy deciding whether one is needed; commenting everything is cheaper than the deliberation. Trivial getters/setters are the rare exception.

Follow the language's doc tool conventions (Javadoc, Doxygen, godoc, JSDoc, docstrings). If the project has none, copy a neighbouring project's.

## The no-op test

After writing a comment, ask: **could someone who has never seen this code have written this comment from the code beside it?** If yes, it is a no-op — delete it or replace it.

Two reliable symptoms:

- The comment reuses the words in the name it documents. `// Downcast PARAMETER to TYPE` above `downCastParameter(String parameter, String type)` adds one word: "to".
- The comment sits at the same level of detail as the code — one line of prose per line of code.

The fix is to change altitude. Every useful comment is either **lower** than the code (precision) or **higher** than the code (intuition).

## Lower: precision on declarations

Names and types are imprecise. On a variable, argument, or return value, answer whatever applies:

- What are the units?
- Are boundaries inclusive or exclusive?
- Is null permitted, and what does it mean?
- Who frees/closes/owns this resource?
- What invariants always hold ("this list always has at least one entry")?
- What does a *missing* entry mean?

Document **nouns, not verbs** — what the variable represents, not how the code manipulates it. If your comment reads "toggled to TRUE when X, toggled to FALSE when Y", rewrite it as "true means X has happened since Y" and let the reader infer the writes.

```
// Vague:
uint32_t offset;             // Current offset in resp buffer

// Precise:
// Position in this buffer of the first object that hasn't been
// returned to the client.
uint32_t offset;
```

## Higher: intuition on blocks

Above a block, a loop, or a method body, say what the code is *trying to do* and *why we get here* — never how.

Ask: what is the simplest sentence that explains everything in this block? A good one gives the reader a framework ("append this key hash onto an existing RPC that hasn't been sent yet") from which the individual conditions become self-explanatory — and gives them a basis to judge whether the code is correct.

- Before each major block of a long method: one abstract line ("Phase 1: scan active RPCs for completions").
- Before a non-obvious loop: what **one iteration** accomplishes.
- Before non-obvious code: why it exists. For a bug fix, name the cause and link the issue ID rather than restating the report.

Short methods that do one thing need no implementation comments at all — the interface comment already said it.

## Interface comments vs implementation comments

Keep them separate. Interface = what a caller needs to use it. Implementation = how it works inside. A caller must never have to read implementation detail to use the thing.

A method's interface comment covers:

1. One or two sentences on behaviour as the caller perceives it.
2. Every argument and the return value, including constraints and dependencies between arguments.
3. Side effects — anything affecting future system behaviour that isn't the result.
4. Exceptions it can emit.
5. Preconditions (list must be sorted; some other method must run first). Minimise these, then document what remains.

A class's interface comment covers the abstraction it provides, what one instance represents, and its limitations (e.g. "single-threaded; processes one request at a time"). No internals, no config-parameter tour, no "include this header" — the reader can guess that.

**Red flag — implementation leaking into interface docs.** Strip it; if it's still useful, move it inside the body.

**Red flag — the interface comment *has* to describe the implementation.** That's a design signal: the class or method is shallow. Fix the design, not the comment.

Also cut from interface comments anything invisible to the caller — crash recovery the system handles automatically, private constants, RPC names.

## Cross-module decisions

For a decision spanning several files (a protocol, an enum whose values must be registered in six places):

- If there is one place a developer is *forced* to touch, put the documentation there — e.g. a checklist comment at the end of the enum listing every other file to update.
- Otherwise write it once in a central `designNotes` / `CONTEXT.md` section and leave one-line pointers at each dependent site: `// See "Zombies" in designNotes.` Never duplicate the prose.

## Reviewing comments

Work through the diff and apply, in order:

1. Any declaration without a comment → add one.
2. Any comment that survives the no-op test → delete or raise/lower its altitude.
3. Any interface comment containing implementation detail → strip; ask whether the module is shallow.
4. Any comment contradicting the code → the code changed; fix the comment.

If a reader says a comment isn't obvious, it isn't obvious. Don't argue — find what confused them and fix the comment or the code.

Done when: every declaration in the diff is commented, no remaining comment could be reconstructed from the code beside it, and no interface comment mentions internals.
