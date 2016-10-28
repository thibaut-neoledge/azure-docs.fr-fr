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
   ms.date="08/19/2016"
   ms.author="masnider"/>


# Limitation du comportement de Service Fabric Cluster Resource Manager
Même si vous avez configuré le Cluster Resource Manager , le cluster peut être interrompu. Par exemple il peut y avoir des défaillance de nœud ou de domaine d’erreur simultanées. Que se passerait-il si cela se produisait pendant une mise à niveau ? Resource Manager fera de son mieux pour tout corriger, mais, dans ce cas, vous pouvez envisager d’appliquer une protection afin que le cluster lui-même ait une chance de se stabiliser (les nœuds qui peuvent se rétablir le font, les conditions de réseau se corrigent elles-mêmes, les bits corrigés sont déployés). Pour résoudre ce genre de problème, Service Fabric Cluster Resource Manager comprend plusieurs limitations. Notez que ces limitations peuvent entraîner des interruptions et qu’elles ne doivent généralement pas être utilisées, sauf si la quantité de travail parallèle pouvant être exécutée dans le cluster a été bien évaluée, ainsi que la fréquence à laquelle vous devez corriger ces événements de reconfiguration macroscopique non planifiés (c’est-à-dire : « Les très mauvais jours »).

En général, nous recommandons d’éviter les très mauvais jours en utilisant d’autres options (telles que les mises à jour régulières de code et éviter la surplanification du cluster pour commencer) plutôt que de limiter votre cluster pour l’empêcher d’utiliser des ressources lorsqu’il tente de s’auto-corriger). Les limitations ont des valeurs par défaut que nous avons déterminées par défaut grâce à notre expérience, mais vous devez sans doute les calculer et les régler en fonction de votre charge réelle attendue. Bien qu’une bonne pratique consiste à limiter et charger le cluster, vous pouvez identifier des cas (jusqu’à ce que vous puissiez les résoudre) pour lesquels vous devez mettre en place des limitations, même si cela signifie que le cluster prendra plus de temps à se stabiliser.

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

La plupart du temps, les clients utilisent ces limitations parce qu’ils utilisent déjà un environnement à limites de ressources (tels qu’une bande passante réseau limitée dans des nœuds individuels ou des disques qui ne répondent pas aux conditions requises pour la construction de réplicas parallèles qui y sont placés), ce qui signifie que ces opérations ne réussiraient pas ou seraient très lentes. Dans ce cas, les clients apprécient l’idée d’augmenter potentiellement la durée nécessaire au cluster pour atteindre un état stable, notamment le fait de savoir que l’exécution se fait avec une fiabilité inférieure alors que les limitations sont appliquées.

## Étapes suivantes
- Pour en savoir plus sur la façon dont Cluster Resource Manager gère et équilibre la charge du cluster, consultez l’article sur l’[équilibrage de la charge](service-fabric-cluster-resource-manager-balancing.md)
- Cluster Resource Manager comporte de nombreuses options permettant de décrire le cluster. Pour en savoir plus sur celles-ci, consultez cet article sur la [description d’un cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

<!---HONumber=AcomDC_0824_2016-->