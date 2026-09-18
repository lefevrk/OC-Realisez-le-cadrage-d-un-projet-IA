# Critère de succès du PoC

## Critère principal (formulation métier)

Le PoC est un succès si, en moyenne sur l'ensemble des profils testés :

- **au moins 3 des 5 articles recommandés sont jugés pertinents** (note ≥ 1 sur l'échelle 0-2 définie dans `probleme-ml.md`) ;
- **au moins 1 des 5 est jugé très pertinent** (note 2) ;
- **le meilleur article est classé en position 1 ou 2** dans au moins la moitié des profils.

Autrement dit : un utilisateur qui consulte ses recommandations doit y retrouver, la plupart du temps, plus de la moitié d'articles dans son style, avec au moins un vrai coup de cœur bien placé.

## Traduction technique (usage interne)

**NDCG@5 ≥ 0,7**, calculé sur le pool de candidats jugés par profil (pas seulement les 5 articles retournés — voir le protocole de vérité terrain dans `probleme-ml.md`). Concrètement, un NDCG@5 de 0,7 correspond au critère métier ci-dessus : 3 articles pertinents dont 1 très pertinent bien classé donnent déjà un NDCG@5 proche de ce seuil, et le dépassent dès que le classement est un peu meilleur que le minimum requis.

Ce n'est pas une reformulation exacte du critère métier ci-dessus (les deux ne se déduisent pas algébriquement l'un de l'autre) : ce sont deux vérifications complémentaires calibrées sur le même niveau d'exigence, calculées à partir des mêmes jugements experts, pour les deux approches comparées dans `probleme-ml.md`.

## Critère de comparaison entre approches

Le PoC ne se limite pas à un seuil absolu : il doit aussi montrer que l'approche B (matching structuré par similarité vectorielle) surpasse l'approche A (correspondance par tags, baseline) sur ces mêmes indicateurs. Sans écart net, la valeur ajoutée du scoring par similarité vectorielle par rapport à une simple recherche par tags resterait à démontrer.

## Ce que ce critère ne valide pas

Il mesure la pertinence perçue par des experts métier sur un échantillon restreint (10 à 15 profils simulés) et un catalogue Fashion-Insta extrait pour l'occasion — pas la satisfaction des utilisateurs finaux réels, ni la performance à l'échelle des 400 000 utilisateurs visés. Ces validations relèvent des phases suivantes.
