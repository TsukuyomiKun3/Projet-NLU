# Exercice — Ton propre assistant NLU (sujet libre, livrable léger)

```
make setup              # MLflow + RustFS (comme aux TP2 et TP3)
uv sync
make exemple-domotique  # (facultatif) partir de l'exemple domotique
make valider            # vérifier tes données (donnees/)
make run                # RÉGLER : entraîner + mesurer sur la VALIDATION
make prompt-exemples    # le prompt système, réglé lui aussi sur la validation
make juger              # JUGER : porte de qualité sur ton test (une fois)
make livrer             # LIVRER : livrables/livrable_<groupe>.zip (~2-3 Mo)
make evaluer            # vérifier son livrable avant de le remettre
```

Importer un jeu en ligne (classification) : `uv run python importer.py --help`.

Énoncé pas à pas : **EXERCICE_PROJET_NLU.md**.
