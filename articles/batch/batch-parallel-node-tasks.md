<properties
   pageTitle="Optimiser l’utilisation de nœuds Batch avec des tâches parallèles | Microsoft Azure"
   description="Améliorer l’efficacité et réduire les coûts en utilisant moins de nœuds de calcul lors de l’exécution de tâches simultanées sur chaque nœud dans un pool Azure Batch"
   services="batch"
   documentationCenter=".net"
   authors="mmacy"
   manager="timlt"
   editor=""/>

   <tags
   	ms.service="batch"
   	ms.devlang="multiple"
   	ms.topic="article"
   	ms.tgt_pltfrm="vm-windows"
   	ms.workload="big-compute"
   	ms.date="09/30/2015"
   	ms.author="v-marsma"/>

# Optimiser l’utilisation des ressources de calcul Azure Batch avec les tâches de nœud simultanées

L’exécution de tâches parallèles à grande échelle est une fonctionnalité principale d’Azure Batch, et cette possibilité s’étend non seulement à plusieurs nœuds de calcul exécutant vos tâches, mais aussi à l’exécution de tâches simultanées sur ces nœuds. Avec Batch, l’exécution de tâches parallèles est très évolutive.

Autoriser l’exécution de tâches simultanées sur les nœuds de calcul d’un pool permet d’optimiser l’utilisation des ressources sur un plus petit nombre de nœuds dans le pool. Alors que certains scénarios tirent parti de toutes les ressources d’un nœud disponibles pour l’allocation à une seule tâche, un certain nombre de situations profitent de l’autorisation accordée à plusieurs tâches de partager ces ressources :

 - **Réduction du transfert de données** lorsque les tâches sont en mesure de partager des données. Dans ce scénario, la copie de données partagées dans un plus petit nombre de nœuds et l’exécution de tâches en parallèle sur chaque nœud peuvent considérablement réduire les frais de transfert de données, surtout si les données à copier sur chaque nœud doivent être transférées entre des régions géographiques.

 - **Optimisation de l’utilisation de la mémoire** lorsque les tâches nécessitent une grande quantité de mémoire, mais seulement pendant de courtes périodes et à des moments variables au cours de l’exécution. Des instances de nœud moins nombreuses, mais plus grandes et avec davantage de mémoire peuvent être employées pour gérer efficacement de tels pics, avec des tâches parallèles exécutées sur chaque nœud tirant parti de la mémoire abondante des nœuds à des moments différents.

 - **Atténuation des limites au nombre de nœuds** lorsque la communication entre les nœuds est requise au sein d’un pool. Actuellement, les pools configurés pour la communication entre les nœuds sont limités à 50 nœuds. Par conséquent, un plus grand nombre de tâches peuvent être exécutées simultanément si chaque nœud dans un pool de ce type est capable d’exécuter des tâches en parallèle.

 - **Réplication d’un cluster de calcul local**, comme lorsque vous déplacez un environnement de calcul vers Azure pour la première fois. L’augmentation du nombre maximal de tâches de nœud peut être effectuée pour refléter plus précisément une configuration physique existante, si cette configuration exécute actuellement plusieurs tâches par nœud de calcul.

## Exemple de scénario

Pour illustrer les avantages de l’exécution de tâches parallèles, supposons que votre application de tâche soit dotée de critères de processeur et de mémoire tels qu’une taille de nœud Standard\_D1 soit adaptée mais que pour exécuter la tâche dans le délai imparti, 1 000 nœuds de ce type soient requis. Au lieu d’utiliser les nœuds Standard\_D1, vous pouvez employer des nœuds Standard\_D14 avec 16 cœurs et activer l’exécution de tâches parallèles. Dans ce cas, vous pouvez donc utiliser *16 fois moins de nœuds* : au lieu de 1 000 nœuds, seuls 63 sont requis. Cela améliore considérablement le temps et l’efficacité de l’exécution des tâches si des fichiers d’application ou des données de référence de grande taille sont requis pour chaque nœud.

## Activer l’exécution des tâches parallèles

La configuration des nœuds de calcul dans votre solution Batch pour l’exécution des tâches parallèles s’effectue au niveau du pool. Lorsque vous travaillez avec l’API .NET Batch, la propriété [CloudPool.MaxTasksPerComputeNode][maxtasks_net] est définie lors de la création d’un pool. Dans l’API REST Batch, l’élément [maxTasksPerNode][maxtasks_rest] est défini dans le corps de la demande lors de la création du pool.

Azure Batch permet un nombre maximum de tâches par nœud allant jusqu’à quatre fois (4x) le nombre de cœurs de nœud. Par exemple, si le pool est configuré avec des nœuds de grande taille (quatre cœurs), alors la valeur `maxTasksPerNode` peut être définie sur 16. Des informations sur le nombre de cœurs pour chaque taille de nœud sont disponibles dans [Tailles de machines virtuelles](../virtual-machines/virtual-machines-size-specs.md). Vous trouverez plus d’informations sur les limites de service dans [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).

> [AZURE.TIP]Veillez à prendre en compte la valeur `maxTasksPerNode` lors de la construction d’une [formule de mise à l’échelle automatique][enable_autoscaling] pour votre pool. Par exemple, une formule qui évalue `$RunningTasks` pourrait être considérablement affectée par une augmentation des tâches par nœud. Consultez [Mettre automatiquement à l’échelle les nœuds de calcul dans un pool Azure Batch](batch-automatic-scaling.md) pour plus d’informations.

## Répartition des tâches

Lorsque les nœuds de calcul au sein d’un pool sont en mesure d’exécuter des tâches simultanément, il est important de spécifier comment vous souhaitez que vos tâches soient réparties entre les nœuds dans le pool.

À l’aide de la propriété [CloudPool.TaskSchedulingPolicy][task_schedule], vous pouvez spécifier que les tâches doivent être affectées uniformément entre tous les nœuds dans le pool (« diffusion ») ou qu’autant de tâches que possible doivent être affectées à chaque nœud avant que les tâches soient affectées à un autre nœud dans le pool (« compression »).

Pour illustrer l’importance de cette fonctionnalité, examinons le pool de nœuds Standard\_D14 dans l’exemple ci-dessus, configuré avec une propriété [CloudPool.MaxTasksPerComputeNode][maxtasks_net] d’une valeur de 16. Si la propriété [CloudPool.TaskSchedulingPolicy][task_schedule] est configurée avec une propriété [ComputeNodeFillType][fill_type] de type *Pack*, l’utilisation des 16 cœurs de chaque nœud est optimisée et un [pool de mise à l’échelle automatique](./batch-automatic-scaling.md) est autorisé pour nettoyer les nœuds inutilisés du pool (nœuds sans aucune tâche affectée), limitant ainsi l’utilisation des ressources et économisant de l’argent.

## Exemple .NET Batch

Cet extrait de code de l’API [.NET Batch][api_net] illustre une demande de création d’un pool contenant quatre grands nœuds avec un maximum de quatre tâches par nœud, et spécifiant une stratégie de planification des tâches qui remplit chaque nœud avec des tâches avant d’affecter des tâches à un autre nœud dans le pool. Pour plus d’informations sur l’ajout de pools à l’aide de l’API .NET Batch, consultez [BatchClient.PoolOperations.CreatePool][poolcreate_net].

        CloudPool pool = batchClient.PoolOperations.CreatePool(poolId: "mypool",
        													osFamily: "2",
        													virtualMachineSize: "large",
        													targetDedicated: 4);
        pool.MaxTasksPerComputeNode = 4;
        pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
        pool.Commit();

## Exemple REST Batch

Cet extrait de code de l’API [REST Batch][api_rest] illustre une demande de création d’un pool contenant deux grands nœuds avec un maximum de quatre tâches par nœud. Pour plus d’informations sur l’ajout de pools à l’aide de l’API REST, consultez [Ajout d’un pool à un compte][maxtasks_rest].

        {
          "id": "mypool",
          "vmSize": "Large",
          "osFamily": "2",
          "targetOSVersion": "*",
          "targetDedicated": 2,
          "enableInterNodeCommunication": false,
          "maxTasksPerNode": 4
        }

> [AZURE.NOTE]L’élément `maxTasksPerNode` et la propriété [MaxTasksPerComputeNode][maxtasks_net] peuvent uniquement être définis au moment de la création du pool. Ils ne peuvent pas être modifiés après qu’un pool a déjà été créé.

## Explorer l’exemple de projet

Découvrez le projet [ParallelNodeTasks][parallel_tasks_sample] sur GitHub, un exemple de code de travail qui illustre l’utilisation de la propriété [CloudPool.MaxTasksPerComputeNode][maxtasks_net]. Cette application de console C# utilise la bibliothèque [.NET Batch][api_net] pour créer un pool avec un ou plusieurs nœuds de calcul et exécute un nombre configurable de tâches sur ces nœuds pour simuler la charge variable. La sortie de l’application indique quels nœuds ont exécuté chaque tâche, ainsi qu’un résumé des paramètres et de la durée des tâches. La partie Résumé de la sortie de deux exécutions différentes de l’exemple d’application apparaît ci-dessous.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

La première exécution de l’exemple d’application montre qu’avec un nœud unique dans le pool et en utilisant la valeur par défaut d’une tâche par nœud, la durée de la tâche dépasse trente minutes.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

La deuxième exécution de l’exemple montre une réduction significative de la durée de la tâche en raison de la configuration d’un pool avec quatre tâches par nœud, ce qui permet à l’exécution des tâches parallèles de terminer les tâches en divisant la durée par quatre approximativement.

> [AZURE.NOTE]Les durées des tâches dans les résumés ci-dessus n’incluent pas le temps de création du pool. Chacune des tâches ci-dessus a été envoyée à des pools créés précédemment dont les nœuds de calcul étaient à l’état *Inactif* au moment de l’envoi.

## Carte thermique Batch Explorer

[Batch Explorer][batch_explorer], l’un des [exemples d’application][github_samples] d’Azure Batch, contient une fonctionnalité *Carte thermique* qui permet de visualiser l’utilisation des cœurs de nœud au sein d’un pool. Lorsque vous exécutez l’exemple d’application [ParallelTasks][parallel_tasks_sample], utilisez la fonctionnalité Carte thermique pour visualiser facilement l’activité des cœurs de nœud.

![Carte thermique Batch Explorer][1]

*Carte thermique Batch Explorer montrant quatre nœuds avec quatre cœurs chacun, chaque cœur exécutant actuellement une tâche*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/fr-FR/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[maxtasks_rest]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

[1]: ./media/batch-parallel-node-tasks\heat_map.png

<!---HONumber=Oct15_HO1-->