# **Projet InnovaCode 2026 – Sécurité routière à Ottawa**

## Contexte

Chaque année, des milliers de collisions routières surviennent à Ottawa. La Ville met à disposition un jeu de données ouvert détaillant ces événements (lieu, date, conditions, gravité, etc.). L’objectif du projet est d’exploiter ces données pour identifier des zones à risque et proposer des solutions concrètes d’amélioration de la sécurité routière.

## Objectif

- Réaliser une analyse exploratoire des données de collision.
- Identifier des grappes spatiales (clusters) d’accidents à l’aide d’un algorithme de clustering (DBSCAN).
- Caractériser chaque cluster par son volume d’accidents, son taux de blessures, le type d’impact dominant, les conditions météo, etc.
- Produire une visualisation interactive (carte) avec l’API Google Maps pour représenter les zones à risque, la densité des accidents et des points cliquables détaillés.
- Fournir des recommandations opérationnelles à la Ville d’Ottawa.

## Jeu de données

- **Source** : [Traffic Collision Data – Ville d’Ottawa](https://open.ottawa.ca/datasets/ottawa::traffic-collision-data/explore)
- **Période** : 2017–2024 (années disponibles)
- **Nombre d’enregistrements** : 94 406 collisions après nettoyage
- **Colonnes principales** : coordonnées géographiques, date, classification de l’accident (blessure / dommage matériel), type d’impact, état de la chaussée, conditions météo, luminosité, contrôle de la circulation, nombre de véhicules, usagers vulnérables, etc.

## Méthodologie

### 1. Analyse exploratoire (EDA)
- Nettoyage des coordonnées aberrantes.
- Conversion des dates et création de colonnes temporelles (mois, jour, heure).
- Visualisation des distributions : gravité, types d’impact, conditions météo, contrôle, etc.
- Cartographie préliminaire des points d’accidents.

### 2. Clustering spatial (DBSCAN)
- Algorithme : DBSCAN (`eps=0.001°`, `min_samples=5`).
- Résultat : 1 404 clusters identifiés, 82 372 accidents regroupés (89 % des données).
- Un cluster ultra‑dominant (cluster 2) concentre 36 089 accidents (point noir majeur).

### 3. Caractérisation des clusters
Pour chaque cluster, calcul :
- Nombre d’accidents
- Taux de blessures (accidents avec blessures / total)
- Mode (valeur la plus fréquente) : type d’impact, état de la chaussée, météo, luminosité, contrôle, mois, jour de semaine

### 4. Priorisation
Score de priorité = nombre d’accidents × taux de blessures.  
Les 10 premiers clusters (par score) sont retenus pour l’affichage sur la carte interactive.

### 5. Génération du fichier JSON (`total_risk_zones.json`)
- **zones** : centroïde, rayon (proportionnel au nombre d’accidents), risque (critique, élevé, modéré, faible).
- **heatmap_points** : 5 000 points échantillonnés avec poids (1 pour blessure, 0,5 pour matériel).
- **clickable_points** : tous les points des 10 premiers clusters (données détaillées pour les popups).

## Livrables

- **Notebook Jupyter** : analyse exploratoire, clustering, caractérisation, export JSON.
- **Script Python** : génération autonome du JSON à partir du DataFrame nettoyé.
- **Fichier JSON** : `total_risk_zones.json`.
- **Carte interactive** : fichier HTML utilisant l’API Google Maps (visualisation des zones, heatmap, points cliquables).

## Limitations et perspectives

- **Données** : certaines colonnes (usagers vulnérables, blessures détaillées) présentent beaucoup de valeurs manquantes.
- **Clustering** : le paramètre `eps` influence la taille des clusters ; une calibration plus fine pourrait être réalisée avec une validation métier.
- **Carte** : l’affichage de tous les points cliquables peut ralentir l’affichage sur des machines anciennes ; une pagination ou un clustering des marqueurs pourrait être ajouté.
- **Améliorations possibles** :
  - Intégrer des données de trafic en temps réel.
  - Ajouter un calculateur d’itinéraire sécurisé (alerte lors du passage dans une zone à risque).
  - Proposer des itinéraires alternatifs évitant les zones dangereuses.

## Licence

Les données sont fournies par la Ville d’Ottawa sous licence ouverte.  
Le code est disponible sous licence MIT.