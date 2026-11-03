# Prédiction de conversion à la newsletter

Prédire si un visiteur s'inscrit à la newsletter à partir de données de trafic (pays, âge, nouveau visiteur, source, nombre de pages visitées). Métrique principale : F1-score sur la classe « converti ».

## Notebook

- `conversion_prediction.ipynb` : exploration, baselines, optimisation, sélection du modèle et du seuil, évaluation sur le test, facteurs explicatifs, fichier de prédictions.

## Données

Les données ne sont pas incluses dans le dépôt.

- `conversion_data_train.csv` : données avec labels.
- `conversion_data_test.csv` : données sans labels, à prédire.

Placer les deux fichiers à la racine du dépôt.

## Installation

```bash
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
```

## Démarche

1. **Baselines** : régression logistique (F1 = 0,512) et Random Forest (F1 = 0,499), sur le même découpage 80/20 stratifié.
2. **Optimisation** : recherche d'hyperparamètres sur un échantillon de 30 000 lignes du train, pour la Random Forest et le gradient boosting.
3. **Sélection** : Random Forest et gradient boosting comparés par validation croisée sur le train complet, avec un seuil de décision optimisé pour chacun.
4. **Évaluation** : une seule évaluation sur le test.
5. **Facteurs explicatifs** : importance par permutation, calculée sur le test.

## Résultats

Les chiffres sont issus des cellules du notebook.

- **Baselines** : régression logistique F1 = 0,512 (rappel 95 %, précision 35 %) ; Random Forest par défaut F1 = 0,499 (rappel 87,6 %, précision 34,9 %).
- **Optimisation** (F1 de validation, échantillon de 30 000 lignes) : Random Forest 0,579 (profondeur 30, 100 arbres) ; gradient boosting 0,528.
- **Sélection** (validation croisée sur le train, seuil optimisé) : Random Forest F1 = 0,739 (seuil 0,934) ; gradient boosting F1 = 0,766 (seuil 0,942). Le gradient boosting est retenu.
- **Test** : F1 = 0,773 ; rappel = 73,3 % ; précision = 81,9 % ; F1 train = 0,768.
- **Facteurs explicatifs** (baisse de F1 après permutation, sur le test) : nombre de pages visitées 0,733 ; statut de nouveau visiteur 0,061 ; pays 0,041 ; âge 0,022 ; source 0,0005.

Le seuil de 0,942 maximise le F1 en validation croisée. Un autre seuil serait préférable si un faux positif (relance inutile) ou un faux négatif (prospect perdu) coûte davantage.

## Limites

- Les données sont observationnelles : elles montrent des associations, pas des effets causaux. Les recommandations du notebook doivent être testées en A/B.
- Certains âges sont aberrants (jusqu'à 123 ans), et la tranche 10-20 ans est à vérifier.
- Les écarts entre pays ne sont pas expliqués.
- Le F1 de validation est légèrement optimiste, car le seuil est choisi sur le train ; le F1 test est la référence.
- Les valeurs dépendent de la version des bibliothèques : utiliser les versions de `requirements.txt` pour reproduire les chiffres.

## Reproduction

1. Placer les deux fichiers CSV à la racine du dépôt.
2. Installer les dépendances.
3. Exécuter le notebook (« Restart & Run All »). Il crée `conversion_data_test_predictions_antoine.csv`, qui n'est pas versionné.

## Source

Ce projet a été réalisé dans le cadre de la formation en Data Science chez **Jedha Bootcamp**, qui a fourni le jeu de données de conversion.

Dans le cadre de ce challenge (*Conversion Rate Challenge*), les données proviennent de l'historique de trafic et d'inscriptions de la newsletter hebdomadaire [Data Science Weekly](https://www.datascienceweekly.org/). L'objectif simulé est de concevoir un modèle prédictif pour identifier si un visiteur va s'abonner à la newsletter en fonction de ses caractéristiques (pays, âge, source, pages visitées).

## Licence

- **Données** : mises à disposition par l'école dans un but strictement pédagogique et non commercial. Les fichiers ne sont pas redistribués dans ce dépôt.
- **Code et analyse** : MIT.
