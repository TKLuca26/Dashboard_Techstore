# TECHSTORE Sarl — Tableau de bord des ventes (Power BI)

Rapport Power BI d'une seule page qui transforme un fichier Excel de ventes brut en outil de pilotage : chiffre d'affaires, marge, performance des produits, des régions, des modes de paiement et des commerciaux.

Le projet a été construit avec **Claude** connecté à **Power BI Desktop** (serveur MCP Power BI Modeling et compétences Microsoft Fabric pour Claude), au format **PBIP** : le modèle et le rapport sont stockés en fichiers texte (TMDL et PBIR), donc versionnables sur GitHub.

![Aperçu du tableau de bord](docs/apercu-dashboard.png)

---

## Sommaire

- [Contexte et objectif](#contexte-et-objectif)
- [Données](#données)
- [Indicateurs clés (KPI)](#indicateurs-clés-kpi)
- [Contenu du rapport](#contenu-du-rapport)
- [Principaux enseignements](#principaux-enseignements)
- [Modèle de données](#modèle-de-données)
- [Difficultés rencontrées](#difficultés-rencontrées)
- [Limites et pistes d'amélioration](#limites-et-pistes-damélioration)
- [Ouvrir le projet](#ouvrir-le-projet)
- [Structure du dépôt](#structure-du-dépôt)

---

## Contexte et objectif

TECHSTORE Sarl vend du matériel high-tech (ordinateurs, smartphones, tablettes, accessoires) dans quatre régions, via huit commerciaux et quatre modes de paiement (espèces, carte bancaire, Mobile Money, Orange Money).

Objectif : répondre en un coup d'œil à cinq questions de gestion.

1. Combien vendons-nous, et avec quelle marge ?
2. Comment les ventes évoluent-elles d'un mois à l'autre ?
3. Quels produits et quelles catégories portent le chiffre d'affaires, et lesquels portent la marge ?
4. Où (régions) et comment (modes de paiement) nos clients achètent-ils ?
5. Quels commerciaux performent le mieux ?

## Données

| Élément | Détail |
|---|---|
| Source | `TECHSTORE.xlsx`, feuille `TECHSTORE` |
| Volume | 513 lignes, une ligne par commande (513 commandes distinctes) |
| Période | du 31/12/2023 au 20/05/2025 (l'essentiel entre janvier et juin 2024) |
| Monnaie | FCFA (XAF) |
| Colonnes | Date, ID Commande, Produit, Catégorie, Mode de paiement, Prix d'achat (unitaire), Quantité, Prix unitaire de vente, Région géographique, Commercial |

## Indicateurs clés (KPI)

Toutes les mesures sont regroupées dans une table dédiée `Mesures`, classées par dossier.

| Mesure | Formule (DAX simplifié) | Rôle |
|---|---|---|
| **Chiffre d'affaires** | `SUMX(TECHSTORE, Quantité × Prix unitaire)` | Volume de ventes en FCFA |
| **Coût d'achat** | `SUMX(TECHSTORE, Quantité × Prix d'achat)` | Coût des marchandises vendues |
| **Marge brute** | `Chiffre d'affaires − Coût d'achat` | Ce que l'activité rapporte réellement |
| **Taux de marge** | `DIVIDE(Marge brute, Chiffre d'affaires)` | Rentabilité en % du CA |
| **Commandes** | `DISTINCTCOUNT(ID Commande)` | Nombre de commandes |
| **Unités vendues** | `SUM(Quantité)` | Volume physique |
| **Panier moyen** | `DIVIDE(Chiffre d'affaires, Commandes)` | Valeur moyenne d'une commande |
| **Part du CA** | `DIVIDE(CA, CALCULATE(CA, ALLSELECTED()))` | Poids d'un élément dans la sélection (infobulles) |
| **Période analysée** | dates min. et max. de la sélection | Libellé dynamique de l'en-tête |

Valeurs sur l'ensemble des données :

| Chiffre d'affaires | Marge brute | Taux de marge | Commandes | Unités vendues | Panier moyen |
|---|---|---|---|---|---|
| 811 537 021 FCFA | 28 697 600 FCFA | 3,5 % | 513 | 1 888 | 1 581 944 FCFA |

## Contenu du rapport

Une page 1920 × 1080, entièrement interactive : chaque filtre et chaque clic sur un graphique filtre le reste de la page.

| Zone | Visuel | Question traitée |
|---|---|---|
| En-tête | Titre et période analysée (dynamique) | Sur quelle période porte la lecture ? |
| Filtres | 5 listes déroulantes : Mois, Région, Catégorie, Commercial, Mode de paiement | Analyser un périmètre précis |
| Bandeau KPI | 6 indicateurs clés | Situation globale en une ligne |
| Tendance | Colonnes (CA) et courbe (taux de marge) par mois | Saisonnalité et évolution de la rentabilité |
| Catégories | Barres horizontales triées | Qui porte le CA ? |
| Produits | Barres horizontales triées (12 produits) | Produits phares et produits marginaux |
| Régions | Histogramme | Répartition géographique |
| Paiement | Anneau en % du CA | Comment les clients paient |
| Commerciaux | Tableau CA, marge, taux de marge | Classement de l'équipe de vente |

Les infobulles des graphiques par catégorie, produit et région affichent aussi la marge brute, le taux de marge et la part du CA.

## Principaux enseignements

**1. Le CA est porté par les produits les moins rentables.**
Ordinateurs : 33,5 % du CA, mais 1,4 % de marge. Le MacBook Pro représente à lui seul 20,9 % du CA avec 1,0 % de marge.
À l'inverse, les accessoires ne font que 12,7 % du CA mais dégagent plus de 10 % de marge.

**2. La marge est fixe par unité, pas proportionnelle au prix.**
Sur toutes les lignes, prix de vente − prix d'achat = 15 200 FCFA par unité, quel que soit le produit. Résultat : plus un produit est cher, plus son taux de marge est faible. C'est le point le plus important à faire valider par la direction (politique de prix ou artefact du fichier).

**3. Les espèces dominent.**
Espèces : 51,8 % du CA ; carte bancaire : 35,0 % ; Mobile Money et Orange Money : 13,3 % à eux deux.

**4. Une équipe commerciale homogène.**
Les huit commerciaux réalisent entre 10,0 % (Emma) et 15,0 % (Charlie) du CA. David se distingue par le meilleur taux de marge (6,4 %, contre environ 3 % pour les autres), ce qui suggère un mix de produits plus orienté accessoires.

**5. Des régions équilibrées.**
Le Nord mène avec 29,3 % du CA ; les trois autres régions se situent entre 23 % et 25 %. L'Ouest a le meilleur taux de marge (4,6 %).

**6. Un pic en janvier 2024, un creux en juin.**
Janvier 2024 : 140,3 M FCFA ; juin 2024 : 90,3 M FCFA, le plus bas du premier semestre.

## Modèle de données

```
Calendrier (1) ──── (*) TECHSTORE        Mesures (table de mesures, sans relation)
   Date                 Date
   Année                ID Commande, Produit, Catégorie
   Mois (trié)          Mode de paiement, Région, Commercial
                        Prix d'achat, Prix unitaire, Quantité
```

- **TECHSTORE** : table de faits, chargée et nettoyée avec Power Query.
- **Calendrier** : table de dates calculée (DAX), marquée comme table de dates. La colonne `Mois` (« janv. 2024 ») est triée par un index caché `Année × 100 + mois`, pour un ordre chronologique et non alphabétique.
- **Mesures** : table vide qui sert uniquement à ranger les mesures.

## Difficultés rencontrées

### Qualité des données

| Problème détecté | Traitement |
|---|---|
| Fichier Excel non tabulaire (lignes vides, en-têtes décalés, colonnes vides) | Power Query : transpositions, suppression des lignes et colonnes vides, promotion des en-têtes |
| Prix d'achat saisis avec une lettre devant (`C43705`, `B1471705`) | Remplacés par la valeur numérique dans Power Query |
| Catégorie mal orthographiée : `Acessoires1` | Remplacée par `Accessoires` |
| Mode de paiement tronqué : ` bancaire` | Remplacé par `Carte bancaire` |
| Colonnes inutiles ou redondantes (`Total Vente`, `jour`, `Mois` en texte) | Supprimées ; le CA est recalculé par mesure, les mois viennent du calendrier |
| Ambiguïté sur le prix d'achat (unitaire ou total ?) | Comparaison ligne à ligne avec le prix de vente : il s'agit d'un prix **unitaire** |

### Anomalies signalées mais non corrigées

Ces points demandent une décision métier : les corriger à l'aveugle fausserait l'analyse.

- **Prix d'achat négatif** : une vente de « Souris sans fil » (200 unités) a un prix d'achat de −9 800 FCFA. Elle ajoute environ 3 M FCFA de marge fictive : hors cette ligne, la marge totale serait d'environ 25,7 M FCFA, soit 3,2 % au lieu de 3,5 %.
- **Dates hors période** : 7 ventes au 31/12/2023 et 13 ventes en mai 2025 (100,2 M FCFA), alors que toutes les autres vont de janvier à juin 2024. Probables erreurs de saisie de l'année.
- **Marge unitaire constante** (15 200 FCFA) : voir les enseignements ci-dessus.

### Difficultés techniques

- **Connexion** : les serveurs Fabric en ligne (FabricIQ, point de terminaison SQL) n'ont pas pu se connecter, faute de mode d'authentification compatible. Le projet a donc été réalisé en local, via Power BI Desktop.
- **Axe des mois** : avec des mois manquants (juillet 2024 à avril 2025), un axe de dates continu aurait affiché un grand vide. Un axe par catégories trié chronologiquement a été retenu.
- **Actualisation des données** : après la modification du modèle depuis Claude, le chargement des données a dépassé le délai de l'outil et la table est restée vide. L'actualisation a été faite manuellement dans Power BI Desktop.
- **Deux fenêtres Desktop** : le même rapport ouvert dans deux instances de Power BI Desktop bloquait la vérification automatique ; il a fallu en fermer une.
- **Mise en forme** : quelques défauts ne se sont vus qu'au rendu (étiquettes « 0M% » sur la courbe de taux, texte blanc sur fond blanc, colonne coupée dans le tableau) et ont été corrigés après capture d'écran.

## Limites et pistes d'amélioration

- Faire valider les anomalies ci-dessus avec la direction commerciale, puis les corriger à la source.
- Ajouter des objectifs (budget par mois, par commercial) pour comparer le réalisé au prévu.
- Ajouter une page de détail (drill-through) par commercial ou par produit.
- Remplacer le chemin local du fichier Excel par un paramètre, ou par une source partagée (SharePoint, OneDrive).

## Ouvrir le projet

**Prérequis** : Power BI Desktop (version récente, prenant en charge le format PBIP et TMDL).

1. Cloner ou télécharger le dépôt.
2. Ouvrir `Dashboard_TECHSTORE.pbip` dans Power BI Desktop.
3. **Mettre à jour le chemin du fichier Excel** : la requête pointe vers un chemin local (`C:\Users\...\TECHSTORE.xlsx`). Dans Power Query, ouvrir la requête `TECHSTORE` et modifier l'étape `Source`.
4. Cliquer sur **Actualiser**.

## Structure du dépôt

```
Dashboard_TECHSTORE.pbip                 Fichier projet à ouvrir dans Power BI Desktop
TECHSTORE.xlsx                           Données sources
Dashboard_TECHSTORE.SemanticModel/       Modèle de données (TMDL)
  definition/tables/TECHSTORE.tmdl         Table de faits + requête Power Query
  definition/tables/Calendrier.tmdl        Table de dates
  definition/tables/Mesures.tmdl           Les 9 mesures DAX
  definition/relationships.tmdl            Relations
Dashboard_TECHSTORE.Report/              Rapport (PBIR) : page, visuels, mise en forme
docs/apercu-dashboard.png                Capture du tableau de bord
```

## Auteur

**Luc Arnauld TIENOUE KAMOLOGNE** — Formateur et consultant IA et Data (Claude, Power BI), Douala, Cameroun.
