# Cadrage d'un projet IA — Fashion-Insta

Ce dépôt documente le cadrage progressif d'un projet de recommandation d'articles de mode fondé sur l'analyse de photos de vêtements. Il a vocation à conserver les décisions, hypothèses, recherches et livrables produits au fil du projet.

## Objectif

L'objectif est de définir un **proof of concept (PoC)** permettant d'évaluer si des photos de la garde-robe d'un utilisateur peuvent servir à recommander des articles pertinents dans un catalogue de mode.

Le travail porte notamment sur :

- le périmètre et les critères de succès du PoC ;
- les données nécessaires, leur qualité et leurs contraintes d'usage ;
- l'approche ML et les modalités d'évaluation ;
- l'architecture cible et les choix de déploiement ;
- la planification, les compétences nécessaires et l'estimation économique ;
- la protection des données personnelles et les principaux risques.

## Contexte métier et valeur ajoutée de l'IA

Le rappel du contexte métier (chiffres clés Fashion-Insta, projet d'application mobile, enjeu du COMEX) et l'argumentaire de valeur ajoutée de l'IA (pourquoi une approche ML plutôt qu'une règle statique, impact business attendu) sont formalisés dans [`Travail/01-poc/contexte-valeur-ia.md`](Travail/01-poc/contexte-valeur-ia.md).

## Périmètre du PoC

Le PoC se concentre sur la recommandation stylistique : analyser des photos de l'utilisateur portant les vêtements de sa garde-robe, puis proposer un petit nombre d'articles du catalogue Fashion-Insta correspondant aux goûts déduits de cette garde-robe.

Il ne vise pas à réaliser une application mobile complète. Les fonctionnalités telles que l'essayage virtuel, la personnalisation générative, la recommandation à partir des tendances ou des avis, et l'intégration e-commerce sont hors périmètre à ce stade.

Le détail (décision de cadrage, données retenues, hors périmètre, hypothèses) est formalisé dans [`Travail/01-poc/perimetre-poc.md`](Travail/01-poc/perimetre-poc.md).

Le besoin est traduit en problème ML dans [`Travail/01-poc/probleme-ml.md`](Travail/01-poc/probleme-ml.md) : deux approches candidates (correspondance par tags, matching structuré par similarité vectorielle) sont comparées sur un protocole d'évaluation commun, jugé par des experts métier à l'aveugle.

Le dataset candidat pour simuler les photos de garde-robe est documenté dans [`Travail/01-poc/datasets-candidats.md`](Travail/01-poc/datasets-candidats.md) (DeepFashion Consumer-to-shop, côté "consumer" uniquement), évalué contre le vrai catalogue Fashion-Insta une fois celui-ci extrait.

Le critère de succès métier et sa traduction technique (NDCG@5 ≥ 0,7) sont fixés dans [`Travail/01-poc/critere-succes.md`](Travail/01-poc/critere-succes.md), avec un critère de comparaison explicite entre les deux approches candidates.

La durée et le dimensionnement en profils/jours-hommes du PoC (~21,5 jours-hommes, 4 à 5 semaines) sont détaillés dans [`Travail/01-poc/dimensionnement.md`](Travail/01-poc/dimensionnement.md), avec les principaux risques de chiffrage.

Les limites et risques du PoC (périmètre, donnée, méthode, exécution) sont synthétisés dans [`Travail/01-poc/limites-risques.md`](Travail/01-poc/limites-risques.md), qui renvoie vers le détail déjà formalisé dans les documents précédents plutôt que de le redupliquer. Les prochaines étapes hors périmètre seront présentées directement dans le support de restitution (.pptx).

## Structure du dépôt

```text
.
├── README.md                        # Vue d'ensemble et conventions du dépôt
├── .gitignore                       # Exclusions de versionnement
├── Projet/                          # Mission et documents fournis par l'école/Alicia
├── Sources/                         # Ressources de référence (métier, données, pricing Azure, templates)
├── Travail/
│   ├── 01-poc/
│   │   ├── perimetre-poc.md         # Cadrage du périmètre du PoC (entrées, sorties, hors périmètre, hypothèses)
│   │   ├── probleme-ml.md           # Traduction en problème ML : approches candidates et protocole d'évaluation
│   │   ├── datasets-candidats.md    # Dataset candidat pour simuler les photos de garde-robe, et ses limites
│   │   ├── critere-succes.md        # Critère de succès métier et traduction technique (NDCG@5)
│   │   ├── dimensionnement.md       # Durée, profils et jours-hommes du PoC, et risques de chiffrage
│   │   ├── limites-risques.md       # Synthèse des limites et risques du PoC (renvois, sans redétailler)
│   │   └── contexte-valeur-ia.md    # Rappel du contexte métier et valeur ajoutée de l'IA
│   ├── 02-solution-production/      # Travaux à venir : architecture et déploiement cible
│   └── 03-donnees-personnelles/     # Travaux à venir : traitement des données personnelles et risques
└── Presentation/                    # Support de restitution final
```

La structure évoluera avec les prochaines étapes. Les répertoires seront ajoutés lorsqu'ils accueilleront un contenu utile au projet.

## Feuille de route

- [x] Formaliser le périmètre et les hypothèses du PoC.
- [x] Définir l'approche de recommandation et le protocole d'évaluation.
- [x] Identifier un jeu de données candidat et évaluer son adéquation.
- [x] Définir le critère de succès métier du PoC (seuil chiffré à valider).
- [x] Estimer la durée et les profils/jours-hommes nécessaires au PoC.
- [x] Lister les limites et risques du PoC (prochaines étapes hors périmètre : dans le .pptx).
- [x] Rappeler le contexte métier et formaliser la valeur ajoutée de l'IA.
- [ ] Proposer le system design cible.
- [ ] Construire la timeline de livraison et le dimensionnement économique du projet en production.
- [ ] Documenter le traitement des données personnelles, les risques et les mesures associées.
- [ ] Préparer le support de restitution final (.pptx).

## État actuel

Le contexte métier et la valeur ajoutée de l'IA (`contexte-valeur-ia.md`), le périmètre du PoC (`perimetre-poc.md`), sa traduction en problème ML (`probleme-ml.md`, deux approches comparées), le dataset candidat (`datasets-candidats.md`), le critère de succès (`critere-succes.md`), le dimensionnement (`dimensionnement.md`, ~21,5 jours-hommes sur 4 à 5 semaines) et les limites/risques (`limites-risques.md`) sont formalisés. Prochaine étape : construire le contenu du support de restitution (.pptx).