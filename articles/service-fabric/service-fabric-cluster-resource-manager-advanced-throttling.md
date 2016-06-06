<properties
   pageTitle="Limitation dans Service Fabric Cluster Resource Manager | Microsoft Azure"
   description="Apprenez à configurer les limiteurs fournis par Service Fabric Cluster Resource Manager."
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
   ms.date="05/20/2016"
   ms.author="masnider"/>


# Limitation du comportement de Service Fabric Cluster Resource Manager
Parfois, même si vous avez correctement configuré Resource Manager, le cluster peut être interrompu (nombreux échecs des nœuds ou des domaines d’erreur, pendant que des mises à niveau sont en cours, pendant que des mises à jour sont appliquées, etc.). Resource Manager fera de son mieux pour tout corriger, mais dans ces cas-là vous pouvez envisager d’appliquer une protection afin que le cluster lui-même ait une chance de se stabiliser (les nœuds qui peuvent se rétablir le font, les conditions de réseau se corrigent elles-mêmes, les bits corrigés sont déployés). Pour résoudre ce genre de situations, Resource Manager inclut plusieurs limitations. Notez qu’elles sont relativement « radicales » et qu’elles ne doivent généralement pas être utilisées, sauf si la quantité de travail parallèle pouvant être exécutée dans le cluster a été bien évaluée, ainsi que la fréquence à laquelle vous devez corriger ces événements de reconfiguration macroscopique non planifiés (c’est-à-dire : « Les très mauvais jours »).

En général, nous recommandons d’éviter les très mauvais jours en utilisant d’autres options (telles que les mises à jour régulières de code et éviter la surplanification du cluster pour commencer) plutôt que de limiter votre cluster pour l’empêcher d’utiliser des ressources lorsqu’il tente de s’auto-corriger). Cela dit, vous pouvez identifier des cas (jusqu’à ce que vous puissiez les résoudre) dans lesquels vous devez disposer de limitations en place, même si cela signifie que le cluster prendra plus de temps à se stabiliser.

##Configuration des limitations
Les limitations qui sont incluses par défaut sont les suivantes :

-	GlobalMovementThrottleThreshold : contrôle le nombre total de mouvements dans le cluster au cours d’une certaine période (définie comme valeur GlobalMovementThrottleCountingInterval en secondes)
-	MovementPerPartitionThrottleThreshold : contrôle le nombre total de mouvements pour n’importe quelle partition de service au cours d’une certaine période (la valeur MovementPerPartitionThrottleCountingInterval en secondes)

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

N’oubliez pas que la plupart du temps, quand des clients utilisent ces limitations, c’est parce qu’ils se trouvent déjà dans un environnement avec des ressources limitées (par exemple, une bande passante réseau limitée en nœuds individuels), ce qui signifie que ces opérations ne réussissent pas ou qu’elles sont de toute façon ralenties. En outre, ces clients ne s’inquiétent pas, car ils savent que le temps nécessaire au cluster pour atteindre un état stable est potentiellement étendu et que les opérations peuvent finir par s’exécuter avec une fiabilité inférieure pendant qu’elles sont limitées.

## Étapes suivantes
- Pour en savoir plus sur la façon dont Cluster Resource Manager gère et équilibre la charge du cluster, consultez l’article sur l’[équilibrage de la charge](service-fabric-cluster-resource-manager-balancing.md)
- Cluster Resource Manager comporte de nombreuses options permettant de décrire le cluster. Pour en savoir plus sur celles-ci, consultez cet article sur la [description d’un cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

<!---HONumber=AcomDC_0525_2016-->