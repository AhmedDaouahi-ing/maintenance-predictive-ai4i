# Projet 5 — Maintenance prédictive d'une machine-outil (ML + logique floue)

Module « Techniques avancées pour systèmes intelligents » — 3ᵉ année Génie Mécatronique, ENISo — 2026/2027
Binôme : Ahmed Daouahi — Mohamed Bennour

Dépôt : https://github.com/AhmedDaouahi-ing/maintenance-predictive-ai4i

## Objectif

À partir des mesures d'une fraiseuse (jeu **AI4I 2020**), le projet :

1. prédit si la machine va tomber en panne (Random Forest, comparé à une baseline, une régression logistique, XGBoost et l'approche standard des notebooks publics) ;
2. combine la probabilité de panne, l'usure de l'outil et l'échauffement dans un **système flou** (scikit-fuzzy, 3 entrées, 10 règles) qui donne une **priorité de maintenance** : faible, moyenne ou urgente.

## Résultats principaux

| | Rappel | Précision | F1 |
|---|---|---|---|
| Baseline « jamais de panne » (validation croisée) | 0,00 | 0,00 | 0,00 |
| Random Forest de référence, variables brutes (validation croisée) | 0,54 | 0,89 | 0,67 |
| **Notre Random Forest (validation croisée, 5 plis)** | **0,82** | **0,89** | **0,85** |
| XGBoost (validation croisée, 5 plis) | 0,83 | 0,78 | 0,80 |

- Seuil de décision retenu : **0,4** (F2-score maximal en validation croisée).
- Jeu de test : 56 pannes détectées sur 68 ; les pannes manquées sont surtout des pannes par usure de l'outil (TWF), aléatoires dans ce jeu.
- Système flou : **90 % des pannes du test classées « urgente »**, une seule classée « faible » ; 83 % des mesures saines restent en priorité faible.

## Contenu du dépôt

```
.
├── maintenance_predictive_ai4i.ipynb   # notebook complet (code + résultats + interprétations)
├── maintenance_predictive_ai4i.html    # version lisible sans Jupyter
├── ai4i2020.csv                        # jeu de données (10 000 lignes)
├── requirements.txt
└── README.md
```

## Exécution

### Sur Kaggle (environnement imposé)

1. Créer un notebook Kaggle et importer `maintenance_predictive_ai4i.ipynb` (File > Import Notebook).
2. Ajouter le jeu de données : **Add Input** > rechercher « AI4I 2020 Predictive Maintenance Dataset » (`stephanmatzka/predictive-maintenance-dataset-ai4i-2020`).
3. Activer Internet dans les paramètres de la session (pour installer `scikit-fuzzy`). Le CPU suffit : pas besoin de GPU.
4. **Run All**. Durée : moins d'une minute.

Le notebook installe automatiquement `scikit-fuzzy` et `xgboost` s'ils sont absents, puis trouve le fichier dans `/kaggle/input/`.

### En local

```bash
git clone https://github.com/AhmedDaouahi-ing/maintenance-predictive-ai4i.git
cd maintenance-predictive-ai4i
python -m venv .venv
source .venv/bin/activate        # Windows : .venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook maintenance_predictive_ai4i.ipynb
```

Le notebook lit `ai4i2020.csv` à la racine du dépôt.

## Reproductibilité

- Graine aléatoire fixée (`GRAINE = 42`) pour le découpage, la validation croisée et les modèles.
- Le jeu de test (20 %) n'est utilisé qu'une fois, à la fin ; le modèle et le seuil sont choisis par validation croisée sur le jeu d'entraînement.
- Les colonnes TWF, HDF, PWF, OSF et RNF (type de panne, connu après la panne) sont exclues des entrées pour éviter la fuite de données.
- Selon la version de scikit-learn, les résultats peuvent varier de quelques centièmes.

## Source des données

S. Matzka, *Explainable Artificial Intelligence for Predictive Maintenance Applications*, 2020 — UCI Machine Learning Repository : https://archive.ics.uci.edu/dataset/601/ai4i+2020+predictive+maintenance+dataset
