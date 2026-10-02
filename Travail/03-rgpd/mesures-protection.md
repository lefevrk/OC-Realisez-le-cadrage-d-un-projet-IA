# Mesures de protection des données personnelles — solution en production

## Objectif

Pour chaque risque identifié dans `principes-rgpd-risques.md`, ce document propose la mesure correspondante et, conformément à la recommandation de l'`Apercu.md`, localise précisément où l'implémenter dans le System Design (`../02-solution-production/system-design.md`). C'est le contenu de la seconde slide attendue par Alicia.

## Mesures par risque

| Risque | Mesure proposée | Emplacement dans le System Design |
|---|---|---|
| Détournement de finalité (réutilisation pour entraîner un LLM tiers) | Absence de LLM/API IA grand public dans l'architecture, puis validation DPO des clauses contractuelles et de la configuration de chaque service Azure avant mise en production | Traité en détail dans `garantie-llm-apis-publiques.md` |
| Minimisation insuffisante (visage, tiers, arrière-plan sur les photos) | Cadrage guidé côté application (zone de capture limitée au vêtement) + détection/floutage des visages et des tiers + suppression ou neutralisation de l'arrière-plan avant stockage ; rejet si le vêtement n'est pas exploitable | Nouvelle étape de pré-traitement entre l'upload (Gateway) et l'écriture en **Azure Blob Storage**, avant tout calcul d'embedding |
| Collecte non maîtrisée de contenus de tendances tiers | Conserver uniquement la référence choisie par l'utilisateur par défaut ; si l'analyse de contenu est nécessaire, utiliser une API officielle, une licence ou un partenariat et tracer le droit d'usage | Référence en **Azure SQL Database** ; le cas échéant, contrôle de source à l'ingestion **Azure Data Factory** — détail dans `garantie-llm-apis-publiques.md` |
| Durées de conservation non validées | Formaliser une politique de rétention par type de donnée (photo, rendu, historique, log) et l'exécuter par un job planifié | Job existant **Azure Functions** (brique "Gestion RGPD") : son périmètre doit désormais couvrir explicitement chaque type de donnée de l'inventaire, pas seulement les photos |
| Sécurité et confidentialité des données stockées | Chiffrement au repos et en transit, identités managées, secrets en coffre-fort, réseau privé | Déjà présent dans le System Design (**Microsoft Entra ID, Managed Identities, Key Vault, Private Endpoints**) ; ce livrable formalise qu'il s'agit d'une exigence RGPD, pas seulement d'une bonne pratique de sécurité |
| Pseudonymisation insuffisante entre identité et donnée exploitée par les modèles | Découpler l'identifiant utilisateur réel de l'identifiant utilisé dans l'index vectoriel et les traitements batch : une table de correspondance isolée, à accès restreint, fait le lien | Entre **Azure SQL Database** (table de correspondance, accès restreint via Entra ID) et **Azure AI Search** (index ne portant qu'un identifiant pseudonyme, jamais l'identité réelle) |
| Droits des personnes traités manuellement à l'échelle | Maintenir le traitement manuel au pilote/MVP (déjà acté), mais formaliser un SLA de réponse et prioriser l'extension en libre-service dès le Run | Job **Azure Functions** existant, étendu avec un portail self-service en Run (`../02-solution-production/timeline.md`) |
| Absence de démonstration de conformité (accountability) | Réaliser une Analyse d'Impact relative à la Protection des Données (AIPD) avant le lancement du pilote, et tenir un registre des traitements | Action de gouvernance, en amont du MVP — ne modifie pas le System Design mais conditionne son feu vert |
| Absence de transparence sur les rendus de virtual try-on (deepfake, AI Act art. 50, en vigueur depuis le 2 août 2026) | Apposer un marquage technique lisible par machine et détectable sur chaque image générée, puis informer clairement l'utilisateur que le rendu est généré/manipulé par IA | Conteneur **Stable Diffusion** sur Azure Container Apps : marquage technique en sortie, avant l'écriture en **Azure Blob Storage** ; application mobile : mention visible lors de la restitution du rendu |
| Base légale incertaine pour le dataset du PoC (DeepFashion, licence non commerciale) | Obtenir la validation juridique/DPO avant toute utilisation ; si elle est accordée, confiner le dataset au PoC (pas de diffusion, pas de réutilisation en production) | N'affecte pas le System Design de production, qui n'utilise jamais ce dataset — voir `garantie-llm-apis-publiques.md` |

## Anonymisation : vue d'ensemble dans le System Design

La recommandation de l'`Apercu.md` demande de situer précisément l'anonymisation dans l'architecture. Trois points distincts :

1. **À l'ingestion** (avant Blob Storage) : détection/floutage des visages et tiers, puis suppression ou neutralisation de l'arrière-plan sur les photos de garde-robe, pour ne conserver que la zone utile au vêtement. La photo source non minimisée n'est pas persistée.
2. **Entre l'identité et les données exploitées par les modèles** (SQL ↔ AI Search) : une table de correspondance pseudonymise l'identifiant utilisateur transmis aux pipelines ML et à l'index vectoriel. L'index catalogue/profil ne contient donc jamais l'identité réelle de l'utilisateur — seuls les vecteurs et un identifiant pseudonyme y sont stockés.
3. **Dans les contenus de tendances** (Data Lake, Azure AI Language) : seul le score de tendance par style/marque est conservé après traitement NLP ; les textes bruts des tiers (auteurs, influenceurs) ne sont pas nécessaires au-delà de ce calcul et n'ont pas vocation à être conservés durablement.

## Limite assumée

Ce document formalise les mesures à mettre en place ; leur implémentation détaillée (schéma de la table de correspondance, SLA exact des droits des personnes, contenu de l'AIPD) reste un travail d'ajustement du System Design, explicitement laissé en *nice to have* par Alicia (`../../Projet/03-Mission/Apercu.md`) et reportable à une échéance ultérieure.
