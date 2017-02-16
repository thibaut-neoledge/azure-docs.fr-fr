---
title: "Service Fabric Cluster Resource Manager - Groupes d’applications | Microsoft Docs"
description: "Vue d’ensemble de la fonctionnalité de groupe d’applications dans Service Fabric Cluster Resource Manager"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 4cae2370-77b3-49ce-bf40-030400c4260d
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: eef9cbca0479801cbac53ae1ae5713d4dac6a4bf
ms.openlocfilehash: 57525cd41d518b12f3f465d5dea7247659e3c8b6


---
# <a name="introduction-to-application-groups"></a>Présentation des groupes d’applications
Service Fabric Cluster Resource Manager gère habituellement les ressources de cluster en répartissant la charge (représentée par les [métriques](service-fabric-cluster-resource-manager-metrics.md)) uniformément dans le cluster. Service Fabric gère également la capacité des nœuds du cluster et le cluster dans son ensemble via la notion de [capacité](service-fabric-cluster-resource-manager-cluster-description.md). Les mesures et la capacité sont idéales pour divers types de charges de travail, mais les modèles qui utilisent beaucoup d’instances d’application Service Fabric différentes imposent parfois des conditions supplémentaires. Parmi les exigences supplémentaires courantes :

* Possibilité de réserver de la capacité pour les services d’une instance d’application dans le cluster
* Possibilité de limiter le nombre total de nœuds que les services au sein d’une application sont autorisés à s’exécuter
* Définition des capacités sur l’instance d’application afin de limiter le nombre ou la consommation des ressources des services qu’elle renferme

Pour répondre à ces exigences, Service Fabric Cluster Resource Manager prend en charge les groupes d’applications.

## <a name="managing-application-capacity"></a>Gestion de la capacité d’application
La capacité de l’application peut servir à limiter le nombre de nœuds couverts par une application. Elle peut également limiter la charge des mesures sur les services d’application sur des nœuds individuels et au total. Elle peut également servir à réserver des ressources du cluster pour l’application.

La capacité des nouvelles applications peut être définie lors de leur création. Elle peut également être mise à jour pour les applications existantes.

### <a name="limiting-the-maximum-number-of-nodes"></a>Limitation du nombre maximal de nœuds
Le cas d’utilisation le plus simple de la capacité d’application est lorsqu’une instanciation d’application doit être limitée à un certain nombre de nœuds au maximum. Si aucune capacité d’application n’est spécifiée, Service Fabric Cluster Resource Manager crée et place les services en suivant les règles normales (équilibrage ou défragmentation).

L’image suivante montre une instance d’application avec et sans un nombre maximal de nœuds définis. Il n’y a aucune garantie concernant le regroupement des réplicas ou des instances des services, ou les nœuds spécifiques utilisés.

<center>
![Instance d’application définissant le nombre maximal de nœuds][Image1]
</center>

Dans l’exemple de gauche, aucune capacité d’application n’est définie pour l’application, qui inclut trois services. Cluster Resource Manager a réparti tous les réplicas sur six nœuds disponibles afin d’optimiser l’équilibre au sein du cluster. Dans l’exemple de droite, nous voyons la même application limitée à trois nœuds.

Le paramètre qui contrôle ce comportement est appelé MaximumNodes. Ce paramètre peut être défini lors de la création de l’application, ou mis à jour pour une instance d’application qui était déjà en cours d’exécution.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –Name fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;

adUpdate.Metrics.Add(appMetric);
```

## <a name="application-metrics-load-and-capacity"></a>Mesures, charge et capacité d’application
Les groupes d’applications vous permettent également de définir les mesures associées à une instance d’application donnée, ainsi que la capacité de l’application par rapport à ces mesures. Cela vous permet de suivre, réserver et limiter la consommation des ressources des services à l’intérieur de cette instance d’application.

Pour chaque mesure d’application, il existe deux valeurs qui peuvent être définies :

* **Capacité totale de l’application** : représente la capacité totale de l’application pour une mesure donnée. Cluster Resource Manager interdit la création de nouveaux services dans cette instance d’application qui provoqueraient le dépassement de cette valeur par la charge totale. Par exemple, supposons que l’instance d’application dispose d’une capacité de 10 et a déjà une charge de cinq. Dans ce cas, la création d’un service avec une charge totale par défaut de 10 est interdite.
* **Capacité maximale du nœud** : indique la charge totale maximale pour les réplicas des services se trouvant dans l’application sur un seul nœud. Si la charge totale sur le nœud dépasse cette capacité, Cluster Resource Manager tente de déplacer des réplicas vers d’autres nœuds afin de respecter la contrainte de capacité.

## <a name="reserving-capacity"></a>Capacité réservée
Une autre utilisation courante pour les groupes d’applications consiste à s’assurer que les ressources au sein du cluster sont réservées à une instance d’application donnée. Cela se produit même si l’instance d’application ne contient pas encore tous les services, ou si ces derniers ne consomment pas encore de ressources. Voyons comment cela fonctionne.

### <a name="specifying-a-minimum-number-of-nodes-and-resource-reservation"></a>Spécification d’un nombre minimal de nœuds et réservation de ressources
La réservation de ressources pour une instance d’application implique de spécifier deux paramètres supplémentaires : *MinimumNodes* et *NodeReservationCapacity*

* **MinimumNodes** : tout comme vous pouvez définir un nombre maximal de nœuds sur lesquels les services d’une application peuvent s’exécuter, vous pouvez également indiquer le nombre minimal. Ce paramètre définit précisément le nombre de nœuds sur lesquels les ressources doivent être réservées, ce qui garantit la capacité au sein du cluster lors de la création de l’instance d’application.
* **NodeReservationCapacity** : le paramètre NodeReservationCapacity peut être défini pour chaque mesure de l’application. Ce paramètre définit la quantité de charge de mesure réservée pour l’application sur n’importe quel nœud où sont placés les réplicas ou les instances des services qu’elle contient.

Voyons un exemple de réservation de capacité :

<center>
![Instances d’application définissant la capacité de réserve][Image2]
</center>

Dans l’exemple de gauche, aucune capacité n’est définie pour les applications. Cluster Resource Manager équilibre tout conformément aux règles normales.

Dans l’exemple de droite, supposons que l’application a été créée avec les paramètres suivants :

* MinimumNodes défini sur deux
* MaximumNodes défini sur trois
* Une mesure d’application définie avec
  * NodeReservationCapacity sur 20
  * MaximumNodeCapacity sur 50
  * TotalApplicationCapacity sur 100

Service Fabric réserve la capacité sur deux nœuds pour l’application bleue et n’autorise pas les services d’autres instances de l’application dans le cluster à utiliser cette capacité. Cette capacité d’application réservée est considérée comme consommée et déduite de la capacité restante sur ce nœud et dans le cluster.

Lorsqu’une application est créée avec réservation, Cluster Resource Manager réserve une capacité égale à MinimumNodes * NodeReservationCapacity (pour chaque mesure). Cependant, la capacité est réservée sur un nœud spécifique uniquement lorsqu’au moins un réplica est placé dessus. Cette dernière réservation permet plus de flexibilité et une meilleure utilisation des ressources dans la mesure où les ressources sont réservées uniquement sur des nœuds lorsque cela est nécessaire.

## <a name="obtaining-the-application-load-information"></a>Obtention des informations sur la charge d’application
Pour chaque application pour laquelle une capacité d’application a été définie, vous pouvez obtenir les informations sur la charge totale signalée par les réplicas de ses services.

Par exemple, la charge peut être récupérée à l’aide de l’applet de commande PowerShell suivant :

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1

```

La requête ApplicationLoad renvoie des informations de base sur la capacité d’application spécifiée pour l’application. Ces informations comprennent les informations sur les nombres minimum et maximum de nœuds, et le nombre de nœuds actuellement occupés par l’application. Elles comprennent également des informations sur chaque mesure de charge d’application, notamment :

* Nom de la mesure : le nom de la mesure.
* Capacité de réserve : la capacité du cluster qui est réservée dans le cluster pour cette application.
* Charge de l’application : la charge totale des réplicas enfants de cette application.
* Capacité d’application : la valeur maximale autorisée pour la charge de l’application.

## <a name="removing-application-capacity"></a>Suppression de la capacité d’application
Une fois les paramètres de capacité d’application définis pour une application, vous pouvez les supprimer à l’aide d’API de mise à jour de l’application ou d’applets de commande PowerShell. Par exemple :

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Cette commande supprime tous les paramètres de capacité d’application de l’application. L’effet de la commande est immédiat. Une fois cette commande terminée, Cluster Resource Manager revient à l’utilisation du comportement par défaut pour la gestion des applications. Les paramètres de capacité d’application peuvent être spécifiés à nouveau via Update-ServiceFabricApplication.

### <a name="restrictions-on-application-capacity"></a>Restrictions sur la capacité d’application
Il existe plusieurs restrictions à respecter concernant les paramètres de capacité d’application. En cas d’erreurs de validation, la création ou la mise à jour de l’application est rejetée avec une erreur, et aucune modification n’est apportée.

* Tous les paramètres entiers doivent être des nombres positifs.
* La valeur du paramètre MinimumNodes ne doit jamais être supérieure à celle du paramètre MaximumNodes.
* Si les capacités d’une mesure de charge sont définies, elles doivent respecter les règles suivantes :
  * La capacité de réservation de nœud ne doit pas être supérieure à la capacité maximale de nœud. Par exemple, vous ne pouvez pas limiter la capacité de la mesure « UC » sur le nœud à deux unités et réserver trois unités sur chaque nœud.
  * Si le paramètre MaximumNodes est spécifié, le résultat du produit de sa valeur par la capacité maximale de nœud ne doit pas être supérieur à la capacité totale d’application. Par exemple, supposons que la capacité maximale de nœud pour les mesures de charge « UC » est définie sur huit. Supposons également que vous définissez le nombre maximal de nœuds sur&10;. Dans ce cas, la capacité totale de l’application doit être supérieure à 80 pour cette mesure de charge.

Les restrictions sont appliquées lors de la création d’applications (côté client) et au cours de la mise à jour des applications (côté serveur).

## <a name="how-not-to-use-application-capacity"></a>Cas dans lesquels la capacité d’application ne doit pas être utilisée
* N’essayez pas d’utiliser les fonctionnalités de groupe d’applications pour contraindre l’application à un sous-ensemble _spécifique_ de nœuds. En d’autres termes, vous pouvez spécifier que l’application peut exécuter au maximum cinq nœuds, mais vous ne pouvez pas déterminer les cinq nœuds dans le cluster. Vous pouvez contraindre une application à des nœuds spécifiques à l’aide des contraintes de placement pour les services.
* N’essayez pas d’utiliser la capacité d’application pour vous assurer que deux services d’une même application sont placés en parallèle. Vous pouvez assurer que les services s’exécutent sur le même nœud à l’aide des affinités ou des contraintes de placement selon vos besoins spécifiques.

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur les autres options disponibles pour la configuration des services, consultez la rubrique sur les autres configurations de Cluster Resource Manager disponibles [En savoir plus sur la configuration des services](service-fabric-cluster-resource-manager-configure-services.md)
* Pour en savoir plus sur la façon dont Cluster Resource Manager gère et équilibre la charge du cluster, consultez l’article sur l’ [équilibrage de la charge](service-fabric-cluster-resource-manager-balancing.md)
* Commencez au début et [obtenez une présentation de Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
* Pour plus d’informations sur le fonctionnement des mesures en général, consultez les informations sur les [mesures de charge Service Fabric](service-fabric-cluster-resource-manager-metrics.md)
* Cluster Resource Manager comporte de nombreuses options permettant de décrire le cluster. Pour en savoir plus sur celles-ci, consultez cet article sur la [description d’un cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png



<!--HONumber=Jan17_HO1-->


