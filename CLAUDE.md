# CLAUDE.md — ki-workshop-prompts

Statische Einseiten-Website (GitHub Pages) mit den drei KI-Workshop-Prompts der AI Factory Austria.
Kein Build, kein Framework, keine Dependencies: `index.html` (DE) und `en/index.html` (EN) sind
jeweils vollständig eigenständig — CSS und JS liegen inline in der Datei.

**Live DE:** https://annsophiebrenk.github.io/ki-workshop-prompts/
**Live EN:** https://annsophiebrenk.github.io/ki-workshop-prompts/en/

## Session Config

- vcs: github
- mirror: none
- waves: 3
- agents-per-wave: 3 (deep: 6)
- recent-commits: 20
- cross-repos: []
- ssot-files: [CLAUDE.md]
- ssot-freshness-days: 30
- stale-branch-days: 14
- stale-issue-days: 60
- issue-limit: 25
- test-command: none
- typecheck-command: none
- lint-command: none
- persistence: true
- memory-cleanup-threshold: 5
- learning-expiry-days: 30
- learnings-surface-top-n: 15
- learning-decay-rate: 0.05
- enforcement: warn
- isolation: auto
- max-turns: auto
- convergence-monitoring: false
- discovery-on-close: false
- discovery-severity-threshold: low
- discovery-confidence-threshold: 60
- plan-baseline-path: ~/Library/CloudStorage/OneDrive-AIATAIFactoryAustria/Claude Projekte/projects-baseline
- plan-default-visibility: public
- plan-prd-location: docs/prd/
- plan-retro-location: docs/retro/
- vault-integration: { enabled: false }
- special: |
    Zwei-Sprachen-Regel: index.html (DE) und en/index.html (EN) sind inhaltlich deckungsgleich.
    Jede Textaenderung an einer Datei MUSS in der anderen nachgezogen werden — sonst driften die Sprachen.
    Das AI:AT-Logo liegt als inline Base64-PNG im Header (ca. 88 KB, eine einzelne Zeile).
    Beim Bearbeiten nie zeilenweise ueber die gesamte Datei diffen — die Logo-Zeile blaeht jeden Diff auf.
    Kein Build-Step: Aenderungen sind nach dem Push auf main direkt live (GitHub Pages).
    Quality Gates (test/typecheck/lint) existieren bewusst nicht — Pruefung erfolgt visuell im Browser.
