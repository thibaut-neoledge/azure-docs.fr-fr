<properties
	pageTitle="Requêtes de liste efficaces dans Azure Batch | Microsoft Azure"
	description="Optimisez vos performances en réduisant la quantité de données renvoyées lors de l’interrogation des entités Azure Batch, tels que des pools, des travaux, des tâches, des nœuds de calcul, etc."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="10/12/2015"
	ms.author="v-marsma"/>

# Interroger efficacement le service Azure Batch

Dans cet article, vous allez apprendre à réduire le nombre d’éléments et la quantité de données renvoyées lors de l’utilisation de l’API [Batch.NET][api_net] afin d’interroger le service Batch pour obtenir des listes de travaux, de tâches, de nœuds de calcul, etc.

Azure Batch est un calcul, et dans un environnement de protection, les entités telles que les nœuds de travaux, de tâches et de calculs peuvent se compter par milliers. L’obtention d’informations sur ces éléments peut générer une grande quantité de données qui peuvent être transférées sur chaque requête. La limitation du nombre d’éléments et du type d’informations renvoyés pour chacun permet d’augmenter la vitesse des requêtes et par conséquent, les performances de votre application.

Les listes de travaux, de tâches et de nœuds de calcul sont des exemples d’opérations que pratiquement toutes les applications utilisant Azure Batch doivent effectuer assez régulièrement. La surveillance est un cas d’utilisation courant. Pour déterminer la capacité et l’état d’un pool, par exemple, vous devez interroger tous les nœuds de calcul de ce pool. De même, il est nécessaire d’interroger les tâches d’un travail pour déterminer si une des tâches est encore présente dans la file d’attente.

Cet extrait de code de l’API [Batch.NET][api_net] récupère toutes les tâches associées à un travail, ainsi que l’ensemble complet des propriétés de ces tâches :

```
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks = batchClient.JobOperations.ListTasks("job-001");
```

Il est toutefois possible d’interroger bien plus efficacement une liste en ajoutant une propriété [ODATADetailLevel][odata] à la méthode [JobOperations.ListTasks][net_list_tasks]. Cet extrait de code renvoie simplement les propriétés d’identifiant, de ligne de commande et d’informations du nœud de calcul pour les tâches terminées uniquement :

```
// Configure an ODATADetailLevel specifying a subset of tasks and their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks = batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

Si, dans l’exemple de scénario ci-dessus, les tâches associées au travail se comptent par milliers, les résultats de la deuxième requête seront généralement renvoyés bien plus rapidement que lors de la première requête. Nous allons vous expliquer ci-après comment utiliser une propriété ODATADetailLevel lors de l’énumération d’éléments avec l’API Batch.NET.

> [AZURE.IMPORTANT]Il est vivement recommandé de **toujours** spécifier une propriété ODATADetailLevel à vos appels d’API de liste .NET pour optimiser l’efficacité et les performances de votre application. En spécifiant un niveau de détail, vous pouvez réduire le temps de réponse du service Batch, améliorer l’utilisation du réseau et réduire l’utilisation de la mémoire par les applications clientes.

## Outils permettant une requête efficace

Les API [Batch.NET][api_net] et [Batch REST][api_rest] permettent de réduire le nombre d’éléments renvoyés dans une liste, ainsi que la quantité d’informations renvoyées pour chacun d’eux, en spécifiant les chaînes *filter*, *select* et *expand* lors de l’exécution des requêtes de liste.

- **filter** : la *chaîne filter* est une expression qui réduit le nombre d’éléments renvoyés. Par exemple, vous pouvez répertorier uniquement les tâches en cours d’exécution pour un travail ou répertorier uniquement les nœuds de calcul prêts à exécuter des tâches.
  - Une chaîne filter est composée d’une ou plusieurs expressions, une expression consistant en un nom de propriété, un opérateur et une valeur. Les propriétés spécifiables sont propres à chaque type appel d’API, de même que les opérateurs pris en charge pour chaque propriété.
  - Plusieurs expressions peuvent être combinées à l’aide des opérateurs logiques `and` et `or`.
  - Exemple de chaîne filter qui répertorie uniquement les tâches en cours d’exécution : `startswith(id, 'renderTask') and (state eq 'running')`
- **select** : la *chaîne select* limite les valeurs de propriété renvoyées pour chaque élément. Il est possible de spécifier une liste de propriétés pour un élément dans la chaîne select, auquel cas seules ces valeurs de propriété seront renvoyées avec les résultats de la requête de liste.
  - Une chaîne select est constituée d’une liste de noms de propriétés séparés par des virgules. Toutes les propriétés d’un élément renvoyé par l’opération de liste peuvent être spécifiées.
  - Exemple de chaîne select qui spécifie uniquement trois propriétés devant être renvoyées pour chaque tâche : `id, state, stateTransitionTime`
- **expand** : la *chaîne expand* réduit le nombre d’appels d’API nécessaires pour obtenir des informations. Vous pouvez obtenir des informations plus détaillées pour chaque élément de liste en appelant une seule API de liste au lieu d’obtenir la liste, puis d’effectuer un appel pour chaque élément dans la liste.
  - De la même manière que la chaîne select, la chaîne expand détermine si certaines données doivent être incluses dans les résultats de la requête de liste.
  - La chaîne expand est uniquement prise en charge pour les listes de travaux, de planifications, de tâches et de pools. Elle ne prend actuellement en charge que les informations de statistiques.
  - Exemple de chaîne expand spécifiant que les informations statistiques doivent être renvoyées pour chaque élément : `stats`
  - Quand toutes les propriétés sont requises et qu’aucune chaîne select n’est spécifiée, la chaîne expand *doit* être utilisée pour obtenir les informations statistiques. Si une chaîne select est utilisée pour obtenir un sous-ensemble de propriétés, la propriété `stats` peut alors être spécifiée dans la chaîne select. Il n’est pas nécessaire de spécifier la chaîne expand.

> [AZURE.NOTE]Lors de la construction des trois types de chaînes de requête (filter, select, expand), vous devez vous assurer que les noms de propriété et la casse correspondent à ceux de leurs homologues de l’API REST. Par exemple, lorsque vous travaillez avec [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) .NET, vous devez spécifier **état** au lieu d’**État** même si la propriété .NET est [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Consultez les tableaux ci-dessous pour connaître les mappages de propriété entre les API .NET et REST.

### Spécifications des chaînes filter, select et expand

- Les propriétés spécifiées dans les chaînes filter, select et expand équivalent aux noms de propriété qui apparaissent dans l’API [Batch REST][api_rest], ce qui est également le cas lorsque vous utilisez la bibliothèque [Batch .NET][api_net].
- Tous les noms de propriété respectent la casse, mais les valeurs de propriété ne respectent pas la casse
- Les chaînes de date/heure peuvent être d’un format ou de l’autre et doivent être précédées de `DateTime`
  - Exemple de format W3CDTF : `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  - Exemple de format RFC1123 : `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
- Les chaînes booléennes ont la valeur`true` ou `false`
- Si une propriété ou un opérateur non valide est spécifié, une erreur `400 (Bad Request)` se produit

## Interrogation efficace dans Batch.NET

Dans l’API [Batch .NET][api_net], la propriété [ODATADetailLevel][odata] permet de fournir les chaînes filter, select et expand aux opérations de liste. Un objet ODataDetailLevel possède trois propriétés de chaîne publique qui peuvent être spécifiées dans le constructeur ou être directement définies. Cet objet est ensuite transmis en tant que paramètre aux différentes opérations de liste telles que [ListPools][net_list_pools], [ListJobs][net_list_jobs] et [ListTasks][net_list_tasks].

- [ODATADetailLevel.FilterClause][odata_filter] : limite le nombre d’éléments renvoyés
- [ODATADetailLevel.SelectClause][odata_select] : spécifie un sous-ensemble de valeurs de propriété renvoyé avec chaque élément
- [ODATADetailLevel.ExpandClause][odata_expand] : récupère les données d’élément en appelant une seule API au lieu d’émettre des appels pour chacune

L’extrait de code suivant utilise l’API .NET Batch pour interroger efficacement le service Batch pour les statistiques d’un ensemble spécifique de pools. Dans ce scénario, l’utilisateur Batch est doté de pools de test et de production, dont les ID sont respectivement précédés de « test » et de « prod ». Dans l’extrait de code, *myBatchClient* est une instance initialisée correctement de [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient).

	// First we need an ODATADetailLevel instance on which to set the expand, filter, and select
	// clause strings
	ODATADetailLevel detailLevel = new ODATADetailLevel();

	// We want to pull only the "test" pools, so we limit the number of items returned by using a
	// FilterClause and specifying that the pool IDs must start with "test"
	detailLevel.FilterClause = "startswith(id, 'test')";

	// To further limit the data that crosses the wire, configure the SelectClause to limit the
	// properties returned on each CloudPool object to only CloudPool.Id and CloudPool.Statistics
	detailLevel.SelectClause = "id, stats";

	// Specify the ExpandClause so that the .NET API pulls the statistics for the CloudPools in a single
	// underlying REST API call. Note that we use the pool's REST API element name "stats" here as opposed
	// to "Statistics" as it appears in the .NET API (CloudPool.Statistics)
	detailLevel.ExpandClause = "stats";

	// Now get our collection of pools, minimizing the amount of data returned by specifying the
	// detail level we configured above
	List<CloudPool> testPools = await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();

> [AZURE.TIP]Une instance [ODATADetailLevel][odata] configurée avec les clauses Select et Expand peut également être transmise aux méthodes Get appropriées telles que [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx) pour limiter la quantité de données renvoyées.

## Mappages entre les API Batch REST et .NET

Les noms de propriété dans les chaînes filter, select et expand *doivent* refléter leurs homologues dans l’API REST, aussi bien au niveau du nom que de la casse. Les tableaux ci-dessous fournissent les mappages entre les homologues .NET et REST.

### Mappages pour les chaînes filter

- **.NET LIST METHODS** : chacune des méthodes de l’API .NET dans cette colonne accepte un objet [ODATADetailLevel][odata] comme paramètre.
- **REST LIST REQUESTS** : chaque page de l’API REST dans cette colonne contient une table spécifiant les propriétés et les opérations autorisées dans les chaînes *filter*. Vous devez utiliser ces noms de propriétés et ces opérations lors de la construction d’une chaîne [ODATADetailLevel.FilterClause][odata_filter].

| Méthodes de liste .NET | Requêtes de liste REST |
|---|---|
| [CertificateOperations.ListCertificates][net_list_certs] | [Création de la liste des certificats dans un compte][rest_list_certs]
| [CloudTask.ListNodeFiles][net_list_task_files] | [Création de la liste des fichiers associés à une tâche][rest_list_task_files]
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] | [Création de la liste des états de préparation du travail et des tâches de version de travail pour un travail][rest_list_jobprep_status]
| [JobOperations.ListJobs][net_list_jobs] | [Création de la liste des tâches dans un compte][rest_list_jobs]
| [JobOperations.ListNodeFiles][net_list_nodefiles] | [Création de la liste des fichiers sur un nœud][rest_list_nodefiles]
| [JobOperations.ListTasks][net_list_tasks] | [Création de la liste des tâches associées à un travail][rest_list_tasks]
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] | [Création de la liste des planifications de travaux dans un compte][rest_list_job_schedules]
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] | [Création de la liste des tâches associées à une planification de travail][rest_list_schedule_jobs]
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] | [Création de la liste des nœuds de calcul dans un pool][rest_list_compute_nodes]
| [PoolOperations.ListPools][net_list_pools] | [création de la liste des pools d'un compte][rest_list_pools]

### Mappages pour les chaînes select

- **BATCH .NET TYPES** : types d’API Batch .NET
- **REST API ENTITIES** : chaque page de cette colonne contient une ou plusieurs tables qui répertorient les noms de propriété de l’API REST pour le type concerné. Ces noms de propriété sont utilisés lors de la construction des chaînes *select*. Vous devez utiliser ces mêmes noms de propriétés lors de la construction d’une chaîne [ODATADetailLevel.SelectClause][odata_select].

| Types Batch .NET | Entités de l’API REST |
|---|---|
| [Certificat][net_cert] | [Obtenir des informations sur un certificat][rest_get_cert] |
| [CloudJob][net_job] | [Obtenir des informations sur un travail][rest_get_job] |
| [CloudJobSchedule][net_schedule] | [Obtenir des informations sur la planification d’un travail][rest_get_schedule] |
| [ComputeNode][net_node] | [Obtenir des informations sur un nœud][rest_get_node] |
| [CloudPool][net_pool] | [Obtenir des informations sur un pool][rest_get_pool] |
| [CloudTask][net_task] | [Obtenir des informations sur une tâche][rest_get_task] |

### Exemple : construction d’une chaîne filter

Lors de la construction d’une chaîne filter pour une propriété [ODATADetailLevel.FilterClause][odata_filter], consultez le tableau de la section *Mappages pour les chaînes filter* pour rechercher la page de documentation de l’API REST correspondant à l’opération de liste que vous souhaitez effectuer. Vous trouverez les propriétés filtrables et leurs opérateurs pris en charge dans le premier tableau à plusieurs lignes de cette page. Si vous souhaitez récupérer toutes les tâches ayant un code de sortie non nul, par exemple, cette ligne sur [Liste des tâches associées à un travail][rest_list_tasks] spécifie la chaîne de propriété applicable et les opérateurs autorisés :

| Propriété | Opérations autorisées | Type |
| :--- | :--- | :--- |
| `executionInfo/exitCode` | `eq, ge, gt, le , lt` | `Int` |

Par conséquent, la chaîne filter permettant de répertorier toutes les tâches ayant un code de sortie non nul serait :

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

### Exemple : construction d’une chaîne select

Pour construire une propriété [ODATADetailLevel.SelectClause][odata_select], consultez le tableau de la section *Mappages pour les chaînes select* et accédez à la page de l’API REST correspondant au type d’entité que vous répertoriez. Vous trouverez les propriétés sélectionnables et leurs opérateurs pris en charge dans le premier tableau à plusieurs lignes de cette page. Si, par exemple, vous souhaitez récupérer uniquement l’ID et la ligne de commande pour chaque tâche dans une liste, vous trouverez ces lignes dans le tableau correspondant sur [Obtenir des informations sur une tâche][rest_get_task] :

| Propriété | Type | Remarques |
| :--- | :--- | :--- |
| `id` | `String` | `The id of the task.` |
| `commandLine` | `String` | `The command line of the task.` |

La chaîne select permettant d’inclure uniquement l’ID et la ligne de commande avec chaque tâche de la liste serait alors :

`id, commandLine`

## Étapes suivantes

Découvrez l’exemple de projet [EfficientListQueries][efficient_query_sample] sur GitHub pour savoir comment une interrogation efficace de liste peut affecter les performances d’une application. Cette application console C# crée et ajoute un grand nombre de tâches à un travail, puis interroge le service de traitement par lots à l’aide de spécifications [ODATADetailLevel][odata] différentes, en affichant un résultat similaire à ce qui suit :

		Adding 5000 tasks to job jobEffQuery...
		5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

		4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
		0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
		59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
		5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
		5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
		5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

		Sample complete, hit ENTER to continue...

Comme indiqué dans les informations de temps écoulé, la limitation des propriétés et le nombre d’éléments retournés peuvent réduire considérablement les temps de réponse des requêtes. Cet exemple et d’autres exemples de projet sont disponibles dans le référentiel [azure-batch-samples][github_samples] sur GitHub.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx

<!---HONumber=Oct15_HO3-->