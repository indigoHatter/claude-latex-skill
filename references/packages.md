# Package Import Conventions

## Assumed Build Environment

This skill assumes **pdfLaTeX** as the compiler and **TeX Live** as the
distribution. This reflects the most common default across editors and
platforms, and the most portable baseline for sharing files.

If a project requires XeLaTeX or LuaLaTeX (e.g., for `fontspec`, Unicode
math, or advanced font selection), that requirement must be declared in the
root file's `REQUIRES` field and in `project_philosophy.md`. Some packages
behave differently or are unnecessary under other engines — note any
engine-specific behavior in the relevant preamble comment. [P, R]

---

## Core Principle: Every Package Earns Its Place

The preamble is the dependency manifest of a LaTeX document. Treat it with the
same discipline a good programmer applies to imports: intentional, documented,
ordered, and minimal. Do not add a package speculatively — add it when a
specific need arises, comment why it's there, and remove it if that need goes
away. A preamble full of mystery packages is a portability and maintenance
liability. [R, M, P]

---

## Grouping and Order

Packages must be grouped by purpose, with a comment header naming each group.
Within groups, load order follows the rules below. Between groups, order is
conventional (encoding → math → layout → figures → tables → typography →
utilities → hyperlinks).

```latex
% --- Encoding & Language ---
\usepackage[T1]{fontenc}        % Output font encoding; required for copy-paste
\usepackage[utf8]{inputenc}     % UTF-8 input encoding; optional since LaTeX 2018
                                % (utf8 is now the default), but harmless and
                                % explicit — include for clarity in shared projects

% --- Mathematics ---
\usepackage{amsmath}            % Core math environments (align, gather, etc.)
\usepackage{amssymb}            % Extended math symbols
\usepackage{mathtools}          % Fixes and extensions to amsmath

% --- Layout & Spacing ---
% (document-class-specific; see project_philosophy.md)

% --- Figures ---
\usepackage{graphicx}           % \includegraphics
\usepackage{float}              % [H] placement specifier

% --- Tables ---
\usepackage{booktabs}           % Professional horizontal rules
\usepackage{tabularx}           % Auto-width columns (X type)
\usepackage{siunitx}            % Numeric alignment and unit formatting

% --- Typography ---
\usepackage{microtype}          % Microtypographic improvements (protrusion, etc.)

% --- Utilities ---
\usepackage{xcolor}             % Color support
\usepackage{enumitem}           % List customization

% --- References & Links (load last) ---
\usepackage{hyperref}           % PDF hyperlinks; almost always load last
\usepackage{cleveref}           % \cref, \Cref; must load AFTER hyperref
```

---

## Load Order Rules

These are not stylistic preferences — violating them causes silent failures or
hard errors. [C, P]

1. `fontenc` before any font packages
2. `amsmath` before `mathtools` (mathtools patches amsmath)
3. `hyperref` near-last — it redefines many internals; packages loaded after it
   must be known to be hyperref-compatible
4. `cleveref` after `hyperref` — hard requirement, no exceptions
5. `babel` or `polyglossia` (language packages) early, before most others

For physics-notation packages (`physics`, `physics2`) and their interaction
with `siunitx`, see `math.md` — they have domain-specific load order
requirements documented there. [C]

If a package's required position is undocumented, consult its CTAN page before
placing it. When in doubt, note the uncertainty in a comment. [R]

---

## Comment Format

Each `\usepackage` line gets an inline comment explaining *why* the package is
present — not what it does in general, but what *this document* needs it for.

**Too generic (bad):**
```latex
\usepackage{siunitx}    % units
```

**Specific and useful (good):**
```latex
\usepackage{siunitx}    % decimal-aligned numeric columns in results table
```

For packages whose purpose is obvious and universal (e.g., `amsmath` in any
math document), a brief functional description is acceptable. The test: would a
competent LaTeX user reading this comment know exactly why this package was
chosen over alternatives? [R]

---

## Package Minimalism

Prefer fewer packages with broader capability over many narrow ones. Before
adding a new package, ask:

1. Does a package already loaded provide this capability?
2. Is a plain LaTeX solution adequate?
3. Does this package conflict with any already loaded? (Check CTAN.)

If a package is added to solve a temporary problem (e.g., a layout experiment),
comment it as such:

```latex
\usepackage{showframe}  % DEBUG: visualize page margins — remove before final
```

---

## Packages That Are Always Wrong

These packages are deprecated, superseded, or actively harmful. Do not load
them under any circumstances. [C]

| Package | Why it's wrong | Use instead |
|---------|---------------|-------------|
| `epsfig` | Obsolete wrapper | `graphicx` |
| `a4` / `a4wide` | Crude margin hacks | `geometry` |
| `t1enc` | Old encoding command | `\usepackage[T1]{fontenc}` |
| `pslatex` | Obsolete font selection | proper font packages |
| `fixltx2e` | Fixes now in LaTeX kernel; causes errors | remove entirely |

The `eqnarray` environment (not a package) is also always wrong — see
`math.md` for rationale and replacement. [C]

---

## Noting Constraints

If a package cannot be used due to a submission constraint (e.g., journal
allowlist, institutional template) or environment limitation, document that
constraint explicitly rather than silently omitting the package:

```latex
% siunitx omitted: not on ACM TAPS approved package list.
% Numeric alignment handled manually via dcolumn (see tables section).
```

This distinguishes intentional omission from oversight, and tells the next
collaborator exactly what the constraint was. [R, M]
