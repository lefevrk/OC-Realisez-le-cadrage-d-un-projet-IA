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

## Périmètre du PoC

Le PoC se concentre sur la recommandation stylistique : analyser des photos de vêtements déjà possédés puis proposer un petit nombre d'articles d'un catalogue ayant une affinité de style.

Il ne vise pas à réaliser une application mobile complète. Les fonctionnalités telles que l'essayage virtuel, la personnalisation générative, la recommandation à partir des tendances ou des avis, et l'intégration e-commerce sont hors périmètre à ce stade.

## Structure du dépôt

```text
.
├── README.md                # Vue d'ensemble et conventions du dépôt
└── .gitignore               # Exclusions de versionnement
```

La structure évoluera avec les prochaines étapes. Les répertoires seront ajoutés lorsqu'ils accueilleront un contenu utile au projet.

## Feuille de route

- [ ] Formaliser le périmètre, les hypothèses et les critères de succès du PoC.
- [ ] Identifier les jeux de données et évaluer leur adéquation.
- [ ] Définir l'approche de recommandation et le protocole d'évaluation.
- [ ] Proposer le system design cible.
- [ ] Construire la timeline de livraison et le dimensionnement du projet.
- [ ] Documenter le traitement des données personnelles, les risques et les mesures associées.
- [ ] Préparer le support de restitution final.

## État actuel

Le dépôt vient d'être initialisé. La première étape consiste à stabiliser le cadrage du Poc et ses critères de succès.