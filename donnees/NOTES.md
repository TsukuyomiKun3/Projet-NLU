# Notes du projet — Domaine Restaurant

## 1. Source et licence des données
Toutes les données (`train.csv`, `validation.csv`, `test.csv`) ont été rédigées à la main par notre groupe pour ce TP (aucune donnée personnelle, aucun dataset externe importé).
- **Train** : 96 phrases (12 par intention)
- **Validation** : 24 phrases (3 par intention)
- **Test** : 24 phrases (3 par intention)

L'ontologie compte 8 intentions (`reserver`, `annuler_reservation`, `modifier_reservation`, `privatiser_salle`, `commander_emporter`, `horaires`, `menu`, `acces`) et 7 types de slots (`personnes`, `date`, `heure`, `reference`, `plat`, `quantite`, `lieu`).

## 2. Choix des réglages (sur la validation)
On a fait varier `TRAIN_MAX_STEPS` de 30 à 150 pas en regardant uniquement la validation :
- **30 pas** : `val_intent_acc = 4%`, `val_exact = 0%` (modèle complètement sous-entraîné, ne respecte pas encore le format)
- **60 pas** : `val_intent_acc = 17%`, `val_exact = 4%`
- **90 pas** : `val_intent_acc = 46%`, `val_exact = 25%`
- **120 pas** : `val_intent_acc = 79%`, `val_exact = 46%`
- **150 pas** : `val_intent_acc = 79%`, `val_exact = 46%` (meilleur run retenu par `juger.py` car `best_val_loss` plus basse)

Côté prompt système, on a comparé `prompts/systeme.txt` et `prompts/exemples_train.txt` sur la validation et on a gardé `prompts/exemples_train.txt` (qui inclut un exemple du train par intention) pour donner sa meilleure chance à `base+prompt`.

## 3. Résultats sur le test scellé et analyse du FAIL de la porte
Le test scellé n'a été lu qu'**une seule fois** (`lectures_test = 1`), à la toute fin :
- **LoRA (150 pas)** : `intent_acc = 71%`, `slot_f1 = 63%`, `exact = 42%`
- **base** : `intent_acc = 0%`, `slot_f1 = 8%`, `exact = 0%`
- **base+prompt** : `intent_acc = 67%`, `slot_f1 = 48%`, `exact = 21%`

La porte affiche `FAIL` alors que le LoRA dépasse bien le seuil des 70 % (`71%`), parce que la marge sur `intent_acc` par rapport à `base+prompt` n'est que de **+4 points** (au lieu des 30 points demandés).
Cela s'explique par le fait que nos noms d'intentions sont en français très explicite (`reserver`, `horaires`, `menu`, etc.) : avec les descriptions et 1 exemple par intention dans le prompt, le modèle de base trouve facilement l'intention tout seul (67 %).
Par contre, dès qu'on regarde l'extraction des slots, le LoRA fait beaucoup mieux : **63 % contre 48 % sur `slot_f1`**, et surtout **deux fois mieux sur `exact` (42 % contre 21 %)**. Si l'objectif est juste de trouver l'intention, un prompt suffit presque ; mais pour extraire proprement les slots au bon format, le fine-tuning LoRA reste nettement meilleur.

## 4. Limites et comparaison avec le test croisé
- **Limites** : avec 96 phrases d'entraînement (contre 40 dans le TP3 de base), 150 pas représentent seulement ~3 époques. Certaines intentions proches (comme `reserver`, `modifier_reservation` et `privatiser_salle` quand l'utilisateur parle de réserver une salle/table) ou les phrases avec 3 ou 4 slots en même temps restent plus difficiles.
- **Test propre vs test croisé** : notre score sur notre propre test a des chances d'être un peu plus élevé que sur le test croisé écrit par un autre groupe, car même en écrivant le test à part, on garde inconsciemment les mêmes tournures de phrases et habitudes de découpage des slots (par ex. ce qu'on met dans `plat` ou `lieu`), alors qu'un autre groupe qui ne voit que `ontologie.yaml` utilisera des formulations inédites.
