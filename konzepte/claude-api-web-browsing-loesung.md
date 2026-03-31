# Claude API Web-Browsing für ki-check-mail — Lösungsansätze

## Ausgangslage

Das Projekt ki-check-mail ist technisch komplett funktionsfähig, aber pausiert. Grund: Die Claude API konnte bisher nicht eigenständig im Web recherchieren. Claude ist als Strategie-Berater und Risiko-Analyst eingeplant, arbeitet aber ohne Web-Zugang nicht auf dem Niveau der anderen 3 KIs (ChatGPT, Gemini, Perplexity), die alle eigenständig im Web suchen.

David (Chefentwickler) hat vorgeschlagen, Claude über Zusatztools Web-Recherche-Fähigkeit zu geben, damit der 4-KI-Prozess vollwertig läuft.

## Aktuelle Recherche-Ergebnisse (März 2026)

### Option 1: Anthropic Web Search Tool (offiziell, empfohlen)

Anthropic hat seit Mai 2025 ein offizielles Web Search Tool in der Claude API. Stand März 2026 ist es General Available (kein Beta-Header mehr nötig).

**So funktioniert es:**
- Man fügt `web_search` als Tool in den API-Request ein
- Claude entscheidet selbst, ob eine Web-Suche nötig ist
- Claude formuliert Suchqueries, bekommt strukturierte Ergebnisse zurück
- Claude kann iterativ suchen (mehrere Queries nacheinander)
- Antworten enthalten automatisch Quellen-Citations
- Seit Februar 2026: Dynamic Filtering — Claude filtert HTML mit Python-Code vor der Verarbeitung

**Kosten:** $10 pro 1.000 Suchen + Standard-Token-Kosten
**Backend:** Brave Search
**Modelle:** Claude 3.5 Sonnet, Claude 3.7 Sonnet, Claude 4+

**Für n8n-Integration:** Im HTTP Request Node den `web_search` Tool einfach zum Tools-Array hinzufügen. Minimale Änderung am bestehenden Workflow.

### Option 2: Perplexity API als Vorrecherche-Schritt

Statt Claude selbst suchen zu lassen, könnte man einen zusätzlichen n8n-Node VOR dem Claude-Prompt einbauen, der Perplexity als Recherche-Tool nutzt und die Ergebnisse als Kontext an Claude weitergibt.

**So funktioniert es:**
- Neuer n8n-Node: Perplexity Sonar API mit der Frage/dem Konzept aufrufen
- Perplexity recherchiert und liefert strukturierte Antworten mit Quellen
- Ergebnis als zusätzlichen Kontext in den Claude-Prompt einfügen

**Kosten:** Sonar: $1/M Input + $1/M Output, Sonar Pro: $3/$15/M, Search: $5 pro 1.000 Requests

**Vorteil:** Perplexity ist spezialisiert auf Web-Recherche, liefert hochwertige Quellen
**Nachteil:** Zusätzlicher API-Call, Claude sucht nicht selbst sondern bekommt nur Kontext

### Option 3: Brave Search API direkt

Direkt die Brave Search API aufrufen und Ergebnisse an Claude geben.

**Kosten:** Kostenloser Tier verfügbar (1 Query/Sekunde, 2.000/Monat), danach $3-5 pro 1.000 Queries
**Vorteil:** Günstig, unabhängig
**Nachteil:** Rohe Suchergebnisse, Claude muss mehr Kontext verarbeiten

### Option 4: n8n Multi-Stage Research (wie Perplexity-Klon)

Es gibt n8n-Workflow-Templates die einen "günstigeren Perplexity-Klon" bauen: DuckDuckGo/Brave für die Suche, dann Artikelextraktion, dann LLM-Zusammenfassung.

**Vorteil:** Volle Kontrolle, günstig
**Nachteil:** Komplex, mehr Wartung, fragile Pipeline

## Empfehlung

**Option 1 (Anthropic Web Search Tool) ist der klare Favorit:**

1. **Minimaler Umbau:** Nur das Tools-Array im Claude HTTP Request anpassen — der Rest bleibt identisch
2. **Claude sucht selbst:** Keine Vorrecherche-Pipeline nötig, Claude formuliert eigene Queries
3. **Offiziell unterstützt:** Kein Hack, kein Workaround — Anthropic-Feature
4. **Citations inklusive:** Quellen kommen automatisch mit
5. **Dynamic Filtering:** Seit Feb 2026 filtert Claude irrelevanten HTML-Content selbst
6. **Kosten überschaubar:** Bei ~20 Suchen pro Review = $0.20 pro Review

**Umsetzungsplan:**
1. Im Core-Workflow `workflow-ki-check-core.json` die Claude R1/R2/R3 HTTP-Requests anpassen
2. Tools-Array mit `web_search` ergänzen (ca. 5 Zeilen JSON pro Node)
3. System-Prompts für Claude aktualisieren: "Recherchiere aktiv im Web"
4. Testen mit einem Beispiel-Konzept
5. Web-Kontext-Workaround aus CLI-Weg kann dann entfallen
6. Workflow aktivieren → Projektpause beenden

**Aufwand:** ~2-4 Stunden für David, da der Workflow bereits steht und nur die Claude-Nodes angepasst werden müssen.
