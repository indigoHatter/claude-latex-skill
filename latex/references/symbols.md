# Symbol Reference

This file covers symbol selection for common mathematical, physical, and
engineering notation: cases where multiple LaTeX commands exist for visually
similar output but carry different semantic meaning, and cases where the
correct command is non-obvious. The goal is to make choices deliberate rather
than accidental.

**Scope:** Logic and proof notation; equality and relation variants; set
notation; calculus and analysis; vector and tensor notation; physics and
engineering symbols; Greek letters with common usage notes.

When a symbol choice depends on domain convention (e.g., nabla notation in
fluids vs. electrodynamics), document the choice in `project_philosophy.md`
and apply it consistently throughout the project. [R, C]

---

## Table of Contents

1. [Logic and Proof Notation](#1-logic-and-proof-notation)
2. [Implication and Equivalence Arrows](#2-implication-and-equivalence-arrows)
3. [Equality and Relation Variants](#3-equality-and-relation-variants)
4. [Set Notation](#4-set-notation)
5. [Calculus and Analysis](#5-calculus-and-analysis)
6. [Vector and Tensor Notation](#6-vector-and-tensor-notation)
7. [Physics and Engineering Symbols](#7-physics-and-engineering-symbols)
8. [Greek Letters — Usage Notes](#8-greek-letters--usage-notes)
9. [Miscellaneous — Commonly Confused](#9-miscellaneous--commonly-confused)

---

## 1. Logic and Proof Notation

| Symbol | Command | Meaning | Notes |
|--------|---------|---------|-------|
| $\forall$ | `\forall` | "for all" | Universal quantifier |
| $\exists$ | `\exists` | "there exists" | Existential quantifier |
| $\nexists$ | `\nexists` | "there does not exist" | Requires `amssymb` |
| $\neg$ | `\neg` | logical NOT | Prefer over `\lnot` (same output, but `\neg` is more idiomatic in math mode) |
| $\land$ | `\land` | logical AND | Prefer over `\wedge` for propositional logic; `\wedge` reads as exterior product in some contexts |
| $\lor$ | `\lor` | logical OR | Prefer over `\vee` for propositional logic |
| $\top$ | `\top` | tautology / true | Also used for transpose — see Section 6 |
| $\bot$ | `\bot` | contradiction / false; also "perpendicular" | Context-dependent — comment if ambiguous |
| $\vdash$ | `\vdash` | syntactic entailment ("proves") | |
| $\models$ | `\models` | semantic entailment ("satisfies") | Do not confuse with `\vDash` (double stroke) |
| $\therefore$ | `\therefore` | "therefore" | Requires `amssymb`. Use in informal proofs; formal proofs prefer explicit quantifier statements |
| $\because$ | `\because` | "because" | Requires `amssymb`. Often overused — prefer prose in formal writing |

**On `\therefore` and `\because`:** These are common in handwritten and
informal proofs but are rarely seen in published mathematics. If your document
is intended for academic submission, prefer explicit connective prose
("thus," "since," "it follows that") over these symbols. If used, be
consistent throughout. [R]

---

## 2. Implication and Equivalence Arrows

This is one of the highest-stakes symbol-choice areas in mathematical writing.
Visually similar commands have distinct semantic weight.

| Symbol | Command | Meaning | When to use |
|--------|---------|---------|-------------|
| $\to$ | `\to` | maps to (function); also weak implication in some notations | Function definition: $f: A \to B$. Avoid for logical implication. |
| $\Rightarrow$ | `\Rightarrow` | implies (double-stroke right arrow) | Standard for logical implication in most math/CS contexts |
| $\implies$ | `\implies` | implies (with extra spacing) | amsmath command. Preferred over `\Rightarrow` in displayed proof steps — the spacing reads more naturally |
| $\Leftrightarrow$ | `\Leftrightarrow` | if and only if (iff) | Compact form |
| $\iff$ | `\iff` | if and only if | amsmath command. Preferred over `\Leftrightarrow` in displayed proof steps |
| $\leftarrow$ | `\leftarrow` / `\gets` | single left arrow | |
| $\Leftarrow$ | `\Leftarrow` | implied by | |
| $\rightrightarrows$ | `\rightrightarrows` | double right arrows (e.g., parallel transport, limits) | Requires `amssymb` |
| $\mapsto$ | `\mapsto` | maps element to element | $x \mapsto x^2$. Do not use `\to` for this. |
| $\hookrightarrow$ | `\hookrightarrow` | injection / inclusion map | |
| $\twoheadrightarrow$ | `\twoheadrightarrow` | surjection | Requires `amssymb` |

**Rule:** In proof writing, use `\implies` and `\iff` (amsmath) for displayed
steps, and `\Rightarrow` / `\Leftrightarrow` inline. Never use `\to` for
logical implication — it reads as a function arrow. [C, R]

---

## 3. Equality and Relation Variants

| Symbol | Command | Meaning | Notes |
|--------|---------|---------|-------|
| $=$ | `=` | equality | |
| $\neq$ | `\neq` | not equal | |
| $\equiv$ | `\equiv` | identical to; congruence; defined as | Context-dependent — see below |
| $\triangleq$ | `\triangleq` | defined as / defined equal to | Requires `amssymb`. Preferred over `:=` in physics and engineering; `:=` preferred in CS/formal math |
| $:=$ | `:=` | defined as (CS/formal math convention) | Two separate characters. If used heavily, define a macro: `\newcommand{\defeq}{:=}` |
| $\doteq$ | `\doteq` | approximately equal (dot over equals) | Less common; context-specific |
| $\approx$ | `\approx` | approximately equal | General approximation |
| $\sim$ | `\sim` | asymptotically equivalent; distributed as; similar | High context-dependence — see below |
| $\simeq$ | `\simeq` | approximately equal (simeq); isomorphic (algebra) | Context-dependent |
| $\cong$ | `\cong` | congruent (geometry); isomorphic (algebra) | Document which sense in `project_philosophy.md` |
| $\propto$ | `\propto` | proportional to | |
| $\ll$ | `\ll` | much less than | |
| $\gg$ | `\gg` | much greater than | |
| $\leq$ | `\leq` | less than or equal | Prefer `\leq` over `\le` (same output; `\leq` is more explicit) |
| $\geq$ | `\geq` | greater than or equal | Prefer `\geq` over `\ge` |
| `?=` | `\overset{?}{=}` | unverified / conjectured equality | Requires `amsmath`. **Do not use `\stackrel`.** See note below. |

**On `\equiv`:** Used for modular congruence ($a \equiv b \pmod{n}$),
logical equivalence, and sometimes "defined as." When used as "defined as,"
prefer `\triangleq` or `:=` instead — `\equiv` can be misread as asserting
a known identity rather than introducing a definition. [C]

**On `\sim`:** This symbol carries different meanings across fields:
- Probability: $X \sim \mathcal{N}(\mu, \sigma^2)$ means "distributed as"
- Analysis: $f(x) \sim g(x)$ means "asymptotically equivalent"
- Geometry: "similar to"

Always clarify the intended sense with a comment or prose if the context is
mixed. [C, R]

**On `\overset{?}{=}` (questioned equality):** Not a standard math symbol,
but common in worksheets and worked examples. Use `\overset{?}{=}` (amsmath),
not `\stackrel{?}{=}`. `\stackrel` strips the math class from the base symbol,
breaking relation spacing around `=`. `\overset` preserves it. For frequent
use, define `\newcommand{\qeq}{\overset{?}{=}}`. [C]

---

## 4. Set Notation

| Symbol | Command | Meaning | Notes |
|--------|---------|---------|-------|
| $\in$ | `\in` | element of | |
| $\notin$ | `\notin` | not an element of | |
| $\subset$ | `\subset` | subset (proper or improper — see below) | |
| $\subseteq$ | `\subseteq` | subset or equal | |
| $\subsetneq$ | `\subsetneq` | proper subset | Requires `amssymb`. Unambiguous; prefer over `\subset` when proper subset is meant |
| $\supset$ | `\supset` | superset | |
| $\supseteq$ | `\supseteq` | superset or equal | |
| $\cup$ | `\cup` | union | |
| $\cap$ | `\cap` | intersection | |
| $\setminus$ | `\setminus` | set difference | Prefer over `\backslash` in set contexts — `\backslash` is a glyph, `\setminus` is a relation |
| $\emptyset$ | `\emptyset` | empty set | Some authors prefer `\varnothing` (rounder glyph); document preference in `project_philosophy.md` |
| $\varnothing$ | `\varnothing` | empty set (variant) | Requires `amssymb` |
| $\mathbb{R}$ | `\mathbb{R}` | real numbers | Requires `amssymb`. `\mathbb` for all number sets. |
| $\mathbb{Z}$ | `\mathbb{Z}` | integers | |
| $\mathbb{N}$ | `\mathbb{N}` | natural numbers | Note: whether $0 \in \mathbb{N}$ is convention-dependent — state the convention |
| $\mathbb{Q}$ | `\mathbb{Q}` | rationals | |
| $\mathbb{C}$ | `\mathbb{C}` | complex numbers | |

**On `\subset` vs. `\subsetneq`:** Many authors use `\subset` to mean
"subset or equal," but some use it to mean "proper subset." This is a known
ambiguity. In any document where the distinction matters, use `\subseteq` and
`\subsetneq` exclusively — never bare `\subset`. Document this convention in
`project_philosophy.md`. [C, R]

**Large operators over sets** (union, intersection, product):

```latex
% Displayed — large symbols with limits above/below
\bigcup_{i=1}^{n} A_i
\bigcap_{k \in K} B_k

% Inline — same commands, but limits become subscripts
```

Use `\bigcup`, `\bigcap`, `\bigsqcup` for n-ary operators; reserve
`\cup` and `\cap` for binary expressions. [C]

---

## 5. Calculus and Analysis

### Limits

```latex
\lim_{x \to a} f(x)          % standard limit
\lim_{x \to a^+} f(x)        % right-hand limit
\lim_{x \to a^-} f(x)        % left-hand limit
\lim_{x \to \infty} f(x)     % limit at infinity
```

Use `\to` (not `\rightarrow`) inside limit subscripts — `\to` is the
semantic command for this context. [C]

### Derivatives

```latex
% Leibniz notation — preferred for engineering and physics
\frac{\mathrm{d}f}{\mathrm{d}x}         % first derivative
\frac{\mathrm{d}^2 f}{\mathrm{d}x^2}    % second derivative

% Partial derivatives
\frac{\partial f}{\partial x}            % standard; no package required

% With physics or physics2 (see math.md for package guidance)
\dv{f}{x}         % first derivative
\dv[2]{f}{x}      % second derivative
\pdv{f}{x}        % partial derivative
```

Do not mix `'` (prime) and Leibniz notation for the same function within a
document without explanation. Document the choice in `project_philosophy.md`.
[R, C]

**On `'` vs `\prime`:** The apostrophe `'` in math mode is syntactic sugar for
`^{\prime}` and is correct for most uses. The failure case: combining it with
an explicit superscript on the same expression. `f''^2` is parsed as `f` +
double-prime + `^2` applied to the last `'`, producing wrong output. Write
`(f'')^2` or `{f''}^2` explicitly whenever a superscript follows a primed
expression. [C]

### Newton (Dot) Notation for Time Derivatives

```latex
\dot{s}     % first time derivative
\ddot{s}    % second time derivative
\dddot{s}   % third — requires amsmath; visually marginal
\ddddot{s}  % fourth — requires amsmath; avoid if possible
```

Appropriate when the independent variable is unambiguously time throughout the
document. Standard in Lagrangian mechanics and control systems; do not mix with
Leibniz notation for the same quantity without comment. `\dddot` and `\ddddot`
compile but look crowded inline — if a third or higher derivative appears
regularly, reconsider whether dot notation is the right choice, or define a
macro (not recommended). Dots are placed at a fixed height regardless of
ascender height; this is a known limitation with no clean standard workaround.
[C, R]

### Integrals

```latex
% Indefinite
\int f(x) \, \mathrm{d}x

% Definite
\int_a^b f(x) \, \mathrm{d}x

% Improper
\int_0^{\infty} f(x) \, \mathrm{d}x

% Double / triple
\iint_D f(x,y) \, \mathrm{d}A
\iiint_V f \, \mathrm{d}V

% Line integral
\oint_C \vec{F} \cdot \mathrm{d}\vec{r}    % closed curve
\int_C \vec{F} \cdot \mathrm{d}\vec{r}     % open curve
```

If `physics` or `physics2` is loaded, `\dd{x}` replaces `\mathrm{d}x`. See
`math.md` for package guidance before loading either.

The thin space `\,` before `\mathrm{d}` (or `\dd{}`) is mandatory — see
`math.md`. [C]

### Summation and Product

```latex
\sum_{i=1}^{n} a_i         % summation
\prod_{i=1}^{n} a_i        % product
\sum_{i \in S} f(i)        % index over a set
```

In displayed math, limits appear above and below. In inline math, they
appear as subscripts/superscripts. If you need displayed-style limits
inline, use `\displaystyle\sum_{i=1}^{n}` — but do this sparingly, as it
disrupts line spacing.

### Infinity and Limits at Infinity

| Symbol | Command | Notes |
|--------|---------|-------|
| $\infty$ | `\infty` | infinity |
| $+\infty$ | `+\infty` | positive infinity — use when sign matters |
| $-\infty$ | `-\infty` | negative infinity |

---

## 6. Vector and Tensor Notation

Document your choice of convention in `project_philosophy.md` and apply it
consistently. Do not mix conventions. [R, C]

### Vector Notation Options

```latex
% Arrow notation — common in introductory physics and engineering
\vec{F}               % short arrow over symbol

% Bold notation — common in advanced physics, applied math, engineering
\mathbf{F}            % bold upright (good for vectors in text-weight math)
\boldsymbol{F}        % bold italic (better for italicized variable names)

% With physics2 (recommended) or physics (legacy) — see math.md
\vb{F}                % bold vector
\vb*{F}               % bold italic vector
```

**Rule:** Do not use plain `F` for a vector — it is indistinguishable from a
scalar in italic math mode. Choose `\vec{}`, `\mathbf{}`, or (if a physics
notation package is loaded) `\vb{}`, and apply consistently. [C]

### Unit Vectors

```latex
% Hat notation — preferred in most engineering and intro physics
\hat{i}, \hat{j}, \hat{k}       % Cartesian
\hat{r}, \hat{\theta}, \hat{z}  % cylindrical
\hat{r}, \hat{\theta}, \hat{\phi} % spherical

% Bold with subscript — preferred in advanced/graduate physics
\mathbf{e}_x, \mathbf{e}_y, \mathbf{e}_z
```

### Dot and Cross Products

```latex
\vec{A} \cdot \vec{B}           % dot product — use \cdot, never a plain period or *
\vec{A} \times \vec{B}          % cross product — use \times
```

### Transpose

```latex
A^T           % transpose — superscript T (upright or italic depending on convention)
A^{\top}      % transpose using \top — unambiguous; preferred in formal math
A^\intercal   % alternative — requires amssymb; less common
```

Document which transpose notation is in use. `A^T` is common but `T` in
italic looks like a variable. `A^{\top}` is unambiguous. [C, R]

### Gradient, Divergence, Curl

```latex
% Standard — no package required
\nabla f                         % gradient
\nabla \cdot \vec{F}             % divergence
\nabla \times \vec{F}            % curl
\nabla^2 f                       % Laplacian (scalar notation)
\Delta f                         % Laplacian (alternative, common in pure math)

% With physics2 (recommended) or physics (legacy) — see math.md
\grad{f}
\div{\vb{F}}
\curl{\vb{F}}
\laplacian{f}
```

**Note:** `\Delta` for the Laplacian is common in pure mathematics but can
conflict with "increment" or "change in" usage in applied contexts. In a
mixed document, prefer `\nabla^2` for the Laplacian and `\Delta` for
increments, or define `\DeclareMathOperator{\Lap}{\Delta}` with a comment.
[C, R]

---

## 7. Physics and Engineering Symbols

### Electrical Engineering

```latex
% Impedance, resistance, reactance — use upright Z, R, X if they are fixed
% quantities (not variables); italic if they vary
Z, R, X                 % italic — variables
\mathrm{Z}, \mathrm{R}  % upright — fixed/labeled quantities (e.g., Thevenin)

% Common: phasor notation
\tilde{V}, \tilde{I}    % tilde for phasors (common in circuit analysis)
\hat{V}                 % hat for phasors (alternative — choose one per project)

% Parallel operator (rarely defined; define it explicitly)
\newcommand{\ppar}{\mathbin{\|}}   % parallel combination: R_1 \ppar R_2
```

### Complex Numbers

```latex
\Re(z), \Im(z)          % real and imaginary parts (fraktur — standard)
\mathrm{Re}(z)          % upright Re — alternative; more readable in some fonts
|z|                     % modulus
\arg(z)                 % argument / phase angle
z^*                     % complex conjugate (physics convention)
\bar{z}                 % complex conjugate (math convention)
```

Document which conjugate notation is used per project. `z^*` is near-universal
in physics; `\bar{z}` is common in mathematics. [R, C]

### Angles and Trigonometry

```latex
% Always use command forms — never plain letters
\sin\theta \quad \cos\theta \quad \tan\theta
\arcsin x \quad \arccos x \quad \arctan x
\cot\theta \quad \sec\theta \quad \csc\theta

% Inverse trig — two conventions
\arctan(x)              % standard in most texts
\tan^{-1}(x)           % engineering convention — use \DeclareMathOperator if preferred

% Hyperbolic
\sinh x \quad \cosh x \quad \tanh x
```

### Energy and Thermodynamics

```latex
\Delta H, \Delta S, \Delta G    % thermodynamic state functions — italic \Delta = change
\partial                         % partial differential — also used in partial molar quantities
```

---

## 8. Greek Letters — Usage Notes

Standard Greek letter commands are well-known; this section covers cases where
multiple variants exist or where usage conventions create ambiguity.

| Symbol | Command | Variant | Variant command | Notes |
|--------|---------|---------|-----------------|-------|
| $\epsilon$ | `\epsilon` | $\varepsilon$ | `\varepsilon` | `\varepsilon` is the open-curved form; standard in analysis and physics. `\epsilon$ is the lunate form. Choose one per project. `\varepsilon` is more commonly "the right one" in physics/engineering. |
| $\phi$ | `\phi` | $\varphi$ | `\varphi` | `\varphi` is the cursive form; used for the golden ratio, Euler's totient, and electric potential in some texts. `\phi` is the straight-stroke form. High ambiguity — document choice. |
| $\theta$ | `\theta` | $\vartheta$ | `\vartheta` | `\vartheta` rarely used outside complex analysis and differential geometry. Default to `\theta`. |
| $\rho$ | `\rho` | $\varrho$ | `\varrho` | `\varrho` rarely used. Default to `\rho`. |
| $\sigma$ | `\sigma` | $\varsigma$ | `\varsigma` | `\varsigma` is the final-sigma form from Greek script. Not used in math notation. Default to `\sigma`. |
| $\pi$ | `\pi` | $\varpi$ | `\varpi` | `\varpi` used in some physics contexts (e.g., canonical momentum). Default to `\pi`. |
| $\kappa$ | `\kappa` | $\varkappa$ | `\varkappa` | `\varkappa` requires `amssymb`. Rare. |

**On `\epsilon` vs. `\varepsilon`:** In analysis and most physics writing,
`\varepsilon` is the correct form for the "epsilon" used in $\varepsilon$-$\delta$
definitions. `\epsilon` looks like the lunate epsilon and is less conventional.
Default to `\varepsilon` unless the project explicitly uses `\epsilon`. [C, R]

**On `\phi` vs. `\varphi`:** This is genuinely convention-dependent.
- Electric potential: often `\varphi` in European physics tradition, `\phi` in American
- Golden ratio: `\varphi` strongly preferred
- Angle: `\phi` most common in introductory physics

Document which form is in use in `project_philosophy.md`. [C, R]

### Uppercase Greek

Most uppercase Greek letters that look identical to Roman letters have no
distinct LaTeX command — `A`, `B`, `E`, `Z`, `H`, `I`, `K`, `M`, `N`, `O`,
`P`, `T`, `X` are used directly. This is intentional; the glyphs are
identical. The ambiguity worth flagging is **tau (T)**: it is visually
identical to Roman T, which is also the common transpose symbol. If your
document uses both, use `A^{\top}` for transpose (unambiguous) rather than
`A^T`. See Section 6. [C, R]

Uppercase Greek letters *with* distinct commands: `\Gamma`, `\Delta`,
`\Theta`, `\Lambda`, `\Xi`, `\Pi`, `\Sigma`, `\Upsilon`, `\Phi`, `\Psi`,
`\Omega`. For variants: `\varGamma`, `\varDelta`, etc. (amsmath). [R]

---

## 9. Miscellaneous — Commonly Confused

| Situation | Wrong | Right | Reason |
|-----------|-------|-------|--------|
| Absolute value | `|x|` | `\lvert x \rvert` | Plain `|` is a relation symbol with wrong spacing as a delimiter; `\lvert`/`\rvert` are the correct paired delimiter commands |
| Norm | `\|\vec{v}\|` or `\|v\|` | `\lVert \vec{v} \rVert` | `\|` produces the double-bar glyph but with wrong delimiter semantics; `\lVert`/`\rVert` are correct |
| Inner product angle brackets | `<x, y>` | `\langle x, y \rangle` | `<` and `>` are relation symbols with wrong spacing for use as delimiters |
| Floor | `[x]` | `\lfloor x \rfloor` | Brackets are ambiguous; semantic delimiter commands are unambiguous |
| Ceiling | `[x]` | `\lceil x \rceil` | Same reason |
| Ellipsis in math | `...` | `\dots` (amsmath) | `...` produces low-baseline dots with wrong spacing — see `math.md` |
| Degree symbol | `^{\circ}` | `\ang{30}` (siunitx) or `30^{\circ}` | `\degree` requires `gensymb`; `siunitx` preferred; `^{\circ}` is acceptable fallback |
| Perpendicular / independence | `\perp` without comment | `\perp` with comment | `\perp` means "perpendicular" in geometry and "independent of" in probability — comment when context is mixed |
| Parallel (relation) | `\|` | `\parallel` | `\|` is a delimiter (norm bars); `\parallel` is the binary relation symbol |
| Parallel combination (circuits) | (undefined) | Define `\newcommand{\ppar}{\mathbin{\|}}` | `\mathbin{}` gives correct binary operator spacing; document in `project_philosophy.md` |
| "Such that" bar in set-builder | `\{x | x > 0\}` | `\{x \mid x > 0\}` | Plain `|` is a relation symbol with wrong spacing as a separator; `\mid` is the correct command |
| Multiplication (scalars) | `*` or `\times` | `\cdot` or juxtaposition | `*` means convolution (`\ast`) in math; `\times` is reserved for cross products. Use `\cdot` for explicit scalar multiply or omit entirely in algebra |
| Cross product | `\cdot` | `\times` | Do not use dot for cross product — it implies scalar result, not vector |
| Convolution | `*` | `\ast` | Bare `*` has no math-class and wrong spacing; `\ast` is the correct binary operator |
| Hat over multi-char expression | `\hat{AB}` | `\widehat{AB}` | `\hat` is sized for a single character; it looks cramped over multiple characters. Use `\widehat` for multi-character bases |
| Evaluation bar | `f(x)|_{x=0}` | `f(x)\big\rvert_{x=0}` | Bare `|` has wrong math class and spacing as an evaluation bar; `\big\rvert` (or `\Bigr\rvert` for taller expressions) is correct. See also auto-sizing delimiters below |
| Modular arithmetic | `\mod`, `\bmod`, `\pmod` (interchangeably) | choose deliberately | `\pmod{n}` adds parentheses and spacing: $a \equiv b \pmod{n}$. `\bmod` is the binary operator form: $a \bmod n$. `\mod` adds extra spacing for display use. Pick the form that matches the typographic context |

### Auto-sizing delimiters

For any delimiter pair wrapping tall expressions (fractions, integrals,
matrices), use `\left` and `\right`:

```latex
% Wrong — fixed-size parentheses look wrong around a fraction
(\frac{a}{b})

% Correct — auto-sized
\left(\frac{a}{b}\right)

% Also available
\left[ \right]    \left\{ \right\}    \left| \right|
\left\langle \right\rangle            \left\lfloor \right\rfloor
```

For cases where `\left`/`\right` produces wrong spacing (e.g., inside
`align` around a single tall term), use the explicit size commands:
`\bigl`, `\Bigl`, `\biggl`, `\Biggl` (and their right counterparts). [C, R]
