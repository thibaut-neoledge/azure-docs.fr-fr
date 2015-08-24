<properties
	pageTitle="Requêtes de liste efficaces dans Azure Batch | Microsoft Azure"
	description="Découvrez comment réduire le nombre d’éléments Azure Batch renvoyés dans une liste, ainsi que la quantité d’informations renvoyées pour chaque élément"
	services="batch"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="Batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="08/04/2015"
	ms.author="davidmu"/>

# Requêtes de liste Batch efficaces

Les méthodes suivantes sont des exemples d'opérations que pratiquement toutes les applications qui utilisent Azure Batch doivent effectuer et ce, en général régulièrement :

- [ListTasks](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx)
- [ListJobs](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx)
- [ListPools](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx)
- [ListCertificates](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx)

La surveillance est un cas d’utilisation courant. Par exemple, pour déterminer la capacité et l’état d’un pool, il convient d’interroger tous les noeuds de calcul (machines virtuelles) du pool. De même, il est nécessaire d’interroger les tâches d’un travail pour déterminer si des tâches sont encore présentes en file d’attente. Dans certains cas, un ensemble complet de données est requis, mais dans d'autres cas seulement un certain nombre d'éléments ou des éléments dans un certain état sont requis.

Notez que le nombre d’éléments qui peuvent être renvoyés et la taille des données requises pour représenter la liste d’éléments peuvent être très importants. La simple recherche d’un grand nombre d'éléments qui entraînent des réponses volumineuses peut entraîner un certain nombre de problèmes :

- Le temps de réponse de l’API Batch peut devenir trop lent. Plus le nombre d’éléments est élevé, plus le temps de requête requis par le service Batch est long. Les nombres d’éléments importants devant être découpés en blocs, la bibliothèque cliente peut avoir besoin d’effectuer plusieurs appels de l’API du service pour obtenir tous les éléments d’une même liste.
- Le traitement de l’API par l’application qui appelle le service Batch prend d’autant plus de temps que le nombre d’éléments à traiter est élevé.
- Davantage de mémoire est consommée dans l'application qui appelle le service Batch, car les éléments sont plus grands et/ou plus nombreux.
- Les éléments plus grands et/ou plus nombreux entraînent un trafic réseau plus important. Le temps de transfert est donc plus lent et, selon l'architecture de l'application, cela peut entraîner une augmentation des frais de réseau pour les données transférées en dehors de la région du compte Batch.

L’API Batch offre la possibilité de réduire le nombre d’éléments renvoyés dans une liste, ainsi que la quantité d’informations renvoyées pour chaque élément. Un paramètre de type [DetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.detaillevel.aspx) peut être spécifié pour les opérations de liste. DetailLevel est une classe de base abstraite, et un objet [ODATADetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx) doit être créé et transmis en tant que paramètre.

Les conditions ci-après s’appliquent à toutes les API :

- Chaque nom de propriété est une chaîne qui correspond à la propriété de l'objet
- Tous les noms de propriété respectent la casse, mais les valeurs de propriété ne respectent pas la casse
- Les chaînes de date/heure peuvent être d'un format ou de l'autre et doivent être précédées de DateTime
	- W3CDTF (par exemple, creationTime gt DateTime’2011-05-08T08:49:37Z’)
	- RFC1123 (par exemple, creationTime gt DateTime’Sun, 08 May 2011 08:49:37 GMT’)
- Les chaînes booléennes ont la valeur « true » ou « false »
- Si une propriété ou un opérateur non valides sont spécifiés, une exception est créée avec une exception interne « 400 (Demande incorrecte) ».
- Le paramètre DetailLevel avec les clauses Select et Expand peut également être transmis aux méthodes « Get » appropriées, par exemple, PoolOperations.GetPool().

L'objet ODataDetailLevel possède trois propriétés publiques qui peuvent être spécifiées dans le constructeur ou définies directement. Les trois propriétés sont toutes des chaînes :

- [FilterClause](#filter) : filtre et éventuellement réduit le nombre d'éléments retournés
- [SelectClause](#select) : spécifie les valeurs de propriété spécifiques qui sont retournées, en réduisant la taille de l'élément et de la réponse
- [ExpandClause](#expand) : retourne toutes les données requises dans un seul appel au lieu de plusieurs

### <a id="filter"></a> FilterClause

Le nombre d'éléments retourné peut être réduit par une chaîne de filtre. Une ou plusieurs valeurs de propriété peuvent être spécifiées pour s'assurer que seuls les éléments requis sont retournés. En voici deux exemples : répertorier uniquement les tâches en cours d’exécution pour un travail, répertorier uniquement les noeuds de calcul qui sont prêts à exécuter des tâches.

[FilterClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx) est une chaîne composée d'une ou plusieurs expressions, une expression consistant en un nom de propriété, un opérateur et une valeur. Les propriétés spécifiables sont propres à chaque appel d’API, de même que les opérateurs pris en charge pour chaque propriété. Plusieurs expressions peuvent être combinées à l'aide des opérateurs logiques « and » et « or ».

Par exemple, un filtre pour répertorier des tâches peut être :

	startswith(name, 'MyTask') and (state eq 'Running')

### <a id="select"></a> SelectClause

Les valeurs de propriété retournées pour chaque élément peuvent être limitées à l'aide d'une chaîne de sélection. Une liste de propriétés pour un élément peut être spécifiée, et seules les valeurs de ces propriétés sont renvoyées.

[SelectClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx) est une chaîne constituée d'une liste de noms de propriétés séparés par des virgules. Toutes les propriétés de l'élément retourné par l'opération de liste peuvent être spécifiées.

	"name, state, stateTransitionTime"

### <a id="expand"></a> ExpandClause

Le nombre d'appels d'API peut être réduit avec une chaîne complète. Vous pouvez obtenir des informations plus détaillées pour chaque élément de liste en appelant une seule API de liste au lieu d'obtenir la liste, puis d'effectuer un appel pour chaque élément dans la liste.

[ExpandClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx) est comparable à la clause Select, car cette chaîne détermine si certaines données sont renvoyées dans les résultats. La clause expand est uniquement prise en charge pour la liste de tâches, la liste de pools et la liste de travaux. Pour l’instant, elle ne gère que les informations statistiques. Quand toutes les propriétés sont requises et qu'il n'y a pas de clause select, la clause expand doit être utilisée pour obtenir les informations statistiques. Si une clause select est utilisée pour obtenir un sous-ensemble de propriétés, les statistiques peuvent alors être spécifiées dans la clause select et la clause expand peut garder la valeur null.

> [AZURE.NOTE]Il est recommandé de toujours utiliser le filtre et les clauses select pour vos appels d'API de liste pour garantir une efficacité maximale et les meilleures performances pour votre application.

<!---HONumber=August15_HO7-->