# Contexte métier et valeur ajoutée de l'IA

## Rappel du contexte métier

**Fashion-Insta** est une entreprise de mode : 10,4 M€ de CA en 2024, réparti à parts quasi égales entre 11 magasins physiques (5,2 M€) et l'e-commerce (5,2 M€, 950 000 visiteurs uniques/an) *(source : document métier d'Alicia)*.

Le projet porte sur une **application mobile de recommandation d'articles vestimentaires à partir de photos** : l'utilisateur se prend en photo avec ses vêtements, l'application propose des articles du catalogue Fashion-Insta dans le même esprit stylistique. L'app s'appuiera sur Azure, partenaire cloud de l'entreprise.

Alicia (VP Product) porte ce projet auprès du COMEX pour obtenir le feu vert de lancement d'un PoC IA. L'enjeu de ce document de cadrage est de démontrer que la brique technique centrale — la recommandation à partir de photo — est faisable, avant d'investir dans le reste de l'application.

## Valeur ajoutée de l'IA

**Pourquoi une approche IA, et pas une règle simple ou une recherche par mot-clé.** L'utilisateur n'exprime pas ses goûts par des mots-clés ou des préférences déclarées : il fournit une photo. Il faut donc *interpréter visuellement* un style à partir de l'image, puis le rapprocher d'un catalogue d'environ 875 références réparties sur 9 catégories et 5 univers stylistiques — une combinatoire garde-robe × catalogue qui n'est ni exhaustivement définissable par des règles statiques, ni traitable manuellement à l'échelle visée. C'est précisément ce que les approches candidates de `probleme-ml.md` (classification d'attributs + matching par tags ou par similarité vectorielle) permettent de faire.

**Pourquoi cette brique conditionne tout le reste.** C'est la fonctionnalité centrale de l'app : sans elle, les autres besoins exprimés par les métiers (garde-robe, essayage virtuel, personnalisation, préférences déclarées) n'ont pas de socle technique sur lequel s'appuyer. C'est pour cela que le PoC isole cette seule brique (`perimetre-poc.md`, "Décision de cadrage") plutôt que de chercher à couvrir l'ensemble de l'application.

**Impact business attendu, sous réserve que cette brique fonctionne** *(source : étude de marché citée dans le document métier d'Alicia)* :
- **+14 %** de CA web attendu sur 24 mois (l'app renvoie vers le site pour l'achat) ;
- **+4 %** de CA magasin attendu sur 24 mois (effet notoriété/acquisition) ;
- **~400 000 utilisateurs** visés au moins une fois par an, dont plusieurs dizaines de milliers d'utilisateurs actifs.

Ces chiffres restent conditionnés à la faisabilité de la recommandation elle-même : c'est exactement ce que ce PoC teste, avec le critère de succès défini dans `critere-succes.md`.
