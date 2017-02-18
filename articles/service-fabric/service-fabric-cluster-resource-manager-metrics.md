---
title: "Gérer la charge des microservices Azure à l’aide de mesures | Microsoft Docs"
description: "Découvrez comment configurer et utiliser des mesures dans Service Fabric pour gérer la consommation des ressources de service."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 0d622ea6-a7c7-4bef-886b-06e6b85a97fb
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: bb27d279396aa7b670187560cebe2ed074576bad
ms.openlocfilehash: c0e5418574920495bf277f4127b97ff5b07b56ef


---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Gestion de la consommation des ressources et des charges dans Service Fabric à l’aide de mesures
Les mesures sont le terme générique utilisé dans Service Fabric pour les ressources qui intéressent vos services et qui sont fournies par les nœuds dans le cluster. En règle générale, les mesures vous servent à gérer les performances de vos services.

Il peut s’agir par exemple de l’utilisation de la mémoire, du disque ou du processeur. Il s’agit de mesures physiques, des ressources qui correspondent aux ressources physiques sur le nœud qui doivent être gérées. Les mesures peuvent également être (et sont généralement) des mesures logiques. Les mesures logiques sont des éléments tels que « MyWorkQueueDepth », « MessagesToProcess » ou « TotalRecords ». Les mesures logiques sont définies par l’application et correspondent à une certaine consommation de ressources physiques, mais l’application n’est pas vraiment en mesure de les mesurer. Il s’agit d’un cas courant. Nous avons remarqué que la plupart des mesures utilisées sont des mesures logiques. Le plus souvent, cela s’explique par le fait que de nombreux services sont aujourd’hui écrits en code managé. Le code managé fait qu’il peut être difficile de mesurer et de rendre compte de la consommation de ressources physiques d’un objet de service unique à partir d’un processus hôte. La complexité liée à la détermination de mesures personnalisées et à la création de rapports sur celles-ci explique également pourquoi nous proposons des mesures par défaut immédiatement prêtes à l’emploi.

## <a name="default-metrics"></a>Mesures par défaut
Supposons que vous souhaitiez vous lancer et que vous ne sachiez pas quelles ressources vous allez consommer, ni même quelles ressources seraient importantes pour vous. Vous allez donc implémenter, puis créer vos services sans spécifier de mesures. Pas de problème ! Service Fabric Cluster Resource Manager sélectionne des mesures simples pour vous. Si vous ne spécifiez aucune mesure, les mesures par défaut que nous utilisons aujourd’hui sont appelées PrimaryCount, ReplicaCount et Count. Le tableau suivant indique la charge associée à chaque objet de service pour chacune de ces mesures :

| Mesure | Charge de l’instance sans état | Charge secondaire avec état | Charge principale avec état |
| --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |
| ReplicaCount |0 |1 |1 |
| Nombre |1 |1 |1 |

OK, donc avec ces mesures par défaut, qu’obtenez-vous ? Il s’avère que pour des charges de travail de base, vous obtenez une répartition correcte des tâches. Dans l’exemple suivant, examinons ce qui se passe lorsque nous créons deux services. Le premier est un service avec état présentant trois partitions et une taille de jeu de réplicas cible de trois. Le second est un service sans état présentant une partition et un nombre d’instances de trois :

<center>
![Disposition du cluster avec les mesures par défaut][Image1]
</center>

Voici ce que vous obtenez :

* Les réplicas principaux du service avec état ne sont pas empilés sur un nœud unique
* Les réplicas pour la même partition ne sont pas sur le même nœud
* L’ensemble des réplicas principaux et secondaires sont répartis dans le cluster
* L’ensemble des objets de service sont alloués uniformément sur chaque nœud

Bien !

Tout cela fonctionne très bien jusqu’à ce que vous commenciez à exécuter un grand nombre de charges de travail importantes : quelle est la probabilité que le schéma de partitionnement que vous avez choisi garantisse une utilisation des ressources parfaitement uniforme par l’ensemble des partitions ? Quelle est la probabilité que la charge d’un service donné reste constante au fil du temps, ou même simplement identique à celle observée actuellement ?

Dans la pratique, vous pouvez vous contenter des mesures par défaut, mais l’utilisation de votre cluster est alors généralement inférieure à ce que vous souhaitez. En effet, la création de rapports sur les mesures par défaut n’est pas adaptative et part du principe que tous les éléments sont équivalents. Dans le pire des cas, utiliser simplement les mesures par défaut peut également entraîner des surplanifications de nœuds, ce qui peut causer des problèmes de performances. Des mesures personnalisées et des rapports de charge dynamique, qui seront abordées dans la suite de cet article, sont mieux adaptés. Dans le cas d’une charge de travail importante, la probabilité que tous les services soient toujours équivalents est faible. Si vous souhaitez tirer le meilleur parti de votre cluster et éviter de rencontrer des problèmes de performances, vous devez vous tourner vers les mesures personnalisées.

## <a name="custom-metrics"></a>Mesures personnalisées
La configuration des mesures se fait lors de la création du service, sur la base d’une instance de service nommée.

N’importe quelle mesure présente un certain nombre de propriétés qui la décrivent : un nom, une charge par défaut et un poids.

* Nom de la mesure : identificateur unique utilisé par Resource Manager pour la mesure au sein du cluster.
* Charge par défaut : la charge par défaut est représentée différemment selon que le service est avec ou sans état.
  * Pour les services sans état, chaque mesure présente une propriété unique nommée DefaultLoad.
  * Pour les services avec état, vous devez définir les éléments suivants :
    * PrimaryDefaultLoad : quantité par défaut de la mesure que le service consomme lorsqu’il s’agit d’un réplica principal.
    * SecondaryDefaultLoad : quantité par défaut de la mesure que le service consomme lorsqu’il s’agit d’un réplica secondaire.
* Weight : poids de la mesure, qui définit son importance par rapport aux autres mesures utilisées pour ce service.

Si vous définissez des mesures personnalisées et souhaitez également utiliser les mesures par défaut, vous devez explicitement les rajouter. En effet, il faut que la relation entre les mesures par défaut et vos mesures personnalisées soit claire. Par exemple, vous accordez peut-être plus d’importance à la consommation de mémoire ou à la mesure WorkQueueDepth qu’à la répartition des réplicas principaux.

### <a name="defining-metrics-for-your-service---an-example"></a>Exemple de définition de mesures pour votre service
Supposons que vous souhaitiez configurer un service qui signale une mesure appelée « MemoryInMb » et que vous souhaitiez également utiliser les mesures par défaut. Supposons également que vous ayez effectué des mesures et que vous sachiez que normalement un réplica principal de ce service utilise 20 unités de la mesure « MemoryInMb », tandis que les réplicas secondaires de ce même service en utilisent 5. Vous savez que la mémoire est la mesure la plus importante en matière de gestion des performances de ce service spécifique, mais vous souhaitez néanmoins que les réplicas principaux soient équilibrés. L’équilibrage des réplicas principaux s’avère judicieux pour éviter que la perte d’un nœud ou d’un domaine d’erreur n’ait un impact sur la majorité des réplicas principaux. En plus de ces ajustements, vous souhaitez utiliser les mesures par défaut.

Voici le code que vous devez écrire pour créer un service avec cette configuration de mesures :

Code :

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
StatefulServiceLoadMetricDescription memoryMetric = new StatefulServiceLoadMetricDescription();
memoryMetric.Name = "MemoryInMb";
memoryMetric.PrimaryDefaultLoad = 20;
memoryMetric.SecondaryDefaultLoad = 5;
memoryMetric.Weight = ServiceLoadMetricWeight.High;

StatefulServiceLoadMetricDescription primaryCountMetric = new StatefulServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

StatefulServiceLoadMetricDescription replicaCountMetric = new StatefulServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

StatefulServiceLoadMetricDescription totalCountMetric = new StatefulServiceLoadMetricDescription();
totalCountMetric.Name = "Count";
totalCountMetric.PrimaryDefaultLoad = 1;
totalCountMetric.SecondaryDefaultLoad = 1;
totalCountMetric.Weight = ServiceLoadMetricWeight.Low;

serviceDescription.Metrics.Add(memoryMetric);
serviceDescription.Metrics.Add(primaryCountMetric);
serviceDescription.Metrics.Add(replicaCountMetric);
serviceDescription.Metrics.Add(totalCountMetric);

await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

(Rappel : si vous voulez simplement utiliser les mesures par défaut, vous n’avez pas du tout besoin d’utiliser la collection de mesures ou d’intervenir.)

Maintenant que nous avons introduit le concept des mesures et vu un exemple d’application, nous allons examiner chacun de ces paramètres plus en détail et décrire le comportement que vous obtiendrez.

## <a name="load"></a>charger
La définition de mesures a pour but de représenter certaines charges. Une charge correspond à la quantité d’une mesure spécifique qui est consommée par une instance ou un réplica de service sur un nœud donné. La charge attendue peut être configurée lors de la création d’un service, mise à jour après la création du service et signalée pour chaque objet de service.

## <a name="default-load"></a>Charge par défaut
La charge par défaut correspond à la charge consommée par défaut par chaque objet de service (instance ou réplica). Pour les services plus simples, la charge par défaut est une définition statique qui n’est jamais mise à jour et qui est donc utilisée pendant toute la durée de vie du service. La charge par défaut est idéale pour les scénarios de planification de capacité simples où certaines quantités de ressources sont dédiées à différentes charges de travail.

Cluster Resource Manager permet aux services avec état de spécifier une charge par défaut différente pour leurs réplicas principaux et secondaires, tandis que les services sans état peuvent seulement spécifier une valeur. Dans le cas des services avec état, les charges par défaut des réplicas principaux et secondaires sont généralement différentes, puisque les réplicas n’exécutent pas les mêmes tâches selon qu’ils remplissent l’un ou l’autre rôle. Par exemple, les réplicas principaux assument généralement les opérations de lecture et d’écriture (ainsi que la plupart des tâches de calcul), ce qui n’est pas le cas des réplicas secondaires. La charge par défaut d’un réplica principal devrait être supérieure à celle des réplicas secondaires, mais les valeurs réelles dépendent de vos propres mesures.

## <a name="dynamic-load"></a>Charge dynamique
Supposons que vous exécutiez votre service depuis un certain temps. En procédant à certaines analyses, vous avez observé que :

1. Certaines partitions ou instances d’un service donné consomment plus de ressources que d’autres.
2. La charge de certains services varie au fil du temps.

De nombreux facteurs peuvent causer ces types de fluctuations de charge. Le service ou la partition peuvent être associés à un client spécifique ou correspondre à des charges de travail qui varient au cours de la journée. Quelle que soit la raison, il n’existe pas de valeur unique utilisable pour la charge par défaut. La valeur que vous choisissez pour la charge par défaut sera forcément inadaptée à un moment ou un autre. C’est problématique, car lorsque la charge par défaut est incorrecte, Resource Manager alloue un nombre trop important ou insuffisant de ressources pour votre service. Certains nœuds se retrouvent alors sur ou sous-utilisés, même si Cluster Resource Manager considère que le cluster est équilibré. Les charges par défaut sont toujours adaptées, car elles fournissent certaines informations, mais elles n’offrent pas un tableau complet des charges de travail importantes la plupart du temps. C’est la raison pour laquelle Cluster Resource Manager permet à chaque objet de service de mettre à jour sa propre charge pendant l’exécution. On parle de « rapports de charge dynamique ».

Les rapports de charge dynamique permettent aux réplicas ou aux instances d’ajuster leur allocation ou la charge signalée pour les mesures pendant toute leur durée de vie. Des réplicas ou des instances de service inactifs signalent généralement qu’ils utilisent une faible quantité d’une mesure spécifique. Des réplicas ou des instances de service actifs signalent quant à eux qu’ils en utilisent une quantité plus importante.

Les rapports par réplica ou par instance permettent à Cluster Resource Manager de réorganiser les objets de service individuels dans le cluster pour garantir que les services obtiennent les ressources dont ils ont besoin. Les services actifs peuvent ainsi récupérer les ressources de réplicas ou d’instances qui sont actuellement inactifs ou moins actifs.

Dans votre modèle Reliable Service, le code permettant de signaler dynamiquement la charge se présenterait ainsi :

Code :

```csharp
this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
```

Les réplicas ou les instances de service peuvent signaler une charge uniquement pour les mesures incluses dans leur configuration lors de leur création. La liste des mesures qu’un service peut signaler correspond à l’ensemble de mesures spécifié au moment de la création du service. La liste des mesures associées au service peut également être mise à jour dynamiquement. Si un réplica ou une instance de service tente de signaler une charge pour une mesure non incluse dans sa configuration actuelle, Service Fabric consigne ce rapport mais l’ignore. Si des mesures valides étaient également signalées dans cet appel d’API, ces rapports sont acceptés et utilisés. C’est un point non négligeable, car cela permet une plus grande expérimentation. Le code peut mesurer et signaler toutes les mesures qu’il connaît, et l’opérateur peut spécifier et mettre à jour la configuration de mesures de ce service sans avoir à modifier le code. Par exemple, l’administrateur ou l’équipe responsable des opérations peut désactiver une mesure présentant un rapport incorrect pour un service spécifique, reconfigurer les poids des mesures en fonction du comportement ou activer une nouvelle mesure uniquement une fois que le code a été déployé et validé par le biais d’autres mécanismes.

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Mélange des valeurs de charge par défaut et des rapports de charge dynamique
Si la charge par défaut n’est pas suffisante et les rapports de charge dynamique sont recommandés, est-il possible d’utiliser les deux ? Oui. Il s’agit en fait de la configuration recommandée. Lorsqu’une charge par défaut est définie et les rapports de charge dynamique sont utilisés, la charge par défaut sert d’estimation jusqu’à ce que les rapports dynamiques arrivent. Cluster Resource Manager dispose ainsi de données avec lesquelles travailler. La charge par défaut permet à Cluster Resource Manager de positionner les objets de service à un emplacement adéquat lors de leur création. Si aucune information n’est fournie pour la charge par défaut, le positionnement des services est aléatoire. Lorsque les rapports de charge arrivent ensuite, Cluster Resource Manager doit presque systématiquement déplacer les services.

Reprenons l’exemple précédent et voyons ce qui se passe lorsque nous ajoutons des mesures personnalisées et les rapports de charge dynamique. Dans cet exemple, nous utilisons « Memory » comme exemple de mesure. Supposons que nous ayons initialement créé le service avec état avec la commande suivante :

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Pour rappel, la syntaxe utilisée est ("MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad").

Voyons quelle pourrait être une disposition de cluster possible :

<center>
![Cluster équilibré avec les mesures par défaut et des mesures personnalisées][Image2]
</center>

Informations importantes à noter :

* Comme les réplicas ou les instances utilisent la charge par défaut du service jusqu’à ce qu’ils signalent leur propre charge, nous savons que les réplicas à l’intérieur de la partition 1 du service avec état n’ont pas signalé dynamiquement de charge
* Les réplicas secondaires dans une partition peuvent avoir leur propre charge
* Dans l’ensemble, les mesures semblent équilibrées. Pour rappel, le rapport entre la charge maximale et la charge minimale est égal à 1,75 (le nœud avec la charge la plus importante est N3, celui avec la moins importante N2, et 28/16 = 1,75).

Il reste certaines choses à expliquer :

* Quel élément a permis de déterminer qu’un rapport de 1,75 était raisonnable ou non ? Comment Cluster Resource Manager sait-il si c’est suffisant ou s’il reste d’autres actions à effectuer ?
* À quel moment l’équilibrage se produit-il ?
* Qu’est-ce que cela signifie que Memory (mémoire) était pondérée comme étant « High » (haute) ?

## <a name="metric-weights"></a>Poids des mesures
Il est important de pouvoir suivre les mêmes mesures pour différents services. C’est ce qui permet à Cluster Resource Manager de suivre la consommation dans le cluster, d’équilibrer la consommation entre les nœuds et de faire en sorte que les nœuds ne dépassent pas leur capacité. Cependant, les services n’accordent pas forcément la même importance à une même mesure. Par ailleurs, dans un cluster qui présente un grand nombre de mesures et de services, il se peut qu’il n’existe pas de solution parfaitement équilibrée pour toutes les mesures. Comment Cluster Resource Manager doit-il gérer ces situations ?

Les poids des mesures permettent à Cluster Resource Manager de déterminer comment équilibrer le cluster lorsqu’il n’existe aucune réponse adaptée. Les poids des mesures permettent également à Cluster Resource Manager d’équilibrer des services spécifiques différemment. Les mesures peuvent avoir quatre niveaux de poids : Zero, Low, Medium et High (zéro, faible, moyen et haut). Une mesure avec un poids de Zero n’entre aucunement en compte dans la décision d’équilibrage des éléments, mais sa charge contribue toujours à des aspects tels que la capacité.

Le véritable impact des différents poids de mesure dans le cluster est que Cluster Resource Manager génère plusieurs solutions. Les poids des mesures indiquent à Cluster Resource Manager que certaines mesures sont plus importantes que d’autres. Lorsqu’il n’existe aucune solution parfaite, Cluster Resource Manager peut privilégier des solutions qui équilibrent mieux les mesures de poids supérieur. Si un service considère qu’une mesure n’est pas importante, il peut trouver que l’utilisation de cette mesure cause un déséquilibre. Cela permet à un autre service d’obtenir une répartition uniforme importante pour lui.

Prenons pour exemple quelques rapports de charge et regardons de quelle façon les différents poids de mesure peuvent entraîner des allocations différentes dans le cluster. Dans cet exemple, nous voyons que lorsque le poids relatif des mesures est échangé, Cluster Resource Manager privilégie d’autres solutions et crée des dispositions différentes des services.

<center>
Exemple de poids de mesures et impact sur les solutions d’équilibrage![][Image3]
</center>

Dans cet exemple, nous avons quatre services différents qui signalent tous des valeurs différentes pour deux mesures, MetricA et MetricB. Dans le premier cas, tous les services définissent la mesure MetricA comme la plus importante (Weight = High) et la mesure MetricB comme non importante (Weight = Low). On peut alors observer que Cluster Resource Manager positionne les services de sorte que la mesure MetricA soit mieux équilibrée (elle présente un écart-type plus faible) que la mesure MetricB. Dans le deuxième cas, nous inversons les poids des mesures. Cluster Resource Manager échangerait alors probablement les services A et B afin de proposer une allocation où la mesure MetricB est mieux équilibrée que la mesure MetricA.

### <a name="global-metric-weights"></a>Poids globaux des mesures
Si le service A (ServiceA) définit la mesure A (MetricA) comme étant la plus importante et que le service B (ServiceB) n’a aucune préférence, quel est le poids réel finalement utilisé ?

En fait, plusieurs poids sont suivis pour chaque mesure. Le premier ensemble correspond aux poids que chaque service a définis pour la mesure. L’autre poids est un poids global, qui est égal à la moyenne de tous les services qui signalent cette mesure. Cluster Resource Manager utilise ces deux poids pour calculer les scores des solutions. En effet, il est important qu’un service soit équilibré en fonction de ses propres priorités, mais également que l’ensemble du cluster soit alloué correctement.

Que se passerait-il si Cluster Resource Manager n’accordait aucune importance à l’équilibre global et local ? Eh bien, il est facile de créer des solutions qui sont globalement équilibrées mais qui entraînent une mauvaise allocation des ressources pour des services individuels. Dans l’exemple suivant, examinons les mesures par défaut configurées pour un service avec état et observons ce qui se passe lorsque seul l’équilibre global est pris en considération :

<center>
Impact d’une solution uniquement globale![][Image4]
</center>

Dans l’exemple du haut où nous avons uniquement tenu compte de l’équilibre global, le cluster dans son ensemble est effectivement bien équilibré. Tous les nœuds présentent le même nombre de réplicas principaux et le même nombre total de réplicas. Cependant, si on examine l’impact réel de cette allocation, la situation n’est pas si bonne : la perte d’un nœud a un impact disproportionné sur une charge de travail spécifique, car tous ses réplicas principaux sont également perdus. Par exemple, en cas de défaillance du premier nœud, les trois réplicas principaux des trois différentes partitions du service Circle seraient tous perdus. À l’inverse, les partitions des deux autres services (Triangle et Hexagon) perdent un réplica, mais cela ne crée aucune interruption (à part celle de devoir récupérer le réplica perdu).

Dans l’exemple du bas, Cluster Resource Manager a réparti les réplicas en fonction à la fois de l’équilibre global et de l’équilibre du service. Lorsque Cluster Resource Manager calcule le score de la solution, il donne la majorité du poids à la solution globale et une partie (configurable) aux services individuels. L’équilibre global est calculé en fonction de la moyenne des poids des mesures configurés pour chacun des services. Chaque service est équilibré selon ses propres poids de mesure. Cela permet de s’assurer que les services sont équilibrés entre eux en fonction de leurs propres besoins autant que possible. Par conséquent, en cas de défaillance du même premier nœud, la perte des réplicas principaux (et secondaires) est répartie sur toutes les partitions de tous les services. L’impact est le même pour chacun d’eux.

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur les autres options disponibles pour la configuration des services, consultez la rubrique sur les autres configurations de Cluster Resource Manager disponibles, [En savoir plus sur la configuration des services](service-fabric-cluster-resource-manager-configure-services.md)
* La définition des mesures de défragmentation est une façon de consolider la charge sur les nœuds au lieu de la répartir. Pour savoir comment configurer la défragmentation, reportez-vous à [cet article](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* Pour en savoir plus sur la façon dont Cluster Resource Manager gère et équilibre la charge du cluster, consultez l’article sur [l’équilibrage de la charge](service-fabric-cluster-resource-manager-balancing.md)
* Commencez au début pour [obtenir une présentation de Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
* Le coût du déplacement est une façon de signaler à Cluster Resource Manager que certains services sont plus coûteux à déplacer que d’autres. Pour en savoir plus sur le coût du déplacement, reportez-vous à [cet article](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png



<!--HONumber=Jan17_HO4-->


