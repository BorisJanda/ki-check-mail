# CLAUDE.md — ki-check-mail

## Status: Pausiert

Projekt wird aktuell nicht aktiv weiterverfolgt. Der n8n-Workflow ist deaktiviert.
Grund: Die Claude API unterstützt kein Web-Browsing — damit fehlt ein zentraler Wissenszugang.
Claude ist als Strategie-Berater und Risiko-Analyst eingeplant, kann aber ohne Web-Recherche nicht auf dem Niveau der anderen KIs (Perplexity, Gemini, ChatGPT) arbeiten.
Sobald die Claude API Web-Browsing unterstützt, wird das Projekt wieder aufgenommen.

## Projektkontext

**ki-check-mail** ist ein Email-basierter KI-Analyse-Service für Mitarbeiter und Kunden. Es empfängt Anfragen per Email und antwortet automatisch mit einem strukturierten KI-Review als Word-Anhang.

- Trigger: Email an `kicheck.jr@gmail.com`
- 4 KI-Systeme (ChatGPT, Gemini, Perplexity, Claude) mit je 2 Spezialistenrollen = 8 Rollen in Runde 1
- 3 Runden: Analyse → Kreuzprüfung → Überarbeitung
- Meta-Synthese durch Claude
- Antwort: Email mit Word-Anhang (.doc), Branding "4-KI-Expertise"

Betrieben über n8n-Workflow auf `janda-roscher.app.n8n.cloud`.

**GitHub:** https://github.com/BorisJanda/ki-check-mail

## Verzeichnisstruktur

```
archiv/          ← Ältere Workflow-Versionen
```

**Wichtige Dateien:**
- `synthese-4ki.js` — Synthese-Node-Code (Email-HTML + Word-Generierung, arbeitet mit 4 KI-Outputs)
- `workflow-ki-check-mail.json` — n8n Wrapper-Workflow (Email-Trigger + Core-Aufruf + Versand)
- `workflow-ki-check-core.json` — n8n Core-Workflow (8 R1-Prompts, 3 Runden, Meta-Synthese)
- `KI-Check-Mail Dokumentation [Datum].docx` — Aktuelle Projektdokumentation als Word

## Email-Format

**Betreff-Prefixe:**
- `FRAGE: Deine Frage hier` — Frage-Modus
- `KONZEPT: Konzeptname` — Konzept-Analyse
- Ohne Prefix — wird als KONZEPT behandelt

**Von:** Beliebige Email-Adresse
**An:** kicheck.jr@gmail.com

Zusätzlich: CLI-Aufruf möglich (source: 'cli', keine Email-Antwort, nur Word + GitHub)

**Antwort enthält:**
- Meta-Synthese (Kernaussage, Erkenntnisse, Dissense, Blinde Flecken)
- Quellenlinks (bis 15 in Email, vollständig im Word-Anhang)
- Word-Anhang mit allen Einzelantworten

## 4 KI-Systeme, 8 Spezialistenrollen

In Runde 1 übernimmt jede KI zwei spezialisierte Rollen (= 8 parallele Anfragen).
Ab Runde 2/3 werden die Ergebnisse pro KI zusammengeführt (= 4 konsolidierte Antworten).
Das Endprodukt (Email + Word) zeigt "4-KI-Expertise" mit 4 Einzelantworten.

| KI-System | R1-Rolle A | R1-Rolle B | R2/R3-Rolle (Word) |
|-----------|-----------|-----------|-------------------|
| ChatGPT | Trend-Analyst | Kreativ-Berater | Kreativer Challenger |
| Gemini | Nachrichten-Analyst | Branchen-Monitor | Medien-Faktenprüfer |
| Perplexity | Wettbewerbs-Analyst | Marktforscher | Recherche-Validator |
| Claude | Strategie-Berater | Risiko-Analyst | Strategie-Berater |

**Web-Recherche:** ChatGPT (ja), Gemini (ja, Google), Perplexity (ja, nativ), Claude (nein — nur Web-Kontext aus CLI)

## n8n Workflow

**Wrapper-Workflow:** ki-check-mail (Email → Core → GitHub-Commit → Email+Word)
**Core Sub-Workflow:** ki-check-core (8 R1-Prompts → Merge → R2 → R3 → Meta-Synthese)

- Email-Trigger: IMAP auf `kicheck.jr@gmail.com`
- Output: Email-Antwort mit Word-Anhang + GitHub-Commit des Markdown-Reviews
- Workflow-Status: **deaktiviert** (active: false)

## Bekannte Einschränkungen

- Claude API hat kein Web-Browsing — das ist der Hauptgrund für die Projektpause
- Claude bekommt nur über den CLI-Weg einen Web-Recherche-Kontext mitgegeben
- Nur Perplexity, Gemini und ChatGPT recherchieren eigenständig im Web
- Upgrade-Pfad: Wenn Claude API Web-Browsing bekommt → in Core Sub-Workflow einbauen, Projektpause beenden

## Design-Prinzipien

- Für externe Nutzer (Mitarbeiter, Kunden) ohne CLI-Zugang
- Einfach: Email rein → Email raus
- Word-Anhang mit vollständigen Einzelantworten
- Footer/Branding: "4-KI-Expertise · Powered by JANDA+ROSCHER"

## Email-Credentials

- **Gmail:** kicheck.jr@gmail.com
- Details in Memory: `kicheck_gmail.md`
- WICHTIG: NIEMALS privates Gmail-Konto verwenden
