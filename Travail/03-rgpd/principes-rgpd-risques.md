# Grands principes RGPD et traduction en risques — solution en production

## Objectif

Pour chaque grand principe de l'article 5 du RGPD (et les droits associés), ce document identifie le risque concret qu'il représente pour la solution Fashion-Insta, sur la base de l'inventaire des données personnelles (`inventaire-donnees-personnelles.md`). C'est le contenu attendu de la première slide demandée par Alicia (`../../Projet/03-Mission/Apercu.md`).

## Principes et risques associés

| Principe (Art. 5 RGPD) | Définition courte | Risque dans le projet | Donnée/brique concernée |
|---|---|---|---|
| Licéité, loyauté, transparence | Toute collecte doit avoir une base légale claire et être compréhensible pour la personne concernée | Si des contenus de blogs ou d'influenceurs sont ingérés pour calculer un score de tendance, leur source et leur droit d'usage doivent être tracés ; par défaut, seule la référence choisie par l'utilisateur est conservée | Références de tendances (SQL) ; le cas échéant, signal de tendance (Data Lake, Azure AI Language) |
| Limitation des finalités | Une donnée collectée pour un usage ne peut être réutilisée pour un autre usage sans base légale complémentaire | Les photos de garde-robe ou les textes de tendance pourraient être réutilisés pour entraîner un modèle tiers (LLM) sans que l'utilisateur ou l'auteur l'ait consenti | Photos (Blob Storage), contenus de tendances |
| Minimisation des données | Ne collecter que ce qui est strictement nécessaire à la finalité | Une photo de garde-robe peut capturer un visage, un tiers ou un intérieur, données non nécessaires à la recommandation stylistique ; les événements de conversion ne doivent pas être conservés au-delà des données utiles à la mesure définie | Photos de garde-robe, rendus de virtual try-on, événements de conversion |
| Exactitude | Les données doivent être tenues à jour et corrigibles | Un style ou une marque préférée obsolète fausse la recommandation sans que l'utilisateur puisse facilement la corriger (droit de rectification manuel à ce stade) | Styles/marques déclarés (SQL) |
| Limitation de la conservation | Une donnée ne doit être conservée que le temps nécessaire à la finalité | Les durées de rétention des photos, rendus et logs ne sont encore que des hypothèses de chiffrage, pas une politique validée (`../02-solution-production/couts.md`, `../02-solution-production/limites-risques.md`) | Photos, rendus, historique, logs |
| Intégrité et confidentialité (sécurité) | Les données doivent être protégées contre l'accès non autorisé, la perte ou l'altération | Des photos d'utilisateurs mal sécurisées (accès trop large, absence de chiffrement) exposent la solution à une fuite de données personnelles à fort impact potentiel | Toutes les briques de stockage |
| Responsabilité (accountability) | Le responsable de traitement doit être en mesure de démontrer sa conformité | Aucune AIPD (analyse d'impact) ni registre des traitements n'a encore été formalisé pour ce projet, alors que le profilage à partir d'images le justifie probablement | Ensemble du traitement |

## Droits des personnes concernées (Art. 12 à 22)

Le traitement manuel actuel des demandes d'accès, de rectification, de suppression et d'opposition (`../02-solution-production/limites-risques.md`, "Données personnelles et RGPD") est un risque en soi : au-delà du MVP, le volume d'utilisateurs visé (~400 000) rend ce traitement manuel intenable sans délai de réponse maîtrisé — le RGPD impose une réponse dans un délai d'un mois.

## Risque spécifique soulevé par le DPO : usage des données pour l'entraînement de LLM

Le DPO attend une garantie explicite que les données des utilisateurs ne servent pas à entraîner un modèle de langage tiers. C'est un risque de **détournement de finalité** (si les données quittent le périmètre contractuel Fashion-Insta/Azure) combiné à un risque de **perte de maîtrise du sous-traitant** (si un service tiers grand public est utilisé sans garantie contractuelle). Ce point est traité spécifiquement dans `garantie-llm-apis-publiques.md`.

## Risques complémentaires identifiés en recherche

Deux risques ne ressortaient pas directement de l'`Apercu.md` mais apparaissent en creusant le sujet pour le COMEX :

- **Base légale du dataset du PoC** : le dataset DeepFashion utilisé pour simuler la garde-robe est publié sous licence recherche non commerciale, sans consentement RGPD documenté des personnes photographiées. La compatibilité de son usage dans un PoC Fashion-Insta doit être validée par le juridique/DPO avant utilisation ; il ne doit pas être réutilisé en production. Détail dans `garantie-llm-apis-publiques.md`.
- **Transparence sur le contenu généré (AI Act, art. 50, en vigueur depuis le 2 août 2026)** : le rendu du virtual try-on est un contenu manipulé ressemblant à une personne réelle (deepfake au sens du règlement), soumis à une obligation de marquage si l'utilisateur le repartage à des tiers. C'est un risque de conformité complémentaire au RGPD, pas couvert par les principes de l'article 5. Détail dans `garantie-llm-apis-publiques.md`.

## Vers les mesures de protection

Chaque risque listé ci-dessus a sa mesure correspondante dans `mesures-protection.md`, qui constitue la seconde slide attendue par Alicia.
