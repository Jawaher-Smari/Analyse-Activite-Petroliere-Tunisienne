# Rapport du Projet

> **Description** : Analyse des flux d'importation, d'exportation et de production des produits pétroliers en Tunisie  
> **Outil** : Power BI Desktop  
> **Source des données** : [Portail National des Données Ouvertes — data.gov.tn](https://data.gov.tn)  
> **Période couverte** : 1990 – 2023  


## 1. Contexte Général

L'industrie pétrolière constitue l'un des piliers de l'économie, que ce soit à l'échelle nationale ou internationale. La Tunisie entre dans l'ère pétrolière dès 1957, avec la création de la TRAPSA (Compagnie des Transports par Pipe-Lines au Sahara) et la mise en place des premières infrastructures d'évacuation du brut via le terminal de La Skhira.

L'ère pétrolière tunisienne s'est caractérisée par la découverte progressive de champs pétroliers et la création continue de sociétés dont l'activité principale est d'explorer, d'exploiter, de raffiner et de distribuer du pétrole. 

C'est dans ce contexte que s'inscrit ce projet d'analyse de données, qui vise à explorer et à interpréter les flux d'importation, d'exportation et de production des produits pétroliers en Tunisie, afin d'en dégager les tendances, les déséquilibres et les dynamiques clés sur la période étudiée.

### Questions analytiques

- Q1. Quelle est l'évolution des importations annuelles de pétrole brut sur la période ?
- Q2. Quelle est l'évolution de la production totale de pétrole brut sur la période 1990–2023 ?
- Q3. Quels sont les 5 champs les plus productifs historiquement ?
- Q4. Quelle est la tendance des exportations annuelles de pétrole brut sur la période ?
- Q5. Quelle région tunisienne contribue le plus à la production nationale, et cette répartition a-t-elle évolué dans le temps ?
- Q6. Quel est le taux de déclin annuel de la production nationale ?

### KPIs définis

| KPI | Formule | Interprétation |
|-----|---------|----------------|
| **Taux de Couverture** | Production / Importations brut × 100 | Mesure dans quelle proportion la production locale couvre les besoins en importation. Un taux < 100% signifie une dépendance croissante aux importations |
| **Balance Commerciale Pétrolière** | Exportations − Importations totales | Indique si la Tunisie est excédentaire ou déficitaire sur le pétrole |
| **Taux de Déclin Annuel** | (Production N − Production N-1) / Production N-1 | Mesure le rythme de déclin de la production nationale d'une année sur l'autre |

---

## 2. Exploration des Données

### 2.1 Source des données

Le jeu de données est collecté et extrait du **Portail National des Données Ouvertes en Tunisie** ([data.gov.tn](https://data.gov.tn)). Il est constitué de 6 fichiers CSV.

### 2.2 Vue d'ensemble des fichiers

| Fichier | Contenu | Période | Granularité | Nb lignes |
|---------|---------|---------|-------------|-----------|
| `export_annuel_petrole_brute.csv` | Date et quantité totale exportée (kt) | 2005–2021 | Annuelle | 21 |
| `import_annuel_petrole_brute.csv` | Date et quantité totale importée (kt) | 2005–2021 | Annuelle | 17 |
| `import_annuel_produits_petroliers.csv` | Date et quantité importée par produit (kt) | 2005–2023 | Annuelle | 19 |
| `production_annuel_petrole_champ.csv` | Date et quantité produite par champ pétrolier (kt) | 1990–2023 | Annuelle | 34 |
| `production_annuel_petrole_qualite.csv` | Date et quantités de production par qualité de pétrole (kt) | 1990–2021 | Annuelle | 32 |
| `production_annuel_petrole_region.csv` | Date et quantités en kt de production par gouvernorat | 1990–2019 | Annuelle | 30 |

### 2.3 Anomalies identifiées lors du profiling

**`export_annuel_petrole_brute.csv`**
- Présence de lignes vides
- Types des colonnes (Date, Nombre Décimal) non détectés automatiquement
- Inconsistance dans l'arrondi des valeurs numériques

**`import_annuel_petrole_brute.csv`**
- Types des colonnes (Date, Nombre Décimal) non détectés automatiquement

**`import_annuel_produits_petroliers.csv`**
- Libellés de colonnes trop longs
- 2 colonnes contiennent globalement des valeurs manquantes
- Type de la colonne Date non détecté automatiquement

**`production_annuel_petrole_champ.csv`**
- Types des colonnes (Date, Nombre Décimal) non détectés automatiquement
- Inconsistance dans l'arrondi des valeurs numériques
- Présence d'une colonne de totaux

**`production_annuel_petrole_qualite.csv`**
- Types des colonnes (Date, Nombre Décimal) non détectés automatiquement
- Inconsistance dans l'arrondi des valeurs numériques
- Présence d'une colonne de totaux

**`production_annuel_petrole_region.csv`**
- Type de la colonne Date non détecté automatiquement
- Présence d'une colonne de totaux

> **Remarque globale :** Les périodes couvertes varient d'un fichier à l'autre, ce qui nécessitera ultérieurement des ajustements lors de la phase de modélisation. Par ailleurs, la colonne commune entre les fichiers est la colonne des dates.

### 2.4 Premières observations métier

**Observation 1 — Diminution progressive des exportations annuelles de pétrole brut**

Les données révèlent une tendance baissière continue des exportations tunisiennes de pétrole brut sur l'ensemble de la période analysée. Cette évolution s'explique par l'épuisement progressif des réserves nationales : la production tunisienne culmine à 118 000 barils/jour en 1980, puis s'établit à 63 000 barils/jour en 2015. Mécaniquement, la baisse de la production nationale réduit les volumes disponibles à l'exportation.

**Observation 2 — Anomalies des importations annuelles de pétrole brut : 2010/2011 et 2019**

Deux ruptures nettes sont observées dans les données d'importation de pétrole brut. La première, autour de 2010-2011, coïncide avec la Révolution tunisienne et l'instabilité en Libye, principal fournisseur de brut de la STIR avant cette date. La seconde, en 2019, s'explique directement par l'arrêt de l'activité de raffinage : la production de la STIR dans le gouvernorat de Bizerte a été suspendue en raison de mouvements sociaux du personnel, sachant que la STIR est l'unique entreprise tunisienne chargée de l'importation et du raffinage du pétrole brut.

**Observation 3 — Importations quasi nulles de lampant et de white spirit**

Les données enregistrent des volumes d'importation très faibles, voire nuls, pour ces deux produits sur l'ensemble de la période. La STIR ayant pour objet le raffinage du pétrole brut pour la production de carburants destinés à satisfaire les besoins du marché intérieur, la Tunisie n'a pas besoin d'importer le lampant et le white spirit, qui sont entièrement produits localement à partir du brut raffiné.

**Observation 4 — Diminution progressive de la production annuelle par champ**

La production nationale agrégée affiche un déclin structurel confirmé par les sources officielles. Selon le Ministère de l'Industrie, de l'Énergie et des Mines, la production de pétrole brut en 2019 a baissé de 9% en raison de la poursuite du déclin naturel des principaux champs, en l'absence de nouvelles découvertes compensatrices.

**Observation 5 — Champs sans production de pétrole brut dans le dataset**

Plusieurs champs présents dans le dataset n'enregistrent aucune valeur de production pétrolière. Trois explications principales se dégagent :

- La **nature gazière** de certains gisements : Miskar et Hasdrubal sont exclusivement gaziers
- La **mise en production récente** de certains champs : Nawara n'a démarré qu'en 2020, avec plusieurs années de retard
- L'**épuisement des réserves** : le sous-investissement dans la prospection et le désengagement progressif des investisseurs étrangers ont conduit à l'abandon de champs marginaux devenus économiquement non rentables

---

## 3. Préparation des Données

Cette phase a pour objectif de transformer les données brutes identifiées en Phase 2 en un ensemble structuré, cohérent et exploitable dans Power BI. Les opérations de préparation ont été réalisées via Power Query, en veillant à préserver la traçabilité des transformations effectuées. Les fichiers originaux sont conservés dans leur état brut dans le dossier `data/raw/`, tandis que les versions traitées sont versionnées dans `data/processed/`.

### 3.1 Opérations communes

Les transformations suivantes ont été appliquées à l'ensemble des fichiers :

- Correction des types de données avec paramètres régionaux décimal anglais (États-Unis)
- Renommage des colonnes selon une nomenclature claire et cohérente
- Suppression des lignes entièrement vides
- Suppression des lignes et colonnes de totaux
- Arrondi des valeurs numériques à deux décimales pour une meilleure lisibilité dans les visuels
- Remplacement des valeurs nulles par zéro, dans la mesure où une valeur nulle traduit vraisemblablement une absence d'activité (non-importation, non-exportation, non-production)

### 3.2 Opérations spécifiques

**`export_annuel_petrole_brute.csv`**
- Suppression des dernières lignes vides en fin de fichier

**`import_annuel_produits_petroliers.csv`**
- Quatre valeurs négatives ont été identifiées, physiquement impossibles dans le contexte d'un tableau d'importations. Elles ont été traitées comme suit :

| Produit | Date | Valeur aberrante | Traitement | Valeur de remplacement |
|---------|------|-----------------|------------|----------------------|
| Essences | Fév 2013 | -16.0 kt | Moyenne des mois valides de 2013 | **45.33 kt** |
| Petcoke | Sep 2011 | -21.68 kt | Moyenne des mois valides de 2011 | **43.67 kt** |
| GPL | Juin 2006 | -2.75 kt | Moyenne des mois valides de 2006 | **31.29 kt** |
| Jet | Mars 2021 | -0.027 kt | Remplacée par 0 (valeur négligeable) | **0 kt** |

- Suppression des dernières lignes vides en fin de fichier

---
