---
name: improve-readability
description: Code readability principles from John Ousterhout's A Philosophy of Software Design, drawing on Ch. 2 (Nature of Complexity), 4 (Modules Should Be Deep), 10 (Define Errors Out of Existence), 13 (Comments Describe What Isn't Obvious), 14 (Choosing Names), 17 (Consistency), and 18 (Code Should Be Obvious). Covers obvious code, strategic naming, why-not-what comments, eliminating special cases, and visual grouping. Use when improving readability, naming variables, writing comments, or reviewing code for clarity and cognitive load.
---

In A Philosophy of Software Design, John Ousterhout frames code readability not just as a matter of aesthetics, but as a critical mechanism for minimizing cognitive load and eliminating unknown unknowns (the two main drivers of software complexity).

If a developer cannot understand code quickly, it increases the risk of introducing bugs during maintenance. While many of Ousterhout's principles focus on high-level system design, he provides concrete, low-level recommendations aimed directly at making code readable, transparent, and obvious.

1. Make Code "Obvious" (The Transparency Principle)
Ousterhout argues that readability means code should be obvious. If code is obvious, someone else can read it and quickly understand its behavior, performance, and safety boundaries without having to spend significant cognitive effort or read between the lines.

Reduce Cognitive Load: Every time a reader has to stop and guess why a variable is used, what a magic number means, or what an architectural side-effect might be, their mental stack fills up. Clear code ensures that the reader's cognitive load is reserved exclusively for the inherent difficulty of the problem, rather than the accidental complexity of the syntax.

Consistency: Consistent formatting, naming conventions, and architectural patterns dramatically improve readability. If similar problems are always solved using the same patterns, readers can skim familiar code structures and quickly spot what makes a specific section unique.

2. Strategic and Meaningful Naming
Naming is one of the most powerful tools for readability. Ousterhout offers precise rules for selecting names:

Precision over Vagueness: Names should be specific and unambiguous. Avoid broad, generic variable names like data, value, or result unless their scope is incredibly narrow (2–3 lines).

Consistency across Contexts: If a specific concept is referred to by a certain word in one part of the codebase, use the exact same word everywhere else (e.g., don't alternate between client, customer, and user to represent the same entity).

Name Length Should Match Scope: Broadly scoped variables or public API methods require highly descriptive, longer names to maintain clarity across the system. Conversely, variables with an active lifespan of only a few lines (like an index loop counter) can be short (e.g., i or j) because their entire context is instantly visible.

3. Write Comments that Describe the "Why" (Not the "What")
Ousterhout dedicates multiple chapters to documentation, asserting that good comments are foundational to readability. His golden rule is that comments should capture information that was in the mind of the designer but cannot be easily gleaned from the code itself.

Don't Repeat the Code: A comment like // Increment i by 1 followed by i++; adds zero value, increases visual noise, and decreases overall readability.

Explain the "Why": Comments should explain the design rationale, unexpected edge cases, or business logic dependencies. If you had to make a non-obvious choice to fix a bug or optimize performance, document why you chose that approach.

Describe Interfaces Precedent to Implementation: Interface comments should describe the abstraction (what the module does from a high-level perspective) rather than the implementation details (how it does it). This allows developers to use an API safely without diving into the source code.

4. Eliminate Special Cases
Complex code structures filled with nested if-else blocks and deeply layered exception handling are highly unreadable.

Design Out the Exceptions: Ousterhout recommends structuring your code so that the normal, common-case execution flow inherently handles the edge cases without requiring unique branching or error blocks.

Deep Modules, Shallow Interfaces: Ensure that code reads smoothly by hiding complex operational details behind a clean, simple API. A method or class should do a lot of heavy lifting under the hood while exposing a highly readable, intuitive interface to the rest of the application.

5. Group Related Code Visually
Just as paragraphs break up walls of text in prose, layout and whitespace break up complex logic in code.

Visual Paragraphs: Use blank lines to partition a single method into logical sub-steps or "paragraphs." Each block of code should represent a single, cohesive action.

Align Comments with Blocks: Place a brief introductory comment right before a logical code paragraph to act as a "chapter title," giving the reader a map of what that specific block achieves before they look at the lines of syntax.

Summary Checklist for Readable Code
According to Ousterhout's philosophy, you can test the readability of your code by asking:

Can a developer completely unfamiliar with this component quickly understand its purpose without asking me?

Are the names so precise that they leave no room for misinterpretation?

Do my comments provide structural, architectural, or philosophical context that the code alone cannot convey?
