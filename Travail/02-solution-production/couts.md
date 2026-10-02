# Dimensionnement économique — coûts one-shot et récurrents

## Méthodologie

- **RH** : jours-hommes tirés de [`timeline.md`](timeline.md) (staffing détaillé par semaine et par phase), valorisés aux coûts salariaux journaliers internes (Data Scientist 350 €/j, Data Engineer 370 €/j, Tech Lead Data 400 €/j, MLOps Engineer 360 €/j — grille interne Fashion-Insta). Les taux en pourcentage de la timeline sont des arrondis d'affichage ; le calcul utilise les jours issus du détail hebdomadaire. Le temps de l'Expert métier (style) pendant le PoC n'est pas valorisé dans ce chiffrage limité aux profils Data & IA.
- **Technologie** : pour API Management, VM CPU, Container Apps GPU, Container Registry, Azure SQL Database, Blob Storage et Azure AI Search, prix publics sans remise de l'[API Retail Prices Microsoft](https://learn.microsoft.com/en-us/rest/api/cost-management/retail-prices/azure-retail-prices), relevés le 25 septembre 2026, en USD. Best case : France Central, T4 ; worst case : API/CPU/SQL/stockage/recherche en France Central et GPU A100 en Italy North (A100 serverless indisponible en France Central). Conversion indicative 1 USD ≈ 0,92 EUR. Les autres lignes sont des ordres de grandeur à confirmer dans le calculateur Azure ; elles ne constituent pas un devis contractuel.
- **One-shot** = effort de construction (PoC + MVP + stabilisation Run) : RH de développement + consommation Azure à échelle pilote pendant ces phases.
- **Récurrent** = coût annuel du régime de croisière après les 7 semaines de stabilisation du Run : RH d'exploitation + consommation Azure à l'échelle cible. Le trafic distingue 50 000 utilisateurs fréquents et 350 000 occasionnels parmi les 400 000 utilisateurs annuels visés ; ce découpage et les rendus par session sont des hypothèses à mesurer.
- Les scénarios encadrent l'usage du virtual try-on : le best case retient un T4 et le worst case un modèle plus lourd sur A100, tous deux facturés à l'usage. Le scénario de GPU permanent figure séparément comme test de résistance.

## Coûts RH — one-shot (construction)

Jours-hommes repris de `timeline.md` (moyennes de phase), valorisés au tarif du profil.

| Phase | Profil | Jours-hommes | Coût |
|---|---|---|---|
| PoC | Data Scientist / ML Engineer | 14,5 | 5 075 € |
| PoC | Data Engineer | 4,5 | 1 665 € |
| **Sous-total PoC** | | **19** | **6 740 €** |
| MVP (45 j. ouvrés) | DS1 (8 semaines à 100 %, 1 à 50 %) | 42,5 | 14 875 € |
| MVP | DS2 (20 %) | 9,0 | 3 150 € |
| MVP | DS3 (7 semaines à 100 %, 2 à 30 %) | 38 | 13 300 € |
| MVP | DE1 (8 semaines à 100 %, 1 à 50 %) | 42,5 | 15 725 € |
| MVP | DE2 (8 semaines à 100 %, 1 à 50 %) | 42,5 | 15 725 € |
| MVP | Tech Lead (50 %) | 22,5 | 9 000 € |
| MVP | MLOps (100 %) | 45,0 | 16 200 € |
| **Sous-total MVP** | | **242,0** | **87 975 €** |
| Run — stabilisation (35 j. ouvrés) | DS1 (30 %) | 10,5 | 3 675 € |
| Run — stabilisation | DS2 (6 semaines à 100 %, 1 à 20 %) | 31 | 10 850 € |
| Run — stabilisation | DS3 (30 %) | 10,5 | 3 675 € |
| Run — stabilisation | DE1 (18 j-h, `timeline.md`) | 18 | 6 660 € |
| Run — stabilisation | DE2 (17 j-h, `timeline.md`) | 17 | 6 290 € |
| Run — stabilisation | Tech Lead (30 %) | 10,5 | 4 200 € |
| Run — stabilisation | MLOps (100 %) | 35,0 | 12 600 € |
| **Sous-total Run — stabilisation** | | **132,5** | **47 950 €** |
| **Total RH one-shot** | | | **142 665 €** |

## Coûts technologiques — one-shot (construction)

Le PoC n'engage pas de dépense Azure de production : c'est de l'expérimentation (notebook, dataset local/DeepFashion), pas une infrastructure provisionnée. Les services Azure démarrent progressivement sur les **16 semaines de construction** (MVP 9 + Run stabilisation 7), selon le Gantt de [`timeline.md`](timeline.md). La recommandation préférences/tendances est livrée après quatre semaines de Run ; son endpoint et le signal NLP sont donc comptés sur les trois dernières semaines de stabilisation.

| Service | Mise en service | Semaines actives sur les 16 de construction |
|---|---|---|
| Gateway (API Management), Blob Storage, SQL Database, Data Lake, Data Factory, registre de conteneurs | S1 (jalon de décision passé) | 16 |
| Infra GPU virtual try-on (Container Apps) | S1 | 16 |
| File d'attente (Service Bus) | S1 | 16 |
| Observabilité (Monitor), sécurité transverse | S1 | 16 |
| Azure AI Search (service + schéma) | ~S4 | 13 |
| Socle RGPD (Functions) | ~S4 | 13 |
| Endpoint embedding (reco garde-robe) | ~S6 (au plus tard) | 11 |
| Ouverture panel pilote (trafic réel, encore faible) | ~S8-S9 | — |
| Endpoint reco préférences, signal de tendance (NLP) | S5 du Run, après la construction de `run1` | 3 |

Pour les services permanents, le coût de construction est le coût annuel du même scénario × semaines actives / 52. Le calcul GPU suit le trafic du pilote : MVP S8-9 à 10 % du trafic cible, puis sept semaines de Run avec une montée linéaire de 10 % à 100 % (équivalent de 4,05 semaines à plein trafic). Cette rampe de trafic est une hypothèse de planification, distincte de la rampe des gains marketing. L'entraînement initial est provisionné séparément. Montants arrondis à l'euro ; les coûts annuels de référence figurent dans les tableaux récurrents ci-dessous. Les 16 semaines ne comprennent pas le PoC, qui dure en plus 4 à 5 semaines.

| Service | Semaines facturées | Best case | Worst case |
|---|---:|---:|---:|
| API Management | 16 | 500 € | 2 333 € |
| Blob Storage | 16 | 196 € | 783 € |
| SQL Database | 16 | 944 € | 1 887 € |
| Data Lake Storage | 16 | 46 € | 77 € |
| Data Factory | 16 | 185 € | 277 € |
| Service Bus | 16 | 34 € | 46 € |
| Azure Monitor | 16 | 85 € | 391 € |
| Sécurité transverse | 16 | 123 € | 200 € |
| Azure AI Search | 13 | 204 € | 611 € |
| Endpoint embedding | 11 | 598 € | 1 197 € |
| Endpoint préférences | 3 | 163 € | 326 € |
| Azure AI Language | 3 | 12 € | 23 € |
| Azure Container Registry Premium | 16 | 172 € | 172 € |
| Provision services annexes (détail plus bas) | 16 | 462 € | 1 538 € |
| GPU serverless, trafic pilote | 4,05 semaines équivalentes | 278 € | 3 690 € |
| Entraînement initial | Forfait | 300 € | 600 € |
| **Total technologique construction** | | **≈ 4 302 €** | **≈ 14 151 €** |

## Total one-shot

| | Best case | Worst case |
|---|---|---|
| RH | 142 665 € | 142 665 € |
| Technologie (construction) | 4 302 € | 14 151 € |
| **Total** | **≈ 146 967 €** | **≈ 156 816 €** |

## Coûts RH — récurrents (Run, régime de croisière, par an)

Sur ~220 jours ouvrés/an, aux taux de staffing « Run — croisière » de `timeline.md`.

| Profil | Taux | Jours/an | Coût/an |
|---|---|---|---|
| DS1 | 15 % | 33 | 11 550 € |
| DS2 | 20 % | 44 | 15 400 € |
| DS3 | 15 % | 33 | 11 550 € |
| DE1 | 25 % | 55 | 20 350 € |
| DE2 | 25 % | 55 | 20 350 € |
| Tech Lead | 15 % | 33 | 13 200 € |
| MLOps Engineer | 60 % | 132 | 47 520 € |
| **Total RH récurrent/an** | | | **139 920 €** |

Le MLOps Engineer reste le poste RH récurrent le plus coûteux (47 520 €/an, un tiers du total RH récurrent) — cohérent avec le goulot d'étranglement déjà identifié dans `roles-responsabilites.md` et `timeline.md`.

## Coûts technologiques — récurrents (Run à cible, par an)

Dimensionné pour les ~400 000 utilisateurs/an de l'étude Marketing, répartis **à titre d'hypothèse** entre 50 000 fréquents et 350 000 occasionnels. Le catalogue d'environ 875 références n'augmente pas avec le trafic, contrairement aux photos, rendus et requêtes.

### Compute (le poste le plus sensible aux hypothèses)

Coût d'une instance CPU permanente Linux (Standard_DS3_v2, **France Central, 0,351 $/h**) : 0,351 × 8 760 h × 0,92 ≈ **2 829 €/an**. Deux endpoints temps réel existent dans le System Design (embedding/profil utilisateur, reco préférences & tendances) ; la reco garde-robe elle-même interroge Azure AI Search, sans compute dédié.

Virtual try-on : le calcul part d'un **entonnoir de rendus**, à valider sur le panel pilote. Best case : 50 000 utilisateurs fréquents × 12 sessions × 30 % avec essayage × 1 rendu = 180 000 ; 350 000 occasionnels × 1 session × 10 % × 1 rendu = 35 000, soit **215 000 rendus/an**. Worst case : 50 000 fréquents × 12 sessions × 40 % × 1,25 rendu = 300 000 ; 350 000 occasionnels × 1 session × ~17 % × 1 rendu ≈ 60 000, soit **~360 000 rendus/an**. Les durées et le nombre de variantes essayées sont des hypothèses, pas des mesures. Les pics horaires et les p95 de latence restent à tester.

| Brique | Service Azure | Best case | Worst case | Hypothèse |
|---|---|---|---|---|
| Endpoint embedding + endpoint reco préférences | Azure ML online endpoint (Standard_DS3_v2, France Central) | 5 658 € | 11 316 € | Best : 1 instance par endpoint (2 829 € × 2). Worst : 2 instances par endpoint (2 829 € × 4). |
| Calcul de la base structurée | Azure SQL Database, General Purpose Gen5 provisionné | 3 067 € | 6 134 € | France Central, sans remise : best 2 vCores × 0,380542 $/h × 8 760 h × 0,92 ; worst 4 vCores × 0,761084 $/h × 8 760 h × 0,92. |
| Virtual try-on génératif (GPU) | **Azure Container Apps — GPU serverless, exécutions courtes déclenchées par Service Bus** | ≈ 3 568 € | ≈ 47 375 € | Best : T4 en France Central, 215 000 rendus × 40 s facturées. Worst : A100 en Italy North, 360 000 rendus × 60 s facturées. Détail des mètres et limites ci-dessous. |
| Entraînement / réentraînement périodique | Azure ML (batch, GPU ponctuel) | 300 € | 600 € | Quelques heures de calcul par mois pour le réentraînement planifié (`roles-responsabilites.md`). |
| **Sous-total Compute** | | **12 593 €** | **65 425 €** | |

**Calcul GPU au tarif public régional, en USD** : best = 215 000 × 40 s × (0,000091 $/s T4 + 8 × 0,000024 $/s vCPU + 56 × 0,000003 $/s Gio) × 0,92 = **3 568 €**. Worst = 360 000 × 60 s × (0,000688 $/s A100 + 24 × 0,000034 $/s vCPU + 220 × 0,000004 $/s Gio) × 0,92 = **47 375 €**. Les 40 s représentent 20 s d'inférence et 20 s de démarrage/fin d'exécution en moyenne ; les 60 s représentent 45 s d'inférence et 15 s supplémentaires. Ces temps **ne sont pas démontrés** pour un modèle d'essayage virtuel. Le chiffrage suppose qu'une exécution GPU se termine après son rendu. La compatibilité pratique d'une tâche événementielle avec le profil GPU, le temps de chargement des poids et la latence p95 doivent être établis au MVP pour valider cette estimation.

**Sensibilité à la durée d'activation** : le coût GPU à l'usage n'est pas nécessairement égal au seul temps d'inférence. Le délai de 300 s avant extinction cité par Azure concerne les réplicas d'une *app* standard, pas une tâche qui s'achève. Si l'architecture devait garder une réplique T4 active toute l'année, les mêmes mètres régionaux donneraient ≈ **13 085 €/an** au lieu de 3 568 € ; ce repli doit être testé si les tâches courtes ne satisfont pas le délai utilisateur. Le registre de conteneurs Premium (chiffré plus bas) peut aider à réduire le démarrage à froid. Une mise à disposition permanente de deux VM A100 dédiées reste un test de résistance séparé, à re-tarifer dans la région retenue.

### Storage

| Brique | Service Azure | Best case | Worst case | Hypothèse |
|---|---|---|---|---|
| Photos garde-robe + rendus | Azure Blob Storage, Hot | 636 € | 2 544 € | France Central, premier palier de stockage : best ~3 000 Go × 0,0192 $/Go/mois en LRS ; worst ~6 000 Go × 0,0384 $/Go/mois en GRS, le tout × 12 × 0,92. Cela représente ~7,5 ou ~15 Mo par utilisateur annuel ; le nombre de photos/rendus conservés et la durée de rétention devront confirmer ces volumes. Transactions et transfert sont couverts provisoirement par la provision annexe. |
| Lac de données (catalogue, tendances) | Azure Data Lake Storage | 150 € | 250 € | Volumétrie faible, le catalogue ne grossit pas avec le trafic. |
| **Sous-total Storage chiffré** | | **786 €** | **2 794 €** | |

### Autres services

La ligne « provision services annexes » couvre provisoirement l'exécution de l'API et l'orchestration du try-on derrière API Management (App Service ou Functions), les transactions Blob, les sauvegardes, la sortie réseau et les charges de plateforme non détaillées. Ce n'est **pas un tarif Azure vérifié** : 1 500 €/an best et 5 000 €/an worst sont des réserves de planification, à remplacer par des SKU et volumes mesurés. Le développement de l'application mobile est hors du périmètre Data & IA chiffré ici.

| Brique | Service Azure | Best case | Worst case | Hypothèse |
|---|---|---|---|---|
| Index vectoriel | Azure AI Search | 814 € | 2 442 € | France Central, Basic à 0,101 $/h/réplica × 8 760 h × 0,92. Best : 1 réplica sans SLA ; worst : 3 réplicas pour le SLA de requêtes **et d'indexation**, puisque des index sont publiés pendant l'exploitation. |
| Gateway applicatif (contrats d'API Data/IA) | Azure API Management, France Central | 1 625 € | 7 581 € | Best : Basic 0,2016 $/h ; worst : Standard 0,9407 $/h, selon l'API Retail Prices Microsoft. |
| File d'attente virtual try-on | Azure Service Bus | 110 € | 150 € | Tier Standard ; le nombre d'opérations devra inclure au moins dépôt et lecture de chaque demande, puis les reprises éventuelles. À ce stade, le forfait mensuel domine l'estimation. |
| Ingestion catalogue/tendances | Azure Data Factory | 600 € | 900 € | |
| Signal de tendance (NLP) | Azure AI Language | 200 € | 400 € | |
| Purge RGPD, jobs planifiés | Azure Functions | ~0 € | ~0 € | Sous le palier gratuit (1M exécutions/mois). |
| Observabilité | Azure Monitor | 276 € | 1 272 € | |
| Sécurité transverse | Private Endpoints (5 à 8 services), Key Vault, Entra ID | 400 € | 650 € | Private Endpoint : 0,01 $/h/endpoint (≈ 80,6 €/an chacun) ; Key Vault et Entra ID négligeables à ce volume. |
| Registre du conteneur GPU | Azure Container Registry Premium, France Central | 560 € | 560 € | 1,6666 $/jour × 365 × 0,92 ; le stockage et une éventuelle réplication en Italy North restent à contrôler. Premium permet l'artifact streaming pour réduire le démarrage à froid. |
| Provision services annexes | Voir ci-dessus | 1 500 € | 5 000 € | Hypothèse de cadrage, à remplacer par des lignes Azure détaillées. |
| **Sous-total Autres** | | **6 085 €** | **18 955 €** | |

### Total technologie récurrente

| | Best case | Worst case |
|---|---|---|
| Compute | 12 593 € | 65 425 € |
| Storage chiffré | 786 € | 2 794 € |
| Autres | 6 085 € | 18 955 € |
| **Total** | **≈ 19 464 €** | **≈ 87 174 €** |

## Total récurrent annuel (Run à cible)

| | Best case | Worst case |
|---|---|---|
| RH | 139 920 € | 139 920 € |
| Technologie | 19 464 € | 87 174 € |
| **Total/an** | **≈ 159 384 €** | **≈ 227 094 €** |

## Risques de chiffrage

- **Le GPU du virtual try-on reste le facteur dominant de l'écart best/worst du compute récurrent** (≈3 568 € à ≈47 375 €). Les volumes, la durée facturée et le démarrage à froid sont à mesurer au pilote. Le T4 est disponible en France Central ; l'A100 serverless du worst case est en Italy North. L'impact du transfert interrégion et les quotas GPU sont à valider.
- Les prix publics Microsoft relevés par SKU et région ne tiennent pas compte du contrat Azure de Fashion-Insta, des remises éventuelles ni des fluctuations de change. Plusieurs services restent estimés et la provision annexe n'est pas un devis. Le chiffrage est un **ordre de grandeur de cadrage**, à réaffiner avant engagement budgétaire.
- Le taux de change USD→EUR utilisé (0,92) est indicatif ; les montants sont à réactualiser au moment du chiffrage définitif.
- Le coût technologique pendant la construction inclut les trois semaines d'exploitation des préférences/tendances en fin de stabilisation. Le profil GPU est fixé pour les deux scénarios, mais le trafic pilote, la latence et la durée réelle d'activation restent à mesurer. Le coût d'API, de réseau et de sauvegarde est pour l'instant couvert par une provision, non par un devis détaillé.

## Sources

- [API Retail Prices Microsoft](https://learn.microsoft.com/en-us/rest/api/cost-management/retail-prices/azure-retail-prices) — prix publics utilisés pour Container Apps GPU/vCPU/mémoire, VM DS3 v2, API Management, Container Registry, SQL Database (General Purpose Compute Gen5, 2 et 4 vCores), Storage (Blob Hot LRS/GRS, premier palier) et Azure Cognitive Search (Basic Unit). Filtres `serviceName` et `armRegionName` France Central ou Italy North ; taux horaires ou mensuels convertis avec le change indicatif ci-dessus.
- [Choose a service tier for Azure AI Search](https://learn.microsoft.com/en-us/azure/search/search-sku-tier)
- [Azure Data Factory Pricing — Integrate.io](https://www.integrate.io/blog/azure-data-factory-pricing/)
- [Azure Functions Pricing — Modal](https://modal.com/blog/azure-function-pricing-guide)
- [Azure AI Language Pricing — OMR Reviews](https://omr.com/en/reviews/product/azure-ai-language/pricing)
- [Azure Monitor Pricing — MonitoringCost.com](https://monitoringcost.com/azure-monitor-cost)
- [Azure Machine Learning managed online endpoint pricing — Microsoft Learn](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-view-online-endpoints-costs?view=azureml-api-2)
- [Gérer les coûts et l'optimisation — Azure Machine Learning](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-manage-optimize-cost?view=azureml-api-2)
- [Azure Service Bus Pricing](https://azure.microsoft.com/en-us/pricing/details/service-bus/)
- [Azure Private Link Pricing](https://azure.microsoft.com/en-us/pricing/details/private-link/)
- [SLA et quotas Azure AI Search (réplicas requis)](https://learn.microsoft.com/en-us/azure/search/search-limits-quotas-capacity)
- [Azure Container Apps — GPU serverless](https://learn.microsoft.com/en-us/azure/container-apps/gpu-serverless-overview)
- [Azure Container Apps — profils de charge GPU](https://learn.microsoft.com/en-us/azure/container-apps/workload-profiles-overview), [tarification Container Apps](https://azure.microsoft.com/en-us/pricing/details/container-apps/)
- [Azure Container Apps — tâches déclenchées par événement](https://learn.microsoft.com/en-us/azure/container-apps/jobs), [mise à l'échelle et délai d'extinction](https://learn.microsoft.com/en-us/azure/container-apps/scale-app)
- [Azure Machine Learning — Batch endpoints (traitement différé, sans contrainte de latence)](https://learn.microsoft.com/en-us/azure/machine-learning/concept-endpoints-batch?view=azureml-api-2)
