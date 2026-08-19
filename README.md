# KI-Workshop – Prompt-Bibliothek / AI Workshop – Prompt Library

> **Archiviert am 2026-08-19 — nicht mehr die Leitfassung.**
> Redigiert werden die Prompt-Texte jetzt im KI Use Case Studio:
> `src/lib/prompts/content.ts` in `gitlab.aiat-poc.at/ai-at/products/usecase-studio`,
> live und öffentlich (ohne Passwort) unter
> https://usecase-studio.apps.aiat-poc.at/prompts — dort zweisprachig über `?lang=en`,
> mit Einreiche-Weg über `/upload`.
> Die GitHub-Pages-Seite dieses Repos läuft vorerst unverändert weiter, wird aber
> nicht mehr gepflegt. Prompt-Änderungen hier erzeugen Drift.

Drei copy-paste-fertige Prompts für den KI-Workshop (AI:AT) – zweisprachig.

**Live DE:** https://annsophiebrenk.github.io/ki-workshop-prompts/
**Live EN:** https://annsophiebrenk.github.io/ki-workshop-prompts/en/

| Schritt | Deutsch | English |
|---|---|---|
| 1 | Problemgetrieben (P1–P12) | Problem-driven (P1–P12) |
| 2 | Chancengetrieben (C1–C8) | Opportunity-driven (O1–O8) |
| 3 | Lösungsgetrieben – kopierbare Ergebnis-Tabelle | Solution-driven – copyable result table |
| + | Ergebnisse einreichen (Anonymisierung) | Submit results (anonymisation) |

## Aufbau

- `index.html` – deutsche Version
- `en/index.html` – englische Version

Beide Dateien sind eigenständig: CSS und JS liegen inline, es gibt keinen Build-Schritt.
Der `<style>`-Block ist in beiden Dateien identisch, der `<script>`-Block unterscheidet
sich nur im Label des Kopier-Buttons.

**Wichtig:** Jede inhaltliche Änderung muss in beiden Dateien nachgezogen werden,
sonst driften die Sprachversionen auseinander.
