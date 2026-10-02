# Inventaire des données personnelles — solution en production

## Objectif

Alicia et le DPO alertent spécifiquement sur l'analyse d'image, mais le System Design (`../02-solution-production/system-design.md`) fait transiter plusieurs autres catégories de données personnelles. Cet inventaire les recense toutes : il conditionne la qualification des risques (`principes-rgpd-risques.md`) et les mesures de protection (`mesures-protection.md`).

## Données traitées

| Donnée | Nature | Brique (système design) | Finalité | Base légale la plus probable | Sensibilité particulière |
|---|---|---|---|---|---|
| Photos de garde-robe | Image de la personne, pouvant inclure visage, logement, tiers présents à l'image | Azure Blob Storage | Calculer l'embedding visuel et recommander des articles | Exécution du contrat (fonctionnalité demandée par l'utilisateur) | Image d'une personne ; risque de sur-collecte (visage, tiers, arrière-plan) |
| Rendus du virtual try-on | Image dérivée, combinant la photo de l'utilisateur et l'article recommandé | Azure Blob Storage | Afficher le rendu à l'utilisateur | Exécution du contrat | Même sensibilité que la photo source, génère une nouvelle image personnelle |
| Styles et marques préférés déclarés | Préférence déclarée explicitement | Azure SQL Database | Filtrer/trier la recommandation préférences & tendances | Consentement (déclaration active) ou exécution du contrat | Révèle des goûts, exploitable à des fins de profilage commercial |
| Avis et notes sur les recommandations | Contenu généré par l'utilisateur | Azure SQL Database | Mesurer la pertinence, alimenter le feedback | Exécution du contrat / intérêt légitime | Peu sensible en soi, mais peut révéler des opinions |
| Historique de recommandations et feedback | Donnée comportementale | Azure SQL Database | Mesure d'impact, boucle de feedback | Intérêt légitime | Profilage et traçage du comportement dans le temps |
| Événements de conversion (impression, clic, panier, achat) | Donnée comportementale rattachable à un compte ou à un identifiant pseudonyme | Azure Data Factory, Data Lake Storage et Azure SQL Database | Mesurer conversion et valeur business, améliorer la recommandation | Intérêt légitime, à mettre en balance et à rendre transparent | Peut révéler habitudes d'achat et préférences ; profilage commercial |
| Identifiant utilisateur (`user_id`) | Identifiant de compte, référencé par toutes les briques Data & IA | Gateway applicatif, SQL, Blob | Rattacher chaque donnée au bon utilisateur | Exécution du contrat | Clé de ré-identification transverse à toutes les autres données du tableau |
| Données d'authentification et de session (jeton, identifiant technique, claims minimales) | Données de compte transmises au gateway ; la gestion du compte reste hors périmètre du schéma | Application mobile, Gateway applicatif, Microsoft Entra ID | Authentifier la requête et appliquer les droits d'accès | Exécution du contrat et sécurité du service | Compromission de session ou élévation de privilèges |
| Logs techniques (IP, device, timestamps) | Donnée technique indirectement identifiante | Azure Monitor | Sécurité, disponibilité, debug | Intérêt légitime | Ré-identification possible par croisement avec d'autres logs |
| Messages d'orchestration et statuts de virtual try-on | Identifiant pseudonyme, référence article, statut et, selon l'implémentation, lien temporaire vers la photo ou le rendu | Azure Service Bus, Azure SQL Database, Blob Storage | Exécuter et restituer l'essayage virtuel | Exécution du contrat | Le contenu du message ne doit pas embarquer la photo ; limiter sa durée de vie et journaliser sans URL signée |
| Références de tendances choisies par l'utilisateur (nom/URL de blogs, sites ou comptes d'influenceurs) ; contenus tiers uniquement si une source autorisée est raccordée | Préférence utilisateur ; le contenu éventuel reste une donnée de tiers, distincte des données utilisateur | Azure SQL Database ; Azure Data Lake Storage via Azure Data Factory seulement en cas de source autorisée | Personnaliser la recommandation ; calculer un score de tendance uniquement si nécessaire | Exécution du contrat pour la préférence déclarée ; licence, partenariat ou conditions d'une API officielle pour tout contenu tiers ingéré | Ne pas collecter le contenu tiers par défaut ; tracer la source et son droit d'usage — voir `garantie-llm-apis-publiques.md` |

## Qualification des photos de garde-robe

Une photo de personne est une donnée personnelle à **fort impact potentiel** : elle peut montrer le visage, le domicile, des tiers, ou révéler indirectement des informations intimes. Elle n'entre toutefois dans la catégorie juridique des **données sensibles** au sens de l'article 9 du RGPD que si elle est traitée à des fins d'**identification biométrique unique** (reconnaissance faciale, par exemple). Ce n'est pas l'usage prévu ici : le traitement porte sur les vêtements, pas sur l'identité de la personne.

Cette distinction ne diminue pas l'exigence de protection. Au contraire, le cas nominal doit réduire la photo au vêtement utile : cadrage guidé, détection et floutage des visages, puis suppression ou neutralisation de l'arrière-plan avant le stockage. Sans ces contrôles, la donnée collectée dépasse la finalité de recommandation stylistique. C'est un risque de **minimisation**, traité dans `principes-rgpd-risques.md` et `mesures-protection.md`.

## Profilage automatisé

La combinaison recommandation garde-robe + recommandation préférences/tendances constitue un **profilage** au sens de l'article 4.4 du RGPD (évaluation des goûts et habitudes pour personnaliser une offre commerciale). Il ne produit pas de décision avec effet juridique significatif pour l'utilisateur (pas de refus de contrat, pas d'exclusion) : le régime strict de l'article 22 ne s'applique donc pas en l'état, mais l'obligation de **transparence** sur l'existence de ce profilage reste due.

## Tiers concernés par la collecte de tendances

Le signal de tendance (`../02-solution-production/system-design.md`, brique "Signal de tendance") ne collecte pas de contenu tiers par défaut : l'utilisateur référence seulement ses sources de tendance. Si Fashion-Insta choisit d'ingérer leurs textes pour calculer un score de tendance, elle le fait uniquement depuis une source autorisée (licence, partenariat ou API officielle), comme détaillé dans `garantie-llm-apis-publiques.md`.
