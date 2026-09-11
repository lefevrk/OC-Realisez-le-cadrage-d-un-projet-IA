# Dataset candidat pour le PoC

## Dataset retenu : DeepFashion — Consumer-to-shop Clothes Retrieval

Sous-ensemble du dataset DeepFashion (CUHK MMLab) qui associe des photos de personnes portant un vêtement ("consumer", prises en conditions réelles) à des photos catalogue du même article ("shop", prises en conditions contrôlées).

- 194 165 photos "consumer" (portées), sur 239 557 images et 33 881 items au total.
- 303 attributs annotés (longueur, coupe, motif, etc.), mais aucune catégorie vestimentaire fine ni style — seulement 3 types grossiers (haut / bas / corps entier).
- Accès : téléchargement direct (Google Drive), licence recherche non commerciale.

Ce dataset est le plus proche structurellement du scénario du PoC : une photo de vêtement porté mise en correspondance avec un article catalogue.

**Usage retenu pour le PoC** : n'utiliser que le côté "consumer" (photo portée) pour simuler la garde-robe photographiée de l'utilisateur. Le côté "shop" de DeepFashion n'est pas nécessaire : le document métier confirme que Fashion-Insta dispose déjà, pour chaque référence produit, de plusieurs images (dont certaines "portées"), d'une description, d'un prix et de tags de style — avec un coût d'extraction déjà connu (3 jours-homme Data Engineer). Le PoC peut donc être évalué directement contre le vrai catalogue Fashion-Insta une fois cette extraction faite, plutôt que contre un catalogue de substitution.

## Limites

- **Licence non commerciale** : utilisable pour le PoC, mais une source réelle ou re-licenciée devra être trouvée pour la version produite.
- **Aucune correspondance native avec les 5 styles Fashion-Insta** (essentiel urbain, casual chic, professionnel moderne, streetwear, bohème féminin), ni avec les catégories produit du catalogue (pantalons/jeans, chemises/blouses, etc.) : le dataset ne fournit ni catégorie fine ni style, seulement 303 attributs descriptifs bruts et 3 types grossiers. Les experts métier doivent annoter directement les photos retenues avec les catégories et styles Fashion-Insta qu'ils connaissent déjà, sans avoir à apprendre la taxonomie propre à DeepFashion — celle-ci n'est utile qu'en interne pour présélectionner les photos à soumettre aux experts, pas comme référentiel de correspondance.

> Estimation du temps métier associé : un atelier de cadrage pour fixer les critères de reconnaissance des 5 styles Fashion-Insta sur photo (environ une demi-journée avec 1 à 2 experts), puis l'annotation directe du style dominant sur les photos des profils retenus (10 à 15 profils, de l'ordre de 5 à 8 photos chacun, soit 50 à 120 images). Au total, de l'ordre de **1 à 1,5 jour-homme métier**, à affiner à l'étape de dimensionnement global.

- **Dépendance à l'extraction du catalogue Fashion-Insta.** Utiliser le vrai catalogue plutôt qu'un catalogue de substitution rend l'évaluation directement pertinente pour le métier, mais conditionne le démarrage des tests à la disponibilité des données catalogue extraites (déjà identifiée comme prérequis dans `perimetre-poc.md`).
- **Photos datées** (dataset publié en 2016) : les styles et tendances vestimentaires représentés peuvent ne plus correspondre aux tendances actuelles.
- **Qualité variable des photos "consumer"** (pose, éclairage, occlusion partielle du vêtement), ce qui peut nécessiter un tri manuel pour constituer des profils de test propres.
- **Ce n'est pas une donnée d'utilisateur Fashion-Insta réel** : c'est un proxy du comportement utilisateur final, pas une preuve que les utilisateurs réels de l'application produiront des photos comparables (angle, cadrage, qualité).

## Pistes de repli

Si l'accès ou la qualité s'avèrent insuffisants : Street2Shop ("Where to Buy It", UNC/ICCV 2015) offre le même principe de paires photo portée / photo catalogue sur un volume plus réduit (20 357 photos de rue), donc plus rapide à manipuler pour un premier test.
