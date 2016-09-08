<properties
   pageTitle="Service Fabric Cluster Resource Manager - Groupes d’applications | Microsoft Azure"
   description="Vue d’ensemble de la fonctionnalité de groupe d’applications dans Service Fabric Cluster Resource Manager"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>

# Présentation des groupes d’applications
Service Fabric Cluster Resource Manager gère habituellement les ressources de cluster en répartissant la charge (représentée par les métriques) uniformément dans le cluster. Service Fabric gère également la capacité des nœuds du cluster et le cluster dans son ensemble via la notion de capacité. Ceci est idéal pour divers types de charges de travail, mais les modèles qui utilisent beaucoup d’instances d’application Service Fabric différentes imposent parfois des conditions supplémentaires. Parmi les exigences supplémentaires courantes :

- Possibilité de réserver de la capacité pour les services d’une instance d’application sur un certain nombre de nœuds
- Possibilité de limiter le nombre total de nœuds sur lesquels un ensemble de services au sein d’une application peut s’exécuter
- Définition des capacités sur l’instance d’application afin de limiter le nombre ou la consommation des ressources des services qu’elle renferme

Afin de répondre à ces exigences, nous avons développé la prise en charge de ce que nous appelons des groupes d’applications.

## Gestion de la capacité d’application
La capacité d’application peut être utilisée pour limiter le nombre de nœuds couverts par une application, ainsi que la charge totale des mesures des instances d’applications sur les nœuds individuels. Elle peut également servir à réserver des ressources du cluster pour l’application.

La capacité des nouvelles applications peut être définie lors de leur création. Elle peut également être mise à jour pour les applications existantes qui ont été créées sans indication de la capacité d’application.

### Limitation du nombre maximal de nœuds
Le cas d’utilisation le plus simple de la capacité d’application est lorsqu’une instanciation d’application doit être limitée à un certain nombre de nœuds au maximum. Si aucune capacité d’application n’est spécifiée, Service Fabric Cluster Resource Manager instanciera des réplicas selon les règles normales (équilibrage ou défragmentation), ce qui signifie généralement que ses services seront répartis sur tous les nœuds disponibles dans le cluster, ou, si la défragmentation est activée, sur un certain nombre de nœuds (arbitraire mais plus petit).

L’illustration suivante montre le placement potentiel d’une instance d’application sans nombre maximal de nœuds défini, puis la même application avec un nombre maximal de nœuds défini. Notez qu’il n’y a aucune garantie concernant le regroupement des réplicas ou des instances des services.

![Instance d’application définissant le nombre maximal de nœuds][Image1]

Dans l’exemple de gauche, aucune capacité d’application n’est définie pour l’application, qui inclut trois services. Cluster Resource Manager a fait le choix logique de répartir tous les réplicas sur six nœuds disponibles afin d’optimiser l’équilibre au sein du cluster. L’exemple de droite présente la même application mais cette fois limitée à trois nœuds : Service Fabric Cluster Resource Manager a atteint l’équilibre optimal pour les réplicas des services de l’application.

Le paramètre qui contrôle ce comportement est appelé MaximumNodes. Ce paramètre peut être défini lors de la création de l’application, ou mis à jour pour une instance d’application qui s’exécutait déjà, auquel cas Service Fabric Cluster Resource Manager contraint les réplicas des services d’application au nombre maximal de nœuds défini.

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

## Mesures, charge et capacité d’application
Les groupes d’applications vous permettent également de définir les mesures associées à une instance d’application donnée, ainsi que la capacité de l’application par rapport à ces mesures. Par exemple, vous pouvez indiquer qu’il est possible de créer autant de services que nécessaire dans

Pour chaque mesure, deux valeurs peuvent être définies pour décrire la capacité de l’instance d’application concernée :

-	Capacité totale de l’application : représente la capacité totale de l’application pour une mesure donnée. Service Fabric Cluster Resource Manager tente de limiter le nombre total de charges de mesure des services de cette application à la valeur spécifiée ; en outre, si les services de l’application utilisent déjà la charge à hauteur de cette limite, Service Fabric Cluster Resource Manager interdira la création de nouveaux services et partitions, car elle entraînerait le dépassement de cette limite par la charge totale.
-	Capacité maximale du nœud : indique la charge totale maximale pour les réplicas des services des applications sur un seul nœud. Si la charge totale sur le nœud dépasse cette capacité, Service Fabric Cluster Resource Manager tente de déplacer des réplicas vers d’autres nœuds afin de respecter la contrainte de capacité.

## Capacité réservée
Les groupes d’applications sont également couramment utilisés pour s’assurer que les ressources au sein du cluster sont réservées à une instance d’application donnée, même si l’instance d’application ne renferme pas encore de services, ou s’ils ne consomment pas encore les ressources. Voyons comment cela fonctionne.

### Spécification d’un nombre minimal de nœuds et réservation de ressources
La réservation de ressources pour une instance d’application implique de spécifier deux paramètres supplémentaires : *MinimumNodes* et *NodeReservationCapacity*

- MinimumNodes : tout comme vous pouvez définir un nombre maximal de nœuds cible sur lesquels les services d’une application peuvent s’exécuter, vous pouvez également indiquer le nombre minimal de nœuds sur lesquels une application doit s’exécuter. Ce paramètre définit précisément le nombre minimal de nœuds sur lesquels les ressources doivent être réservées, ce qui garantit la capacité au sein du cluster lors de la création de l’instance d’application.
- NodeReservationCapacity : le paramètre NodeReservationCapacity peut être défini pour chaque mesure de l’application. Il définit la quantité de charge de mesure réservée pour l’application sur n’importe quel nœud où sont placés les réplicas ou les instances des services qu’elle contient.

Voyons un exemple de réservation de capacité :

![Instances d’application définissant la capacité de réserve][Image2]

Dans l’exemple de gauche, aucune capacité n’est définie pour les applications. Service Fabric Cluster Resource Manager équilibre les réplicas et instances des services enfants des applications, ainsi que ceux d’autres services (en dehors de l’application), afin de garantir l’équilibre au sein du cluster.

Dans l’exemple de droite, imaginons que l’application a été créée avec un paramètre MinimumNodes défini sur 2, un paramètre MaximumNodes défini sur 3 et une mesure d’application définie comme suit : une réservation de 20, une capacité maximale sur un nœud de 50 et une capacité totale d’application de 100. Service Fabric réserve de la capacité sur les deux nœuds pour l’application bleue et n’autorise pas les autres réplicas dans le cluster à utiliser cette capacité. Cette capacité d’application réservée est considérée comme consommée et déduite de la capacité de cluster restante.

Lorsqu’une application est créée avec une réservation, Cluster Resource Manager réserve de la capacité à une valeur égale au produit MinimumNodes * NodeReservationCapacity dans le cluster, mais il ne réservera pas de capacité sur des nœuds spécifiques tant que les réplicas des services de l’application ne seront pas créés et placés. Cela offre de la flexibilité, étant donné que les nœuds sont choisis pour les nouveaux réplicas uniquement lorsqu’ils sont créés. La capacité est réservée sur un nœud spécifique lorsqu’au moins un réplica est placé dessus.

## Obtention des informations sur la charge d’application
Pour chaque application pour laquelle une capacité d’application a été définie, vous pouvez obtenir les informations sur la charge totale signalée par les réplicas de ses services. Pour ce faire, Service Fabric fournit des requêtes PowerShell et API gérée.

Par exemple, la charge peut être récupérée à l’aide de l’applet de commande PowerShell suivant :

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1

```

Le résultat de cette requête contient les informations de base sur la capacité d’application qui a été spécifiée pour l’application, comme le nombre de nœuds minimum et maximum. Il inclut également des informations sur le nombre de nœuds que l’application utilise actuellement. Par conséquent, pour chaque mesure de charge, vous disposerez des informations suivantes :
- Nom de la mesure : le nom de la mesure.
-	Capacité de réserve : la capacité du cluster qui est réservée dans le cluster pour cette application.
-	Charge de l’application : la charge totale des réplicas enfants de cette application.
-	Capacité d’application : la valeur maximale autorisée pour la charge de l’application.

## Suppression de la capacité d’application
Une fois les paramètres de capacité d’application définis pour une application, vous pouvez les supprimer à l’aide d’API de mise à jour de l’application ou d’applets de commande PowerShell. Par exemple :

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Cette commande supprime tous les paramètres de capacité de l’application. Service Fabric Cluster Resource Manager se met alors à traiter cette application comme toute autre application du cluster pour laquelle ces paramètres ne sont pas définis. L’effet de la commande est immédiat et Cluster Resource Manager supprime tous les paramètres de capacité de cette application ; pour les spécifier de nouveau, il est nécessaire d’appeler l’API de mise à jour de l’application en utilisant les paramètres appropriés.

## Restrictions sur la capacité d’application
Il existe plusieurs restrictions à respecter concernant les paramètres de capacité d’application. En cas d’erreurs de validation, la création ou la mise à jour de l’application est rejetée avec une erreur. Tous les paramètres entiers doivent être des nombres positifs. En outre, pour les paramètres individuels, les restrictions sont les suivantes :

-	La valeur du paramètre MinimumNodes ne doit jamais être supérieure à celle du paramètre MaximumNodes.
-	Si les capacités d’une mesure de charge sont définies, elles doivent respecter les règles suivantes :
  - La capacité de réservation de nœud ne doit pas être supérieure à la capacité maximale de nœud. Par exemple, vous ne pouvez pas limiter la capacité de la mesure « UC » sur le nœud à 2 unités et réserver 3 unités sur chaque nœud.
  - Si le paramètre MaximumNodes est spécifié, le résultat du produit de sa valeur par la capacité maximale de nœud ne doit pas être supérieur à la capacité totale d’application. Par exemple, si vous définissez la capacité maximale de nœud pour la mesure de charge « UC » sur 8 et que vous définissez le nombre maximal de nœuds sur 10, la capacité totale d’application doit être supérieure à 80 pour cette mesure de charge.

Les restrictions sont appliquées lors de la création d’applications (côté client) et au cours de la mise à jour des applications (côté serveur). Voici un exemple de non-respect des conditions requises lors de la création, dans la mesure où MaximumNodes < MinimumNodes. La commande échoue dans le client avant que la requête ne soit envoyée au cluster Service Fabric :

``` posh
New-ServiceFabricApplication –Name fabric:/MyApplication1 –MinimumNodes 6 –MaximumNodes 2
```

Voici un exemple de mise à jour non valide. Si nous prenons une application existante et que nous mettons à jour le nombre maximal de nœuds, la mise à jour s’effectue :

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MaximumNodes 2
```

Après cela, nous pouvons essayer de mettre à jour le nombre minimal de nœuds :

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MinimumNodes 6
```

Le client ne dispose pas de suffisamment de contexte relatif à l’application. Il autorise donc la mise à jour à passer au cluster Service Fabric. Toutefois, dans le cluster, Service Fabric valide le nouveau paramètre avec les paramètres existants et l’opération de mise à jour échoue, car le nombre minimal de nœuds est supérieur au nombre maximal de nœuds. Dans ce cas, les paramètres de capacité d’application reste inchangés.

Ces restrictions sont mises en place pour que Cluster Resource Manager soit en mesure de placer au mieux les réplicas des services d’applications.

## Cas dans lesquels la capacité d’application ne doit pas être utilisée

-	N’utilisez pas la capacité d’application pour contraindre l’application à un sous-ensemble de nœuds : bien que Service Fabric garantisse que le nombre maximal de nœuds est respecté pour chaque application pour laquelle une capacité d’application a été définie, les utilisateurs ne peuvent pas décider sur quels nœuds elles sont instanciées. Il est pour cela possible d’utiliser des contraintes de placement pour les services.
-	N’utilisez pas la capacité d’application pour vous assurer que deux services d’une même application sont toujours placés en parallèle. Utilisez pour cela des relations d’affinité entre les services. Les affinités peuvent être limitées aux services qui doivent être regroupés.

## Étapes suivantes
- Pour plus d’informations sur les autres options disponibles pour la configuration des services, consultez la rubrique sur les autres configurations de Cluster Resource Manager disponibles [En savoir plus sur la configuration des services](service-fabric-cluster-resource-manager-configure-services.md)
- Pour en savoir plus sur la façon dont Cluster Resource Manager gère et équilibre la charge du cluster, consultez l’article sur l’[équilibrage de la charge](service-fabric-cluster-resource-manager-balancing.md)
- Commencez au début et [obtenez une présentation de Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- Pour plus d’informations sur le fonctionnement des mesures en général, consultez les informations sur les [mesures de charge Service Fabric](service-fabric-cluster-resource-manager-metrics.md)
- Cluster Resource Manager comporte de nombreuses options permettant de décrire le cluster. Pour en savoir plus sur celles-ci, consultez cet article sur la [description d’un cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)


[Image1]: ./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]: ./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png

<!---HONumber=AcomDC_0824_2016-->