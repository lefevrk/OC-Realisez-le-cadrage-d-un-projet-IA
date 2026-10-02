# Garantie sur l'entraînement de LLM et alternative aux APIs publiques

## Objectif

Deux points explicitement soulevés pour le COMEX, complétés par deux points de vigilance identifiés en recherche :

1. Le DPO exige la garantie que les données des utilisateurs ne servent pas à entraîner des LLM (`../../Projet/03-Mission/Apercu.md`, mail d'Alicia).
2. La recommandation de l'`Apercu.md` demande une alternative aux APIs publiques, si la solution en utilise.
3. La base légale du dataset utilisé pendant le PoC (en complément, voir plus bas).
4. L'obligation de marquage des rendus de virtual try-on au titre de l'AI Act (en complément, voir plus bas).

## Ce que l'architecture actuelle fait déjà bien

Le System Design (`../02-solution-production/system-design.md`) n'appelle aucun LLM ni aucune API IA grand public (pas d'API OpenAI/ChatGPT publique). Les briques IA retenues sont déployées dans l'abonnement Azure de Fashion-Insta ou exploitées comme services Azure sous contrat avec Microsoft :

- **Azure AI Language** pour le signal de tendance,
- **Azure Machine Learning** pour l'embedder visuel et le filtre de préférences,
- **Stable Diffusion**, déployé en conteneur privé sur **Azure Container Apps** (pas d'appel à une API de génération d'image publique).

C'est un argument structurant à présenter au COMEX : le choix d'éviter les APIs IA grand public a été fait dès le System Design, pas ajouté a posteriori pour répondre au DPO.

## Garantie apportée au DPO

La garantie demandée porte sur les **données des utilisateurs**. Elle est apportée par le flux retenu, qui n'envoie ces données à aucun LLM :

- les photos, rendus, préférences et identifiants restent dans les stockages Azure de Fashion-Insta ; la photo minimisée est traitée uniquement par l'embedder visuel déployé par Fashion-Insta sur Azure ML et, pour le virtual try-on, par son conteneur Stable Diffusion privé ;
- **Azure AI Language** ne reçoit que les textes de tendances issus de sources partenaires, jamais les photos ou préférences des utilisateurs ; il n'est donc pas sur le chemin de leurs données ;
- l'index **Azure AI Search** ne contient que les vecteurs et métadonnées nécessaires à la recherche. Microsoft indique que les données client d'Azure AI Search ne sont pas utilisées pour entraîner ou améliorer ses modèles ;
- aucune ressource Azure OpenAI, Foundry Models ou API LLM grand public n'est autorisée dans cette architecture. Toute introduction ultérieure d'un LLM déclenche une revue DPO et une mise à jour du registre des traitements avant son activation.

Ce choix est vérifiable dans le System Design : aucun flux depuis les données utilisateur ne rejoint un endpoint LLM. Il constitue donc la garantie d'architecture présentable au COMEX, complétée par le contrôle contractuel suivant :

- conserver dans le dossier DPO les clauses Microsoft applicables (Product Terms et Data Protection Addendum) et la référence de la configuration retenue ;
- utiliser les **Private Endpoints** entre les ressources Azure lorsque le service le permet, et désactiver les accès réseau publics de ces ressources lorsque possible. Ces contrôles réduisent l'exposition réseau ; ils ne constituent pas, à eux seuls, la garantie de non-entraînement ;
- si Azure AI Language est utilisé, activer `LoggingOptOut` **explicitement** sur les opérations utilisées pour le score de tendance (Key Phrase Extraction, Sentiment Analysis, NER) : à la différence des endpoints PII/santé, ce paramètre est à `false` par défaut sur ces opérations, donc les textes envoyés sont conservés 48 h sauf activation volontaire. Cette mesure ne porte pas sur les données utilisateur, qui ne sont jamais transmises à ce service.

**Précision sur l'embedder visuel (Azure ML)** : la garantie explicite de Microsoft *« n'entraîne pas les modèles tiers sur les données client »* documentée pour Azure Machine Learning ne concerne, telle que publiée, que le Model Catalog / Models-as-a-Service (modèles fine-tunés par ce service). Ce n'est pas le cas de notre embedder : c'est un modèle **entraîné par les Data Scientists de Fashion-Insta eux-mêmes**, sur un pipeline Azure ML et un endpoint managé dédiés à Fashion-Insta — il n'y a donc pas de modèle tiers qui apprendrait sur les photos des utilisateurs en arrière-plan. L'argument à présenter au DPO est donc double : (1) pas de LLM tiers sur le chemin des données, (2) le seul modèle qui voit ces données est un modèle propriétaire dont Fashion-Insta maîtrise l'entraînement.

## Sources de tendances : protection par défaut et alternative aux APIs publiques

Le besoin métier prévoit que l'utilisateur puisse référencer des blogs, sites de conseil ou comptes d'influenceurs. Par défaut, la solution conserve uniquement cette **référence** (nom ou URL) comme préférence utilisateur dans Azure SQL Database : elle n'ingère ni ne stocke le contenu du site ou du compte tiers.

Si un score de tendance nécessite effectivement d'analyser du contenu tiers, la solution utilise exclusivement une source dont l'usage est autorisé : API officielle avec conditions compatibles, flux sous licence ou partenariat. Le scraping ad hoc de contenu public n'est pas retenu. Cette règle répond à la recommandation de l'`Apercu.md` : aucune API publique non maîtrisée n'est nécessaire pour traiter les données des utilisateurs.

Dans ce cas seulement, le pipeline **Azure Data Factory → Data Lake Storage → Azure AI Language** traite les textes fournis par cette source autorisée. La référence utilisateur, les données de tendance et les données personnelles de l'utilisateur restent séparées ; Azure AI Language ne reçoit jamais les photos ou préférences identifiantes de l'utilisateur.

## Message clé pour le COMEX

« Les données utilisateur ne sont transmises à aucun LLM : elles sont traitées par des modèles et conteneurs privés déployés pour Fashion-Insta. Aucun service Azure OpenAI, Foundry Models ou API LLM publique ne fait partie de l'architecture. Toute évolution en ce sens devra être soumise au DPO avant activation. »

## Références à joindre au dossier DPO

- [Confidentialité d'Azure AI Language — Microsoft Learn](https://learn.microsoft.com/en-us/azure/ai-foundry/responsible-ai/language-service/data-privacy) : traitement régional et rétention temporaire éventuelle à prendre en compte.
- [Confidentialité d'Azure AI Search — Microsoft Learn](https://learn.microsoft.com/en-us/azure/search/search-security-built-in) : engagement sur l'absence d'utilisation des données client pour entraîner ou améliorer les modèles.
- [Confidentialité des modèles Azure Machine Learning — Microsoft Learn](https://learn.microsoft.com/en-us/azure/machine-learning/concept-data-privacy?view=azureml-api-2) : les déploiements sur calcul géré sont soumis aux engagements Azure ; les modèles téléchargés restent sous le contrôle de Fashion-Insta.
- [Microsoft Products and Services Data Protection Addendum](https://www.microsoft.com/licensing/docs/view/Microsoft-Products-and-Services-Data-Protection-Addendum-DPA) : document contractuel à faire valider pour les services et la souscription effectivement retenus.

## Point de vigilance complémentaire : dataset du PoC

Le dataset DeepFashion utilisé pour simuler la garde-robe pendant le PoC (`../01-poc/datasets-candidats.md`) est publié sous une licence **recherche non commerciale** ([accord de diffusion CUHK MMLab](https://mmlab.ie.cuhk.edu.hk/projects/DeepFashion/DeepFashionAgreement.pdf), [licence DeepFashion-MultiModal](https://github.com/yumingj/DeepFashion-MultiModal/blob/main/LICENSE)) : ce sont des photos de personnes réelles collectées sur Internet, sans mention de consentement RGPD pour un usage par une entreprise. La licence limite explicitement l'usage à la recherche non commerciale ; la compatibilité de ce PoC mené pour Fashion-Insta doit donc être validée par le juridique/DPO avant toute utilisation. Dans tous les cas, le dataset ne peut pas être réutilisé en production : le projet bascule vers le vrai catalogue et les vraies photos Fashion-Insta avant la mise en production (`../01-poc/perimetre-poc.md`). Point à rappeler explicitement si le DPO interroge sur l'origine des données du PoC.

## Point de vigilance complémentaire : AI Act et marquage des rendus de virtual try-on

L'article 50 du règlement européen sur l'IA (AI Act), **applicable depuis le 2 août 2026**, impose un marquage des contenus de type *deepfake* : image générée ou manipulée qui ressemble à une personne réelle et pourrait sembler authentique. Le rendu du virtual try-on (`../02-solution-production/system-design.md`, brique "Virtual try-on génératif") correspond à cette définition : il manipule la photo de l'utilisateur pour y faire apparaître un vêtement différent.

Deux obligations complémentaires doivent être couvertes. En tant que fournisseur du système de virtual try-on, Fashion-Insta doit intégrer au rendu un **marquage technique lisible par machine et détectable**. En tant que déployeur, Fashion-Insta doit aussi informer clairement l'utilisateur, dans l'application et au plus tard lors de son exposition au rendu, que l'image a été générée ou manipulée par IA. Cette information reste nécessaire si le rendu est partagé à des tiers.

**Mesures intégrées au System Design** : le conteneur applique le marquage technique à chaque image produite avant son écriture en Blob Storage, et l'application affiche une mention claire lors de la restitution du rendu — voir `mesures-protection.md`.

Sources : [AI Act — Transparency Rules Article 50](https://artificialintelligenceact.eu/transparency-rules-article-50/), [Article 50 — AI Act Service Desk, Commission européenne](https://ai-act-service-desk.ec.europa.eu/en/ai-act/article-50).
