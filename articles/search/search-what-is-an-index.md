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

Azure Search est un service de recherche cloud qui fournit un moteur de recherche, des fonctions de recherche qui active une expérience de type Bing ou Google dans votre application personnalisée .NET et l’API REST pour l’intégration Azure Search avec votre application web ou bureau, et le stockage de données et les données avec possibilités de recherche sous la forme d’*index*.

##Qu’est-ce qu’un index ?

Un *index* est un stockage permanent des documents et d’autres éléments utilisés par un service Azure Search.

Plus précisément, elle contient toutes les données de recherche utilisées dans un index de traitement, l’exécution d’une requête ou le retour d’une liste de résultats de recherche, une structure de navigation à choix multiples, ou une page de détails de votre application. Dans Azure Search , un index contient également des données non consultables à utiliser dans les expressions de filtre ou les profils de score (utilisés pour affiner une requête ou améliorer un score de classement de recherche), ainsi que les structures étendues à votre application personnalisée qui s’intègre à Azure Search.

Si vous êtes familiarisé avec les concepts de base de données, un index est conceptuellement semblable à une table, et les documents sont à peu près équivalents aux lignes d’une table.

Les sections principales d’un index, qui sont décrites plus loin dans cet article, sont les suivantes :-champs qui définissent le corps d’un *document*, avec des comportements spécifiques, des profils de score, des suggestions, des options CORS, un analyseur par défaut

Dans le cadre de l’approvisionnement d’un service de recherche pour l’utiliser avec votre application, vous devez créer un index. La création d’un index est une tâche centrée sur la définition de son schéma. Il comprend au minimum la définition de champs et la configuration d’attributs. Vous pouvez également définir les profils de score et des suggestions pour améliorer l’expérience de recherche.

Pour créer un schéma, vous pouvez utiliser le portail ou écrire du code qui appelle dans le kit de développement logiciel .NET ou l’API REST.

Une fois que vous avez défini le schéma et créé l’index, le remplissage est une opération distincte. Pour plus d’informations sur la réception des données après la création de l’index, consultez [Importer des données dans Azure Search](search-what-is-data-import.md).

##Comment les index sont-ils utilisés dans Azure Search ?

Toutes les opérations liées aux données effectuées par Azure Search consomment ou retournent des données à partir d’un index. Il n’existe aucune exception : vous ne pouvez pas référencer le service vers d’autres sources de données à côté de l’index pour renvoyer les données externes dans une réponse.

Pour les applications qui s’accumulent et opèrent sur les données, telles que les transactions commerciales d’une application de détail en ligne stockant des données dans une base de données OLTP, un index Azure Search est une source de données supplémentaire dans votre solution globale. L’index est un stockage de données dédié utilisé uniquement pour votre service de recherche. Le fait d’avoir une source de données dédiée à proximité de votre service garantit la cohérence des résultats de recherche, réduit la volatilité, réduit le nombre d’allers-retours entre votre application et Azure Search et améliore les performances générales des opérations de recherche, car il n’existe pas de concurrence pour les ressources ou les données.

##Instructions de définition d’un schéma

Les schémas sont créés en tant que structures JSON. Vous devez créer un index pour chacune des solutions personnalisées que vous intégrez à Azure. Vous ne pouvez pas associer ou joindre plusieurs index, mais vous pouvez créer des index complexes pouvant recevoir une gamme de scénarios de recherche requis par votre solution. Par exemple, si vos applications prennent en charge plusieurs langues, l’ensemble de champs de votre index peut présenter des variantes linguistiques pour chaque champ.

Lorsque vous concevez votre index, prenez le temps lors de la phase de planification de réfléchir chaque décision. La modification d’un index une fois déployé implique la reconstruction et le rechargement des données. Si vous créez un index dans le code, cette étape sera plus facile si vous la créez manuellement dans le portail.

Avoir une solide connaissance des données sources d’origine est essentiel pour la création d’un bon schéma. Vous voudrez savoir quels types de données correspondent au champ à utiliser comme *clé* identifiant de manière unique chaque document dans l’index et quels champs doivent être exposés dans une liste ou une page de détails.

Le contenu d’image, audio ou vidéo doit être stocké ailleurs, aussi longtemps que l’index inclut un champ représentatif avec une URL vers une ressource. Tout contenu, notamment le contenu binaire sur lequel il est impossible de faire de recherches doit être stocké ailleurs, puis référencé par un URI dans un champ de votre index.

Comme vous pouvez l’imaginer, l’index doit être synchronisé régulièrement avec d’autres sources de données utilisées dans votre solution. Dans un catalogue de vente au détail en ligne, la base de données d’inventaire qui enregistre les transactions commerciales doit contenir les mêmes références, tarifs et disponibilités comme données exposées via les résultats de recherche. En fonction de la latence acceptable pour votre solution, vous devez découvrir que la synchronisation de données peut avoir lieu chaque semaine, une fois par jour, ou quasiment en temps réel en utilisant l’inscription simultanée dans la base de données d’inventaire et un index d’Azure Search. [Importer des données dans Azure Search](search-what-is-data-import.md) explique en détail les options disponibles.

##Éléments du schéma

Les index se composent de champs avec attributs, de valeurs de données et autres éléments qui façonnent la façon dont les résultats de requêtes et les résultats sont présentés.

- Les champs définissent le corps d’un document. Un détaillant en ligne veut des documents pour chaque référence SKU, une organisation diffusant des actualités souhaitera un document pour illustrer chaque article, et un portail infomédia voudra des documents pour chaque fournisseur ou vitrine.
- Les attributs (ou propriétés) sont des annotations sur le champ qui spécifient le type, la longueur, la valeur et les comportements de données autorisés pour ce champ. Vous pouvez spécifier si un champ offre des possibilités de recherche, consultable, affichage dans les résultats de recherche ou triable, le tout sur une base champ par champ. Vous pouvez également spécifier l’analyseur de langage à utiliser au niveau du champ. Une *clé* est un champ réservé dans le schéma qui fournit un identificateur unique pour chaque document dans la collection de champs.
- Les profils de score sont des critères utilisés pour améliorer le classement des correspondances ayant plus de caractéristiques définies par le profil. Par exemple, supposons qu’un terme de recherche correspond à un nom de produit et une description de produit, il se peut que vous vouliez que lesdits correspondances et noms de produit soient classés plus haut que ceux dans une description.
- Les suggestions, également connues sous le nom de requêtes à saisie ou frappe automatique sont définies comme section dans l’index.
- Les analyseurs de langue par défaut peuvent être spécifiés au niveau de l’index, être applicables globalement à tous les champs.
- Les options de CORS activent les scripts inter-domaines et font également partie d’une définition d’index.

## Attributs d’index

|**Propriété**|Description|
|------------|-----------|
|**Affichable dans les résultats d’une recherche**|Définit si un champ peut être retourné dans un résultat de recherche.|
|**Filtrable**|Permet d’utiliser le champ dans des requêtes **$filter**.|
|**Triable**|Permet d’utiliser le champ comme option de tri.|
|**À choix multiple**|Permet d’utiliser un champ pour le filtrage autonome dans une structure de navigation à facettes. En général, les champs contenant des valeurs répétitives que vous pouvez utiliser pour regrouper plusieurs documents (par exemple, plusieurs documents appartenant à un seul produit ou une catégorie de service) sont les plus adaptés en tant que facettes.|
|**Clé**|Fournit un ID unique pour chaque document, utilisé pour rechercher des documents. Chaque index doit avoir une clé. Un seul champ peut être la clé et il doit être défini sur Edm.String.|
|**Possibilité de recherche**|Indique que le champ peut faire l’objet d’une recherche en texte intégral.|

## Stockage dans le cloud.

Étant donné qu’Azure Search est un service de recherche entièrement géré, le stockage de données est traité comme une opération interne. En tant que programmeur, il est important de comprendre que vous ne pouvez pas ignorer les API Azure Search pour vous connecter directement à l’index, ni contrôler la taille ou l’état de l’index ou de la santé, sauf via les notifications de portail ou les appels d’API. Il n’existe aucune fonctionnalité de sauvegarde ou de restauration d’index, ni d’optimisation ou de réglage des performances au niveau de la couche de stockage. En coulisses, les données sont stockées en tant que stockage d’objets blobs, mais le meilleur moyen de penser au stockage physique et à la gestion d’un index est de le considérer comme un détail d’implémentation qui pourrait changer à l’avenir. Un des avantages principaux du service est l’approche sans intervention de la gestion des données.

Si les exigences en matière de volume de requête ou de stockage de données changent au fil du temps, vous pouvez augmenter ou diminuer la capacité en ajoutant ou en déplaçant des réplicas et des partitions. Pour plus d’informations, consultez [Gérer votre service de recherche dans Azure](search-manage.md) ou [Limites de service](search-limits-quotas-capacity.md).

<!---HONumber=Nov15_HO3-->