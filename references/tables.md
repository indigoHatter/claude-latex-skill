# Table Conventions

Tables are among the most commonly mishandled elements in LaTeX documents.
The vertical-rule, double-rule style inherited from word processors is not
just ugly — it actively impedes readability by adding visual noise that
competes with the data. This file encodes the booktabs approach, which is
the current standard (ca. 2026) in professional and academic publishing. [R, C]

---

## Required Packages

```latex
\usepackage{booktabs}   % \toprule, \midrule, \bottomrule
\usepackage{tabularx}   % auto-width X columns
\usepackage{siunitx}    % numeric alignment; S column type
```

These three packages work together. A table that needs numeric alignment
needs all three. A simple text table may need only `booktabs` and `tabularx`.

---

## The Booktabs Rules

**Never use vertical rules.** Vertical rules in tables are a typographic
antipattern: they fragment the reading path and imply a cell-boundary
significance that the data rarely has. Use whitespace and column alignment
to separate columns instead. [R, C]

**Never use double horizontal rules.** `\hline\hline` is always wrong.
`\toprule` and `\bottomrule` have built-in weight that serves the same
visual purpose without the doubled-line artifact. [C]

**Use exactly three rule types:**

| Command | Weight | Position |
|---------|--------|----------|
| `\toprule` | Heavy | Above column headers |
| `\midrule` | Medium | Below column headers; between major row groups |
| `\bottomrule` | Heavy | Below last data row |

```latex
\begin{table}[htbp]
    \centering
    \caption{Measured resistor values and tolerances.}
    \label{tab:resistors}
    \begin{tabularx}{\linewidth}{l S[table-format=4.1] S[table-format=1.2] X}
        \toprule
        {Component} & {Nominal (\unit{\ohm})} & {Tolerance (\%)} & {Notes} \\
        \midrule
        R1 & 1000.0 & 0.05 & Precision reference \\
        R2 &  470.2 & 5.00 & Standard film \\
        R3 & 2200.0 & 1.00 & \\
        \bottomrule
    \end{tabularx}
\end{table}
```

---

## Caption Placement

**Captions go above tables, below figures.** This is not a style preference —
it is a reading convention: table captions orient the reader before they parse
the data; figure captions annotate after the image is seen. Placing a table
caption below makes the reader parse a table without knowing its context. [R, C]

```latex
% Correct
\begin{table}[htbp]
    \centering
    \caption{...}       % above
    \label{tab:...}
    \begin{tabularx}{...}
    ...
    \end{tabularx}
\end{table}

% Wrong
\begin{table}[htbp]
    \centering
    \begin{tabularx}{...}
    ...
    \end{tabularx}
    \caption{...}       % below — don't do this
\end{table}
```

The `\label` always follows `\caption` immediately. Placing `\label` before
`\caption` produces incorrect reference numbers. [C]

---

## Column Types

### `tabularx` and the `X` Column

`tabularx` takes a total width argument (usually `\linewidth`) and distributes
remaining space among `X`-typed columns. Use it whenever a table should span
the full text width, or whenever a column contains wrapping text.

```latex
% Full-width table; last column absorbs remaining space
\begin{tabularx}{\linewidth}{l l X}
```

Multiple `X` columns share the remaining space equally. Use `>{\raggedright\arraybackslash}X`
for left-aligned wrapping text (the default `X` is fully justified, which
looks poor in narrow columns):

```latex
\begin{tabularx}{\linewidth}{l >{\raggedright\arraybackslash}X}
```

### `S` Column (siunitx)

The `S` column type aligns numbers at the decimal point. Always use it for
columns of numeric data. [C]

```latex
% Declare format to avoid alignment surprises
S[table-format=3.2]     % up to 3 digits before decimal, 2 after
S[table-format=1.3e2]   % scientific notation
```

**Column headers over `S` columns must be wrapped in braces** to prevent
`siunitx` from trying to parse the header text as a number:

```latex
{Voltage (\unit{\volt})}    % correct
Voltage (\unit{\volt})      % wrong — siunitx will throw a warning or error
```

### Alignment Defaults

| Content type | Column specifier |
|---|---|
| Short text labels | `l` |
| Numeric data | `S[table-format=...]` |
| Long/wrapping text | `>{\raggedright\arraybackslash}X` |
| Centered short entries | `c` (sparingly) |

Right-alignment (`r`) for text columns is almost never correct — reserve it
for numeric columns where `S` is unavailable. [R]

---

## Row Spacing

Do not use `\\[Xpt]` to add space between rows — it is fragile and
inconsistent. For additional space between logical row groups, use `\addlinespace`
from booktabs:

```latex
R1 & 1000.0 & Reference \\
R2 &  470.2 & Standard  \\
\addlinespace
R3 & 2200.0 & High-power \\   % visual separation without a rule
```

For a hard division between row groups that warrants a rule, use `\midrule`.
Choose `\addlinespace` when the groups are related; `\midrule` when they are
categorically distinct. [R]

---

## Table Placement

Use `[htbp]` as the default placement specifier. This instructs LaTeX to try:
here, then top of page, then bottom of page, then a dedicated floats page.
Never use `[h]` alone — it frequently causes layout failures when LaTeX cannot
fit the table at the exact position requested. `[H]` (from the `float` package)
forces placement absolutely and should be reserved for cases where float
position is genuinely critical, with a comment explaining why. [M, C]

---

## Long Tables

For tables that may span multiple pages, use the `longtable` package rather
than `tabularx`. `longtable` handles page breaks, repeated headers, and footer
rows. Note this in the preamble comment when `longtable` is loaded, as it
changes how the table environment works. [M, P]

---

## What Not To Do

```latex
% Wrong: vertical rules
\begin{tabular}{|l|c|r|}
\hline
...
\hline
\end{tabular}

% Wrong: double rules
\hline\hline

% Wrong: \hline in a booktabs table (mixes conventions, inconsistent weight)
\toprule
...
\hline    % don't mix \hline with booktabs commands
...
\bottomrule

% Wrong: caption below table
\end{tabularx}
\caption{...}

% Wrong: label before caption
\label{tab:x}
\caption{...}
```

See `antipatterns.md` for the consolidated do-not-do list. [C]
