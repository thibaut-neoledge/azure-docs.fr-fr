---
title: Limitation dans Service Fabric Cluster Resource Manager | Microsoft Docs
description: "Apprenez à configurer les limiteurs fournis par Service Fabric Cluster Resource Manager."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 4a44678b-a5aa-4d30-958f-dc4332ebfb63
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 8a8419497bda3f1df523d6aff28028548abc155a


---

# <a name="throttling-the-behavior-of-the-service-fabric-cluster-resource-manager"></a>Limitation du comportement de Service Fabric Cluster Resource Manager
Même si vous avez configuré le Cluster Resource Manager , le cluster peut être interrompu. Par exemple, il peut y avoir des défaillance de nœud ou de domaine d’erreur simultanées. Que se passe-t-il alors si cela se produit pendant une mise à niveau ? Le Gestionnaire de ressources de cluster essaie de résoudre tous les problèmes, mais cela peut introduire une évolution dans le cluster. Les limitations servent de filet de sécurité afin que le cluster puisse utiliser des ressources pour se stabiliser. Les nœuds reviennent, les partitions réseau sont réparées et les bits corrigés sont déployés.

Pour résoudre ce genre de problème, le Gestionnaire de ressources de cluster Service Fabric comprend plusieurs limitations. Ces limitations sont relativement lourdes. Ces paramètres par défaut ne doivent pas être modifiés, sauf si le volume de travail que le cluster peut effectuer en parallèle a été revu avec attention.

Les limitations ont des valeurs par défaut que l’équipe Service Fabric a considérées comme pertinentes. Si vous devez les modifier, vous devez les adapter à la charge de travail réelle attendue. Vous pouvez décider que vous avez besoin de certaines limitations, même si cela signifie que la stabilisation du cluster prend généralement plus de temps.

## <a name="configuring-the-throttles"></a>Configuration des limitations
Les limitations qui sont incluses par défaut sont les suivantes :

* GlobalMovementThrottleThreshold : ce paramètre contrôle le nombre total de mouvements dans le cluster au cours d’une certaine période (définie comme valeur GlobalMovementThrottleCountingInterval en secondes)
* MovementPerPartitionThrottleThreshold : ce paramètre contrôle le nombre total de mouvements pour n’importe quelle partition de service au cours d’une certaine période (la valeur MovementPerPartitionThrottleCountingInterval en secondes)

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

via ClusterConfig.json pour les déploiements autonomes ou Template.json pour les clusters hébergés sur Azure :

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "GlobalMovementThrottleThreshold",
          "value": "1000"
      },
      {
          "name": "GlobalMovementThrottleCountingInterval",
          "value": "600"
      },
      {
          "name": "MovementPerPartitionThrottleThreshold",
          "value": "50"
      },
      {
          "name": "MovementPerPartitionThrottleCountingInterval",
          "value": "600"
      }
    ]
  }
]
```

La plupart du temps, les clients utilisent ces limitations, car ils travaillent déjà dans un environnement aux ressources restreintes. Par exemple, une bande passante réseau limitée dans les nœuds individuels ou des disques qui ne sont pas en mesure de générer de nombreux réplicas en parallèle en raison des limites de débit. Avec ces restrictions en place, les opérations déclenchées en réponse aux incidents échouaient ou fonctionnaient au ralenti, même sans les limitations. Dans ces cas de figure, les clients savaient qu’ils devaient prolonger la durée nécessaire au cluster pour avoir un état stable. Les clients comprenaient également qu’ils pourraient fonctionner avec une fiabilité globale inférieure pendant l’application des limitations.

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur la façon dont Cluster Resource Manager gère et équilibre la charge du cluster, consultez l’article sur l’ [équilibrage de la charge](service-fabric-cluster-resource-manager-balancing.md)
* Cluster Resource Manager comporte de nombreuses options permettant de décrire le cluster. Pour en savoir plus sur celles-ci, consultez cet article sur la [description d’un cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).



<!--HONumber=Jan17_HO1-->


