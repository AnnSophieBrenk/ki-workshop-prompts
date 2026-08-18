# Issue #87 — Domain ai-prompts.aiat-poc.at + CORS für Intake-Endpoint

**Angelegt:** https://gitlab.aiat-poc.at/ai-at/products/usecase-studio/-/work_items/87
**Assignee:** PhilippJahn
**Labels:** `type::chore`, `area::infra`, `priority::high`, `status::ready`, `dev::philipp`

> Dieses Dokument ist die lokale Kopie des Issue-Bodys. Änderungen bitte in GitLab
> vornehmen — dort ist die lebende Fassung.

---

## Kontext

Die Workshop-Prompt-Seite liegt heute auf GitHub Pages
(`github.com/AnnSophieBrenk/ki-workshop-prompts` → `annsophiebrenk.github.io/ki-workshop-prompts/`)
und soll nach `gitlab.aiat-poc.at` umziehen, erreichbar unter **`ai-prompts.aiat-poc.at`**.

Die Seite ist rein statisch: zwei HTML-Dateien mit inline CSS/JS, **kein Build-Schritt**, keine Dependencies.

- `index.html` — deutsche Fassung (Startseite)
- `en/index.html` — englische Fassung, per DE|EN-Umschalter im Header verbunden

Neu ist ein Sende-Formular: Teilnehmende fügen die anonymisierte Tabelle aus Prompt 4 ein,
drücken Senden, und der Inhalt landet in der **bestehenden** Datenbank dieses Projekts.
Es wird **keine zweite Datenbank** gebaut — `use_case_entries` deckt alle Felder ab.

Das Formular ist fertig und getestet (DE + EN). Solange der Endpoint fehlt, zeigt es beim
Senden einen Hinweis auf `venturestudio@ai-at.eu` statt einer Fehlermeldung.

## Was ohne dich läuft

Kein Handlungsbedarf, nur zur Einordnung — das macht Ann-Sophie selbst:

- GitLab-Projekt `ai-at/products/ki-workshop-prompts` anlegen inkl. History-Import
- Route-Handler `POST /api/intake` in diesem Repo (Server Actions sind nicht cross-origin aufrufbar)
- LLM-Klassifikation von Abteilung und Thema über `llm.ai-at.eu` gegen `taxonomy`
- Abgleich der Seite `/prompts` mit den neueren Prompt-Texten

## Was wir von dir brauchen

### 1. Domain + Auslieferung `ai-prompts.aiat-poc.at`

- DNS-Record + TLS nach eurem Standard
- Auslieferung statischer Dateien — Vorschlag GitLab Pages (`pages.aiat-poc.at` existiert),
  alternativ dieselbe Strecke wie `usecase-studio`. Bitte den für euch günstigeren Weg wählen.
- **Startseite muss die deutsche Fassung sein** (`/` → `index.html`), Englisch unter `/en/`

### 2. CORS + Rate-Limit für den Intake-Pfad

Damit die Seite unter `ai-prompts.aiat-poc.at` in diese Datenbank schreiben kann:

- CORS-Freigabe für Origin `https://ai-prompts.aiat-poc.at` auf `POST /api/intake`
  (Caddy bzw. `proxy.ts`, wo aktuell auch die CSP gesetzt wird)
- Rate-Limit auf diesem Endpoint gegen Massen-Einträge

### 3. Bestätigung zum anon-Schreibpfad

Der anonyme Insert ist per RLS an einen offenen Workshop gebunden (`is_workshop_open`,
Migration `0007`). Das bleibt bewusst so — Teilnehmende geben den Workshop-Code im Formular ein.
Bitte nur bestätigen, dass `get_workshop_by_code` für die `anon`-Rolle vom neuen Endpoint aus
erreichbar ist. **Keine RLS-Änderung gewünscht.**

## Zieldatenmodell (existiert bereits)

| Feld | Herkunft |
|---|---|
| `use_case_entries.problem_text` | Spalte „Problem" der eingefügten Tabelle |
| `use_case_entries.loesung_text` | Spalte „KI-Lösungsidee" |
| `use_case_entries.abteilung` | LLM-Klassifikation gegen `taxonomy` (`typ='abteilung'`) |
| `use_case_entries.use_case_art` | LLM-Klassifikation gegen `taxonomy` (`typ='use_case_art'`) = Thema |
| `participants.branche` | Freitext im Formular (optional) |

Längen-Backstops aus Migration `0013` gelten unverändert (Problem/Lösung je max. 4000 Zeichen).

## Definition of Done

- [ ] `https://ai-prompts.aiat-poc.at/` liefert die deutsche Seite aus
- [ ] `https://ai-prompts.aiat-poc.at/en/` liefert die englische Seite aus, Umschalter funktioniert
- [ ] CORS-Freigabe für `POST /api/intake` steht, Rate-Limit aktiv
- [ ] `get_workshop_by_code` vom Endpoint aus für `anon` bestätigt
