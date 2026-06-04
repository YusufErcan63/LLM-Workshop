# Make-or-Buy-Memo — Annotation der restlichen Stellenanzeigen

**An:** Projektleitung FIDP-Informationsextraktionsprojekt
**Von** Yusuf
**Datum:** 2026-06-04
**Betreff:** Empfehlung zur Annotationsstrategie für 60+ weitere Anzeigen

---

# Ausgangslage

Wir haben 12 Anzeigen hand-annotiert (Phase 2) und dieselben 12 von Claude Opus 4.6 annotieren lassen (Phase 5). Die Inter-Rater-Übereinstimmung auf den drei kategorialen Kern-Feldern ist hoch, aber nicht durchgängig perfekt: vertragsart κ = 1,000, homeoffice κ = 0,886, erfahrungslevel κ = 0,786 — bei insgesamt 7 abweichenden Feldern über alle 12 Anzeigen. Das Gesamtniveau ist damit hoch, aber nicht perfekt; Handlungsbedarf besteht vor allem dort, wo die Kategoriegrenzen unscharf sind.

# Empfehlung: Frontier für die Kern-Felder, verstärkte Kontrolle bei erfahrungslevel und skills
Ich empfehle einen **Hybrid-Ansatz:** Das Frontier-LLM annotiert alle 60+ restlichen Anzeigen automatisch, aber nicht vollständig ohne Kontrolle.

**Warum Frontier für die Kern-Felder — mit unterschiedlichem Vertrauensgrad?**
Auf vertragsart vertraue ich dem Frontier-Modell vollständig — κ = 1,000, kein einziger Disagreement auf n=12. Hier ist das Schema klar und das Modell hält sich konsequent daran.
Auf homeoffice (κ = 0,886) ist das Vertrauen hoch; die wenigen Abweichungen sind auf Randfälle zurückzuführen, keine systematischen Fehler.
Auf erfahrungslevel (κ = 0,786) ist Vorsicht geboten — das ist das schwächste Feld. Die Disagreements 1004 und 1006 zeigen: Die Grenze zwischen nicht_genannt und junior ist unscharf, und das Modell zieht sie anders als ich. Hier empfehle ich eine erweiterte Stichproben-Kontrolle, nicht nur 10 %, sondern eher 15–20 % der Fälle, bis ein stabileres Bild entsteht.

**Warum verstärkte Kontrolle bei skills?**
Bei gehalt_min_eur war das Frontier zuverlässig — null Disagreements auf n=12, exakt gleiche Entscheidungen wie ich. Hier reicht eine leichte Stichproben-Kontrolle zur Sicherheit.
Bei skills_top3 dagegen hatte das Frontier die meisten Abweichungen: In den Fällen 1004, 1009 und 1012 hat das Modell Tools erfasst, die ich leer gelassen hatte. Das dreht die Intuition um — nicht das Modell macht die Fehler, sondern möglicherweise ich. Die Tool/Methodik-Grenze bleibt dennoch unscharf. Deshalb empfehle ich eine systematische Stichproben-Kontrolle, um zu klären, wessen Entscheidungen besser mit dem Schema übereinstimmen, bevor wir das Feld ohne Kontrolle skalieren.

# Schwellwert-Logik
Meine Grenze: Wenn eine Stichproben-Kontrolle (10 % = ca. 6 Anzeigen, bei erfahrungslevel 15–20 %) mehr als 1 Fehler auf einem kategorialen Feld zeigt (≥ 17 % Fehlerrate), würde ich das gesamte Feld manuell nachkontrollieren. Bei gehalt_min_eur: Fehlerrate > 20 % triggert vollständigen Review. Unter diesen Schwellen vertraue ich der Frontier-Annotation.

# Risiko-Sicherung
Bevor κ-Berechnungen oder Pipeline-Evals auf der Frontier-annotierten Daten laufen:
python annotation/validate.py annotation/frontier_gold.csv
— Schema-Verletzungen fliegen raus und werden manuell korrigiert oder per Korrektur-Turn nachgebessert. Das ist keine optionale Maßnahme, sondern Pflichtschritt. Ein ungeprüftes frontier_gold.csv könnte κ-Werte verzerren, wenn Werte wie "möglich" statt "ja" unbemerkt bleiben.

**Wenn ich falsch liege:** Der schlimmste Fall ist, dass wir systematisch falsch annotierte Trainingsdaten produzieren. Das fiele erst auf, wenn ein auf diesen Daten trainiertes Modell merkwürdige Fehler macht — spät und teuer. Deshalb ist die Stichprobe nicht optional.

## Fazit
Frontier annotiert, Mensch kontrolliert stichprobenartig. Die κ-Werte zwischen 0,79 und 1,0 je nach Feld rechtfertigen das Vertrauen in den Hybrid-Ansatz — gleichzeitig belegen sie, dass n=12 zu klein ist, um das Modell ohne jede Kontrolle auf 60+ Anzeigen hochzuskalieren. Der Aufwand für eine 10–20-%-Kontrolle (ca. 6–12 Anzeigen, ~20–30 min) ist im Verhältnis zum Risiko klar vertretbar. Die echten Zahlen bestätigen diese Einschätzung sogar stärker als ein perfektes κ = 1,0 es täte — weil sie zeigen, wo die Grenzen tatsächlich liegen.