<properties 
	pageTitle="Implémentation de la navigation à facettes dans Azure Search" 
	description="Navigation à facettes dans Azure Search" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="04/16/2015" 
	ms.author="heidist"/>

#Implémentation de la navigation à facettes dans Azure Search#

La navigation à facettes est un mécanisme de filtrage qui fournit une navigation autonome d'extraction dans les applications de recherche. Bien que les termes "navigation à facettes" ne vous soient pas familiers, il est presque certain que vous avez déjà utilisé cette fonctionnalité. Comme l'indique l'exemple ci-dessous, la navigation à facettes correspond tout simplement aux catégories utilisées pour filtrer les résultats.

## Apparence ##

 ![][1]
  
Les facettes peuvent vous aider à trouver ce que vous recherchez, tout en s'assurant que vous obtenez au moins un résultat. En tant que développeur, les facettes vous permettent d'exposer les critères de recherche les plus utiles pour naviguer dans votre corpus de recherche. Dans les applications de vente au détail en ligne, la navigation à facettes repose souvent sur les marques, les catégories (chaussures pour enfants), la taille, le prix, la popularité et les évaluations.

L'implémentation de la navigation à facettes varie en fonction des technologies de recherche et peut être très complexe. Dans Azure Search, la navigation à facettes est créée au moment de la requête, à l'aide de champs attribués spécifiés précédemment dans votre schéma. Dans les requêtes créées par votre application, une requête doit envoyer des *paramètres de requête de facette* afin de recevoir les valeurs de filtre de facette disponibles pour ce jeu de résultats du document. Pour réduire réellement le jeu de résultats du document, l'application doit appliquer une expression `$filter`.

En termes de développement d'application, l'écriture de code qui construit les requêtes constitue la majeure partie du travail. La plupart des comportements de l'application que vous souhaiteriez obtenir de la navigation à facettes est fournie par le service, y compris la prise en charge intégrée de la configuration des plages et l'obtention des décomptes pour les résultats de la facette. Le service comprend également des valeurs par défaut qui vous aident à éviter les structures de navigation difficiles à gérer.

Pour une expérience pratique, nous vous recommandons de consulter cet exemple sur CodePlex : [Catalogue AdventureWorks Azure Search](https://azuresearchadventureworksdemo.codeplex.com/)

Vous pouvez également regarder la [Présentation approfondie d'Azure Search](http://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). À 45:25 sur la vidéo, vous trouverez une démonstration sur la façon d'implémenter des facettes.

Cet article contient les sections suivantes :

- [Procédure de création](#howtobuildit)
- [Création de la couche de présentation](#presentationlayer)
- [Création de l'index](#buildindex)
- [Vérification de la qualité des données](#checkdata)
- [Création de la requête](#buildquery)
- [Conseils sur la façon de contrôler la navigation à facettes](#tips)
- [Navigation à facettes basée sur des valeurs de plage](#rangefacets)
- [Navigation à facettes basée sur des points géographiques](#geofacets)
- [Faites un essai](#tryitout)

##Pourquoi l'utiliser ?##
Les applications de recherche les plus efficaces ont plusieurs modèles d'interaction en plus d'une zone de recherche. La navigation à facettes constitue un point d'entrée alternatif à la recherche. Elle offre une alternative pratique à la saisie manuelle des expressions de recherche complexes.

##Les notions de base##

Si vous êtes novice en développement de la recherche, considérez que la navigation à facettes affiche les possibilités de recherche autonome. Il s'agit d'un type d'expérience de recherche détaillée, en fonction de filtres prédéfinis, utilisés pour limiter rapidement les résultats de la recherche à l'aide d'actions de type pointer et cliquer.

**Modèle d'interaction**

L'expérience de recherche pour la navigation à facettes est itérative. Donc, essayons de la comprendre comme séquence de requêtes qui se déroulent en réponse aux actions de l'utilisateur.

Le point de départ est une page d'application qui offre une navigation à facettes, généralement placée sur la périphérie. La navigation à facettes est souvent présentée sous forme d'arborescence avec des cases à cocher pour chaque valeur ou du texte interactif.

1.	Une requête envoyée à Azure Search spécifie la structure de la navigation à facettes par le biais d'un ou plusieurs paramètres de requête de facette. Par exemple, la requête peut inclure `facet=Rating`, éventuellement avec une option `:values` ou `:sort` pour affiner la présentation.
2.	La couche de présentation renvoie une page de recherche qui fournit une navigation à facettes, à l'aide des facettes spécifiées dans la requête.
3.	Face à une structure de navigation à facettes qui inclut le paramètre Évaluation, l'utilisateur clique sur « 4 » pour indiquer que seuls les produits dotés d'une évaluation minimale de 4 doivent être affichés. 
4.	En réponse, l'application envoie une requête qui inclut `$filter=Rating ge 4` 
5.	La couche de présentation met à jour la page en affichant un jeu de résultats réduit, contenant uniquement les éléments qui répondent aux nouveaux critères (dans ce cas, les produits avec une évaluation de 4 et supérieure).

Une facette est un paramètre de requête, mais ne la confondez pas avec l'entrée de requête. Elle n'est jamais utilisée comme critère de sélection dans une requête. Considérez plutôt les paramètres de requête de facette comme des entrées de la structure de navigation qui est renvoyée dans la réponse. Pour chaque paramètre de requête de facette que vous fournissez, Azure Search évaluera le nombre de documents dans les résultats partiels pour chaque valeur de la facette.

Notez le `$filter` à l'étape 4. Il s'agit d'un aspect important de la navigation à facettes. Bien que les facettes et les filtres soient indépendants dans l'API, vous aurez besoin des deux pour fournir l'expérience attendue.

**Modèles de conception**

Dans le code d'application, le modèle consiste à utiliser les paramètres de requête de facette pour renvoyer la structure de navigation à facettes, ainsi que les résultats de la facette, plus une expression $filter qui gère l'événement de clic. Considérez l'expression `$filter` comme le code derrière la réduction réelle des résultats de la recherche renvoyés à la couche de présentation. Dans le cas d'une facette Couleurs, le fait de cliquer sur la couleur Rouge est implémenté par le biais d'une expression `$filter` qui sélectionne uniquement les éléments qui ont une couleur rouge.

**Principes fondamentaux de la requête dans Azure Search**

Dans Azure Search, une requête est spécifiée par le biais d'un ou de plusieurs paramètres de requête (consultez [Rechercher des documents](http://msdn.microsoft.com/library/azure/dn798927.aspx) pour obtenir une description de chacun d'eux). Aucun des paramètres de requête n'est requis, mais vous devez en avoir au moins un pour qu'une requête soit valide.

La précision, généralement interprétée comme la possibilité de filtrer les résultats non pertinents, s'effectue par le biais d'une ou de ces deux expressions :

- **search=**<br/> La valeur de ce paramètre constitue l'expression de recherche. Il peut s'agir d'une portion de texte unique ou d'une expression de recherche complexe qui comprend plusieurs termes et opérateurs. Sur le serveur, une expression de recherche est utilisée pour la recherche en texte intégral. Elle interroge les champs pouvant faire l'objet d'une recherche dans l'index pour la correspondance des termes et renvoie les résultats classés. Si vous définissez `search` sur null, l'exécution de la requête est effectuée sur la totalité de l'index (c'est-à-dire, `search=*`). Dans ce cas, d'autres éléments de la requête, comme un `$filter` ou un profil de score, seront les principaux facteurs qui influencent les documents renvoyés (`($filter`) et dans quel ordre (`scoringProfile` ou `$orderb`).

- **$filter =**<br/> Un filtre est un mécanisme puissant pour limiter la taille des résultats de la recherche basés sur les valeurs des attributs de document spécifiques. Un `$filter` est évalué en premier, suivi de la logique de facettes qui génère les valeurs disponibles et les décomptes correspondants pour chaque valeur

Les expressions de recherche complexes diminuent les performances de la requête. Si possible, utilisez des expressions de filtre bien construites pour augmenter la précision et améliorer les performances de la requête.

Pour mieux comprendre comment un filtre ajoute plus de précision, comparez une expression de recherche complexe à une expression qui contient une expression de filtre :

- `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`

- `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Bien que les deux requêtes soient valides, la seconde est supérieure si vous cherchez des établissements autres que des « motels » avec stationnement à Seattle. La première requête repose sur ces mots spécifiques étant mentionnés ou non mentionnés dans des champs de type chaîne comme Nom, Description et tout autre champ contenant des données pouvant faire l'objet d'une recherche. La deuxième requête recherche des correspondances précises sur des données structurées et est susceptible d'être beaucoup plus précise.

Dans les applications qui incluent la navigation à facettes, vous devrez veiller à ce que chaque action de l'utilisateur sur une structure de navigation à facettes soit accompagnée d'une réduction du champ des résultats de recherche, obtenue par le biais d'une expression de filtre.

<a name="howtobuildit"></a>
# Procédure de création #

La navigation à facettes dans Azure Search est implémentée dans le code d'application qui crée la requête, mais elle s'appuie sur des éléments prédéfinis dans votre schéma.

L'attribut d'index `Facetable [true|false]`, prédéfini sur votre index de recherche, est défini sur des champs sélectionnés pour activer ou désactiver leur utilisation dans une structure de navigation à facettes. Sans `"Facetable" = true`, un champ ne peut pas être utilisé dans la navigation à facettes.

Au moment de la requête, votre code d'application crée une requête qui inclut `facet=[string]`, un paramètre de requête qui fournit le champ sur lequel baser la facette. Une requête peut avoir plusieurs facettes, comme `&facet=color&facet=category&facet=rating`, chaque facette étant séparée par un caractère d'esperluette (&).

Le code d'application doit également construire une expression `$filter` pour gérer les événements de clic dans la navigation à facettes. Un `$filter` réduit les résultats de la recherche, à l'aide de la valeur de facette comme critère de filtre.

Azure Search renvoie les résultats de la recherche, en fonction du ou des mots entrés par l'utilisateur, ainsi que les mises à jour de la structure de la navigation à facettes. Dans Azure Search, la navigation à facettes est une construction à niveau unique, avec des valeurs de facettes et des décomptes des résultats trouvés pour chaque.

La couche de présentation dans votre code fournit l'expérience utilisateur. Elle doit répertorier les éléments constitutifs de la navigation à facettes, comme l'étiquette, les valeurs, les cases à cocher et le décompte. L'API REST Azure Search est indépendante de la plateforme. Vous pouvez donc utiliser la langue et la plateforme que vous souhaitez. L'important est d'inclure des éléments d'interface utilisateur qui prennent en charge l'actualisation incrémentielle, avec l'état de l'interface utilisateur mis à jour lorsque chaque facette supplémentaire est sélectionnée.

Dans les sections suivantes, nous allons étudier comment générer chaque partie, en commençant par la couche de présentation.

<a name="presentationlayer"></a>
# Création de la couche de présentation #

Le fait de travailler à partir de la couche de présentation peut vous aider à découvrir des exigences que vous auriez pu manquer dans le cas contraire et de comprendre les capacités essentielles à l'expérience de recherche.

En termes de navigation à facettes, votre page Web ou d'application affiche la structure de navigation à facettes, détecte une entrée utilisateur sur la page et insère les éléments modifiés.

Pour les applications Web, la méthode AJAX est en général utilisée dans la couche de présentation, car elle vous permet d'actualiser les modifications incrémentielles. Vous pouvez également utiliser ASP.NET MVC ou toute autre plateforme de visualisation qui peut se connecter à un service Azure Search par le biais de HTTP. L'exemple d'application référencé dans cet article (**Catalogue AdventureWorks**) se trouve être une application ASP.NET MVC.

L'exemple suivant, extrait du fichier **index.cshtml** de l'exemple d'application, crée une structure HTML dynamique pour afficher la navigation à facettes sur la page des résultats de la recherche. Dans l'exemple, la navigation à facettes est intégrée à la page des résultats de la recherche et elle s'affiche lorsque l'utilisateur a soumis un terme de recherche.

Notez que chaque facette a une étiquette (Couleurs, Catégories, Prix), une liaison à un champ à facettes (couleur, categoryName, listPrice) et un paramètre `.count`, utilisé pour renvoyer le nombre d'éléments trouvés pour le résultat de facette en question.

  ![][2]
 

> [AZURE.TIP]Lorsque vous concevez la page de résultats, pensez à ajouter un mécanisme de suppression des facettes. Si vous utilisez des cases à cocher, les utilisateurs peuvent facilement deviner comment effacer les filtres. Pour les autres dispositions, vous devrez peut-être utiliser un modèle de navigation ou une autre approche créative. Par exemple, dans l'exemple d'application Catalogue AdventureWorks, vous pouvez cliquer sur le titre, Catalogue AdventureWorks, pour rétablir la page de recherche.

<a name="buildindex"></a>
# Création de l'index #

Les facettes sont activées sur une base de champ par champ dans l'index, au moyen de cet attribut d'index : `"Facetable": true`. Tous les types de champs pouvant être utilisés dans la navigation à facettes sont `Facetable` par défaut. Ces types de champs incluent `Edm.String`, `Edm.DateTimeOffset` et tous les types de champs numériques (globalement, tous les types de champs peuvent être utilisés comme facettes, sauf `Edm.GeographyPoint` qui ne peut pas être utilisé dans la navigation à facettes).

Lorsque vous créez un index, nous vous recommandons, pour la navigation à facettes, de désactiver explicitement les facettes pour les champs qui ne doivent jamais être utilisés comme facettes. En particulier, les champs de chaîne pour les valeurs singleton, comme un ID ou un nom de produit, doivent être définis sur `"Facetable": false` pour empêcher leur utilisation accidentelle (et inefficace) dans la navigation à facettes.

Vous trouverez ci-dessous le schéma pour l'exemple d'application Catalogue AdventureWorks (avec certains attributs en moins pour réduire la taille globale) :

 ![][3]
 
Notez que l'option `Facetable` est désactivée pour les champs de chaîne qui ne doivent pas être utilisés comme facettes, tel qu'un ID ou un nom. Le fait de désactiver les facettes lorsqu'elles ne sont pas nécessaires permet de conserver une taille d'index réduite, ce qui améliore généralement les performances.

> [AZURE.TIP]Nous vous recommandons d'inclure l'ensemble des attributs d'index pour chaque champ. Bien que l'option `Facetable` soit activée par défaut pour presque tous les champs, le fait de configurer volontairement chaque attribut peut vous aider à réfléchir aux implications de chaque décision au sein du schéma.

<a name="checkdata"></a>
# Vérification de la qualité des données 

Lorsque vous développez une application orientée données, la préparation des données est souvent l'une des tâches les plus importantes du travail. Il en va de même pour les applications de recherche. La qualité de vos données a une incidence directe quant à la façon dont la structure de navigation à facettes se matérialise comme prévu ou pas, ainsi que son efficacité pour vous aider à créer des filtres qui réduisent le jeu de résultats.

Dans Azure Search, le corpus de recherche est formé à partir de documents qui remplissent un index. Les documents fournissent les valeurs qui sont utilisées pour calculer les facettes. Si vous souhaitez trier selon la Marque ou le Prix, chaque document doit contenir des valeurs pour *BrandName* et *ProductPrice* qui sont valides, cohérentes et productives en tant qu'option de filtre.

Vous trouverez ci-dessous quelques rappels des points à appliquer :

- Pour chaque champ que vous souhaitez utiliser comme facette, demandez-vous s'il contient des valeurs qui conviennent en tant que filtres dans une recherche autonome. Les valeurs doivent être courtes, descriptives et suffisamment distinctives pour offrir un choix clair entre les différentes options.
- Fautes d'orthographe ou valeurs presque correspondantes. Si vous créez une facette Couleur et que les valeurs de champ incluent Orange et Ornage (faute d'orthographe), une facette basée sur le champ Couleur renverrait les deux options.
- Le texte à casse mixte peut également causer des dégâts dans la navigation à facettes, où orange et Orange s'afficheraient comme deux valeurs différentes. 
- Les versions au singulier et au pluriel de la même valeur peuvent entraîner une facette distincte pour chacune.

Comme vous pouvez l'imaginer, la rigueur en termes de préparation des données est un aspect essentiel d'une navigation à facettes efficace.

<a name="buildquery"></a>
# Création de la requête #

Le code que vous écrivez pour la création de requêtes doit spécifier toutes les parties d'une requête valide, y compris les expressions de recherche, les facettes, les filtres, les profils de score ; tout ce qui sert à formuler une requête. Dans cette section, nous allons explorer l'emplacement où les facettes s'intègrent dans une requête, ainsi que la façon dont les filtres sont utilisés avec des facettes pour fournir un jeu de résultats réduit.

Pour commencer, prenons un exemple. L'exemple suivant, extrait du fichier **CatalogSearch.cs**, construit une requête qui crée une navigation à facettes basée sur la Couleur, la Catégorie et le Prix.

Notez que les facettes font partie intégrante de cet exemple d'application. L'expérience de recherche dans Catalogue AdventureWorks est conçue autour de filtres et de la navigation à facettes. Ceci est évident en raison de la position de la navigation à facettes sur la page. L'exemple d'application inclut des paramètres URI pour les facettes (couleur, catégorie, prix) en tant que propriétés dans la méthode de recherche (telle que créée dans l'exemple d'application).

  ![][4]
 
Un paramètre de requête à facettes est défini sur un champ et, selon le type de données, peut être davantage paramétré par une liste délimitée par des virgules qui inclut `count:<integer>`, `sort:<>`, `intervals:<integer>` et `values:<list>`. Une liste de valeurs est prise en charge pour les données numériques lors de la définition de plages. Consultez [Rechercher des documents (API Azure Search)](http://msdn.microsoft.com/library/azure/dn798927.aspx) pour obtenir des détails sur l'utilisation.

En plus des facettes, la requête formulée par votre application doit également créer des filtres pour limiter le jeu de documents candidats basés sur une sélection de valeur de facette. Pour un magasin de cycles, la navigation à facettes fournit des indices pour répondre aux questions de type « Quels sont les couleurs, fabricants et types de cycles disponibles », tandis que le filtrage répond à des questions du type « Quels cycles sont précisément rouges, VTT, dans cette plage de prix ».

Lorsqu'un utilisateur clique sur « Rouge » pour indiquer que seuls les produits de couleur rouge doivent s'afficher, la requête suivante que l'application envoie inclut `$filter=Color eq ‘Red’`.

## Meilleures pratiques pour la navigation à facettes ##

La liste suivante récapitule certaines meilleures pratiques.

- **Précision**<br/> Utilisation de filtres. Si vous utilisez uniquement les expressions de recherche, la recherche de radical peut entraîner le renvoi d'un document qui ne contient pas la valeur de facette précise dans aucun de ses champs. 

- **Champs cibles**<br/> Dans l'exploration à facettes, vous ne voulez en général inclure que les documents qui contiennent la valeur de facette dans un champ spécifique (facette), non pas n'importe où dans tous les champs de recherche. L'ajout d'un filtre renforce le champ cible en indiquant au service de rechercher uniquement dans le champ à facette pour trouver une valeur correspondante.

- **Efficacité de l'index**<br/> Si votre application utilise exclusivement la navigation à facettes (autrement dit, aucune zone de recherche), vous pouvez marquer le champ en tant que `searchable=false`, `facetable=true` pour produire un index plus compact. En outre, l'indexation se produit uniquement sur les valeurs de facettes entières, sans césure de mots ou indexation des composants d'une valeur à plusieurs mots.

- **Performances**<br/> Les filtres réduisent le jeu de documents candidats pour la recherche et les excluent du classement. Si vous avez un grand jeu de documents, l'utilisation d'une exploration à facettes très sélective vous offrira souvent de meilleures performances.


<a name="tips"></a>
# Conseils sur la façon de contrôler la navigation à facettes #

Vous trouverez ci-dessous des conseils pour vous guider en cas de problèmes spécifiques.

**Ajouter des étiquettes pour chaque champ dans la navigation à facettes**

Les étiquettes sont généralement définies dans le code HTML ou le formulaire (**index.cshtml** dans l'exemple d'application). Il n'existe aucune API dans Azure Search pour les étiquettes de navigation à facettes ou tout autre type de métadonnées.

**Définir les champs qui peuvent servir de facette**

N'oubliez pas que le schéma de l'index détermine quels champs sont disponibles pour être utilisés comme facettes. En supposant qu'un champ puisse être utilisé comme facette, la requête spécifie quels champs utiliser comme facettes. Le champ que vous utilisez comme facette fournit les valeurs qui apparaissent sous l'étiquette.

Les valeurs qui s'affichent sous chaque étiquette sont récupérées à partir de l'index. Par exemple, si le champ de facette est *Couleur*, les valeurs disponibles pour le filtrage supplémentaire seront les valeurs de ce champ (Rouge, Noir et ainsi de suite).

Pour les valeurs de type Numérique et DateHeure uniquement, vous pouvez définir explicitement des valeurs sur le champ de facette (par exemple, `facet=Rating,values:1|2|3|4|5`). Une liste de valeurs est autorisée pour ces types de champs afin de simplifier la séparation des résultats de la facette en plages contiguës (plages basées sur des valeurs numériques ou des périodes de temps).

**Ajuster les résultats de la facette**

Les résultats de la facette sont des documents trouvés dans les résultats de la recherche qui correspondent à un terme de la facette. Dans l'exemple suivant, dans les résultats de la recherche pour *informatique en nuage*, 254 éléments ont également la *spécification interne* comme type de contenu. Les éléments ne sont pas nécessairement mutuellement exclusifs. Si un élément répond aux critères des deux filtres, il est compté dans chacun d'eux. Cela est possible lors de l'utilisation des facettes sur les champs `Collection(Edm.String)`, qui sont souvent utilisés pour implémenter le balisage de document.

		Search term: "cloud computing"
		Content type
		   Internal specification (254)
		   Video (10) 

En général, si vous trouvez que les résultats de la facette sont toujours trop volumineux, nous vous recommandons d'ajouter plus de filtres, comme expliqué dans les sections précédentes, afin de donner aux utilisateurs de votre application davantage d'options pour affiner la recherche.

**Limiter les éléments dans la navigation à facettes**

Pour chaque champ à facettes dans l'arborescence de navigation, il existe une limite par défaut de 10 valeurs. Cette valeur par défaut est judicieuse pour les structures de navigation, car elle permet de conserver une taille gérable pour la liste des valeurs. Vous pouvez remplacer la valeur par défaut en affectant une valeur à compter.

- `&facet=city,count:5` spécifie que seules les 5 premières villes trouvées dans les résultats en tête du classement sont renvoyées en tant que résultat de la facette. Avec le terme de recherche « aéroport » et 32 correspondances, si la requête spécifie `&facet=city,count:5`, seules les cinq premières villes uniques avec le plus de documents dans les résultats de la recherche sont incluses dans les résultats de la facette.

Notez la différence entre les résultats de la recherche et les résultats de la facette. Les résultats de la recherche sont tous les documents qui correspondent à la requête. Les résultats de la facette sont les correspondances pour chaque valeur de facette. Dans l'exemple, les résultats de la recherche incluront des noms de villes qui ne sont pas dans la liste de classification de la facette (5, dans notre exemple). Les résultats qui sont filtrés par le biais de la navigation à facettes deviennent visibles lorsque l'utilisateur efface les facettes ou choisit d'autres facettes en plus de « Ville ».

> [AZURE.NOTE]Traiter de `count` lorsqu'il existe plus d'un type peut prêter à confusion. Le tableau suivant offre un bref résumé de l'utilisation du terme dans l'API Azure Search, un exemple de code et la documentation.

- `@colorFacet.count`<br/> Dans le code de présentation, un paramètre de décompte doit s'afficher sur la facette. Il est utilisé pour afficher le nombre de résultats de la facette. Dans les résultats de la facette, le décompte indique le nombre de documents qui correspondent au terme ou à la plage de la facette.

- `&facet=City,count:12`<br/> Dans une requête de facette, vous pouvez définir le décompte sur une valeur. La valeur par défaut est 10, mais vous pouvez définir une valeur supérieure ou inférieure. Le paramètre `count:12` renvoie les 12 premières correspondances dans les résultats de la facette selon le décompte de documents.

- « `@odata.count` »<br/> Dans la réponse de la requête, cette valeur indique le nombre d'éléments correspondants dans les résultats de la recherche. En moyenne, il est supérieur à la somme de tous les résultats de la facette combinés, en raison de la présence d'éléments qui correspondent au terme de la recherche, mais sans correspondance avec la valeur de la facette.


**Niveaux dans la navigation à facettes**

Comme mentionné, il n'existe aucune prise en charge directe de l'imbrication des facettes dans une hiérarchie. Dès le départ, la navigation à facettes ne prend en charge qu’un seul niveau de filtres. Toutefois, des solutions de contournement existent. Vous pouvez encoder une structure hiérarchique de facette dans une `Collection(Edm.String)` avec un point d'entrée par hiérarchie. L'implémentation de cette solution de contournement n'est pas abordée dans cet article, mais vous pouvez vous renseigner sur les collections dans [OData par exemple](http://msdn.microsoft.com/library/ff478141.aspx).

**Validation des champs**

Si vous générez la liste de facettes dynamiquement en fonction d'une entrée utilisateur non fiable, vous devez soit valider que les noms des champs à facettes sont valides, soit contourner les noms lors de la génération des URL à l'aide de `Uri.EscapeDataString()` dans .NET ou son équivalent dans la plateforme de votre choix.

**Décomptes dans les résultats de la facette**

Lorsque vous ajoutez un filtre à une requête à facettes, vous pouvez souhaiter conserver l'instruction de facette (par exemple, `facet=Rating&$filter=Rating ge 4`). Techniquement, le paramètre facette=Évaluation n'est pas nécessaire, mais le fait de le conserver renvoie les décomptes des valeurs de facettes pour les évaluations de 4 et supérieures. Par exemple, si un utilisateur clique sur « 4 » et que la requête inclut un filtre pour une valeur supérieure ou égale à « 4 », des décomptes sont renvoyés pour chaque évaluation égale à 4 et supérieure.

**Implications en matière de partitionnement sur les décomptes de facettes**

Dans certaines circonstances, il est possible que les décomptes de facettes ne correspondent pas aux jeux de résultats (consultez [Navigation à facettes dans Azure Search (publication du forum)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

Les décomptes de facettes peuvent être erronés en raison de l'architecture de partitionnement. Chaque index de recherche a plusieurs partitions et chacune d'elles indique les N premières facettes par décompte de document, qui est ensuite combiné en un résultat unique. Si certaines partitions ont beaucoup de valeurs correspondantes, tandis que d'autres en ont moins, il est possible que certaines valeurs de facettes soient manquantes ou sous-comptabilisées dans les résultats.

Ce comportement peut changer à tout moment mais si vous rencontrez ce problème aujourd'hui, vous pouvez le contourner en gonflant artificiellement le décompte :<number> sur un nombre très élevé pour appliquer la déclaration complète depuis chaque partition. Si la valeur de décompte : est supérieure ou égale au nombre de valeurs uniques dans le champ, vous êtes sûr d'obtenir des résultats précis. Toutefois, lorsque les décomptes de documents sont réellement élevés, les performances baissent, alors utilisez cette option judicieusement.

<a name="rangefacets"></a>
# Navigation à facettes basée sur une plage de valeurs #

L'utilisation de facettes sur des plages est une condition d'application de recherche courante. Les plages sont prises en charge pour les données numériques et les valeurs DateHeure. Vous pouvez en savoir plus sur chaque approche dans [Rechercher des documents (API Azure Search)](http://msdn.microsoft.com/library/azure/dn798927.aspx).

Azure Search simplifie la création de plage en fournissant deux approches pour calculer une plage. Pour les deux approches, Azure Search crée les plages appropriées avec les entrées que vous avez fournies. Par exemple, si vous spécifiez des valeurs de plage de 10|20|30, Azure Seach crée automatiquement les plages 0-10, 10-20, 20-30. L'exemple d'application supprime les intervalles qui sont vides.

**Approche 1 : utiliser le paramètre d'intervalle**<br/> Pour définir les facettes de prix par incréments de 10 $, vous devez spécifier : `&facet=price,interval:10`


**Approche 2 : utiliser une liste de valeurs**<br/> Pour les données numériques, vous pouvez utiliser une liste de valeurs. Prenez en compte la plage de facette pour listPrice, indiquée comme suit :

  ![][5]

La plage est spécifiée dans le fichier **CatalogSearch.cs** à l'aide d'une liste de valeurs :

    facet=listPrice,values:10|25|100|500|1000|2500

Chaque plage est créée avec 0 comme point de départ, une valeur de la liste comme point de terminaison, puis la plage précédente en moins pour créer des intervalles discrets. Azure Search effectue cela dans le cadre de la navigation à facettes. Vous n'avez pas à écrire du code pour structurer chaque intervalle.

### Création d'un filtre pour les plages de facettes ###

Pour filtrer les documents en fonction d'une plage sélectionnée par l'utilisateur, vous pouvez utiliser les opérateurs de filtre `"ge"` et `"lt"` dans une expression en deux parties qui définit les points de terminaison de la plage. Par exemple, si l'utilisateur choisit la plage 10-25, le filtre serait `$filter=listPrice ge 10 and listPrice lt 25`.

Dans l'exemple d'application, l'expression de filtre utilise les paramètres **priceFrom** et **priceTo** pour définir les points de terminaison. La méthode **BuildFilter** dans **CatalogSearch.cs** contient l'expression de filtre qui vous donne les documents dans une plage.

  ![][6]

<a name="geofacets"></a>
# Navigation à facettes basée sur des points géographiques #

Il est courant de voir des filtres qui vous aident à choisir un magasin, un restaurant ou une destination en fonction de sa proximité à votre emplacement actuel. Ce type de filtre peut ressembler à la navigation à facettes, mais c'est tout simplement un filtre. Nous le mentionnons ici pour ceux d'entre vous qui recherchent spécifiquement des conseils d'implémentation pour ce problème de conception particulier.

Il existe deux fonctions géospatiales dans Azure Search, **geo.distance** et **geo.intersects**.

- La fonction **geo.distance** renvoie la distance en kilomètres entre deux points, l'un étant un champ et l'autre une constante considérée comme partie du filtre. 

- La fonction **geo.intersects** renvoie la valeur « true » si un point donné se trouve dans un polygone donné, où le point est un champ et le polygone est spécifié en tant que liste constante de coordonnées considérées comme partie du filtre.

Vous trouverez des exemples de filtres dans [Syntaxe d'expression OData (Azure Search)](http://msdn.microsoft.com/library/azure/dn798921.aspx). Pour en savoir plus sur la recherche géospatiale, consultez [Création d'une application de recherche géospatiale dans Azure Search](search-create-geospatial.md).

<a name="tryitout"></a>
# Faites un essai #

La démonstration Adventure Works pour Azure Search sur Codeplex contient les exemples référencés dans cet article. Lorsque vous utilisez les résultats de la recherche, observez les modifications de construction de la requête dans l'URL. Cette application ajoute des facettes à l'URI lors de la sélection de chaque.

1.	Configurez l'exemple d'application (consultez [Création de votre première application pour obtenir des instructions](search-create-first-solution.md)). 

	Notez le schéma défini dans le fichier Program.cs du projet CatalogIndexer. Il spécifie les champs pouvant être utilisés comme facettes pour couleur, listPrice, taille, poids, categoryName et modelName. Seuls certains d'entre eux (couleur, listPrice, categoryName) sont implémentés dans la navigation à facettes.

3.	Exécutez l'application.

	En premier lieu, seule la zone de recherche est visible. Vous pouvez cliquez sur le bouton Rechercher tout de suite pour obtenir tous les résultats ou entrez un terme à rechercher.

	![][7]
 
4.	Entrez un terme à rechercher, par exemple « vélo », puis cliquez sur **Rechercher**. La requête s'exécute rapidement.
 
	Une structure de navigation à facettes est également renvoyée avec les résultats de la recherche. Dans l'URL, les facettes pour Couleurs, Catégories et Prix sont égales à « null ». Dans la page de résultats de la recherche, la structure de la navigation à facettes inclut des décomptes pour chaque résultat de facette.

	 ![][8]
 
5.	Cliquez sur une couleur, une catégorie et la plage de prix. Les facettes sont égales à « null » pour la recherche initiale, mais lorsqu'elles prennent des valeurs, les éléments qui ne correspondent plus sont supprimés des résultats de la recherche. Notez que les modifications apportées à votre requête sont reflétées dans l'URI.

	![][9]
 
6.	Pour effacer la requête à facettes afin de tester des comportements de requête différents, cliquez sur **Catalogue AdventureWorks** en haut de la page.

	![][10]
 
<a name="nextstep"></a>
# Étape suivante #

Pour tester vos connaissances, vous pouvez ajouter un champ de facette pour *modelName*. L'index est déjà configuré pour cette facette, donc aucune modification de l'index n'est requise. Mais vous devrez modifier le code HTML pour inclure une nouvelle facette pour Modèles et ajouter le champ de facette dans le constructeur de la requête.

Pour plus d'informations sur les principes de conception pour la navigation à facettes, nous vous recommandons les liens suivants :

- [Conception pour la recherche à facettes](http://www.uie.com/articles/faceted_search/)
- [Modèles de conception : navigation à facettes](http://alistapart.com/article/design-patterns-faceted-navigation)

<!--Anchors-->
[How to build it]: #howtobuildit
[Build the presentation layer]: #presentationlayer
[Build the index]: #buildindex
[Check for data quality]: #checkdata
[Build the query]: #buildquery
[Tips on how to control faceted navigation]: #tips
[Faceted navigation based on range values]: #rangefacets
[Faceted navigation based on GeoPoints]: #geofacets
[Try it out]: #tryitout

<!--Image references-->
[1]: ./media/search-faceted-navigation/Facet-1-slide.PNG
[2]: ./media/search-faceted-navigation/Facet-2-CSHTML.PNG
[3]: ./media/search-faceted-navigation/Facet-3-schema.PNG
[4]: ./media/search-faceted-navigation/Facet-4-SearchMethod.PNG
[5]: ./media/search-faceted-navigation/Facet-5-Prices.PNG
[6]: ./media/search-faceted-navigation/Facet-6-buildfilter.PNG
[7]: ./media/search-faceted-navigation/Facet-7-appstart.png
[8]: ./media/search-faceted-navigation/Facet-8-appbike.png
[9]: ./media/search-faceted-navigation/Facet-9-appbikefaceted.png
[10]: ./media/search-faceted-navigation/Facet-10-appTitle.png

<!--Link references-->
[Designing for Faceted Search]: http://www.uie.com/articles/faceted_search/
[Design Patterns: Faceted Navigation]: http://alistapart.com/article/design-patterns-faceted-navigation
[Create your first application]: search-create-first-solution.md
[OData expression syntax (Azure Search)]: http://msdn.microsoft.com/library/azure/dn798921.aspx
[Create a geospatial search application in Azure Search]: search-create-geospatial.md
[Azure Search Adventure Works Demo]: https://azuresearchadventureworksdemo.codeplex.com/
[http://www.odata.org/documentation/odata-version-2-0/overview/]: http://www.odata.org/documentation/odata-version-2-0/overview/
[Faceting on Azure Search forum post]: ../faceting-on-azure-search.md?forum=azuresearch
[Search Documents (Azure Search API)]: http://msdn.microsoft.com/library/azure/dn798927.aspx


<!--HONumber=54--> 