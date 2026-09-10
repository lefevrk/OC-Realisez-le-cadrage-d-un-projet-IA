# Périmètre du PoC - Fashion-Insta

## Décision de cadrage

Le PoC évalue la faisabilité et la valeur d'une recommandation d'articles Fashion-Insta à partir de photos montrant l'utilisateur portant les vêtements de sa garde-robe.

Il répond à une seule question : **peut-on proposer, à partir de photos montrant un utilisateur dans les vêtements de sa garde-robe, des articles du catalogue correspondant à ses goûts et suffisamment pertinents pour être explorés ?**

## Fonctionnalité à démontrer

1. Un jeu de photos de l'utilisateur portant les vêtements de sa garde-robe est fourni au système, ainsi que l'accès au catalogue Fashion-Insta comme réservoir de recommandations.
2. Le système analyse ces photos et retourne une liste courte d'articles du catalogue Fashion-Insta correspondant aux goûts déduits de la garde-robe de l'utilisateur.
3. Une démonstration illustre les recommandations obtenues pour permettre une revue métier des résultats.

Le format de démonstration peut être une interface légère ou un notebook de résultats : aucune application mobile complète, ni spécification d'interface, n'est requise à ce stade.

## Données retenues

### Entrées

- Photos de l'utilisateur portant les vêtements de sa garde-robe : ce jeu de données n'existe pas en interne et doit être trouvé ou constitué (dataset public proche du comportement utilisateur final, cf. étape de recherche de données).
- Catalogue Fashion-Insta : images de plusieurs angles, descriptions sommaires, prix et tags de style.

### Sorties

- Top-k d'articles du catalogue Fashion-Insta, classés par ordre de pertinence au regard des goûts déduits de la garde-robe.
- Métadonnées produit nécessaires à la revue métier : catégorie, style, prix et visuel.

### Préparation indispensable

Les données catalogue sont aujourd'hui dispersées entre plusieurs sources non centralisées et doivent d'abord être extraites et rapprochées par l'équipe Data Engineering. La qualité, la couverture et la cohérence des images et tags devront être contrôlées avant toute expérimentation ML.

## Hors périmètre

Le PoC isole volontairement cette brique de recommandation stylistique. Les autres usages de l'application cible (garde-robe et essayage virtuel dans l'app mobile, personnalisation générative des articles, recommandation par préférences déclarées ou tendances, avis utilisateurs, gestion RGPD, intégration e-commerce, déploiement à l'échelle) sont des évolutions produit prévues pour les phases suivantes et ne conditionnent pas la faisabilité testée ici.

## Hypothèses à valider pendant le PoC

- Le catalogue couvre assez de catégories complémentaires pour former des recommandations utiles (pantalons/jeans, chemises/blouses, vestes/manteaux, t-shirts/tops, robes/jupes, costumes/tailleurs, accessoires et chaussures).
- Les tags et images permettent de reconnaître ou d'inférer les styles proposés : essentiel urbain, casual chic, professionnel moderne, streetwear et bohème féminin.
- Un échantillon de photos de garde-robe peut être obtenu de manière licite et suffisamment varié pour une évaluation métier.
- Les experts métier peuvent juger si une recommandation correspond bien au goût vestimentaire exprimé par la garde-robe de l'utilisateur, et annoter un jeu de test sur cette base.
