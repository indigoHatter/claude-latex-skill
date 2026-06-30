# File Structure and Modularity

How a LaTeX project is organized determines how well it scales, how safely it
can be edited, and how clearly it communicates its own structure to a new
collaborator. These are not aesthetic choices — they are architectural ones.
[R, M, P]

---

## Standalone vs. Modular Projects

### Standalone Files

A standalone file contains everything needed to compile: `\documentclass`,
full preamble, `\begin{document}`, content, `\end{document}`. Use this
structure for:

- Single-document projects with no shared content
- Self-contained handouts, worksheets, or reference sheets
- Prototypes and drafts not yet split into modules

A standalone file is not a lesser structure. Many documents should stay
standalone. The mistake is keeping a document standalone after it has outgrown
the form — when the preamble is being copy-pasted between files, or when
sections are being commented out to work on one part, modularity has become
necessary. [M]

### Modular Projects

A modular project has one **root file** and one or more **content files**.

```
project/
├── main.tex              % root: documentclass, preamble, \include calls
├── macros.tex            % shared macro definitions
├── project_philosophy.md
├── sections/
│   ├── ch01-intro.tex    % content file: no \documentclass
│   └── ch02-methods.tex
└── figures/
    ├── fig-circuit.pdf
    └── fig-results.pdf
```

The root file owns: `\documentclass`, the full preamble, all `\usepackage`
calls, macro definitions, and the document skeleton. Content files own only
their content — no preamble, no `\begin{document}`. [M, P]

---

## `\input` vs. `\include`

These commands are not interchangeable. Choosing the wrong one creates
problems that are subtle and irritating to diagnose. [M, C]

### `\input{file}`

Behaves exactly as if the file's contents were pasted in place. No side
effects. Can appear anywhere — in the preamble, inside environments, nested
inside other `\input` calls.

Use `\input` for:
- Reusable fragments: macro definitions, table contents, tikz figures
- Preamble partials (e.g., a shared package block across projects)
- Any inclusion that must not force a page break

### `\include{file}`

Forces a page break before and after the included content. Creates its own
`.aux` file for the included content. Enables `\includeonly{}`.

Use `\include` for:
- Top-level document divisions: chapters, major sections, appendices
- Any content that naturally begins on a new page
- Large projects where selective compilation (see below) is valuable

**`\include` cannot be nested.** An `\include`-d file cannot itself call
`\include`. If nesting is needed, use `\input` inside an `\include`-d file.
[C]

### `\includeonly` — Selective Compilation

This is the primary reason to prefer `\include` for large projects. In the
root file's preamble:

```latex
% Compile only chapters 2 and 3; cross-references to other chapters
% remain valid from previous full compilation
\includeonly{ch02-methods,ch03-results}
```

During active work on a large document, this cuts compile time dramatically
while preserving correct page numbers and cross-references (as long as a full
compilation has been run at least once). Comment out `\includeonly` for final
compilation.

Document the presence of `\includeonly` with a comment — a collaborator
opening the root file and finding incomplete output needs to know why:

```latex
% \includeonly active — compiling subset for drafting. Remove for final build.
\includeonly{ch02-methods}
```

Both the selective compilation workflow and its documentation serve
Readability and Maintainability. [R, M]

---

## Root File Conventions

The root file is the entry point for every collaborator and every build tool.
It should be self-explanatory as a document map:

```latex
% ============================================================
% FILE:     main.tex
% PROJECT:  week3-handout
% VERSION:  20260507.001
% AUTHOR:   P. Harrison
% PURPOSE:  Root file for Week 3 handout. Compiles full document.
%           Content files: sections/gauss-law.tex, sections/examples.tex
% REQUIRES: none beyond standard TeX Live
% ============================================================
% KNOWN WARNINGS:
%   (none)
% ============================================================

\documentclass[11pt]{article}

% --- Encoding & Language ---
\usepackage[T1]{fontenc}
% ... (full preamble per packages.md)

% --- Document Structure ---
\begin{document}

\input{sections/gauss-law}    % Gauss's Law: statement, derivation, examples
\input{sections/examples}     % Practice problems with solutions

\end{document}
```

Inline comments on `\input`/`\include` lines are not optional — they are the
document map. A collaborator reading only the root file should understand what
the project contains and in what order. [R]

---

## Content File Conventions

Content files have no `\documentclass` or `\begin{document}`. They begin with
their file header (see `headers.md`) and then their content directly:

```latex
% ============================================================
% FILE:     sections/enzyme-kinetics.tex
% PROJECT:  biochem-lecture-notes
% VERSION:  20260507.001
% AUTHOR:   J. Okonkwo
% PURPOSE:  Enzyme kinetics section: Michaelis-Menten derivation and examples.
%           Input-ted by main.tex.
% REQUIRES: amsmath (align env), siunitx (units in rate expressions)
% ============================================================
% KNOWN WARNINGS:
%   (none)
% ============================================================

\section{Enzyme Kinetics}

The rate of an enzyme-catalyzed reaction is given by...
```

The `PURPOSE` field should name the root file that includes this content.
This makes the dependency explicit and navigable without an IDE. [R, M]

The `REQUIRES` field in a content file lists the packages *this file
introduces* — not the full preamble. This makes the root file auditable: every
package in the preamble should be traceable to at least one content file's
`REQUIRES` field, or to the root file's own structural needs. If a content
file genuinely introduces no new dependencies, write `none` — but be honest
about it; "none" means you checked, not that you skipped the field. The goal
is traceability upward to the root, not exhaustive redundancy. [M, P]

---

## Communicating Structure to Collaborators

When handing a modular project to a new collaborator, or returning to one
after a long absence, the project should be self-orienting. Ensure:

1. **The root file is named unambiguously.** `main.tex` is conventional and
   acceptable. `document.tex` or `final_v3.tex` are not. [R]

2. **The directory structure is shallow and logical.** Deeply nested folders
   are rarely justified in LaTeX projects. One level of subdirectories
   (`figures/`, `sections/`, `tables/`) is usually sufficient. [M]

3. **A `project_philosophy.md` exists** at the project root documenting class
   choice, any deviations from this skill, and any non-obvious structural
   decisions. If it doesn't exist, note its absence. [R]

4. **Every non-obvious dependency is documented.** If the project requires a
   font, a custom `.sty` file, or a specific compiler (e.g., LuaLaTeX rather
   than pdfLaTeX), that requirement lives in the root file header's `REQUIRES`
   field and in `project_philosophy.md`. Do not assume pdfLaTeX — if the
   document uses `fontspec`, Unicode input, or any package that implies a
   specific engine, name the engine explicitly:

   ```latex
   % REQUIRES: LuaLaTeX (fontspec, Unicode math); will not compile under pdfLaTeX
   ```
   [P, R]

---

## When to Split a File

Split a standalone file into a modular project when any of the following is
true:

- The file exceeds ~300 lines and distinct sections are being edited
  independently
- You are commenting out large blocks to work on one section at a time
- The same preamble is being copied to a second file in the same project
- Compile time is becoming a friction point during editing

Do not split prematurely. A modular project has overhead — more files to
track, a root file to maintain, `\includeonly` to manage. That overhead is
worth it at scale; it is waste at small scale. [M]

---

## Build Automation: `latexmk`

LaTeX frequently requires multiple compilation passes to resolve references,
generate a table of contents, or process a bibliography correctly. Running
`pdflatex` once is often insufficient; running it the right number of times
manually is error-prone.

`latexmk` automates this. It detects what passes are needed and runs them in
the correct order:

```bash
latexmk -pdf main.tex        % compile with pdfLaTeX, as many passes as needed
latexmk -pdf -pvc main.tex   % continuous mode: recompile on file change
latexmk -C                   % clean all build artifacts
```

For any project with cross-references, a TOC, or a bibliography, `latexmk`
is the recommended build tool. Manual multi-pass compilation is acceptable
for simple standalone files with no references. [M, C]

In a git-tracked project, add a `.latexmkrc` configuration file at the
project root if non-default options are needed (e.g., LuaLaTeX engine,
output directory). Document this in `project_philosophy.md`. [P, R]

---

## Custom Commands

Custom commands defined with `\newcommand` or `\renewcommand` are source
code. Apply the same discipline here as everywhere else. [R, M, C]

**Where to define them:** In the preamble of a standalone file, or in a
dedicated `macros.tex` file `\input`-ed by the root file in a modular
project. Never define custom commands inside a content file — they become
invisible to the root and to other content files. [M]

**Naming:** Descriptive, unambiguous, and consistent with the domain:

```latex
\newcommand{\Efield}{\vec{E}}         % electric field vector

% #1: vector field to curl (e.g. \Efield, \vec{B})
\newcommand{\curl}[1]{\nabla \times #1}

% #1: vector field to diverge (e.g. \Efield, \vec{B})
\newcommand{\divergence}[1]{\nabla \cdot #1}
```

Avoid single-letter command names (`\E`, `\B`) — they collide with existing
LaTeX internals and other packages unpredictably. [C]

**Documenting arguments:** Any macro that takes arguments must document each
one with a `% #N: <description>` comment immediately above the definition.
The description states what the argument *is* semantically — not its position,
not its type, but its role in the expression. [R]

```latex
% #1: variable of integration (e.g. u, \theta, t)
\newcommand{\dd}[1]{\,\mathrm{d}#1}

% #1: numerator function (e.g. y, f(x))
% #2: denominator variable (e.g. x, t)
\newcommand{\deriv}[2]{\tfrac{\mathrm{d}#1}{\mathrm{d}#2}}

% #1: expression inside bars (e.g. x-1, \sin\theta)
\newcommand{\abs}[1]{\left|#1\right|}
```

A zero-argument macro needs no argument comment — its inline comment is
sufficient. A macro whose argument role is genuinely unambiguous from the
command name alone may have a terse comment, but never omit it entirely:
a terse comment that is read in two seconds is better than no comment that
requires reverse-engineering from usage. [R]

**Never redefine kernel commands** (`\section`, `\bf`, `\rm`, etc.) without
an explicit comment explaining why and what the consequences are. Silent
redefinitions are among the hardest bugs to diagnose. [C, R]

**Prefer `\newcommand` over `\def`.** `\newcommand` errors if the command
already exists; `\def` silently overwrites it. The error is preferable to
the silence. [C]

**For complex argument signatures** (optional arguments, starred variants,
key-value options), use the `xparse` package rather than stretching
`\newcommand` beyond its intended use. [C, P]

**Document every custom command** in `project_philosophy.md` with its
intended use and any limitations. A collaborator should not need to reverse-
engineer a macro from its usage. [R]
