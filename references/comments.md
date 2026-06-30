# Inline Commenting Discipline

Comments in LaTeX source are not documentation for the PDF reader — they are
documentation for the source reader. That reader is often a future collaborator,
or future you, encountering the file cold. Write comments accordingly. [R, M]

---

## Core Principle: Comment Intent, Not Mechanics

A comment that restates what the code does is noise. A comment that explains
*why* a decision was made is signal.

```latex
% Noise — the code already says this
\vspace{1em}  % add 1em vertical space

% Signal — this is information the code cannot convey
\vspace{1em}  % compensate for missing \medskip after theorem env in this class
```

The test: if you deleted the comment, would a competent LaTeX user reading the
code lose any information they couldn't recover by reading the code itself? If
no, the comment is noise. If yes, it earns its place. [R]

---

## What Always Gets a Comment

### Domain-Specific Conventions

Any decision that encodes a domain convention invisible to the LaTeX itself must
be flagged. The reader may be a capable LaTeX author but unfamiliar with the
field-specific choice being made.

**Sign conventions (physics/engineering):**
```latex
% Sign convention: current flows into the positive terminal (passive sign convention)
I_1 + I_2 - I_3 = 0
```

**Coordinate system or orientation choices (mathematics/physics):**
```latex
% z-axis points out of the page; right-hand rule applies throughout
\vec{B} = B_0 \hat{z}
```

**Unit system (any quantitative discipline):**
```latex
% All quantities in SI unless noted; eV used for particle energies only
E = \qty{511}{keV}  % electron rest energy
```

**Notation choice (pure mathematics):**
```latex
% Using f' for derivative throughout; \dot{f} reserved for time derivatives only
f'(x) = \lim_{h \to 0} \frac{f(x+h) - f(x)}{h}
```

**Index conventions (linear algebra / tensor notation):**
```latex
% Einstein summation convention in force: repeated indices are summed
T^{ij} A_{ij}   % implicitly summed over i and j
```

The pattern is the same in every domain: when the code is ambiguous without
knowing the convention, name the convention. [R, C]

### Non-Obvious Structural Choices

When a structural decision looks wrong but is intentional, say so:

```latex
% \include used here (not \input) to enable \includeonly during drafting
\include{chapters/ch3-results}
```

```latex
% align* intentional — this derivation is intermediate; eq numbers would imply
% these steps are referenceable, which they are not
\begin{align*}
```

### Manual Spacing and Overrides

Any manual spacing command that isn't immediately obvious (i.e., not the
standard `\,` before a differential) gets a comment:

```latex
\!\!  % optical correction: closing bracket sits too far from preceding radical
```

### Hacks and Workarounds

If something looks like a hack, it probably is one. Use the sanctioned
`% HACK:` label (see TODO and HACK Comments below) and note what it's
working around:

```latex
% HACK: shrink to fit two-column constraint; revisit if template updates past v2.3
{\fontsize{9.5}{11}\selectfont
```

---

## TODO and HACK Comments

Two comment labels are sanctioned by this skill:

```latex
% TODO: replace placeholder values with measured data
% TODO: add figure reference once Fig. 3 is finalized
% HACK: shrink font to fit two-column constraint — revisit if template changes
```

**`% TODO:`** — marks something incomplete or deferred. Use for anything that
must be resolved before the document is final.

**`% HACK:`** — marks a workaround: code that is technically functional but
not the right solution. Always include what is being worked around and when
it should be revisited. A `HACK:` that can never be resolved is a sign the
underlying problem should be fixed instead.

Rules for both labels:
- Always uppercase label, colon, space — `% TODO:` and `% HACK:` exactly.
  No variations (`FIXME:`, `NOTE:`, `XXX:`) unless the project defines and
  documents them in `project_philosophy.md`. If a project does define
  additional labels, they must follow the same format (uppercase, colon,
  space) and be applied consistently across all project files.
- Be specific enough that the item is actionable without re-reading the
  surrounding context
- Resolve by fixing and removing the comment — never annotate as done with
  `% TODO: done` or similar [M]

---

## What Does Not Get a Comment

**Standard environments used standardly:**
```latex
% Don't do this — the environment name is self-documenting
\begin{equation}  % equation environment
```

**Package behavior that is well-documented:**
```latex
% Don't do this
\toprule  % draws a thick top rule (booktabs)
```

**Label names that speak for themselves:**
```latex
% Don't do this
\label{eq:energy-conservation}  % label for energy conservation equation
```

Treat comment space as a limited resource. Overcommenting trains readers to
skip comments, which defeats the purpose. [R]

---

## Comment Formatting

Inline comments have three forms depending on length. Choose the form that
fits — do not force a short explanation into a long form or compress a
necessary explanation into too few words. [R, M]

### Form 1 — Inline at column 48 (preferred for short, uniform lines)

Align to column 48 where the code line is short enough to accommodate it.
This is the default for preamble package declarations and other uniform blocks
where a two-column scan (code left, intent right) aids readability:

```latex
\usepackage{booktabs}           % professional horizontal rules in tables
\usepackage{siunitx}            % unit typesetting throughout all sections
```

Keep the comment itself concise — aim for one clause, not a sentence. If the
comment at column 48 would exceed roughly 80 characters total line length,
use Form 2 instead.

### Form 2 — Single trailing comment, no column constraint

When the code line is too long for column 48 alignment to be practical —
`\input` calls, multi-option `\usepackage` blocks, long command definitions —
place a single inline comment at the end of the line with no forced alignment.
Leave three spaces between the end of the code and the `%` so the comment
doesn't visually bleed into the line. Keep the total line length under
~120 characters. Conciseness still applies: this is not a license for
paragraph-length annotations.

```latex
\input{sections/ee-dc-circuits-recall}   % DC Circuits: Ohm, KVL, KCL, mesh/node, Thevenin/Norton
```

### Form 3 — Standalone comment above the code

When the explanation genuinely requires more than one concise clause — a
rationale that references an external document, a multi-part constraint, or
anything that would exceed ~120 characters as a trailing comment — place it
on its own line above the code it describes:

```latex
% 0.5 in margins per project philosophy §7; adjust toward 0.75 in for prose-heavy sections.
\usepackage[top=0.5in, bottom=0.5in, left=0.5in, right=0.5in]{geometry}
```

**Never use hanging multi-line trailing comments.** A comment that begins
at the end of a code line and continues on subsequent lines is always wrong —
it is harder to read than either a single trailing comment or a standalone
block above. If it won't fit in one line, it goes above. [R, M]

```latex
% Wrong — hanging continuation
\usepackage{geometry}   % sets page margins
                        % see philosophy for rationale

% Right — standalone above
% Margins per ee-philosophy.md §7; adjust toward 0.75 in for prose-heavy sections.
\usepackage[top=0.5in, bottom=0.5in, left=0.5in, right=0.5in]{geometry}
```

---

## Section Dividers

Use comment dividers to delineate logical sections of a long preamble or
document body. Keep them consistent in style:

```latex
% ============================================================
% PREAMBLE — Mathematics
% ============================================================
```

Do not invent a new divider style mid-document. If the project doesn't have
one established, use the form above. [M, R]
