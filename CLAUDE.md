# CLAUDE.md — ki-check-mail

## Projektkontext

**ki-check-mail** ist ein Email-basierter KI-Analyse-Service für Mitarbeiter und Kunden. Es empfängt Anfragen per Email und antwortet automatisch mit einem strukturierten KI-Review als Word-Anhang.

- Trigger: Email an `kicheck.jr@gmail.com`
- 8 KI-Spezialisten via APIs (parallel)
- 3 Runden: Analyse → Kreuzprüfung → Überarbeitung
- Meta-Synthese durch Claude
- Antwort: Email mit Word-Anhang (.doc)

Betrieben über n8n-Workflow auf `janda-roscher.app.n8n.cloud`.

**GitHub:** https://github.com/BorisJanda/ki-check-mail

## Verzeichnisstruktur

```
archiv/          ← Ältere Workflow-Versionen
```

**Wichtige Dateien:**
- `synthese-4ki.js` — Backup des Synthese-Node-Codes (inkl. Email-HTML + Word-Generierung)
- `workflow-ki-check-mail.json` — n8n Workflow-Export (lokal)

## Email-Format

**Betreff:** `FRAGE: Deine Frage hier` oder `KONZEPT: Konzeptname`

**Von:** Beliebige Email-Adresse
**An:** kicheck.jr@gmail.com

**Antwort enthält:**
- Meta-Synthese (Kernaussage, Erkenntnisse, Dissense, Blinde Flecken)
- Quellenlinks
- Word-Anhang mit allen Einzelantworten

## 8 Spezialisten

| # | Spezialist | KI | Fokus |
|---|-----------|-----|-------|
| 1 | Trend-Analyst | ChatGPT | Aktuelle Trends |
| 2 | Kreativ-Berater | ChatGPT | Unkonventionelle Ansätze |
| 3 | Nachrichten-Analyst | Gemini | Aktuelle Meldungen |
| 4 | Branchen-Monitor | Gemini | Branchenlage & Markt |
| 5 | Wettbewerbs-Analyst | Perplexity | Wettbewerber & Marktposition |
| 6 | Marktforscher | Perplexity | Studien, Zahlen, Daten |
| 7 | Strategie-Berater | Claude | Chancen, Risiken, Empfehlungen |
| 8 | Risiko-Analyst | Claude | Schwächen, blinde Flecken |

## n8n Workflow

**Wrapper-Workflow:** ki-check-mail (Email → Core → Email+Word)
**Core Sub-Workflow:** ki-check-core (Spezialisten + Runden + Meta)

- Email-Trigger: IMAP auf `kicheck.jr@gmail.com`
- Output: Email-Antwort mit Word-Anhang + GitHub-Commit

## Bekannte Einschränkungen

- Claude recherchiert NICHT im Web (API hat kein Web-Browsing)
- Nur die anderen KIs (Perplexity, Gemini) recherchieren eigenständig
- Upgrade-Pfad: Wenn Claude API Web-Browsing bekommt → in Core Sub-Workflow einbauen

## Design-Prinzipien

- Für externe Nutzer (Mitarbeiter, Kunden) ohne CLI-Zugang
- Einfach: Email rein → Email raus
- Word-Anhang mit vollständigen Einzelantworten
- Footer: "Powered by JANDA+ROSCHER"

## Email-Credentials

- **Gmail:** kicheck.jr@gmail.com
- Details in Memory: `kicheck_gmail.md`
- WICHTIG: NIEMALS privates Gmail-Konto verwenden
