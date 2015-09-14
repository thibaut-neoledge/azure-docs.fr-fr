<properties
	pageTitle="Requêtes de liste efficaces dans Azure Batch | Microsoft Azure"
	description="Apprenez à réduire la quantité de données renvoyées et à augmenter les performances lors de l’interrogation des pools, travaux, tâches, nœuds de calcul Azure, et plus encore."
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
	ms.date="08/27/2015"
	ms.author="davidmu;v-marsma"/>

# Requêtes de liste Batch efficaces

Azure Batch est un calcul, et dans un environnement de protection, les entités telles que les nœuds de travaux, de tâches et de calculs peuvent se compter par milliers. L’obtention d’informations sur ces éléments peut générer une grande quantité de données qui peuvent être transférées sur chaque requête. La limitation du nombre d’éléments et du type d’informations renvoyés pour chacun permet d’augmenter la vitesse des requêtes et par conséquent, les performances de votre application.

Les méthodes [Batch .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx) suivantes sont des exemples d’opérations que pratiquement toutes les applications utilisant Azure Batch doivent effectuer régulièrement :

- [ListTasks](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx)
- [ListJobs](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx)
- [ListPools](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx)
- [ListComputeNodes](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx)

La surveillance est un cas d’utilisation courant. Pour déterminer la capacité et l’état d’un pool exige l’interrogation de tous les nœuds de calcul (machines virtuelles) du pool, par exemple. De même, il est nécessaire d’interroger les tâches d’un travail pour déterminer si une des tâches est encore présente dans la file d’attente. Dans certains cas, un ensemble complet de données est requis, mais dans d’autres cas, une partie seulement des éléments ou d’une collection d’éléments dans un certain état sont requis.

Il est important de savoir que le nombre d’éléments renvoyés et la quantité de données requises pour représenter ces éléments peuvent être très importants. La simple recherche d’un grand nombre d’éléments entraînant des réponses volumineuses peut générer un certain nombre de problèmes :

- Le temps de réponse de l’API Batch peut devenir trop lent. Plus le nombre d’éléments est élevé, plus le temps de requête requis par le service Batch est long. Les nombres d’éléments importants devant être divisés en blocs, la bibliothèque cliente peut avoir besoin d’effectuer plusieurs appels de l’API pour obtenir tous les éléments d’une même liste.
- Le traitement de l’API par l’application qui appelle le service Batch prend d’autant plus de temps que le nombre d’éléments à traiter est élevé.
- Davantage de mémoire est consommée par l’application qui appelle le service Batch, lorsque les éléments sont plus grands et/ou plus nombreux.
- Les éléments plus grands et/ou plus nombreux génèrent un trafic réseau plus important. Le temps de transfert est donc plus lent et, selon l'architecture de l'application, cela peut entraîner une augmentation des frais de réseau pour les données transférées en dehors de la région du compte Batch.

Les conditions ci-après s’appliquent à toutes les API Batch :

- Chaque nom de propriété est une chaîne qui correspond à la propriété de l'objet
- Tous les noms de propriété respectent la casse, mais les valeurs de propriété ne respectent pas la casse
- La casse et les noms de propriété sont comme les éléments qui figurent dans l’API REST Batch
- Les chaînes de date/heure peuvent être rédigées dans l’un des deux formats et doivent être précédées de DateTime
	- W3CDTF (par exemple, *creationTime gt DateTime’2011-05-08T08:49:37Z’*)
	- RFC1123 (par exemple, *creationTime gt DateTime’Sun, 08 Mai 2011 08:49:37 GMT’*)
- Les chaînes booléennes ont la valeur « true » ou « false »
- La spécification d’une propriété ou d’un opérateur non valide entraîne une erreur « 400 (demande incorrecte) »

## Interrogation efficace dans Batch.NET

L’API Batch .NET offre la possibilité de réduire le nombre d’éléments renvoyés dans une liste, ainsi que la quantité d’informations renvoyées pour chaque élément en spécifiant la propriété [DetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.detaillevel.aspx) d’une requête. DetailLevel est une classe de base abstraite, et un objet [ODATADetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx) doit être créé et transmis en tant que paramètre vers les méthodes appropriées.

L’objet ODataDetailLevel possède trois propriétés de chaînes publiques qui peuvent être spécifiées dans le constructeur ou définies directement :

- [FilterClause](#filter) : filtre et éventuellement réduit le nombre d'éléments retournés
- [SelectClause](#select) : spécifie les valeurs de propriété spécifiques qui sont renvoyées, en réduisant l’élément et la taille de la réponse
- [ExpandClause](#expand) : retourne toutes les données requises dans un seul appel au lieu de plusieurs

> [AZURE.TIP]Une instance DetailLevel configurée avec les clauses Select et Expand peut également être transmise aux méthodes Get appropriées telles que [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx) pour limiter la quantité de données renvoyées.

### <a id="filter"></a> FilterClause

Le nombre d'éléments retourné peut être réduit par une chaîne de filtre. Une ou plusieurs valeurs de propriété avec des qualificateurs peuvent être spécifiées pour s’assurer que seuls les éléments pertinents pour votre requête sont renvoyés. Par exemple, vous voulez peut-être répertorier uniquement les tâches en cours d’exécution d’un travail ou répertorier uniquement les nœuds de calcul prêts à exécuter des tâches.

 [FilterClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx) est une chaîne composée d’une ou de plusieurs expressions, une expression consistant en un *nom de propriété*, un *opérateur* et une *valeur*. Les propriétés spécifiables sont propres à chaque appel d’API, de même que les opérateurs pris en charge pour chaque propriété. Plusieurs expressions peuvent être combinées à l’aide des opérateurs logiques **and** et **or**.

Par exemple, cette chaîne de filtre renvoie uniquement les tâches en cours d’exécution dont *displayName* commence par « MyTask » :

	startswith(displayName, 'MyTask') and (state eq 'Running')

Chaque article de l’API REST Batch de lot ci-dessous contient un tableau spécifiant les propriétés prises en charge et les opérations de ces propriétés pour les autres opérations de liste.

- [création de la liste des pools d'un compte](https://msdn.microsoft.com/library/azure/dn820101.aspx)
- [Création de la liste des nœuds de calcul dans un pool](https://msdn.microsoft.com/library/azure/dn820159.aspx)
- [Création de la liste des tâches dans un compte](https://msdn.microsoft.com/library/azure/dn820117.aspx)
- [Création de la liste des états de préparation du travail et des tâches de version de travail pour un travail](https://msdn.microsoft.com/library/azure/mt282170.aspx)
- [Création de la liste des planifications de travaux dans un compte](https://msdn.microsoft.com/library/azure/mt282174.aspx)
- [Création de la liste des tâches associées à une planification de travail](https://msdn.microsoft.com/library/azure/mt282169.aspx)
- [Création de la liste des tâches associées à un travail](https://msdn.microsoft.com/library/azure/dn820187.aspx)
- [Création de la liste des fichiers associés à une tâche](https://msdn.microsoft.com/library/azure/dn820142.aspx)
- [Création de la liste des certificats dans un compte](https://msdn.microsoft.com/library/azure/dn820154.aspx)
- [Création de la liste des fichiers sur un nœud](https://msdn.microsoft.com/library/azure/dn820151.aspx)

> [AZURE.IMPORTANT]Lorsque vous spécifiez des propriétés dans les trois types de clause, assurez-vous que le nom et la casse de la propriété correspondent à ceux de leurs homologues d’élément API REST Batch. Par exemple, lorsque vous travaillez avec [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) .NET, vous devez spécifier **état** au lieu d’**état** même si la propriété .NET est [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Pour vérifier le nom et le cas de la propriété **état** appropriée, par exemple, vous devez vérifier le nom d’élément dans [Obtenir des informations sur une tâche](https://msdn.microsoft.com/library/azure/dn820133.aspx) dans la documentation de l’API REST Batch.

### <a id="select"></a> SelectClause

Les valeurs de propriété retournées pour chaque élément peuvent être limitées à l'aide d'une chaîne de sélection. Une liste de propriétés pour un élément peut être spécifiée, et seules les valeurs de ces propriétés sont renvoyées.

[SelectClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx) est une chaîne constituée d'une liste de noms de propriétés séparés par des virgules. N’importe quelle combinaison de propriétés disponibles pour un élément retourné par une opération de liste peut être spécifiée.

	"name, state, stateTransitionTime"

### <a id="expand"></a> ExpandClause

Le nombre d’appels d’API peut être réduit avec une clause d’expansion. Vous pouvez obtenir des informations plus détaillées pour chaque élément de liste en appelant une seule API de liste au lieu d’obtenir la liste, puis d’effectuer un appel pour chaque élément de la liste.

[ExpandClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx) est comparable à la clause Select, car cette chaîne détermine si certaines données sont renvoyées dans les résultats. La clause expand est uniquement prise en charge pour la liste de tâches, la liste de pools et la liste de travaux. Pour l’instant, elle ne gère que les informations statistiques. Quand toutes les propriétés sont requises et qu’aucune clause select n’est spécifiée, la clause expand doit être utilisée pour obtenir les informations statistiques. Si une clause select est utilisée pour obtenir un sous-ensemble de propriétés, les « statistiques » peuvent alors être spécifiées dans la clause select et la clause expand peut garder la valeur null.

## Exemple de requête efficace

Vous trouverez ci-dessous un extrait de code qui utilise l’API .NET Batch pour interroger efficacement le service Batch pour les statistiques d’un ensemble spécifique de pools. Dans ce scénario, l’utilisateur Batch est doté de pools de test et de production, dont les ID sont respectivement précédés de « test » et de « prod ». Dans l’extrait de code, *myBatchClient* est une instance initialisée correctement de [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient).

	// First we need an ODATADetailLevel instance on which to set the expand, filter, and select
	// clause strings
	ODATADetailLevel detailLevel = new ODATADetailLevel();

	// Specify the ExpandClause so that the .NET API pulls the statistics for the CloudPools in a single
	// underlying REST API call. Note that we use the pool's REST API element name "stats" here as opposed
	// to "Statistics" as it appears in the .NET API (CloudPool.Statistics)
	detailLevel.ExpandClause = "stats";

	// We want to pull only the "test" pools, so we limit the items returned by using a Filterclause and
	// specifying that the pool IDs must start with "test"
	detailLevel.FilterClause = "startswith(id, 'test')";

	// To further limit the data that crosses the wire, configure the SelectClause to limit the
	// properties returned on each CloudPool object to only CloudPool.Id and CloudPool.Statistics
	detailLevel.SelectClause = "id, stats";

	// Now get our collection of pools, minimizing the amount of data returned by specifying the
	// detail level we configured above
	List<CloudPool> testPools = myBatchClient.PoolOperations.ListPools(detailLevel).ToList();

> [AZURE.TIP]Il est recommandé de *toujours* utiliser le filtre et les clauses select pour vos appels d’API de liste pour garantir une efficacité maximale et les meilleures performances pour votre application.

## Étapes suivantes

1. Si ce n’est pas encore fait, consultez la documentation des API Batch de documentation convenant à votre scénario de développement
    - [Batch REST](https://msdn.microsoft.com/library/azure/dn820158.aspx)
    - [Batch .NET](https://msdn.microsoft.com/library/azure/dn865466.aspx)
2. Saisissez les [exemples de commandes Azure](https://github.com/Azure/azure-batch-samples) sur GitHub et explorez le code

<!---HONumber=September15_HO1-->