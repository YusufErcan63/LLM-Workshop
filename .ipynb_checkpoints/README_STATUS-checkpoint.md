# Projektstand & Ausführen auf JupyterHub

Kurzüberblick, was lauffähig ist und was noch echte Läufe/Daten braucht.

## Reihenfolge auf dem Hub

1. **gauss** — `notebooks/02_extract.ipynb` von oben nach unten ausführen.
   Erzeugt: `predictions.jsonl` (Baseline), `predictions_iter_A.jsonl`,
   `predictions_iter_B.jsonl`, `predictions_7b_full.jsonl`.
   (Die mitgelieferten `iter_A/iter_B/7b_full` sind echte 7B-Läufe; ein erneuter
   Durchlauf überschreibt sie mit frischen echten Werten — inkl. der bisher
   fehlenden Baseline.)
2. **euler** — Kernel neu starten, in `02_extract.ipynb` nur die GPU-Check-Zelle
   und die **Phase-6-3B-Sektion** ausführen (die 7B-Zellen NICHT — 7B passt nicht
   in 16 GB). Erzeugt: `predictions_3b_full.jsonl`. Die 3B-Lade-Zelle fängt das
   GPTQ-Cache-Problem ab und lädt nötigenfalls einmalig einen sauberen Build neu.
3. **egal wo** — `notebooks/03_eval.ipynb` ausführen. Liest alle Predictions-Dateien
   und baut die Accuracy-/Iterations-Tabellen aus echten Zahlen. Leere Dateien
   werden sauber mit „—" abgefangen.
4. `notebooks/01_explore.ipynb` läuft eigenständig (Korpus + κ + Edge Cases).

## Noch offen (nur mit echten Daten lösbar)

- **Baseline** (`predictions.jsonl`) ist noch leer → Schritt 1 erzeugt sie.
- **3B** (`predictions_3b_full.jsonl`) ist noch leer → Schritt 2 erzeugt sie.
- **`frontier_gold.csv` ist aktuell eine Kopie von `meine_gold.csv`** (κ = 1,0,
  keine echten Disagreements). Für Phase 5 die 12 Anzeigen real durch ein
  Frontier-Modell schicken und die echten Ausgaben eintragen — Anleitung steht in
  `notebooks/04_frontier_compare.ipynb` und `CHEATSHEETS/frontier-llm-workflow.md`.
- **Korpus** (`daten/eigener_korpus.jsonl`): noch nicht über die BA-Jobbörse-API
  gezogen (Phase 1). Läuft ohne GPU, auch im Hub-Terminal.
- Im Run-Header von `01_explore` den Pair-Partner-Namen eintragen.

## Hinweis

`03_eval` enthält einen echten Befund: Iteration B (Stundenlohn-Regel) hat
`gehalt_min_eur` nicht verbessert, sondern von 100 % auf 83 % gesenkt
(zwei Monatsgehälter fälschlich genullt). Das ist bewusst so dokumentiert —
eine ehrlich ausgewertete, widerlegte Hypothese ist Teil der Bewertung.
