# Bibliography and Citation Workflow

Citations are the one area of LaTeX where the right choice depends heavily on
context you can't determine from the document alone — specifically, whether a
journal or institution mandates a particular system. This file states the
current community consensus, explains the tradeoffs honestly, and gives you
the information to make the right call per project. [R, M, P]

---

## The Short Answer

**For new projects where you control the toolchain:** use `biblatex` with the
`biber` backend. It is actively maintained, handles Unicode and modern source
types natively, and its styles are written in standard LaTeX rather than the
arcane `.bst` stack language.

**For journal submissions or templates that mandate a `.bst` style:** use
`bibtex`. The `.bib` file format is shared between systems, so you can develop
with `biblatex` and switch at submission time with minimal friction.

**Do not use `natbib` for new projects.** It predates `biblatex`, has not been
meaningfully updated, and `biblatex` subsumes all of its functionality.

---

## The Two Systems

### BibTeX (legacy — still required in some contexts)

BibTeX has been the LaTeX citation standard since 1985. It is still required
by many journals, particularly in physics, mathematics, and the natural
sciences, which provide `.bst` style files that cannot be used with
`biblatex`.

```latex
% Minimal BibTeX setup
\bibliographystyle{plain}       % or your journal's .bst file
\bibliography{references}       % filename without .bib extension
```

**Build sequence:** `pdflatex` → `bibtex` → `pdflatex` → `pdflatex`

BibTeX limitations worth knowing:
- No native Unicode support (accented names require `{\'e}` escapes)
- Fixed entry types: no `@online`, `@software`, `@dataset` — abuse `@misc`
- Bibliography styles written in reverse-Polish stack notation (`.bst` files);
  modifying them requires learning a separate language
- No built-in support for DOIs, URLs in a consistent way

If your project requires BibTeX, use `latexmk` to manage the multi-pass build
automatically (see `structure.md`). [M]

### BibLaTeX + Biber (recommended for new projects)

`biblatex` is a modern rewrite of the bibliography system, using LaTeX macros
throughout. `biber` is its dedicated backend, replacing BibTeX as the
processor.

```latex
% Recommended minimal biblatex setup
\usepackage[
    backend=biber,
    style=numeric,      % or: authoryear, alphabetic, apa, ieee, etc.
    sorting=nyt         % sort by name, year, title
]{biblatex}
\addbibresource{references.bib}   % path relative to main.tex; .bib extension required
                                  % (unlike \bibliography{}, which omits the extension)

% In document body, where bibliography should appear:
\printbibliography
```

**Build sequence:** `pdflatex` → `biber` → `pdflatex` → `pdflatex`

Or with `latexmk` (recommended — handles the sequence automatically):

```bash
latexmk -pdf main.tex
```

Key advantages over BibTeX:
- Full Unicode support via biber
- Modern entry types: `@online`, `@software`, `@dataset`, `@patent`
- Styles written in standard LaTeX; customization is tractable
- Modular: load only the features you need
- DOI, URL, and eprint fields handled natively
- Actively maintained

**Note on `backend=biber`:** Always specify `backend=biber` explicitly. The
default in some distributions is still `backend=bibtex` for compatibility;
explicit declaration makes intent unambiguous and avoids surprises on
collaborators' machines. [R, M]

---

## The `.bib` File

Both systems use the same `.bib` file format. This is the key fact that
makes the systems interchangeable at the data level:

```bibtex
@article{einstein1905,
    author  = {Einstein, Albert},
    title   = {Zur Elektrodynamik bewegter K{\"o}rper},
    journal = {Annalen der Physik},
    year    = {1905},
    volume  = {322},
    number  = {10},
    pages   = {891--921},
    doi     = {10.1002/andp.19053221004}
}

@book{knuth1984,
    author    = {Knuth, Donald E.},
    title     = {The {\TeX}book},
    publisher = {Addison-Wesley},
    year      = {1984}
}

@online{overleaf2024,
    author  = {{Overleaf}},
    title   = {Bibliography management with biblatex},
    url     = {https://www.overleaf.com/learn/latex/Bibliography_management_with_biblatex},
    year    = {2024},
    urldate = {2024-11-01}
}
```

**Key conventions for `.bib` files:**

- Author names in `Lastname, Firstname` format for BibTeX compatibility
- Protect capitalization in titles with `{Braces}` around letters that must
  stay uppercase: `{LaTeX}`, `{Fourier}`, `{Einstein}`
- Use `--` (em-dash) for page ranges, not a single hyphen
- Include `doi` fields where available — they improve bibliography quality and
  are handled natively by `biblatex`
- Name the file `references.bib` unless there is a genuine reason to split
  (see `naming.md`)

---

## Choosing a Citation Style

`biblatex` ships with several built-in styles. Choose based on field
convention or journal requirements:

| Style | Use case |
|-------|----------|
| `numeric` | Standard in engineering, CS, physics |
| `authoryear` | Standard in social sciences, humanities |
| `alphabetic` | Common in mathematics (e.g., [Ein05]) |
| `ieee` | IEEE-format journals and conferences |
| `apa` | APA format (social sciences) |
| `verbose` | Humanities footnote-style citations |

If a journal provides a `biblatex` style package (e.g., `biblatex-ieee`),
load that instead:

```latex
\usepackage[backend=biber, style=ieee]{biblatex}
```

For journals that provide a `.bst` file but not a `biblatex` equivalent,
fall back to BibTeX for that project. Document the constraint in
`project_philosophy.md`:

```markdown
## Citation System
BibTeX (not biblatex) — required by journal template (phys.bst).
```

[R, M]

---

## Switching from BibTeX to BibLaTeX

If you inherit a BibTeX project and want to migrate:

1. Replace `\bibliographystyle{...}` and `\bibliography{...}` with:
   ```latex
   \usepackage[backend=biber, style=numeric]{biblatex}
   \addbibresource{references.bib}
   ```
2. Replace `\bibliography{references}` in the body with `\printbibliography`
3. Change your build command from `bibtex` to `biber`
4. The `.bib` file is unchanged — no conversion needed

For most documents, this is the entire migration. More complex BibTeX
customizations (custom `.bst` macros, `natbib` citation commands) require
manual translation; consult the `biblatex` documentation. [M]

---

## `.gitignore` Additions

Add bibliography build artifacts to `.gitignore` in git-tracked projects:

```
# Bibliography build artifacts
*.bbl
*.blg
*.bcf       % biber control file — regenerated on every build
*.run.xml   % biber auxiliary file
```

The `.bib` file itself is source — track it. [M]
