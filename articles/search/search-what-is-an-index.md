<properties
	pageTitle="Index dans Azure Search | Microsoft Azure | Service de recherche cloud hébergé"
	description="Qu’est-ce qu’un index dans Azure Search et comment l’utiliser ?"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/09/2015"
	ms.author="heidist"/>

# Index dans Azure Search
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

Azure Search est un service de recherche cloud hébergé qui fournit un moteur de recherche, des fonctions de recherche qui permettent une expérience de type Bing ou Google dans votre application personnalisée, API .NET et REST pour l’intégration d’Azure Search avec votre application web ou de bureau, et le stockage de données avec possibilité de recherche sous la forme d’un *index*.

##Qu’est-ce qu’un index ?

Un *index* est un stockage permanent des *documents* et d’autres éléments utilisés par un service Azure Search. Les documents sont une unité de base de données dans lesquelles on peut faire des recherches. Un détaillant peut avoir un document correspondant à chaque référence SKU, une organisation diffusant des actualités disposer d’un document pour illustrer chaque article, et une entreprise de médias associer un document à chaque vidéo ou morceau de musique de sa bibliothèque. Pour comparer avec des éléments de base de données plus familiers, d’un point de vue conceptuel, un*index* est similaire à une *table*, et les *documents* équivalent plus ou moins aux *lignes* d’une table.

L’index qui contient des documents est un jeu de données plat (en général, un sous-ensemble de données créées ou recueillies pendant les opérations normales via des procédures informatisées, notamment des transactions commerciales, la publication de contenu, les achats) souvent stocké dans des bases de données relationnelles ou NoSQL. Un index permet d’accéder à des documents lorsque vous extrayez ou transmettez un jeu de données contenant des lignes provenant d’autres sources de données à votre index.

Dans les limites des opérations de moteur de recherche, un index contient toutes les données dans lesquelles faire des recherches utilisées pour traiter un index, exécuter une requête ou retourner l’un des éléments suivants : une liste de résultats, une structure de navigation à facettes, une page de détails pour un document unique. Dans Azure Search, un index peut également contenir des données dans lesquelles il est impossible de faire des recherches utilisées en interne dans les expressions de filtre ou dans les profils de score fournissant des critères utilisés pour améliorer un score de classement de recherche.

##Quand créer un index ?

Dans le cadre de l’approvisionnement d’un service de recherche pour l’utiliser avec votre application, vous devez créer un index. La création d’un index est une tâche centrée sur la définition de son schéma. Il comprend au minimum la définition de champs et la configuration d’attributs. Si vous le souhaitez, vous pouvez étendre un index pour y inclure des profils de score, des suggestions et des valeurs personnalisées par défaut.

Pour créer un schéma définissant un index, vous pouvez utiliser le portail ou écrire un code émettant un appel dans le kit de développement logiciel .NET ou l’API REST.

Une fois que vous avez défini le schéma et créé l’index, le remplissage est une opération distincte. Pour plus d’informations sur la réception des données après la création de l’index, consultez [Importer des données dans Azure Search](search-what-is-data-import.md).

##Spécification du schéma JSON d’un index

Les sections principales d’un index de recherche Azure, telles qu’elles sont articulées dans le format d’échange de données JSON, se présentent comme suit.

|Élément de schéma|Description|
|--------------|-----------|
|Collection de champs|Les champs définissent un document. Un jeu de données que vous extrayez ou transmettez dans un index doit fournir des valeurs ou des valeurs NULL pour chaque champ compatibles avec le type de données et la longueur de champ décrits dans le schéma.|
|[Attributs](#index-attributes)|Propriétés ou annotations d’un champ qui spécifie le type, la longueur, la valeur et les comportements de données autorisés pour le champ. Vous pouvez spécifier si un champ offre des possibilités de recherche, consultable, affichage dans les résultats de recherche ou triable, le tout sur une base champ par champ. Vous pouvez également spécifier l’analyseur de langage de remplacement au niveau du champ.
|[Profils de score](https://msdn.microsoft.com/library/azure/dn798928.aspx)|Critères utilisés pour améliorer le classement des correspondances présentant le plus grand nombre de caractéristiques définies par le profil. Par exemple, supposons qu’un terme de recherche correspond à un nom de produit et une description de produit, il se peut que vous vouliez que lesdits correspondances et noms de produit soient classés plus haut que ceux dans une description.|
|[Générateurs de suggestions](https://msdn.microsoft.com/library/azure/mt131377.aspx)|Également connus sous le nom de requêtes à saisie ou frappe automatiques, ils sont définis comme une section dans l’index.|
|[Analyseurs de langage par défaut]()|Les analyseurs de langage par défaut peuvent aussi être spécifiés au niveau de l’index, être applicables globalement à tous les champs.|
|Options CORS|Le cas échéant, permet de partage de ressources cross-origin, dans lesquelles les requêtes d’une ressource utilisée par une page web émise sur une limite de domaine. CORS est toujours désactivée, à moins que vous l’activiez spécifiquement pour vos index.|

<a name="index-attributes"></a>
##Attributs d’index

Les attributs sont définis sur des champs individuels pour spécifier la façon dont le champ est utilisé. Le tableau suivant énumère les attributs que vous pouvez spécifier.

|**Propriété**|Description|
|------------|-----------|
|**Affichable dans les résultats d’une recherche**|Définit si un champ peut être retourné dans un résultat de recherche.|
|**Filtrable**|Permet d’utiliser le champ dans des requêtes **$filter**.|
|**Triable**|Permet d’utiliser le champ en tant que colonne de tri dans le résultat.|
|**À choix multiple**|Permet d’utiliser un champ pour le filtrage autonome dans une structure de navigation à facettes par un utilisateur. En général, les champs contenant des valeurs répétitives que vous pouvez utiliser pour regrouper plusieurs documents (par exemple, plusieurs documents appartenant à une seule marque ou catégorie de service) sont les mieux adaptés en tant que facettes.|
|**Clé**|Chaîne fournissant un ID unique à chaque document utilisé pour rechercher des documents. Chaque index doit avoir une clé. Un seul champ peut être la clé et il doit être défini sur Edm.String.|
|**Possibilité de recherche**|Indique que le champ peut faire l’objet d’une recherche en texte intégral.|


##Comment les index sont-ils utilisés dans Azure Search ?

Toutes les opérations liées aux données effectuées par Azure Search consomment ou retournent des données à partir d’un index. Il n’existe aucune exception : vous ne pouvez pas référencer le service vers d’autres sources de données en dehors de l’index pour renvoyer les données externes dans une réponse émise par votre service de recherche.

Pour les applications qui s’accumulent et opèrent sur les données, telles que les transactions commerciales d’une application de détail en ligne stockant des données dans une base de données OLTP, un index Azure Search est une source de données supplémentaire de votre solution globale. L’index est un stockage de données dédié utilisé uniquement pour votre service de recherche. Le fait d’avoir une source de données dédiée à proximité de votre service de recherche garantit la cohérence des résultats de recherche, réduit la volatilité, réduit le nombre d’allers-retours entre votre application et Azure Search et améliore les performances d’ensemble des opérations de recherche, car il n’existe pas de concurrence pour les ressources ou les données.

##Instructions de définition d’un schéma

Les schémas sont créés en tant que structures JSON. Vous devez créer un index pour chacune des solutions personnalisées que vous intégrez à Azure. Vous ne pouvez pas associer ou joindre plusieurs index, mais vous pouvez créer des index complexes pouvant recevoir une gamme de scénarios de recherche requis par votre solution. Par exemple, si vos applications prennent en charge plusieurs langues, l’ensemble de champs de votre index peut présenter des variantes linguistiques pour chaque champ.

Lorsque vous concevez votre index, prenez le temps lors de la phase de planification de réfléchir chaque décision. La modification d’un index une fois déployé implique la reconstruction et le rechargement des données. Si vous créez un index dans le code, cette étape sera plus facile si vous la créez manuellement dans le portail.

Avoir une solide connaissance des données sources d’origine est essentiel pour la création d’un bon schéma. Vous voudrez savoir quels types de données correspondent au champ à utiliser comme *clé* identifiant de manière unique chaque document dans l’index et quels champs doivent être exposés dans une liste ou une page de détails.

**Démarrage avec des données relationnelles**

Des données relationnelles peuvent être difficiles à transformer un jeu de données plat. Si possible, essayez d’utiliser l’entrepôt de données ou la base de données de rapports de votre entreprise si vous en avez un. Il s’agit généralement du meilleur choix, car le modèle est généralement dénormalisé. Dans le cas contraire, vous devrez compter sur des requêtes pour obtenir l’ensemble de lignes dont vous avez besoin. Pour obtenir un exemple et savoir comment procéder, consultez [Modélisation de la base de données d’inventaire AdventureWorks pour Azure Search](http://blogs.technet.com/b/onsearch/archive/2015/09/08/modeling-the-adventureworks-inventory-database-for-azure-search.aspx).

**Notamment les données binaires**

Vous souhaitez récupérer une image, un contenu audio ou vidéo dans vos résultats de recherche. Dans ce cas, les fichiers doivent être stockés ailleurs, avec l’index incluant un champ représentatif avec une URL vers une ressource. Tout contenu, notamment le contenu binaire sur lequel il est impossible de faire de recherches doit être stocké dans un espace de stockage moins cher, puis référencé par un URI dans un champ de votre index.

**Synchronisation des données**

Comme vous pouvez l’imaginer, l’index doit être synchronisé régulièrement avec d’autres sources de données utilisées dans votre solution. Dans un catalogue de vente au détail en ligne, la base de données d’inventaire qui enregistre les transactions commerciales doit contenir les mêmes références, tarifs et disponibilités comme données exposées via les résultats de recherche. En fonction de la latence acceptable pour votre solution, vous devez découvrir que la synchronisation de données peut avoir lieu chaque semaine, une fois par jour, ou quasiment en temps réel en utilisant l’inscription simultanée dans la base de données d’inventaire et un index d’Azure Search. [Importer des données dans Azure Search](search-what-is-data-import.md) explique en détail les options disponibles.

## Stockage dans le cloud.

Étant donné qu’Azure Search est un service de recherche entièrement géré, le stockage de données est traité comme une opération interne. En tant que programmeur, il est important de comprendre que vous ne pouvez pas ignorer les API Azure Search pour vous connecter directement à l’index, ni contrôler la taille ou l’état de l’index ou de la santé, sauf via les notifications de portail ou les appels d’API. Il n’existe aucune fonctionnalité de sauvegarde ou de restauration d’index, ni d’optimisation ou de réglage des performances au niveau de la couche de stockage. En coulisses, les données sont stockées en tant que stockage d’objets blobs, mais le meilleur moyen de penser au stockage physique et à la gestion d’un index est de le considérer comme un détail d’implémentation qui pourrait changer à l’avenir. Un des principaux avantages du service est l’approche sans intervention physique de la gestion des ressources de calcul de votre application de recherche.

Si les exigences en matière de volume de requête ou de stockage de données changent au fil du temps, vous pouvez augmenter ou diminuer la capacité en ajoutant ou en déplaçant des réplicas et des partitions. Pour plus d’informations, consultez [Gérer votre service de recherche dans Azure](search-manage.md) ou [Limites de service](search-limits-quotas-capacity.md).

<!---HONumber=Nov15_HO4-->