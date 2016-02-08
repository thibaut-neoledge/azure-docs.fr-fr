<properties
	pageTitle="Optimiser l’utilisation de nœuds Batch avec des tâches parallèles | Microsoft Azure"
	description="Améliorer l'efficacité et réduire les coûts en utilisant moins de nœuds de calcul lors de l'exécution de tâches simultanées sur chaque nœud dans un pool Azure Batch"
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
	ms.date="01/22/2016"
	ms.author="marsma" />
	
# Optimiser l’utilisation des ressources de calcul Azure Batch avec les tâches de nœud simultanées

Dans cet article, vous allez apprendre à exécuter plusieurs tâches simultanément sur chaque nœud de calcul au sein de votre pool Azure Batch. En autorisant l'exécution de tâches simultanées sur les nœuds de calcul d'un pool, vous pouvez optimiser l'utilisation des ressources sur un plus petit nombre de nœuds dans le pool. Pour certaines charges de travail, cela peut vous faire gagner du temps et de l’argent.

Alors que certains scénarios tirent parti de toutes les ressources d’un nœud disponibles pour l’allocation à une seule tâche, un certain nombre de situations profitent de l’autorisation accordée à plusieurs tâches de partager ces ressources :

 - **Réduction du transfert de données** lorsque les tâches sont en mesure de partager des données. Dans ce scénario, vous pouvez considérablement réduire les frais de transfert de données en copiant les données partagées vers un plus petit nombre de nœuds et en exécutant les tâches en parallèle sur chaque nœud. Cela s'applique surtout si les données à copier sur chaque nœud doivent être transférées entre des régions géographiques.

 - **Optimisation de l’utilisation de la mémoire** lorsque les tâches nécessitent une grande quantité de mémoire, mais seulement pendant de courtes périodes et à des moments variables au cours de l’exécution. Vous pouvez employer des nœuds de calcul moins nombreux mais de plus grande taille, avec plus de mémoire pour gérer efficacement ces pics. De cette façon, ces nœuds ont plusieurs tâches exécutées en parallèle sur chaque nœud, mais chaque tâche bénéficie de la mémoire abondante des nœuds à des moments différents.

 - **Atténuation des limites au nombre de nœuds** lorsque la communication entre les nœuds est requise au sein d’un pool. Actuellement, les pools configurés pour la communication entre les nœuds sont limités à 50 nœuds de calcul. Par conséquent, un plus grand nombre de tâches peuvent être exécutées simultanément si chaque nœud dans un pool de ce type est capable d'exécuter des tâches en parallèle.

 - **Réplication d'un cluster de calcul local**, comme lorsque vous déplacez un environnement de calcul vers Azure pour la première fois. Vous pouvez augmenter le nombre maximal de tâches de nœud pour refléter plus précisément une configuration physique existante, si cette configuration exécute actuellement plusieurs tâches par nœud de calcul.

## Exemple de scénario

Voici un exemple qui illustre les avantages de l'exécution de tâches en parallèle. Supposons que votre application de tâche a des exigences en termes de processeur et de mémoire adaptées à une taille de nœud Standard\_D1. Cependant, afin d'exécuter le travail dans le délai imparti, il vous faut 1 000 nœuds de ce type.

Au lieu d’utiliser les nœuds Standard\_D1 avec 1 cœur de processeur, vous pouvez employer des nœuds Standard\_D14 avec 16 cœurs chacun et activer l’exécution de tâches parallèles. Dans ce cas, vous pouvez donc utiliser *16 fois moins de nœuds* : au lieu de 1 000 nœuds, seuls 63 sont requis. Cela améliore considérablement le temps et l’efficacité de l’exécution des tâches si des fichiers d’application ou des données de référence de grande taille sont requis pour chaque nœud.

## Activer l’exécution des tâches parallèles

Vous configurez les nœuds de calcul dans votre solution Batch pour l'exécution des tâches parallèles au niveau du pool. Lorsque vous utilisez la bibliothèque .NET Batch, la propriété [CloudPool.MaxTasksPerComputeNode][maxtasks_net] est définie lors de la création d'un pool. Si vous utilisez l'API REST Batch, l'élément [maxTasksPerNode][maxtasks_rest] est défini dans le corps de la demande lors de la création du pool.

Azure Batch vous permet de définir un nombre maximum de tâches par nœud allant jusqu'à quatre fois (4x) le nombre de cœurs de nœud. Par exemple, si le pool est configuré avec des nœuds de grande taille (quatre cœurs), alors la valeur `maxTasksPerNode` peut être définie sur 16. Pour plus d'informations sur le nombre de cœurs pour chacune des tailles de nœud, consultez [Tailles de services Cloud](./../cloud-services/cloud-services-sizes-specs.md). Pour plus d'informations sur les limites du service, consultez [Quotas et les limites pour le service Azure Batch](batch-quota-limit.md).

> [AZURE.TIP] Veillez à prendre en compte la valeur `maxTasksPerNode` lors de la construction d'une [formule de mise à l'échelle automatique][enable_autoscaling] pour votre pool. Par exemple, une formule qui évalue `$RunningTasks` pourrait être considérablement affectée par une augmentation des tâches par nœud. Consultez [Mettre automatiquement à l’échelle les nœuds de calcul dans un pool Azure Batch](batch-automatic-scaling.md) pour plus d’informations.

## Répartition des tâches

Lorsque les nœuds de calcul au sein d'un pool sont en mesure d'exécuter des tâches simultanément, il est important de spécifier comment vous souhaitez que vos tâches soient réparties entre les nœuds dans le pool.

La propriété [CloudPool.TaskSchedulingPolicy][task_schedule] vous permet de spécifier que les tâches doivent être attribuées uniformément entre tous les nœuds du pool (« propagation »). Vous pouvez également spécifier qu'autant de tâches que possible doivent être attribuées à chaque nœud avant que les tâches ne soient attribuées à un autre nœud du pool (« compression »).

Pour illustrer l'importance de cette fonctionnalité, examinons le pool de nœuds Standard\_D14 (dans l'exemple ci-dessus) configuré avec une propriété [CloudPool.MaxTasksPerComputeNode][maxtasks_net] d'une valeur de 16. Si la propriété [CloudPool.TaskSchedulingPolicy][task_schedule] est configurée avec une propriété [ComputeNodeFillType][fill_type] de type *Pack*, l'utilisation des 16 cœurs de chaque nœud est optimisée et un [pool de mise à l'échelle automatique](./batch-automatic-scaling.md) est autorisé pour nettoyer les nœuds inutilisés du pool (nœuds sans aucune tâche affectée). Ceci limite l'utilisation des ressources et permet d'économiser de l'argent.

## Exemple .NET Batch

Cet extrait de code de l'API [.NET Batch][api_net] illustre une demande de création d'un pool contenant quatre grands nœuds avec un maximum de quatre tâches par nœud. Une stratégie de planification de tâche est également spécifiée ; elle remplira chaque nœud de tâches avant d'attribuer des tâches à un autre nœud du pool. Pour plus d'informations sur l'ajout de pools à l'aide de l'API .NET Batch, consultez [BatchClient.PoolOperations.CreatePool][poolcreate_net].

        CloudPool pool = batchClient.PoolOperations.CreatePool(poolId: "mypool",
        													osFamily: "2",
        													virtualMachineSize: "large",
        													targetDedicated: 4);
        pool.MaxTasksPerComputeNode = 4;
        pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
        pool.Commit();

## Exemple REST Batch

Cet extrait de code de l'API [REST Batch][api_rest] illustre une demande de création d'un pool contenant deux grands nœuds avec un maximum de quatre tâches par nœud. Pour plus d'informations sur l'ajout de pools à l'aide de l'API REST, consultez [Ajout d'un pool à un compte][maxtasks_rest].

        {
          "id": "mypool",
          "vmSize": "Large",
          "osFamily": "2",
          "targetOSVersion": "*",
          "targetDedicated": 2,
          "enableInterNodeCommunication": false,
          "maxTasksPerNode": 4
        }

> [AZURE.NOTE] Vous pouvez définir l'élément `maxTasksPerNode` et la propriété [MaxTasksPerComputeNode][maxtasks_net] uniquement au moment de la création du pool. Ils ne peuvent pas être modifiés après qu'un pool a déjà été créé.

## Explorer l’exemple de projet

Explorez le projet [ParallelNodeTasks][parallel_tasks_sample] sur GitHub. Il s'agit d'un exemple de code fonctionnel qui illustre l'utilisation de [CloudPool.MaxTasksPerComputeNode][maxtasks_net].

Cette application de console en C# utilise la bibliothèque [.NET Batch][api_net] pour créer un pool avec un ou plusieurs nœuds de calcul. Elle exécute un nombre configurable de tâches sur ces nœuds pour simuler la charge variable. La sortie de l'application spécifie quels nœuds ont exécuté chaque tâche. L'application fournit également un résumé des paramètres du travail et sa durée. La partie Résumé de la sortie de deux exécutions différentes de l’exemple d’application apparaît ci-dessous.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

La première exécution de l'exemple d'application montre qu'avec un nœud unique dans le pool et le paramètre par défaut d'une tâche par nœud, la durée du travail dépasse 30 minutes.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

La deuxième exécution de l'exemple montre une diminution significative de la durée du travail. Cela est dû au fait que le pool a été configuré avec quatre tâches par nœud, ce qui permet l'exécution de tâches parallèles pour terminer le travail en un quart du temps, environ.

> [AZURE.NOTE] Les durées des tâches dans les résumés ci-dessus n’incluent pas le temps de création du pool. Chacune des tâches ci-dessus a été envoyée à des pools créés précédemment dont les nœuds de calcul étaient à l'état *Inactif* au moment de l'envoi.

## Carte thermique Batch Explorer

[Azure Batch Explorer][batch_explorer], l'un des [exemples d'application][github_samples] Azure Batch, contient une fonctionnalité *Carte thermique* qui permet de visualiser l'exécution de tâches. Lorsque vous exécutez l'exemple d'application [ParallelTasks][parallel_tasks_sample], utilisez la fonctionnalité Carte thermique pour visualiser l'exécution de tâches parallèles sur chaque nœud.

![Carte thermique Batch Explorer][1]

*Carte thermique Batch Explorer affichant un pool de quatre nœuds, chaque nœud exécutant quatre tâches*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[maxtasks_rest]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

[1]: ./media/batch-parallel-node-tasks\heat_map.png

<!---HONumber=AcomDC_0128_2016-->