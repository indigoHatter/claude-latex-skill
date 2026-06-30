# File Naming Conventions

File names are part of the project's interface. A collaborator navigating the
directory without an IDE — on a server, in a file picker, in a diff tool —
should be able to understand the project's structure from names alone. [R, M]

---

## Assumed Baseline: Git Version Control

This skill assumes the project is tracked with git. Under git:

- **Never encode version information in filenames.** No `-v2`, no `-final`,
  no `-FINAL-REAL`, no datestamps in filenames. The repository is the
  authoritative version history. The file in the working directory with the
  clean name is always the current version.
- **Never archive by renaming.** There is no `archive/` directory in a
  git-tracked project. Old versions are commits, not files.
- The header `VERSION` field (see `headers.md`) is a human-readable stamp
  for quick identification — it complements git history, it does not replace
  it.

If the project is not git-tracked, see **No Version Control** at the end of
this file.

---

## General Rules

These apply to all files in a LaTeX project regardless of type. [P, M]

- **All lowercase.** Filesystems differ in case-sensitivity; all-lowercase
  names are unambiguous everywhere.
- **Hyphens as word separators.** Not underscores, not camelCase, not spaces.
  Hyphens read cleanly in terminals, URLs, and diff output.
- **No spaces.** Ever. A filename with spaces requires quoting in every
  command-line context and breaks naive build scripts.
- **No special characters** except hyphens and dots (for extensions).
- **Names describe content, not position.** `gauss-law.tex` survives a
  restructure; `section3.tex` becomes a lie the moment sections are reordered.

---

## `.tex` Files

| File | Name |
|------|------|
| Root file | `main.tex` — always, no exceptions |
| Content files | `<descriptive-name>.tex` |
| Shared preamble partial | `preamble.tex` or `packages.tex` |
| Macro definitions | `macros.tex` |

```
main.tex
sections/gauss-law.tex
sections/worked-examples.tex
macros.tex
```

The root file is always `main.tex`. A non-standard root filename forces every
collaborator and every build tool to discover it rather than assume it. The
cost of a non-standard name is never worth it. [R, M]

---

## `.bib` Files

- `references.bib` for a single project bibliography — the default.
- If split by scope, name descriptively: `textbooks.bib`, `papers.bib`,
  `standards.bib`.
- One `.bib` file per project unless there is a genuine organizational reason
  to split. Splitting without reason creates synchronization overhead with no
  benefit. [M]

---

## Figure Files

Figures are prefixed `fig-` so they sort together and are immediately
identifiable in any directory listing.

```
figures/fig-circuit-diagram.pdf
figures/fig-bode-plot.pdf
figures/fig-results-comparison.png
```

**Format guidance:**

| Content type | Format | Reason |
|---|---|---|
| Vector graphics (diagrams, plots, TikZ export, Inkscape) | `.pdf` | Scales without loss; standard LaTeX inclusion |
| Raster images (photographs, screenshots) | `.png` | Lossless; no compression artifacts |
| Photographs where file size matters | `.jpg` | Acceptable only for photos — never for diagrams or plots |

Never use `.jpg` for diagrams, circuit schematics, or data plots. JPEG
compression introduces visible artifacts at edges and fine lines — exactly
where technical figures have detail. [C]

---

## Table Files

When a table is complex enough to be externalized via `\input`, prefix it
`tab-`:

```
tables/tab-resistor-values.tex
tables/tab-measurement-summary.tex
```

Most tables do not need to be externalized. Only split a table into its own
file when it is long enough that it meaningfully disrupts the flow of the
content file, or when it is shared across multiple documents. [M]

---

## Directory Structure

Keep it shallow. One level of subdirectories is almost always sufficient:

```
project/
├── main.tex
├── macros.tex
├── project_philosophy.md
├── sections/
│   ├── introduction.tex
│   └── results.tex
├── figures/
│   ├── fig-setup.pdf
│   └── fig-results.png
└── tables/
    └── tab-measurements.tex
```

Do not create subdirectories speculatively. Add `figures/` when there are
figures, not before. A flat structure with two files is better than a nested
structure with two files. [M]

---

## No Version Control

If the project is not git-tracked, manual versioning is the fallback. Be
aware: **manual versioning depends entirely on consistent discipline. This
skill can describe the convention but cannot enforce it.** Inconsistency in
a manual scheme is worse than no scheme — it creates false confidence in
version history that may be incomplete or wrong.

With that acknowledged, the recommended manual convention:

**Working files** keep their clean names in the project directory. The file
with the clean name is always the current version — no exceptions.

**Before a significant revision**, copy the current version into an `archive/`
subdirectory with an ISO datestamp suffix:

```
archive/gauss-law-20260507.tex
archive/main-20260507.tex
```

Use the ISO date (`YYYYMMDD`), not a version number or descriptive suffix.
Dates are unambiguous and sort correctly. "v2" and "final" are not.

**The `archive/` directory** sits at the project root and is never compiled
directly. Its contents are reference copies only.

```
project/
├── main.tex                        % current — always
├── sections/
│   └── gauss-law.tex               % current — always
└── archive/
    ├── ARCHIVE.md                  % manifest: what was archived and why
    ├── gauss-law-20260507.tex      % snapshot before major revision
    └── main-20260430.tex           % snapshot before restructure
```

**`ARCHIVE.md`** is a plain-text manifest at the root of the `archive/`
directory. It records what was archived and why:

```markdown
# Archive Manifest

## gauss-law-20260507.tex
Archived before restructuring the integral form section. The original
derivation approach was valid but was replaced with a more student-friendly
presentation.

## main-20260430.tex
Archived before adding the worked-examples section. Clean baseline of the
handout-only version.
```

The format and structure of `ARCHIVE.md` is left to the project — what
matters is that the rationale is recorded, not how. [R, M]

Even with this convention in place: if there is any possibility of
transitioning to git, do it before the project grows. Retrofitting git onto
a manually-versioned project is straightforward; reconstructing history from
an inconsistent archive directory is not. [M, P]

---

## Version-Controlled Projects: `.gitignore`

In a git-tracked LaTeX project, the following build artifacts should be
excluded from version control. Add them to `.gitignore` at the project root:

```
# LaTeX build artifacts
*.aux
*.log
*.toc
*.out
*.lof
*.lot
*.fls
*.fdb_latexmk
*.synctex.gz
*.synctex(busy)
*.bbl
*.blg

# Output (optional — omit if you want to track compiled PDFs)
*.pdf
```

Source files (`.tex`, `.bib`, `.sty`, figure source files) are tracked.
Build artifacts are not — they are regenerated on every compile and their
diffs carry no meaningful information. [M]

The `.gitignore` file itself should be committed to the repository so all
collaborators share the same exclusions. [M]
