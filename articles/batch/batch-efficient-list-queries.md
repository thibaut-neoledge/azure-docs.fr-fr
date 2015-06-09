<properties 
	pageTitle="Requêtes de liste efficace" 
	description="Apprenez à réduire le nombre d'éléments renvoyés dans une liste, ainsi que pour réduire la quantité d'informations renvoyées pour chaque élément" 
	services="batch" 
	documentationCenter="" 
	authors="davidmu1" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-windows" 
	ms.workload="multiple"
	ms.date="05/09/2015" 
	ms.author="davidmu"/>

# Requêtes de liste efficace

Les méthodes suivantes sont des exemples d'opérations de pratiquement toutes les applications à l'aide de commandes Azure doit effectuer et a souvent fréquemment effectuer :

- [ListTasks](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listtasks.aspx)
- [ListJobs](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listjobs.aspx)
- [ListWorkitems](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listworkitems.aspx)
- [ListPools](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.ipoolmanager.listpools.aspx)
- [ListCertificates](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icertificatemanager.listcertificates.aspx)

La surveillance est même utiliser la casse ; détermination de la capacité et l'état d'un pool requiert que pool de tous les ordinateurs virtuels est à interroger, par exemple. Un autre exemple consisterait à interroger les tâches d'un travail déterminer si toutes les tâches sont toujours en file d'attente. Dans certains cas, un ensemble complet de données est requis, mais dans d'autres cas que le nombre du nombre total d'éléments ou d'éléments dans un état particulier est requis.

Il est important de savoir que le nombre d'éléments qui peuvent être retournées peut être très volumineux et la taille des données nécessaires pour représenter la liste des éléments peut également être très volumineuse. Interrogation simple pour un grand nombre d'éléments de résultats dans les réponses volumineux peuvent entraîner un certain nombre de problèmes :

- Temps de réponse de lot API peuvent devenir trop lents. Plus que le nombre d'éléments plus le temps de requête requis par le service de traitement par lots. Grand nombre d'éléments doivent être divisée en blocs et donc plusieurs appels d'API de service peuvent doivent être effectuées par la bibliothèque cliente au service pour obtenir tous les éléments de la 1 liste.
- API de l'application qu'appelante lot prend plus de temps de traitement plus d'éléments il sont au processus.
- Plus de mémoire seront consommées dans l'application appelant lot qu'il sont a plusieurs éléments et/ou des éléments plus importants.
- Plusieurs éléments et/ou des éléments plus importants permettra d'augmenter le trafic réseau. Cela prendra plus de temps à transférer et, en fonction de l'architecture d'application, peut entraîner des frais de réseau accrue pour les données transférées en dehors de la région du compte de traitement par lots.

L'API de lot fournit la possibilité des réduire le nombre d'éléments renvoyés dans une liste ainsi que réduire la quantité d'informations renvoyées pour chaque élément. Un paramètre de type [niveau de détail](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.detaillevel.aspx) peut être spécifié pour les opérations de la liste. Niveau de détail est une classe de base abstraite et une [ODATADetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx) objet doit réellement être créée et passée comme paramètre.

Pour toutes les API de situations suivantes :

- Chaque nom de propriété est une chaîne qui correspond à la propriété de l'objet
- Tous les noms de propriété respectent la casse, mais les valeurs de propriété respectent la casse
- Date/heure de chaînes peuvent avoir un des deux formats et doivent être précédées de DateTime
	- W3CDTF (par exemple, creationTime gt DateTime « 2011-05-08T08:49:37Z')
	- RFC1123 (par ex. creationTime gt DateTime'Sun, 08 mai 2011 08:49:37 GMT »)
- Chaînes booléennes sont « true » ou « false »
- Si une propriété non valide ou un opérateur est spécifié, une exception sera créée avec un « 400 (demande incorrecte) » l'exception interne.
- Paramètre de niveau de détail avec les clauses Select et Expand peut également être transmis approprié, méthodes « Get » ; par exemple, IPoolManager.GetPool()

L'objet ODataDetailLevel a trois propriétés publiques qui peuvent être spécifiées dans le constructeur ou définies directement. Les trois propriétés sont toutes des chaînes :

- [FilterClause](#filter) – filtrer et réduire le nombre d'éléments retournés
- [SelectClause](#select) – spécifiez les valeurs de propriété spécifiques qui sont renvoyés, en réduisant la taille de l'élément et de réponse
- [ExpandClause](#expand) – retourner toutes les données requises dans un seul appel plutôt que plusieurs appels

### <a id="filter"></a> FilterClause

Le nombre d'éléments retourné peut être réduit par une chaîne de filtre. Une ou plusieurs valeurs de propriété peuvent être spécifiés pour s'assurer seulement les éléments requis sont retournées. Par exemple, liste uniquement les éléments de travail, liste uniquement l'exécution des tâches d'un projet, répertorient uniquement l'ordinateur virtuel qui sont prêts à exécuter des tâches.

Un [FilterClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx) est une chaîne composée d'une ou plusieurs expressions avec une expression composée d'un nom de propriété, un opérateur et une valeur. Les propriétés qui peuvent être spécifiées sont spécifiques à chaque appel d'API que sont les opérateurs pris en charge pour chaque propriété. Plusieurs expressions peuvent être combinées à l'aide des opérateurs logiques « et » et « ou ».

Par exemple, un filtre de liste de tâches peut être :

	startswith(name, 'MyTask') and (state eq 'Running')

### <a id="select"></a> SelectClause

Les valeurs de propriété sont renvoyées pour chaque élément peuvent être limités à l'aide d'une chaîne de sélection. Une liste de propriétés pour un élément peut être spécifiée et, seules les valeurs de propriété sont retournées.

A [SelectClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx) est une chaîne constituée d'une liste séparée par des virgules de noms de propriétés. Toutes les propriétés de l'élément retourné par l'opération de liste peuvent être spécifiées.

	"name, state, stateTransitionTime"

### <a id="expand"></a> ExpandClause

Le nombre d'appels d'API peut être réduit avec une chaîne de développement. Vous trouverez des informations plus détaillées pour chaque élément de liste avec l'appel d'une API de liste au lieu d'obtenir la liste, puis effectuez un appel pour chaque élément dans la liste.

Un [ExpandClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx) est similaire à la clause Select, les contrôles de la clause Expand si certaines données sont retournées dans les résultats. La clause Expand est uniquement pris en charge pour la liste d'éléments de travail, liste des tâches, liste pool et liste de tâches ; il seulement prend en charge les informations statistiques. Lorsque toutes les propriétés sont requises et il n'est pas une clause select, la clause expand doit être utilisée pour obtenir des informations statistiques. Si une clause select est utilisée pour obtenir un sous-ensemble de propriétés, puis statistiques peuvent être spécifiés dans la clause select et la clause expand peut être vides.

> [AZURE.NOTE]Il est recommandé de toujours utiliser le filtre et sélectionnez les clauses de vos appels d'API de liste garantir une efficacité maximale et les meilleures performances pour votre application.

<!---HONumber=GIT-SubDir-->