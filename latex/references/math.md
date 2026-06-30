# Math Environments and Notation

Mathematics is the reason most technical documents exist. Getting the
environments, spacing, and notation right is not pedantry — it is the
difference between output that communicates and output that misleads. [C, R]

---

## Environment Selection

### The Decision Rule

| Situation | Environment |
|-----------|-------------|
| Single expression, displayed, numbered | `equation` |
| Single expression, displayed, unnumbered | `equation*` |
| Multi-line, aligned, numbered | `align` |
| Multi-line, aligned, unnumbered | `align*` |
| Multi-line, centered, no alignment needed | `gather` / `gather*` |
| Inline expression | `$...$` |

**Never use `eqnarray`.** It is not deprecated in the sense of "old but
harmless" — it produces incorrect spacing around relation symbols and has no
recovery path. It exists in older documents; do not perpetuate it. [C]

### `equation` vs. `align` — When Each Is Right

Use `equation` when the entire mathematical statement fits on one line and
stands alone as a single claim. Use `align` when:

- The derivation spans multiple lines
- You want to align across an equals sign (or inequality, or arrow)
- You are presenting a system of equations

`align` with a single row is not wrong, but `equation` is cleaner for that
case. Do not use `align` just to avoid thinking about which environment fits —
use the one that matches the structure of the mathematics. [R, C]

### Alignment Column Placement in `align`

The `&` character marks the alignment point. Place it *before* the relation
symbol (equals sign, arrow, etc.), not after. This aligns the relation symbols
vertically across lines, which is the visual goal:

```latex
% Correct — relation symbols align
\begin{align}
    F   &= ma \\
    W   &= Fd \\
    P   &= \frac{W}{t}
\end{align}

% Wrong — leads to misaligned output
\begin{align}
    F &= ma \\
    W = &Fd \\
    P = \frac{W}{&t}
\end{align}
```

For multi-step derivations, align at the relation symbol throughout:

```latex
% Calculus example — integrating by substitution
\begin{align}
    \int_0^1 2x\,e^{x^2} \, \mathrm{d}x
        &= \int_0^1 e^u \, \mathrm{d}u    && u = x^2,\; \mathrm{d}u = 2x\,\mathrm{d}x \\
        &= \left[ e^u \right]_0^1 \\
        &= e - 1
\end{align}
```

Continuation lines with no leading term use an empty left column — do not
invent a phantom symbol to fill the space. [R, C]

### Suppressing Equation Numbers

Number an equation if and only if it will be referenced elsewhere in the
document. Unnecessary numbers create noise and imply significance that isn't
there. Use the starred variant (`equation*`, `align*`) for unnumbered display
math, not `\nonumber` on every line of an `align` block — the starred form is
cleaner and signals intent at the environment level. [R]

Exception: if most lines in an `align` block are numbered but one intermediate
step should not be, `\nonumber` on that line is appropriate.

---

## Labeling and Cross-References

Label every numbered equation. Labels are free — unlabeled numbered equations
are a broken promise. [M, R]

**Label prefix convention:**

```latex
\begin{equation}
    F = ma
    \label{eq:newtons-second}
\end{equation}
```

Always use the `eq:` prefix. This distinguishes equation labels from figure
(`fig:`), table (`tab:`), and section (`sec:`) labels, which matters in large
documents where label names collide. [R, M]

Use `\eqref{}` to reference equations, not `(\ref{})`. `\eqref` adds the
parentheses automatically and signals to a collaborator reading the source that
an equation (not a figure or section) is being referenced. [R]

```latex
% Correct
As shown in \eqref{eq:newtons-second}, force is proportional to acceleration.

% Wrong — fragile and ambiguous
As shown in (\ref{eq:newtons-second}), ...
```

---

## Units

Units are not mathematical variables. They must be typeset in upright (roman)
text, never in italic math mode. [C]

**Preferred: `siunitx`**

```latex
\usepackage{siunitx}

% Quantities
\qty{9.81}{m/s^2}           % preferred modern syntax
\qty{3.0e8}{m/s}            % scientific notation handled automatically

% Units alone (in text or table headers)
\unit{kg.m/s^2}
\unit{\newton}              % named units available as macros
```

`siunitx` handles spacing between value and unit, decimal formatting,
uncertainty notation, and table alignment. Use it whenever units appear. [C, R]

**When `siunitx` is unavailable:** use `\text{}` inside math mode.

```latex
% Acceptable fallback — thin space + upright text
$v = 30\,\text{m/s}$

% Wrong — units in italic math mode, no spacing
$v = 30 m/s$
```

Note in the preamble why `siunitx` is absent (see `packages.md`). [R]

---

## Notation Conventions

### Text in Math Mode

Use `\text{}` for words, abbreviations, and labels that appear inside a math
environment. Do not use `\mathrm{}` for running text — `\mathrm` is for
upright mathematical symbols (e.g., differential operators), not words. [C, R]

```latex
% Correct
E_{\text{kinetic}} = \frac{1}{2}mv^2

% Wrong — italic subscript reads as a product of variables
E_{kinetic}
```

### Operators

Use built-in operator commands. Do not type operator names as plain letters —
they will render in italic and lose their spacing. [C]

```latex
% Correct
\sin\theta \quad \cos\theta \quad \ln x \quad \lim_{x \to 0}

% Wrong
sin\theta \quad cos\theta \quad ln x
```

For operators not covered by builtins, define them in the preamble with
`\DeclareMathOperator`:

```latex
\DeclareMathOperator{\curl}{curl}
\DeclareMathOperator{\Res}{Res}
```

This keeps operator formatting consistent and centralizes definitions. [M, R]

**On `\mathrm{}` for operator names:** `\mathrm{ker}`, `\mathrm{curl}`, etc. produce upright text but carry ordinary-symbol math class — spacing around them will be wrong in expressions like `\mathrm{ker}(A)`. Always use `\DeclareMathOperator` for named operators, even one-off ones. `\mathrm{}` is correct only for upright *symbols* (differentials, physical constants, labeled quantities) — not for operator names. [C]

### Differentials

Differentials (`dx`, `dt`, etc.) are operators, not variables, and should be
upright. Standard approach without a physics package:

```latex
\int f(x) \, \mathrm{d}x
```

If `physics2` is loaded with the `nabla` or `ab` module, or `physics` (legacy)
is in use, `\dd{x}` provides the same result semantically:

```latex
\int f(x) \, \dd{x}
```

For guidance on which physics notation package to use (or whether to use one
at all), see the Physics Notation Packages section below.

The thin space `\,` before the differential is not optional — it separates the
integrand from the operator visually. [C]

For the evaluation bar (`f(x)\big\rvert_{x=0}`), see §9 of `symbols.md`.

### Ellipses

Use `\dots` (amsmath). The command is context-aware and chooses between
`\ldots` and `\cdots` automatically. Only override it when the auto-detection
is wrong — in that case, use the explicit semantic commands `\dotsc` (commas),
`\dotsb` (binary operators), `\dotsi` (integrals). Never use `...` in math
mode. [C]

### Quotation Marks and Punctuation

Grammatical punctuation (commas, periods) must not appear inside inline math
delimiters. [C, R]

```latex
% Correct
Let $x$, $y$, and $z$ be real numbers.
We find $x = 3$.

% Wrong — comma and period inside math
Let $x, y, z$ be real numbers.
We find $x = 3.$
```

### Inline Math Delimiters

Two forms are in common use:

```latex
$x^2 + y^2 = z^2$      % TeX primitive — universal; shorter to type
\(x^2 + y^2 = z^2\)    % LaTeX command — preferred by the LaTeX project
```

Both produce identical output. The LaTeX Project recommends `\(...\)` because
`$` is a TeX primitive whose error messages are less informative, and because
`$$...$$` (its display counterpart) actively breaks things. In practice, `$...$`
is overwhelmingly prevalent and entirely functional.

**Skill position:** this is a project-level preference, not a correctness
issue. Whichever form is chosen, apply it consistently. Document the choice
in `project_philosophy.md` if it matters to the project. Never use `$$...$$`
for display math — use `\[...\]` or an environment instead (see below). [R]

Use `\frac{}{}` in displayed equations. In inline math, `\frac` produces output
that is cramped and hard to read — prefer `a/b` notation or use `\dfrac` only
when the fraction is the primary content of the inline expression and readability
demands it. [R, C]

### Unit Vectors and Hat Notation

Two conventions exist for unit vectors; choose one per project and apply it
consistently. Document the choice in `project_philosophy.md`. [R, C]

```latex
% Hat notation — common in introductory physics and engineering
\hat{i}, \hat{j}, \hat{k}          % Cartesian basis vectors
\hat{r}, \hat{\theta}, \hat{\phi}  % spherical
\hat{n}                             % generic normal vector

% Bold notation — common in advanced physics and mathematics
\mathbf{e}_x, \mathbf{e}_y, \mathbf{e}_z
\mathbf{e}_r, \mathbf{e}_\theta
```

Do not mix conventions within a document. Do not use plain `i`, `j`, `k` for
vectors — they are indistinguishable from scalar indices in italic math mode.

---

## Symbols

For symbol selection beyond what this file covers — logical operators,
implication and equivalence arrows, equality variants, set notation,
vector/tensor notation, and the "therefore/because/implies" family — see
`references/symbols.md`. [R, C]

---

## Spacing

Do not use manual spacing commands (`\quad`, `\,`, `\!`, `\ `) to patch around
broken environments. If spacing looks wrong, the environment or notation choice
is likely wrong. Manual spacing is appropriate only for:

- The thin space `\,` before differentials (always required — see above)
- Separating adjacent binary operators or relations that amsmath doesn't handle
- Fine-tuning alignment in `align` blocks where the math genuinely requires it

Document any non-obvious manual spacing with a comment explaining why it's
needed. [R, C]

---

## Physics Notation Packages

For documents heavy in physics notation — Dirac bra-ket, derivatives, vector
calculus operators — a dedicated package can reduce preamble boilerplate
significantly. Two options exist; they are not interchangeable.

### `physics` (legacy — use with caution)

The `physics` package (v1.3, last updated December 2012) is widely found in
existing documents and is still commonly taught. It provides `\ket{}`,
`\bra{}`, `\dd{}`, `\dv{}`, `\pdv{}`, `\vb{}`, and operator shorthands.

**Use it if:** you are working with an existing document that already loads it,
or a collaborator or template requires it.

**Do not start new projects with it.** The package has been unmaintained for
over twelve years. Known problems include: silent redefinition of `\qty`
(conflicts with `siunitx` v3+); redefinition of trig functions in ways that
break other packages; spacing inconsistencies reported on TeX.SE. A community
fork (`physics-patch`) exists specifically to work around its bugs.

If you inherit a document using `physics` alongside `siunitx`, add this shim
to the preamble immediately after both `\usepackage` lines:

```latex
\usepackage{physics}
\usepackage{siunitx}
\AtBeginDocument{\RenewCommandCopy\qty\SI}  % restore siunitx \qty after physics clobbers it
```

Document this shim in a preamble comment with the rationale. [C, R]

### `physics2` (recommended for new projects)

`physics2` (2022, actively maintained) is a modular redesign that avoids
`physics`'s known problems. Load only the modules you need:

```latex
\usepackage{physics2}
\usephysicsmodule{ab}          % automatic braces: \ab(...)
\usephysicsmodule{braket}      % Dirac notation: \bra{}, \ket{}, \braket{}
\usephysicsmodule{ab.legacy}   % optional: physics-compatible \qty, \pqty, etc.
```

`physics2` does not conflict with `siunitx` v3 by default. Consult its
documentation for the full module list. [P, R, C]

**Either way:** these are domain-specific optional packages. Only load one if
the project genuinely uses the notation they provide. Document the choice in
`project_philosophy.md` and note the version constraint in `REQUIRES`. [M, P]

---

## Theorem Environments

For documents presenting formal mathematical results — theorems, lemmas,
propositions, definitions, proofs, remarks — `amsthm` is the standard
package. Do not fake theorem environments with manual bold labels, `\medskip`,
or ad-hoc spacing. `amsthm` provides correct vertical spacing, consistent
numbering, `cleveref` integration, and the `proof` environment with automatic
QED placement. [C, R, M]

### Loading and Styles

```latex
\usepackage{amsthm}   % theorem environments; load after amsmath
```

`amsthm` provides three predefined styles that control the visual weight of
label and body text:

| Style | Label | Body | Use for |
|-------|-------|------|---------|
| `plain` | Bold upright | Italic | Theorems, lemmas, propositions, corollaries |
| `definition` | Bold upright | Upright | Definitions, examples, exercises |
| `remark` | Italic | Upright | Remarks, notes, observations |

The italic body of `plain` style is not decoration — it is a visual signal
that a formal claim follows. Use it only for environments that assert
mathematical statements. Using `plain` style for definitions or examples
misleads the reader. [C, R]

### Declaring Environments

Declare all theorem environments in the preamble (or `macros.tex` for
modular projects), grouped by style and ordered logically:

```latex
% --- Theorem environments ---
\theoremstyle{plain}
\newtheorem{theorem}{Theorem}[section]       % numbered within section: Theorem 2.1
\newtheorem{lemma}[theorem]{Lemma}           % shares counter with theorem
\newtheorem{proposition}[theorem]{Proposition}
\newtheorem{corollary}[theorem]{Corollary}

\theoremstyle{definition}
\newtheorem{definition}[theorem]{Definition} % shares counter
\newtheorem{example}{Example}[section]       % independent counter

\theoremstyle{remark}
\newtheorem*{remark}{Remark}   % unnumbered — * suppresses counter entirely
\newtheorem*{note}{Note}
```

**Counter sharing.** Passing `[theorem]` as the second argument shares the
`theorem` counter, producing interleaved numbering (Theorem 2.1, Definition
2.2, Lemma 2.3). This keeps readers oriented in dense material. Independent
counters (no `[theorem]`) produce separate sequences (Theorem 1, Definition 1,
Lemma 2), which is appropriate when examples are numerous and would otherwise
crowd the theorem count. Document the choice in `project_philosophy.md`. [R]

**Numbering by section.** Passing `[section]` as the third argument resets the
counter each section and prefixes it with the section number (Theorem 3.1).
This is almost always the right choice for any document longer than a short
handout. [R]

### The `proof` Environment

`amsthm` provides `\begin{proof}...\end{proof}` with an automatic QED symbol
(■ by default, customizable via `\renewcommand{\qedsymbol}{}`):

```latex
\begin{proof}
    By induction on $n$. The base case $n = 0$ is immediate.
    For the inductive step, assume the result holds for $n - 1$. \ldots
\end{proof}
```

When the proof ends with a displayed equation, the QED symbol migrates to its
own line. Pin it to the equation with `\qedhere`:

```latex
\begin{proof}
    Substituting \eqref{eq:definition} into the integral gives
    \begin{equation*}
        \int_0^1 f(x)\,\mathrm{d}x = 0. \qedhere
    \end{equation*}
\end{proof}
```

To label a proof for a specific result (e.g., for a deferred or out-of-order
proof):

```latex
\begin{proof}[Proof of \cref{thm:main}]
    \ldots
\end{proof}
```

### Labeling and Cross-References with `cleveref`

Use the `thm:` prefix for all theorem-family labels regardless of environment
type. This is consistent with the `eq:`, `fig:`, `tab:`, `sec:` prefix
convention and prevents collisions: [R, M]

```latex
\begin{theorem}
    \label{thm:cauchy-schwarz}
    For all vectors $\mathbf{u}, \mathbf{v} \in \mathbb{R}^n$, \ldots
\end{theorem}
```

Register each environment with `cleveref` so that `\cref{thm:cauchy-schwarz}`
produces "Theorem 3.1" rather than a bare number. Add after
`\usepackage{cleveref}`:

```latex
\crefname{theorem}{Theorem}{Theorems}
\crefname{lemma}{Lemma}{Lemmas}
\crefname{proposition}{Proposition}{Propositions}
\crefname{corollary}{Corollary}{Corollaries}
\crefname{definition}{Definition}{Definitions}
\crefname{example}{Example}{Examples}
```

With this setup: `\cref{thm:cauchy-schwarz}` → "Theorem 3.1";
`\Cref{thm:cauchy-schwarz}` → "Theorem 3.1" (sentence-initial). Multi-reference
syntax works automatically: `\cref{thm:a,lemma:b}` → "Theorem 3.1 and Lemma 3.4".
[R, M]

### When Not to Use `amsthm`

`amsthm` environments carry semantic weight: the italic body of `plain` style
signals a formal mathematical claim. In problem sets, worksheets, or other
documents that want visually similar boxes for exercises or solutions without
that semantic, consider a `tcolorbox`-based environment instead — it avoids
misleading the reader about what constitutes a proved result. [R, C]
