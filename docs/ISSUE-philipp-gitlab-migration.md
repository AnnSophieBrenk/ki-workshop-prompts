# Issue für Philipp — Umzug ki-workshop-prompts nach GitLab + Domain + Sende-Flow

> **Status:** vorbereitet, noch nicht in GitLab angelegt.
> **Grund:** Das glab-Token für `gitlab.aiat-poc.at` ist abgelaufen (`invalid_token`).
> Nach `glab auth login --hostname gitlab.aiat-poc.at` kann dieses Dokument 1:1 als
> Issue-Body übernommen werden.

**Titel:** `ki-workshop-prompts: GitLab-Projekt, Domain ai-prompts.aiat-poc.at, Intake-Endpoint`
**Assignee:** Philipp
**Labels:** `type:chore`, `area:infra`, `priority:high`, `status:ready`

---

## Kontext

Die Workshop-Prompt-Seite liegt heute auf GitHub Pages
(`github.com/AnnSophieBrenk/ki-workshop-prompts` → `annsophiebrenk.github.io/ki-workshop-prompts/`).
Sie soll nach `gitlab.aiat-poc.at` umziehen und unter **`ai-prompts.aiat-poc.at`** erreichbar sein.

Die Seite ist rein statisch: zwei HTML-Dateien mit inline CSS/JS, **kein Build-Schritt**,
keine Dependencies.

- `index.html` — deutsche Fassung (Startseite)
- `en/index.html` — englische Fassung, per DE|EN-Umschalter im Header verbunden

Neu hinzu kommt ein Sende-Formular: Teilnehmende fügen die anonymisierte Tabelle aus Prompt 4
ein, drücken Senden, und der Inhalt landet in der **bestehenden** Datenbank des
KI Use Case Studio (`ai-at/products/usecase-studio`). Es wird **keine zweite Datenbank** gebaut.

## Was Ann-Sophie mangels Berechtigung nicht selbst kann

### 1. GitLab-Projekt anlegen

- Pfad-Vorschlag: `ai-at/products/ki-workshop-prompts`
  (Alternative: als Unterprojekt zu `usecase-studio` — bitte nach eurer Konvention entscheiden.)
- Sichtbarkeit: **internal** genügt; die Auslieferung erfolgt öffentlich über die Domain.
- Import der bestehenden GitHub-History (kein Neuanfang):
  ```
  git clone --mirror https://github.com/AnnSophieBrenk/ki-workshop-prompts.git
  cd ki-workshop-prompts.git
  git push --mirror git@gitlab.aiat-poc.at:ai-at/products/ki-workshop-prompts.git
  ```
- Branch Protection auf `main` nach eurem Standard.
- Baseline-Governance nachrüsten: laut `projects-baseline/docs/ONBOARDING.md` ist
  `scripts/register-existing-repo.sh` der einzige dokumentierte Brownfield-Einstieg
  (Preview ist Default, `--apply` ist der bewusste zweite Schritt; Scope ist
  `.claude/**`, `AGENTS.md`, `CLAUDE.md`, `CODEOWNERS`, `SECURITY.md`).

### 2. Domain + Auslieferung: `ai-prompts.aiat-poc.at`

- DNS-Record auf die vorhandene Auslieferungsstrecke.
- TLS-Zertifikat nach eurem Standard.
- **Startseite muss die deutsche Fassung sein** (`/` → `index.html`), Englisch liegt unter `/en/`.
- Auslieferung statischer Dateien — Vorschlag: GitLab Pages (`pages.aiat-poc.at` ist vorhanden)
  oder derselbe Weg wie beim Studio. Bitte den für euch günstigeren Weg wählen und hier notieren.

### 3. CORS für den Intake-Endpoint

Der Studio-Upload läuft heute über **Next.js Server Actions** (`submitUpload` in
`src/app/actions/submission.actions.ts`). Server Actions sind **nicht cross-origin aufrufbar**.
Damit die Seite unter `ai-prompts.aiat-poc.at` in die Studio-Datenbank schreiben kann, braucht es:

- einen Route-Handler im Studio, z. B. `POST /api/intake`
  (App-Code — **den übernimmt Ann-Sophie**, siehe unten)
- **CORS-Freigabe** für Origin `https://ai-prompts.aiat-poc.at` auf genau diesem Pfad
  (Caddy bzw. `proxy.ts`, wo aktuell auch die CSP gesetzt wird)
- Rate-Limit auf dem Endpoint gegen Massen-Einträge

### 4. Workshop-Codes

Der anonyme Schreibpfad ist per RLS an einen **offenen Workshop** gebunden
(`is_workshop_open`, Migration `0007`). Das bleibt bewusst so.

Entschieden: Teilnehmende geben den **Workshop-Code im Formular** ein.
Es braucht daher keine RLS-Änderung — nur die Bestätigung, dass
`get_workshop_by_code` für die `anon`-Rolle vom neuen Endpoint aus erreichbar ist.

## Was Ann-Sophie selbst macht (nicht Teil dieses Issues)

- Sende-Formular in `index.html` und `en/index.html` (Markdown-Tabelle einfügen → parsen → senden)
- Route-Handler `POST /api/intake` im Studio inkl. Tests
- LLM-Klassifikation von **Abteilung** und **Thema** über `llm.ai-at.eu`
  (Infrastruktur im Studio vorhanden: `src/lib/ai/chat.ts`); **Branche** wählt der
  Teilnehmende einmal im Formular
- Abgleich der Studio-Seite `/prompts` mit den hier gepflegten, neueren Prompt-Texten

## Zieldatenmodell (existiert bereits, keine Änderung nötig)

Tabelle `use_case_entries`:

| Feld | Herkunft |
|---|---|
| `problem_text` | Spalte „Problem" der eingefügten Tabelle |
| `loesung_text` | Spalte „KI-Lösungsidee" |
| `abteilung` | LLM-Klassifikation gegen `taxonomy` (`typ='abteilung'`) |
| `use_case_art` | LLM-Klassifikation gegen `taxonomy` (`typ='use_case_art'`) = Thema |

`branche` liegt auf `participants` (`taxonomy` `typ='branche'`).
Längen-Backstops aus Migration `0013` gelten unverändert (Problem/Lösung je max. 4000 Zeichen).

## Definition of Done

- [ ] GitLab-Projekt existiert, History vollständig importiert
- [ ] `https://ai-prompts.aiat-poc.at/` liefert die deutsche Seite aus
- [ ] `https://ai-prompts.aiat-poc.at/en/` liefert die englische Seite aus, Umschalter funktioniert
- [ ] CORS-Freigabe für den Intake-Pfad steht, Rate-Limit aktiv
- [ ] GitHub-Repo archiviert oder mit Weiterleitungshinweis versehen
