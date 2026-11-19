# BE COS OPTI — Couverture réactive par constellation de satellites

Projet d'optimisation (ISAE-SUPAERO, 3A COS) : choisir quels passages de satellites réserver au-dessus d'une grande zone afin de pouvoir observer n'importe quel point de cette zone en moins de 12 h, 4 h ou 2 h après un événement, au moindre coût de réservation.

## Le problème

- La zone est découpée en **AOIs** (zones d'intérêt élémentaires). Chaque AOI possède des **accès**, c'est-à-dire des intervalles de visibilité par un satellite. Ces accès sont regroupés en **passages** (*passes*).
- Les objectifs de réactivité sont traduits en **requêtes d'observation** de priorité 1 (12 h), 2 (4 h) et 3 (2 h). Une requête est satisfaite si au moins un de ses accès est couvert pendant 30 s par un passage réservé.
- Une réservation coûte un **coût fixe par passage** plus un **coût par unité de temps réservée**.
- Le problème a deux critères à optimiser : maximiser la satisfaction des requêtes et minimiser le coût.

## Contenu du notebook

[`reactive_covering.ipynb`](reactive_covering.ipynb) est organisé en huit parties :

1. **Données et outils d'affichage** : chargement d'une instance et tracé des passages réservés sur la carte.
2. **Heuristique gloutonne** : solution de référence. Les passages sont triés par nombre d'accès par unité de temps.
3. **Modèle CPLEX, passages complets** : PLNE (docplex) où chaque passage est réservé sur toute sa durée.
4. **Modèle CPLEX, passages partiels** : la fenêtre de réservation de chaque passage est optimisée, avec des contraintes big-M sur les dates de début et de fin.
5. **Visualisation des réservations** : chronogramme des passages, des fenêtres réservées et des accès couverts.
6. **Animation de la couverture** : progression de la couverture des AOIs, exportée en GIF.
7. **Front de Pareto coût / satisfaction** : le modèle est résolu sous une série de budgets maximaux.
8. **Comparaison des coûts** : décomposition des coûts en part fixe et part variable.

## Résultats indicatifs (instance `data2`, 6 867 requêtes, 69 passages)

| Méthode | Requêtes satisfaites | Passages réservés | Coût |
|---|---|---|---|
| CPLEX, passages complets (optimal) | 88,5 % | 14 | 5 637 |
| CPLEX, passages partiels (limite de 120 s) | ≈ 94–95 % | 22–23 | ≈ 6 000–6 300 |

Les requêtes de priorité 1 sont couvertes à 100 % dans les deux cas. Le modèle à passages partiels s'arrête sur sa limite de temps de 2 minutes, si bien que ses résultats varient légèrement d'une exécution à l'autre.

## Exécution

```bash
pip install docplex cplex numpy matplotlib pillow
jupyter notebook reactive_covering.ipynb
```

Les instances se trouvent dans [`data/`](data/) : `data12.json`, `data4.json` et `data2.json`, qui correspondent aux buts de réactivité de 12 h, 4 h et 2 h. Le fichier `countries.geo.json` contient le fond de carte.

> **Licence CPLEX** : la version `cplex` installée par pip est la *Community Edition*, limitée à 1 000 variables et 1 000 contraintes. Elle suffit pour `data12`. Pour `data4` et `data2`, il faut CPLEX Optimization Studio (gratuit pour les étudiants via l'IBM Academic Initiative), puis lancer `docplex config --upgrade <dossier CPLEX_Studio>`. On peut aussi remplacer à la main la bibliothèque `libcplex*` du paquet pip par celle de l'installation complète.

## Crédits

Le sujet et les jeux de données ont été fournis par l'ONERA (C. Pralet) dans le cadre du cours d'optimisation de l'ISAE-SUPAERO.
