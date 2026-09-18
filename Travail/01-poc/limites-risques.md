# Limites et risques du PoC

Synthèse des limites déjà identifiées dans les documents précédents, sans les redétailler.

## Limite de périmètre

Le PoC ne couvre que la brique de recommandation stylistique — le détail de ce qui est volontairement exclu (essayage virtuel, préférences déclarées, RGPD, e-commerce, passage à l'échelle) est dans `perimetre-poc.md` ("Hors périmètre").

## Limites de la donnée

- Le dataset utilisé pour simuler la garde-robe est un proxy externe (photos datées, qualité variable, licence non commerciale) — pas des données d'utilisateurs Fashion-Insta réels (`datasets-candidats.md`).
- L'extraction du catalogue Fashion-Insta nécessaire au PoC repose sur un chiffrage existant (3 jours-homme) dont le périmètre exact reste à confirmer (`dimensionnement.md`).

## Limites de la méthode et de l'évaluation

- Les deux approches comparées ne capturent que les attributs explicitement définis (catégorie, couleur, motif...) ; les nuances visuelles fines resteraient hors de portée sans passer à une représentation visuelle apprise, explicitement écartée du PoC (`probleme-ml.md`, "Évolution hors PoC").
- L'évaluation porte sur un échantillon restreint (10 à 15 profils simulés, jugés par quelques experts métier) : elle valide une pertinence perçue à cette échelle, pas la satisfaction des 400 000 utilisateurs visés à terme, ni une performance à l'échelle réelle (`critere-succes.md`, "Ce que ce critère ne valide pas").
- La pertinence perçue dépend du jugement d'un nombre restreint d'experts métier ; aucun accord inter-annotateurs n'est mesuré sur l'échelle 0-2 (`probleme-ml.md`).

## Risques d'exécution

Le poste le plus incertain est la calibration du classifieur d'attributs sur les photos Fashion-Insta ; le plus fragile côté planning est la disponibilité de l'expert métier sur ses créneaux dédiés. Détail et mitigations dans `dimensionnement.md` ("Risques sur ce chiffrage").
