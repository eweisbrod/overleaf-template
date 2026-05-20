@AGENTS.md

# Claude Code Specific Notes

- This repo is the LaTeX paper template half of the two-repo workflow;
  the code lives in `project-template` (sibling repo, typically at
  `c:/_git/project-template/`). When the user asks to change a number
  or table content, the work belongs in `project-template`, not here.
- Citation keys MUST be verified against `Bibliography.bib` before
  insertion. Hallucinated citations are a common AI failure mode; do
  not write `\citet{somekey}` without grepping the bib file first.
- Do not create new files unless necessary. Prefer editing
  `main.tex` over splitting into multiple .tex files; the template
  intentionally lives in one file for readability.
