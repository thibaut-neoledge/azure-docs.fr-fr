<properties 
	pageTitle="Requêtes de liste efficaces" 
	description="Découvrez comment réduire le nombre d'éléments retournés dans une liste ainsi que la quantité d'informations retournées pour chaque élément" 
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

# Requêtes de liste efficaces

Les méthodes suivantes sont des exemples d'opérations que pratiquement toutes les applications qui utilisent Azure Batch doivent effectuer et ce, en général régulièrement :

- [ListTasks](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listtasks.aspx)
- [ListJobs](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listjobs.aspx)
- [ListWorkitems](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listworkitems.aspx)
- [ListPools](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.ipoolmanager.listpools.aspx)
- [ListCertificates](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icertificatemanager.listcertificates.aspx)

La surveillance est un cas d’utilisation courant. Par exemple, pour déterminer la capacité et l'état d'un pool, toutes les machines virtuelles doivent être interrogées. Autre exemple, pour déterminer si des tâches sont encore présentes dans la file d’attente, il est nécessaire d’interroger les tâches d’un travail. Dans certains cas, un ensemble complet de données est requis, mais dans d'autres cas seulement un certain nombre d'éléments ou des éléments dans un certain état sont requis.

Notez que le nombre d'éléments qui peuvent être retournés peut être très volumineux et la taille des données requises pour représenter la liste des éléments peut également être très importante. La simple recherche d’un grand nombre d'éléments qui entraînent des réponses volumineuses peut entraîner un certain nombre de problèmes :

- Le temps de réponse de l’API Batch peut devenir trop lent. Plus le nombre d'éléments est grand, plus le temps de requête requis par le service Batch est long. Les grands nombres d'éléments devant être divisés en blocs, plusieurs appels de l'API du service doivent être effectués par la bibliothèque cliente vers le service pour obtenir tous les éléments d'une seule liste.
- Le traitement de l'API par l'application qui appelle le service Batch prend d'autant plus de temps que le nombre d'éléments à traiter est grand.
- Davantage de mémoire est consommée dans l'application qui appelle le service Batch, car les éléments sont plus grands et/ou plus nombreux.
- Les éléments plus grands et/ou plus nombreux entraînent un trafic réseau plus important. Le temps de transfert est donc plus lent et, selon l'architecture de l'application, cela peut entraîner une augmentation des frais de réseau pour les données transférées en dehors de la région du compte Batch.

L'API Batch offre la possibilité de réduire le nombre d'éléments retournés dans une liste ainsi que la quantité d'informations retournées pour chaque élément. Un paramètre de type [DetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.detaillevel.aspx) peut être spécifié pour les opérations de liste. DetailLevel est une classe de base abstraite et un objet [ODATADetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx) doit en fait être créé et passé en tant que paramètre.

Les conditions suivantes s'appliquent à toutes les API :

- Chaque nom de propriété est une chaîne qui correspond à la propriété de l'objet
- Tous les noms de propriété respectent la casse, mais les valeurs de propriété ne respectent pas la casse
- Les chaînes de date/heure peuvent être d'un format ou de l'autre et doivent être précédées de DateTime
	- W3CDTF (par ex., creationTime gt DateTime’2011-05-08T08:49:37Z’)
	- RFC1123 (par ex., creationTime gt DateTime’Sun, 08 May 2011 08:49:37 GMT’)
- Les chaînes booléennes ont la valeur « true » ou « false »
- Si une propriété ou un opérateur non valide est spécifié, une exception est créée avec une exception interne « 400 (demande incorrecte) ».
- Le paramètre DetailLevel avec les clauses Select et Expand peut également être transmis aux méthodes « Get » appropriées, par exemple, IPoolManager.GetPool()

L'objet ODataDetailLevel possède trois propriétés publiques qui peuvent être spécifiées dans le constructeur ou définies directement. Les trois propriétés sont toutes des chaînes :

- [FilterClause](#filter) : filtre et éventuellement réduit le nombre d'éléments retournés
- [SelectClause](#select) : spécifie les valeurs de propriété spécifiques qui sont retournées, en réduisant la taille de l'élément et de la réponse
- [ExpandClause](#expand) : retourne toutes les données requises dans un seul appel au lieu de plusieurs

### <a id="filter"></a> FilterClause

Le nombre d'éléments retourné peut être réduit par une chaîne de filtre. Une ou plusieurs valeurs de propriété peuvent être spécifiées pour s'assurer que seuls les éléments requis sont retournés. Par exemple, répertorier uniquement les éléments de travail, répertorier uniquement les tâches en cours d'exécution pour un travail, répertorier uniquement les machines virtuelles qui sont prêtes à exécuter des tâches.

[FilterClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx) est une chaîne composée d'une ou plusieurs expressions, une expression consistant en un nom de propriété, un opérateur et une valeur. Les propriétés qui peuvent être spécifiées sont propres à chaque appel d'API, de même que les opérateurs pris en charge pour chaque propriété. Plusieurs expressions peuvent être combinées à l'aide des opérateurs logiques « and » et « or ».

Par exemple, un filtre pour répertorier des tâches peut être :

	startswith(name, 'MyTask') and (state eq 'Running')

### <a id="select"></a> SelectClause

Les valeurs de propriété retournées pour chaque élément peuvent être limitées à l'aide d'une chaîne de sélection. Une liste de propriétés pour un élément peut être spécifiée et seules les valeurs de ces propriétés sont retournées.

[SelectClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx) est une chaîne constituée d'une liste de noms de propriétés séparés par des virgules. Toutes les propriétés de l'élément retourné par l'opération de liste peuvent être spécifiées.

	"name, state, stateTransitionTime"

### <a id="expand"></a> ExpandClause

Le nombre d'appels d'API peut être réduit avec une chaîne complète. Vous pouvez obtenir des informations plus détaillées pour chaque élément de liste en appelant une seule API de liste au lieu d'obtenir la liste, puis d'effectuer un appel pour chaque élément dans la liste.

[ExpandClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx) est similaire à la clause Select. La clause Expand contrôle si certaines données sont retournées dans les résultats. La clause Expand est uniquement prise en charge pour la liste d'éléments de travail, la liste de tâches, la liste de pools et la liste de travaux. Actuellement, elle ne prend en charge que les informations statistiques. Quand toutes les propriétés sont requises et qu'il n'y a pas de clause select, la clause expand doit être utilisée pour obtenir les informations statistiques. Si une clause select est utilisée pour obtenir un sous-ensemble de propriétés, les statistiques peuvent alors être spécifiées dans la clause select et la clause expand peut garder la valeur null.

> [AZURE.NOTE]Il est recommandé de toujours utiliser le filtre et les clauses select pour vos appels d'API de liste pour garantir une efficacité maximale et les meilleures performances pour votre application.

<!---HONumber=July15_HO3-->