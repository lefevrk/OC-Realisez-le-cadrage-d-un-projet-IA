# Limites et risques — solution en production

## Limite de périmètre

Ce cadrage couvre les briques Data & IA de bout en bout (recommandation garde-robe, virtual try-on génératif, recommandation préférences/tendances, boucle de feedback, RGPD). Les comptes utilisateurs, les écrans de l'application mobile, le tunnel d'achat et l'intégration e-commerce sont des briques logicielles acquises, hors chiffrage (`system-design.md`, "Hors périmètre de ce schéma").

## Données personnelles et RGPD

- Les photos de garde-robe et les rendus du virtual try-on sont des données personnelles ; ils suivent les mêmes règles de rétention/purge (`system-design.md`).
- Le socle RGPD (purge automatique planifiée) est actif dès le MVP, mais le traitement des demandes d'accès/modification/suppression/désinscription reste **manuel** jusqu'à l'extension en libre-service, développée en Run (`timeline.md`, "Périmètre RGPD au pilote").
- Les volumes de stockage (photos + rendus) et la durée de rétention réelle restent des hypothèses de cadrage à confirmer (`couts.md`, ligne stockage Blob).
- Le scénario GPU worst case (A100) pourrait nécessiter une région hors France Central (par exemple Italy North, non arrêtée à ce stade), ce qui poserait la question du transfert des photos utilisateur et des règles de localisation (`system-design.md`, virtual try-on).
- La politique de rétention et les décisions de conformité restent à valider par le responsable juridique/DPO, pas figées par l'équipe Data (`roles-responsabilites.md`, RACI).

## Limites du chiffrage économique

- Les coûts technologiques récurrents (GPU, stockage, compute) reposent sur des volumes de trafic et d'essais estimés, pas mesurés ; le pilote doit confirmer ces hypothèses (`couts.md`, "Risques de chiffrage").
- La projection de couverture des coûts (`roi.md`) utilise une marge contributive illustrative de 40 % (à confirmer par Finance) et le chiffre d'affaires additionnel total de l'application, sans répartition connue entre Data & IA et les autres leviers — un ajustement volontairement laissé hors scope à ce stade (`roi.md`, introduction).
- Elle ne couvre donc pas un ROI attribuable démontré, mais une date d'équilibre indicative (~28 à ~41 mois selon le scénario).

## Risques d'exécution

- La capacité MLOps repose sur un unique profil transverse à l'ensemble des briques ML/IA ; les livraisons sont séquencées entre MVP et Run pour éviter la saturation, mais tout retard sur une brique se répercute sur les autres (`roles-responsabilites.md`, "Capacité MLOps").
- Le lancement du panel pilote dépend simultanément de l'endpoint de reco, du virtual try-on et du socle RGPD/sécurité — trois dépendances convergent sur le même jalon (`timeline.md`, Gantt MVP).
- Le démarrage et le temps de rendu du virtual try-on GPU serverless (cold start, latence) ne sont pas mesurés à ce stade ; s'ils dépassent l'hypothèse retenue, le modèle de coût par exécution devra être révisé (`system-design.md`, virtual try-on).
