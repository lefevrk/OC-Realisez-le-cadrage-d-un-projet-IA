# Rôles et responsabilités par brique — solution en production

## Équipe mobilisée

Équipe interne Fashion-Insta : 3 Data Scientists, 2 Data Engineers, 1 Tech Lead Data, 1 MLOps Engineer.

| Profil | Rôle générique | Périmètre sur ce projet |
|---|---|---|
| Data Scientist (×3) | Conception, entraînement et évaluation des modèles ; sélection d'algorithmes, préparation des jeux de données, analyse des résultats. | Conçoit la logique métier et les modèles de chaque brique ML/IA (embedder, filtres de reco, NLP tendance, génératif). |
| Data Engineer (×2) | Manipulation, structuration et mise à disposition des données ; pipelines, stockage, automatisation. | Construit et exploite les briques de données (stockage, ingestion, gateway) qui alimentent les modèles. |
| Tech Lead Data (×1) | Architecture globale, choix technologiques, cohérence technique ; encadrement et validation des orientations. | Valide l'architecture de chaque brique et arbitre les choix transverses (sécurité, coûts, dépendances). Ne développe pas les briques lui-même. |
| MLOps Engineer (×1) | Déploiement et mise en production des modèles ; environnements cloud, automatisation du cycle de vie, monitoring. | Opère en production l'ensemble des briques ML/IA une fois conçues par les Data Scientists. |

## Répartition nominale indicative

Répartition indicative des 3 Data Scientists et des 2 Data Engineers par domaine, à confirmer selon les disponibilités :

- **DS1 — Recommandation garde-robe** : exécution du PoC (approche B), jalon de décision conserver l'approche par attributs ou entraîner l'embedder appris (voir [`system-design.md`](system-design.md)), agrégation du profil utilisateur, filtres métier de la reco garde-robe.
- **DS2 — Préférences & tendances** : extraction NLP du signal de tendance, logique de filtre/tri de la reco préférences.
- **DS3 — Virtual try-on génératif** : sélection, calibration et évaluation du modèle de diffusion.
- **DE1 — Données utilisateur** : stockage photos, stockage structuré, gateway applicatif, jobs de purge RGPD.
- **DE2 — Données catalogue** : ingestion multi-sources, lac de données, infrastructure de l'index vectoriel.

Cette répartition **n'est pas figée par brique** : en particulier, le MLOps Engineer et le Tech Lead interviennent de façon transverse (voir plus bas), pas sur une brique isolée.

## Répartition par brique

| Brique | Développement | Maintenance / Exploitation | Livrable vérifiable |
|---|---|---|---|
| Contrats d'API Data/IA (gateway) | Data Engineer (DE1) — contrats d'API des services Data/IA exposés (upload photo, requêtes de reco, préférences, avis) | Data Engineer (DE1) | Contrats d'API documentés et testés ; l'équipe applicative (hors périmètre Data & IA) reste dépendante de ces contrats pour son propre gateway front |
| Stockage photos (Blob Storage) | Data Engineer (DE1) | Data Engineer (DE1) | Compte de stockage configuré, règles de cycle de vie (tiering, purge) actives |
| Stockage structuré (SQL Database) | Data Engineer (DE1) | Data Engineer (DE1) | Schéma de base déployé et documenté |
| Ingestion des sources (Data Factory) | Data Engineer (DE2) | Data Engineer (DE2) | Pipelines d'ingestion catalogue/tendances en production, supervisés |
| Stockage du lac de données (Data Lake) | Data Engineer (DE2) | Data Engineer (DE2) | Zones de données (brute/consolidée) organisées et documentées |
| Entraînement et indexation (jalon PoC → embedder) | Data Scientist (DS1) — exécution du PoC, jalon de décision attributs vs. embedder appris | MLOps Engineer (réentraînement planifié, publication des nouvelles versions), Data Scientist (DS1, pertinence et évolutions du modèle) | Rapport de décision du jalon PoC, pipeline d'entraînement versionné |
| Index vectoriel (Azure AI Search) | Data Engineer (DE2, mise en place du service et du schéma) | MLOps Engineer (publication des index, fraîcheur des données) | Index vectoriel opérationnel, procédure de publication documentée |
| Embedding et profil utilisateur (endpoint temps réel) | Data Scientist (DS1) | MLOps Engineer (déploiement, autoscaling, monitoring), Data Scientist (DS1, pertinence et évolutions du modèle) | Endpoint déployé, SLA de latence défini et mesuré |
| Reco garde-robe (filtres métier) | Data Scientist (DS1) | MLOps Engineer (exploitation, disponibilité), Data Scientist (DS1, pertinence et ajustement des filtres selon le feedback) | Filtres métier documentés, testés sur le protocole d'évaluation du PoC |
| Signal de tendance (NLP) | Data Scientist (DS2) | MLOps Engineer (planification du job batch, disponibilité), Data Engineer (DE2, fraîcheur des textes en entrée), Data Scientist (DS2, pertinence du score) | Job batch en production, score de tendance documenté |
| Reco préférences & tendances | Data Scientist (DS2) | MLOps Engineer (exploitation, disponibilité), Data Scientist (DS2, pertinence et évolutions) | Logique de filtre/tri documentée et testable |
| Virtual try-on génératif | Data Scientist (DS3) | MLOps Engineer (infrastructure GPU, file d'attente, disponibilité, coûts), Data Scientist (DS3, qualité des rendus) | Modèle calibré, file d'attente et coûts par essai mesurés |
| Collecte du feedback et mesure d'impact | Data Engineer (DE1, pipeline de collecte/fiabilisation des avis et événements de conversion) | Data Engineer (DE1, exploitation du pipeline de collecte), Data Scientist (analyse des indicateurs de pertinence et de ventes) ; Marketing comme interlocuteur de validation de l'interprétation | Tableau de bord des indicateurs de pertinence/ventes, partagé et validé avec le Marketing (alimente le ROI de l'étape Coûts & ROI) |
| Gestion RGPD (purge, export) | Data Engineer (DE1) | Data Engineer (DE1) pour la mise en œuvre technique ; règles de conservation et décisions de conformité validées avec le responsable métier/juridique compétent | Politique de rétention documentée et validée, jobs de purge testés |
| Sécurité transverse (identité, secrets, réseau) | Tech Lead (architecture de sécurité) + Data Engineer (implémentation) | Data Engineer, revue périodique par le Tech Lead | Revue de sécurité documentée (identités, secrets, accès réseau) |
| MLOps et observabilité | MLOps Engineer (pipelines, registre, alerting) | MLOps Engineer ; arbitrage des seuils de dérive avec le Tech Lead et les Data Scientists concernés | Tableau de bord de monitoring (latence, coût, dérive, taux d'échec) en production |

## Rôles transverses

Le **Tech Lead** valide l'architecture et les choix technologiques de chaque brique (cohérence entre briques, dépendances, sécurité), et arbitre en cas de désaccord entre Data Scientists et Data Engineers sur une brique partagée ; il intervient ponctuellement en support technique si nécessaire, sans porter le développement d'une brique en propre.

Le **MLOps Engineer** assure une exploitation transverse commune à l'ensemble des briques ML/IA (entraînement/indexation, index vectoriel, embedding utilisateur, reco garde-robe, signal de tendance, reco préférences, virtual try-on) : pipelines, registre de modèles, déploiements, monitoring, alertes et capacity planning. Les Data Scientists restent responsables de la qualité fonctionnelle, des seuils métier et des évolutions de leurs modèles respectifs.

La **validation métier** des résultats (pertinence perçue, seuils de succès, interprétation des indicateurs de vente) passe par les instances de gouvernance du projet (Copil, experts métier).

**Capacité MLOps** : plusieurs mises en production simultanées peuvent saturer l'unique MLOps Engineer. La timeline doit séquencer les livraisons et prévoir un taux de staffing adapté à chaque phase.

## RACI — décisions clés

Cette matrice précise les responsabilités liées aux décisions transverses. **A est unique par ligne** (un seul approbateur).

| Décision ou livrable | R — réalise | A — valide | C — consulté | I — informé |
|---|---|---|---|---|
| Choix de l'approche après le PoC (attributs vs. embedder appris) | DS1 | Tech Lead | Expert métier, MLOps Engineer | DS2, DS3, DE1, DE2 |
| Publication d'un modèle et de son index en production | MLOps Engineer | Tech Lead | DS concerné, DE2 | Reste de l'équipe Data |
| Validation de la pertinence des recommandations (seuils métier) | DS concerné | Responsable métier | MLOps Engineer | Tech Lead |
| Politique de rétention et de purge RGPD | DE1 | Responsable juridique/DPO | Responsable métier, Tech Lead | Reste de l'équipe Data |
| Interprétation des indicateurs de pertinence/ventes (ROI) | DS (analyse) | Marketing | DE1 | Tech Lead, Alicia (VP Product) |
