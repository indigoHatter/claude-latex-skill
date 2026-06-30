# Antipatterns

This file is a consolidated audit reference. When reviewing existing LaTeX —
your own or inherited — scan this list first. Each entry states what the
antipattern looks like, why it's wrong, and what to do instead. Rationale is
kept brief; cross-references point to the relevant file for full treatment.

Antipatterns are organized by category. Severity is noted where relevant:
**[hard error]** means it will break compilation or produce provably wrong
output; **[silent failure]** means it compiles but produces incorrect or
misleading results; **[practice]** means it's technically functional but
creates maintainability or readability debt.

---

## Math

**Using `eqnarray`** [silent failure]
```latex
% Wrong
\begin{eqnarray}
    F &=& ma
\end{eqnarray}
```
Produces incorrect spacing around relation symbols. No valid use case.
Replace with `equation` or `align`. → `math.md`

---

**Units in italic math mode** [silent failure]
```latex
% Wrong
$v = 30 m/s$
$F = 10 N$
```
Units are not variables. Italic rendering implies multiplication of unknowns.
Use `siunitx` commands or the plain fallback:

```latex
% Correct — siunitx
$v = \qty{30}{m/s}$
$F = \qty{10}{N}$

% Correct — fallback when siunitx unavailable
$v = 30\,\text{m/s}$
$F = 10\,\text{N}$
```
→ `math.md`

---

**Plain letters for operator names** [silent failure]
```latex
% Wrong
$sin\theta$
$lim_{x \to 0} f(x)$
```
Renders in italic with incorrect spacing. Use `\sin`, `\lim`, etc. For custom
operators, use `\DeclareMathOperator`. → `math.md`

---

**Numbered equations that are never referenced** [practice]
```latex
\begin{equation}
    x = 2   % number appears in output; nothing ever cites it
\end{equation}
```
Unnecessary numbers imply significance and create noise. Use `equation*` or
`align*` for unreferenced display math. → `math.md`

---

**Unlabeled numbered equations** [practice]
```latex
\begin{equation}
    E = mc^2
    % no \label
\end{equation}
```
A numbered equation without a label cannot be referenced reliably. Either
label it or suppress the number. → `math.md`

---

**`(\ref{})` instead of `\eqref{}`** [practice]
```latex
% Wrong
As shown in (\ref{eq:energy}), ...
```
Fragile and semantically ambiguous. Use `\eqref{eq:energy}`. → `math.md`

---

**`\autoref{}` when `cleveref` is loaded** [practice]
```latex
% Suboptimal — \autoref is hyperref's built-in cross-reference macro
\autoref{eq:energy}   % "Equation 1" — inflexible, not customizable
\autoref{fig:circuit}
```
`\autoref` (from `hyperref`) and `\cref` (from `cleveref`) produce similar
output, but `\autoref` offers no customization, no multi-reference syntax
(`\cref{eq:a,eq:b}` → "Equations 2 and 3"), and no sentence-case control
(`\Cref{}` for sentence-initial use). When `cleveref` is loaded it supersedes
`\autoref` entirely — use `\cref{}` and `\Cref{}` exclusively. Mixed use
of both in a single document is a silent consistency failure. → `packages.md`, `math.md`

---

**`...` in math mode** [silent failure]
```latex
% Wrong
$x_1, x_2, ... , x_n$
```
Produces low-positioned dots with incorrect spacing. Use `\dots` (amsmath
selects the correct form automatically). → `math.md`

---

**`\frac` in inline math** [practice]
```latex
% Avoid
The ratio is $\frac{a}{b}$.
```
Produces cramped, hard-to-read output in running text. Use `a/b` notation
inline, or `\dfrac` only when the fraction is the primary content and
readability genuinely demands it. → `math.md`

---

**Missing `\,` before differentials** [silent failure]
```latex
% Wrong
$\int f(x) dx$
```
The integrand and differential run together visually. Always use
`$\int f(x) \, \mathrm{d}x$`. → `math.md`

---

**Punctuation inside inline math delimiters** [practice]
```latex
% Wrong
Let $x, y, z$ be real numbers.
We find $x = 3.$
```
Grammatical punctuation belongs outside math mode. → `math.md`

---

## Tables

**Vertical rules** [practice]
```latex
% Wrong
\begin{tabular}{|l|c|r|}
```
Adds visual noise, fragments the reading path, implies cell-boundary
significance the data rarely has. Remove all `|` from column specs. → `tables.md`

---

**`\hline` in a booktabs table** [practice]
```latex
% Wrong — mixing conventions
\toprule
...
\hline
...
\bottomrule
```
`\hline` has different weight and spacing than booktabs rules. Never mix them.
Use only `\toprule`, `\midrule`, `\bottomrule`, and `\addlinespace`. → `tables.md`

---

**Double rules** [practice]
```latex
% Wrong
\hline\hline
```
Always wrong. `\toprule` and `\bottomrule` provide the visual weight intended
by double rules, without the doubled-line artifact. → `tables.md`

---

**Caption below a table** [practice]
```latex
% Wrong
\end{tabularx}
\caption{Results.}
```
Captions go above tables, below figures. A table caption below forces the
reader to parse the data before knowing its context. → `tables.md`

---

**`\label` before `\caption`** [hard error]
```latex
% Wrong
\label{tab:results}
\caption{Results.}
```
Produces incorrect reference numbers. `\label` must always follow `\caption`
immediately. → `tables.md`

---

**Numeric data in non-`S` columns** [silent failure]
```latex
% Wrong — numbers won't align at decimal point
\begin{tabular}{l r r}
    R1 & 1000.0 & 0.05 \\
    R2 &  470.2 & 5.00 \\
```
Use `S[table-format=...]` columns from `siunitx` for any numeric data.
→ `tables.md`

---

**Unbraced headers over `S` columns** [hard error]
```latex
% Wrong
Voltage (\unit{\volt})   % siunitx tries to parse this as a number
```
Wrap column headers over `S` columns in braces: `{Voltage (\unit{\volt})}`.
→ `tables.md`

---

**`[h]` placement specifier alone** [practice]
```latex
% Wrong
\begin{table}[h]
```
Frequently causes layout failures when LaTeX cannot fit the float at the
exact position. Use `[htbp]` as the default. Reserve `[H]` (float package)
for cases where absolute placement is genuinely required, with a comment. → `tables.md`

---

## File Headers and Versioning

**No file header** [practice]
A `.tex` file produced or edited by this skill without a header block is
non-compliant. See `headers.md` for the required format and the procedure for
handling inherited files that lack one.

---

**Version numbers in filenames** [practice]
```
% Wrong
gauss-law-v2.tex
main-FINAL.tex
main-FINAL-REAL.tex
```
Filenames encode no useful version history and create ambiguity about which
file is current. Version information belongs in the header `VERSION` field;
history belongs in git. → `naming.md`

---

**Omitting fields from the header** [practice]
```latex
% Wrong — PURPOSE left blank
% PURPOSE:
```
Every header field is mandatory. Write `<unknown>` if a value is genuinely
unavailable at creation time. Blank fields provide false assurance. → `headers.md`

---

## Package Management

**Loading deprecated packages** [hard error / silent failure]
See the full list in `packages.md`. Key offenders: `epsfig`, `fixltx2e`,
`t1enc`, `pslatex`. The `eqnarray` environment (not a package) is also always
wrong — see the Math section above.

---

**Speculative package loading** [practice]
```latex
% Wrong — loaded "just in case"
\usepackage{tikz}
\usepackage{listings}
\usepackage{multicol}
```
Every package in the preamble must be needed by the document. Unused packages
increase compile time, raise conflict risk, and mislead collaborators about
what the document uses. → `packages.md`

---

**No inline comment on `\usepackage`** [practice]
```latex
% Wrong
\usepackage{siunitx}
```
Every package load must explain why it's there in the context of *this*
document. → `packages.md`

---

**Wrong load order** [hard error / silent failure]
Loading `cleveref` before `hyperref`, `mathtools` before `amsmath`, or any
package in a position that violates documented requirements causes failures
ranging from hard errors to silent incorrect output. Consult `packages.md`
load order rules and CTAN documentation when uncertain. → `packages.md`

---

## File Structure and Naming

**Non-`main.tex` root filename** [practice]
```
% Wrong
document.tex
thesis_final.tex
assignment-worksheet.tex   % as the root/entry point, not a content file
```
The root file is always `main.tex`. Any other name forces collaborators and
build tools to discover it rather than assume it. → `naming.md`

---

**Spaces or special characters in filenames** [hard error]
```
% Wrong
Gauss Law Notes.tex
week3_handout (final).tex
```
Spaces break command-line tools and naive build scripts without always
producing an obvious error. All filenames: lowercase, hyphens only,
no spaces, no special characters. → `naming.md`

---

**Content-describing position rather than subject** [practice]
```
% Wrong
section3.tex
part-two.tex
```
Position changes; content doesn't. Name files for what they contain:
`gauss-law.tex`, `worked-examples.tex`. → `naming.md`

---

**`.jpg` for diagrams or plots** [practice]
JPEG compression introduces artifacts at edges and fine lines — exactly where
technical figures have detail. Use `.pdf` for vector graphics and `.png` for
raster. `.jpg` is acceptable only for photographs. → `naming.md`

---

## Comments

**Comments that restate the code** [practice]
```latex
% Wrong
\toprule  % draws a top rule
\vspace{1em}  % adds 1em of space
```
Comments document intent, not mechanics. If the comment would be obvious to
any competent LaTeX reader, delete it. → `comments.md`

---

**TODOs without actionable content** [practice]
```latex
% Wrong
% TODO: fix this
% TODO: check
```
A TODO must be specific enough to act on without re-reading surrounding
context. → `comments.md`

---

**Stale TODO entries left after resolution** [practice]
Resolved TODOs must be removed, not annotated as done. A `% TODO: done`
comment is noise. → `comments.md`

---

## General

**`$$...$$` for display math** [silent failure]
```latex
% Wrong
$$F = ma$$
```
`$$...$$` is a TeX primitive. In LaTeX documents it produces wrong vertical
spacing, breaks with `fleqn` and AMS environments, and generates unhelpful
error messages. Use `\[...\]` for unnumbered display math, or `equation` /
`equation*` / `align` / `align*` for full environments. → `math.md`

---

**`\left`/`\right` used uncritically** [practice]
```latex
% Problematic — auto-sizing applies even to single-character delimiters,
% producing oversized parentheses and adding phantom whitespace
\left( x \right)
```
`\left` and `\right` auto-size based on the tallest content between them,
which is wrong for single-character expressions and can introduce unexpected
spacing. Prefer explicit size commands (`\bigl`, `\Bigl`, `\biggl`, `\Biggl`
and right counterparts) whenever the correct size is known. Reserve
`\left`/`\right` for genuinely variable-height content. → `symbols.md`

---

**Ignoring compile warnings** [practice / silent failure]
Unreviewed warnings are bugs deferred, not bugs absent. Every warning in a
delivered file should either be fixed or documented in the `KNOWN WARNINGS`
header block with a rationale. `<unknown - new doc>` or `<unknown - new
revision>` are the correct defaults when verification hasn't been run yet —
never write `(none)` without having verified. → `warnings.md`, `headers.md`,
`compilation.md`

---

**Manual spacing to patch broken output** [practice]
```latex
% Wrong — using \quad to hide an alignment problem
F \quad = ma
```
If spacing looks wrong, the environment or notation choice is likely wrong.
Manual spacing commands (`\quad`, `\!`, `\ `) are appropriate only in
documented, specific cases. → `math.md`

---

## Typography

**Straight quotes or raw TeX backtick-apostrophe for quotation marks** [practice / silent failure]
```latex
% Wrong — straight quotes (paste from word processors; render as inch marks)
"This is quoted text"

% Wrong — TeX primitive style; hardcodes English typographic conventions and
% is fragile under non-English babel/fontenc combinations
``This is quoted text''
```
Use `\enquote{}` from `csquotes`:

```latex
\usepackage{csquotes}

\enquote{This is quoted text}           % locale-aware, correct for document language
\enquote{outer \enquote{inner} text}   % nested quotes handled automatically
```

`\enquote` integrates with `babel` and `polyglossia`, adapts automatically
when the document language changes, and nests without manual escaping. The
backtick-apostrophe form is technically functional in monolingual pdfLaTeX
documents — but `\enquote` costs nothing and is always correct. → `packages.md`

