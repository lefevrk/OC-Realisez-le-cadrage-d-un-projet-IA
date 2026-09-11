# Traduction du besoin métier en problème ML

## Objectif ML du PoC

À partir de plusieurs photos montrant un utilisateur portant les vêtements de sa garde-robe, construire un profil stylistique global puis classer les articles du catalogue Fashion-Insta selon leur affinité avec ce profil.

L'objectif n'est ni de compléter une tenue précise, ni de reproduire une recherche par mot-clé. Il s'agit de recommander des articles du **même style vestimentaire** que celui exprimé par l'ensemble de la garde-robe photographiée.

## Traduction des données en attributs

Les entrées (photos de garde-robe) et la sortie (Top-k catalogue) sont celles déjà définies dans `perimetre-poc.md`. Ce que cette étape ajoute : la façon dont chaque photo est convertie en attributs exploitables par les deux approches ci-dessous.

Chaque photo est décrite par des attributs vestimentaires, par exemple : catégorie, couleur, style, motif, coupe ou silhouette. Les attributs extraits sur les différentes photos d'un même profil sont ensuite agrégés pour représenter son style global.

### Cohorte de test

Le protocole cible les cinq univers stylistiques Fashion-Insta (essentiel urbain, casual chic, professionnel moderne, streetwear, bohème féminin), avec des profils synthétiques composés de photos portées de personnes différentes mais cohérentes stylistiquement.

Cible initiale : 2 à 3 profils par groupe (10 à 15 profils au total), pour limiter le temps expert nécessaire à l'annotation. Ce volume pourra être augmenté (jusqu'à une dizaine par groupe) si la disponibilité métier le permet et si les premiers résultats justifient une évaluation plus large.

## Cible et vérité terrain

Il n'existe pas d'historique interne de clics, d'achats ou d'avis sur des recommandations : le produit n'est pas encore lancé. Le filtrage collaboratif est donc hors de portée du PoC.

La vérité terrain d'évaluation est constituée à partir d'un pool d'articles candidats par profil (de l'ordre de 8 à 10 : les Top-5 des deux approches comparées, avec recouvrement probable, complétés par un ou deux contre-exemples hors style), notés à l'aveugle par des experts métier :

- 0 : hors style ou non pertinent ;
- 1 : cohérent avec le style, mais peu convaincant ou générique ;
- 2 : très pertinent et représentatif du style recherché.

Avec 10 à 15 profils et un pool d'environ 8 à 10 candidats chacun, l'annotation représente de l'ordre de 100 à 150 jugements au total — un volume compatible avec un temps expert limité. Cette méthode évite par ailleurs de demander aux experts d'annoter l'ensemble du catalogue.

## Approche A - Correspondance directe par tags

### Principe

1. Extraire les attributs des photos portées.
2. Agréger ces attributs pour produire un profil de tags dominant pour l'utilisateur.
3. Filtrer les références catalogue partageant les tags ou attributs attendus.
4. Retourner un Top-5 parmi les articles filtrés selon une règle déterministe secondaire, par exemple la couverture des tags.

### Données et prérequis

- Un classifieur d'attributs appliqué aux photos portées, obtenu à partir d'un modèle préentraîné et/ou d'un dataset externe.
- Des tags catalogue suffisamment complets, cohérents et rapprochés des attributs détectés.
- Des règles métier explicites de correspondance entre les tags issus des photos et ceux du catalogue.

### Évaluation

- NDCG@5 sur les notes métier 0-2, pour mesurer la pertinence et l'ordre des cinq premiers articles.
- Analyse par univers stylistique et comparaison avec l'approche B.
- Contrôle de cohérence : part des articles recommandés dont les tags sont compatibles avec le profil attendu.

### Avantages et limites

| Avantages | Limites |
|---|---|
| Très rapide à mettre en oeuvre ; facilement explicable aux métiers. | Logique principalement binaire ; peu de capacité à distinguer deux articles pourtant tous deux éligibles. |
| Exploite les tags déjà présents dans le catalogue. | Dépend fortement de l'exhaustivité et de l'homogénéité des tags. |
| Sert de baseline simple pour mesurer la valeur ajoutée d'une approche plus nuancée. | Le Top-5 peut être arbitraire lorsque de nombreux articles partagent les mêmes tags. |

## Approche B - Matching structuré par similarité vectorielle

### Principe

1. Extraire les mêmes attributs des photos portées.
2. Agréger leurs fréquences ou probabilités pour créer un vecteur de profil stylistique utilisateur.
3. Représenter chaque article catalogue dans le même espace d'attributs.
4. Calculer la similarité (recherche vectorielle simple, ex. cosinus ou distance euclidienne) entre le vecteur profil et chaque vecteur article catalogue, puis retourner les cinq scores les plus élevés.

Contrairement à l'approche A, le classement n'est pas binaire (tag présent/absent) : la similarité vectorielle produit une gradation d'affinité entre le profil et chaque article.

### Données et prérequis

- Les mêmes données image et attributs que l'approche A.
- Une normalisation des valeurs (catégories, couleurs, styles, motifs et coupes) entre photos utilisateur et catalogue.
- Une méthode de similarité adaptée aux variables mixtes (catégorielles et continues).

### Évaluation

- Même protocole de vérité terrain que l'approche A, afin de rendre la comparaison équitable.
- NDCG@5 moyen sur les profils de validation, avec lecture par groupe stylistique.
- Score de pertinence moyen des notes métier dans le Top-5, exprimé ensuite en formulation métier pour le COMEX.

### Avantages et limites

| Avantages | Limites |
|---|---|
| Classe les articles avec une gradation d'affinité plutôt qu'un filtre binaire. | La fonction de similarité doit être justifiée et testée. |
| Reste interprétable : chaque recommandation peut être expliquée par les attributs partagés. | Ne capte que les attributs définis ; les nuances visuelles non étiquetées restent invisibles. |
| Réutilise les données catalogue existantes tout en apportant un moteur de recommandation plus nuancé. | Dépend toujours de la qualité de classification et de la normalisation des attributs. |

## Recommandation pour le PoC

Prioriser l'approche B, le matching structuré par similarité vectorielle. Elle répond mieux au besoin de recommandation personnalisée : elle construit un profil global à partir de plusieurs photos et produit un classement explicable des articles par affinité.

L'approche A doit néanmoins être implémentée comme baseline de comparaison. Elle permettra de démontrer, chiffres à l'appui, que le classement par similarité vectorielle apporte une valeur supérieure à une simple correspondance de tags.

## Évolution hors PoC

Une phase ultérieure pourrait étudier des représentations visuelles apprises directement à partir des images (embeddings), capables de capturer des nuances stylistiques au-delà des attributs explicitement classifiés.
