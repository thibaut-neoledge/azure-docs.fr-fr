<properties
	pageTitle="Requêtes de liste efficaces dans Azure Batch | Microsoft Azure"
	description="Améliorez vos performances en réduisant la quantité de données renvoyées lors de l’interrogation d’entités Azure Batch telles que les pools, les travaux, les tâches, les nœuds de calcul, etc."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="04/21/2016"
	ms.author="marsma" />

# Interroger efficacement le service Azure Batch

Vous allez apprendre à augmenter les performances de votre application Azure Batch en réduisant la quantité de données renvoyées lorsque vous interrogez le service Batch à l’aide de la bibliothèque [Batch .NET][api_net].

Azure Batch offre des fonctionnalités de calcul importantes, et dans un environnement de production, les entités telles que les travaux, les tâches et les nœuds de calcul peuvent se compter en milliers. L’obtention d’informations sur ces éléments peut donc générer une grande quantité de données qui doivent être transférées du service à votre application sur chaque requête. En limitant le nombre d’éléments et le type d’informations renvoyé pour chacun, vous pouvez augmenter la vitesse des requêtes et donc, les performances de votre application.

Presque toutes les applications qui utilisent Azure Batch effectueront une certaine surveillance ou autre opération qui interroge le service batch, souvent à intervalles réguliers. Par exemple, pour déterminer la capacité et l'état d'un pool, vous devez interroger chaque nœud de ce pool. Pour déterminer si des tâches d'un travail sont toujours en attente, vous devez interroger chaque tâche de ce travail. Cet article explique comment exécuter ces types de requêtes de la manière la plus efficace.

Cet extrait de code de l’API [Batch.NET][api_net] récupère chaque tâche associée à un travail, ainsi que *toutes* les propriétés de ces tâches :

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks = batchClient.JobOperations.ListTasks("job-001");
```

Une requête de liste beaucoup plus efficace peut toutefois être exécutée. Pour ce faire, fournissez un objet [ODATADetailLevel][odata] à la méthode [JobOperations.ListTasks][net_list_tasks]. Cet extrait de code renvoie simplement les propriétés d’identifiant, de ligne de commande et d’informations du nœud de calcul pour les tâches terminées uniquement :

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks = batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

Si, dans l’exemple de scénario ci-dessus, les tâches associées au travail se comptent par milliers, les résultats de la deuxième requête seront généralement renvoyés bien plus rapidement que ceux de la première requête. Vous trouverez d’autres informations sur l’utilisation de ODATADetailLevel pour répertorier les éléments avec l’API Batch .NET [ci-dessous](#efficient-querying-in-batch-net).

> [AZURE.IMPORTANT]
Nous vous recommandons vivement de *toujours* fournir un objet ODATADetailLevel à vos appels d’API de liste .NET afin de garantir une efficacité et des performances maximales de votre application. En spécifiant un niveau de détail, vous pouvez réduire les délais de réponse du service Batch, améliorer le taux d’utilisation du réseau et réduire l’utilisation de la mémoire par les applications clientes.

## Outils permettant une requête efficace

Les API [Batch.NET][api_net] et [Batch REST][api_rest] permettent de réduire le nombre d’éléments renvoyés dans une liste, ainsi que la quantité d’informations renvoyées pour chacun d’eux. Pour ce faire, spécifiez des chaînes **filter**, **select** et **expand** lors de l’exécution des requêtes de liste.

### Filtre
La chaîne filter est une expression permettant de réduire le nombre d’éléments renvoyés. Par exemple, vous pouvez répertorier uniquement les tâches en cours d’exécution pour un travail ou répertorier uniquement les nœuds de calcul prêts à exécuter des tâches.

- Une chaîne filter se compose d’une ou de plusieurs expressions dont l’une se compose d’un nom de propriété, d’un opérateur et d’une valeur. Les propriétés spécifiables sont propres à chaque type d’entité que vous interrogez, de même que les opérateurs pris en charge pour chaque propriété.
- Plusieurs expressions peuvent être combinées à l’aide des opérateurs logiques `and` et `or`.
- Cet exemple de chaîne filter répertorie uniquement les tâches de « rendu » en cours d’exécution : `(state eq 'running') and startswith(id, 'renderTask')`.

### Sélectionnez
La chaîne select limite les valeurs de la propriété retournée pour chaque élément. Vous spécifiez une liste de noms de propriétés, et seules ces valeurs de propriétés sont retournées pour les éléments dans les résultats de la requête.

- Une chaîne select est constituée d’une liste de noms de propriétés séparés par des virgules. Vous pouvez spécifier une des propriétés pour le type d'entité que vous interrogez.
- Cet exemple de chaîne select spécifie que seules trois propriétés doivent être renvoyées pour chaque tâche : `id, state, stateTransitionTime`.

### Développez
La chaîne expand réduit le nombre d’appels d’API nécessaires pour obtenir certaines informations. Lorsque vous utilisez une chaîne expand, vous pouvez obtenir davantage d'informations sur chaque élément avec un seul appel d'API. Au lieu d'obtenir la liste des entités, puis de demander des informations pour chaque élément dans la liste, vous utilisez une chaîne expand pour obtenir les mêmes informations dans un seul appel d'API. Plus le nombre d'appels d'API est faible, plus les performances sont élevées.

- De la même manière que la chaîne select, la chaîne expand détermine si certaines données doivent être incluses dans les résultats de la requête de liste.
- La chaîne complète n’est prise en charge que lorsqu’elle est utilisée dans la liste des travaux, la planification de travaux, des tâches et des pools. Actuellement, elle ne prend en charge que les informations statistiques.
- Quand toutes les propriétés sont requises et qu’aucune chaîne select n’est spécifiée, la chaîne expand *doit* être utilisée pour obtenir les informations statistiques. Si une chaîne select est utilisée pour obtenir un sous-ensemble de propriétés, la propriété `stats` peut alors être spécifiée dans la chaîne select. Il est inutile de spécifier la chaîne expand.
- Cet exemple de chaîne expand spécifie que les informations statistiques doivent être renvoyées pour chaque élément dans la liste : `stats`.

> [AZURE.NOTE] Lors de la construction de l’un des trois types de chaînes de requête (filter, select et expand), vous devez vous assurer que les noms de propriété et la casse correspondent à ceux de leurs homologues de l’API REST. Par exemple, lorsque vous travaillez avec [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) .NET, vous devez spécifier **état** et non **État** même si la propriété .NET est [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Consultez les tableaux ci-dessous pour connaître les mappages de propriété entre les API .NET et REST.

### Spécifications des chaînes filter, select et expand

- Les propriétés spécifiées dans les chaînes de filtre, select et expand équivalent aux noms de propriété qui apparaissent dans l’API [Batch REST][api_rest] et ce, même lorsque vous utilisez la bibliothèque [Batch .NET][api_net].
- Les noms de propriété respectent la casse contrairement aux valeurs de propriété.
- Les chaînes de date/heure peuvent être d’un format ou de l’autre et doivent être précédées de `DateTime`.
  - Exemple de format W3C-DTF : `creationTime gt DateTime'2011-05-08T08:49:37Z'`.
  - Exemple de format RFC 1123 : `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`.
- Les chaînes booléennes ont la valeur `true` ou `false`.
- Si une propriété ou un opérateur non valide est spécifié, une erreur `400 (Bad Request)` se produit.

## Interrogation efficace dans Batch.NET

Dans l’API [Batch .NET][api_net], la classe [ODATADetailLevel][odata] sert à fournir les chaînes filter, select et expand aux opérations de liste. La classe ODataDetailLevel possède trois propriétés de chaîne publiques qui peuvent être spécifiées dans le constructeur ou définies directement sur l'objet. Vous transmettez ensuite l'objet ODataDetailLevel en tant que paramètre à plusieurs opérations de liste tel que [ListPools][net_list_pools], [ListJobs][net_list_jobs] et [ListTasks][net_list_tasks].

- [ODATADetailLevel][odata].[FilterClause][odata_filter] : limiter le nombre d’éléments renvoyés.
- [ODATADetailLevel][odata].[SelectClause][odata_select] \: spécifier les valeurs de propriété renvoyées avec chaque élément.
- [ODATADetailLevel][odata].[ExpandClause][odata_expand] \: extraire les données de tous les éléments en utilisant un seul appel d'API au lieu d'appels distincts pour chaque élément.

L’extrait de code suivant utilise l’API .NET Batch pour interroger efficacement le service Batch pour les statistiques d’un ensemble spécifique de pools. Dans ce scénario, l’utilisateur de Batch comporte à la fois des pools de test et des pools de production. Les ID de pool de test sont précédés du préfixe « test » et les ID de production sont précédés de « prod ». Dans l’extrait de code, *myBatchClient* est une instance initialisée correctement de la classe [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient).

```csharp
// First we need an ODATADetailLevel instance on which to set the expand, filter, and select
// clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned by using a
// FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to limit the
// properties that are returned on each CloudPool object to only CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the CloudPools in a single
// underlying REST API call. Note that we use the pool's REST API element name "stats" here as opposed
// to "Statistics" as it appears in the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned by specifying the
// detail level that we configured above
List<CloudPool> testPools = await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [AZURE.TIP] Une instance de [ODATADetailLevel][odata] configurée avec les clauses Select et Expand peut également être transmise aux méthodes Get appropriées telles que [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx) pour limiter la quantité de données renvoyées.

## Mappages entre les API Batch REST et .NET

Les noms de propriété dans les chaînes filter, select et expand *doivent* refléter leurs homologues dans l’API REST, aussi bien au niveau du nom que de la casse. Les tableaux ci-dessous fournissent les correspondances existant entre les homologues .NET et REST.

### Mappages pour les chaînes filter

- **Méthodes de liste .NET** : chacune des méthodes de l’API .NET dans cette colonne accepte un objet [ODATADetailLevel][odata] en tant que paramètre.
- **Requêtes de liste REST** : chaque page de l’API REST dans cette colonne contient une table spécifiant les propriétés et les opérations autorisées dans les chaînes *filter*. Vous devez utiliser ces noms de propriétés et ces opérations lors de la construction d’une chaîne [ODATADetailLevel.FilterClause][odata_filter].

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

- **Types Batch .NET** : types d’API Batch .NET
- **Entités API REST** : chaque page de cette colonne contient une ou plusieurs tables qui répertorient les noms de propriété de l’API REST pour le type concerné. Ces noms de propriété sont utilisés au moment de la construction des chaînes *select*. Vous devez utiliser ces mêmes noms de propriété lors de la construction d’une chaîne [ODATADetailLevel.SelectClause][odata_select].

| Types Batch .NET | Entités de l’API REST |
|---|---|
| [Certificat][net_cert] | [Obtenir des informations sur un certificat][rest_get_cert] |
| [CloudJob][net_job] | [Obtenir des informations sur un travail][rest_get_job] |
| [CloudJobSchedule][net_schedule] | [Obtenir des informations sur la planification d’un travail][rest_get_schedule] |
| [ComputeNode][net_node] | [Obtenir des informations sur un nœud][rest_get_node] |
| [CloudPool][net_pool] | [Obtenir des informations sur un pool][rest_get_pool] |
| [CloudTask][net_task] | [Obtenir des informations sur une tâche][rest_get_task] |

### Exemple : construction d’une chaîne filter

Lors de la construction d’une chaîne filter pour [ODATADetailLevel.FilterClause][odata_filter], consultez le tableau ci-dessous sous « Mappages pour les chaînes filter » pour rechercher la page de documentation de l’API REST correspondant à l’opération de liste que vous souhaitez effectuer. Vous trouverez les propriétés filtrables et leurs opérateurs pris en charge dans le premier tableau à plusieurs lignes de cette page. Si vous souhaitez récupérer toutes les tâches ayant un code de sortie non nul, par exemple, cette ligne sur [Liste des tâches associées à un travail][rest_list_tasks] spécifie la chaîne de propriété applicable et les opérateurs autorisés :

| Propriété | Opérations autorisées | Type |
| :--- | :--- | :--- |
| `executionInfo/exitCode` | `eq, ge, gt, le , lt` | `Int` |

Par conséquent, la chaîne filter permettant de répertorier toutes les tâches dont le code de sortie serait autre que nul serait :

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

### Exemple : construction d’une chaîne select

Pour construire une clause [ODATADetailLevel.SelectClause][odata_select], consultez le tableau qui précède sous « Mappages pour les chaînes select » et accédez à la page de l’API REST correspondant au type d’entité que vous répertoriez. Vous trouverez les propriétés sélectionnables et leurs opérateurs pris en charge dans le premier tableau à plusieurs lignes de cette page. Si, par exemple, vous souhaitez récupérer uniquement l’ID et la ligne de commande pour chaque tâche dans une liste, vous trouverez ces lignes dans le tableau correspondant sur [Obtenir des informations sur une tâche][rest_get_task] :

| Propriété | Type | Remarques |
| :--- | :--- | :--- |
| `id` | `String` | `The ID of the task.` |
| `commandLine` | `String` | `The command line of the task.` |

La chaîne select permettant d’inclure uniquement l’ID et la ligne de commande avec chaque tâche de la liste serait alors :

`id, commandLine`

## Étapes suivantes

### Exemple de code pour des requêtes de liste efficaces

Découvrez l’exemple de projet [EfficientListQueries][efficient_query_sample] sur GitHub pour savoir comment une interrogation efficace de liste peut affecter les performances d’une application. Cette application de console C# crée et ajoute de nombreuses tâches à un travail. Ensuite, elle effectue plusieurs appels de la méthode [JobOperations.ListTasks][net_list_tasks] et transfère des objets [ODATADetailLevel][odata] configurés avec différentes valeurs de propriété pour faire varier la quantité de données à renvoyer. Vous générez ainsi des informations qui ressemblent à ce qui suit :

		Adding 5000 tasks to job jobEffQuery...
		5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

		4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
		0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
		59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
		5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
		5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
		5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

		Sample complete, hit ENTER to continue...

Comme le montrent les informations sur le temps écoulé, vous pouvez réduire de façon significative les temps de réponse de requête en limitant les propriétés et le nombre d’éléments retournés. Cet exemple et d’autres exemples de projet sont disponibles dans le référentiel [azure-batch-samples][github_samples] sur GitHub.

### Forum Azure Batch

Le [Forum Azure Batch][forum] sur MSDN est l’endroit idéal pour discuter de Batch et poser des questions sur le service. Consultez le forum pour obtenir des publications « permanentes » utiles et publiez les questions que vous vous posez pendant la création de vos solutions Batch.


[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[forum]: https://social.msdn.microsoft.com/forums/azure/fr-FR/home?forum=azurebatch
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

<!---HONumber=AcomDC_0427_2016-->