---
title: "Gestionnaire des ressources de cluster Service Fabric : coût du déplacement | Microsoft Docs"
description: "Présentation du coût du mouvement pour les services Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: f022f258-7bc0-4db4-aa85-8c6c8344da32
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: f85365a36aea39b4179805e728c7ddafa140f08b


---
# <a name="service-movement-cost-for-influencing-cluster-resource-manager-choices"></a>Coût lié au déplacement des services pour influencer les choix du Gestionnaire des ressources de cluster
Un facteur important que Service Fabric Cluster Resource Manager prend en compte lorsqu’il essaie de déterminer les modifications à apporter à un cluster est le coût global de la réalisation de cette solution. La notion de « coût » est comparée au niveau d’équilibre qui peut être obtenu.

Le déplacement de réplicas ou d’instances de service coûte au minimum du temps d’UC et de la bande passante réseau. Pour les services avec état, il coûte également la quantité d’espace disque et en mémoire dont vous avez besoin pour créer une copie de l’état avant d’arrêter les anciens réplicas. Vous souhaiterez de toute évidence réduire le coût des solutions associées au Gestionnaire des ressources de cluster Azure Service Fabric. Mais vous ne voulez pas non plus ignorer des solutions qui amélioreraient considérablement l’allocation de ressources dans le cluster.

Cluster Resource Manager dispose de deux modes de calcul et de limitation des coûts, même lorsqu’il tente de gérer le cluster en fonction de ses autres objectifs. Le premier est utilisé lorsque le Gestionnaire des ressources de cluster envisage une nouvelle disposition pour le cluster : il compte chaque déplacement qui serait effectué. Si deux solutions sont générées avec un équilibre (score) similaire, préférez celle présentant le coût le plus bas (nombre total de déplacements).

Cette stratégie fonctionne bien. Mais, comme avec les charges par défaut ou statiques, il est peu probable dans n’importe quel système complexe que tous les déplacements soient égaux. Certains sont susceptibles d’être beaucoup plus coûteux.

## <a name="changing-a-replicas-move-cost-and-factors-to-consider"></a>Modification du coût lié au déplacement d’un réplica et facteurs à prendre en compte
Comme avec la charge de création de rapports (une autre fonctionnalité de Cluster Resource Manager), les services peuvent créer des rapports de façon dynamique sur le coût des déplacements à tout moment.

Code :

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

Le coût de déplacement par défaut peut également être spécifié lors de la création du service.

MoveCost a quatre niveaux : zéro, faible, moyen et élevé. À l’exception de zéro, ces niveaux MoveCost sont liés les uns aux autres. Un coût de déplacement de zéro signifie que le déplacement est gratuit et ne doit pas compter dans le score de la solution. Le fait de définir le coût élevé ne garantit *pas* que le réplica reste au même endroit.

<center>
![Coût du déplacement comme facteur de sélection des réplicas pour les déplacements][Image1]
</center>

MoveCost vous permet de rechercher les solutions qui provoquent globalement le moins de perturbations et qui sont les plus faciles à mettre en place tout en obtenant un équilibre équivalent. La notion de coût d’un service peut être liée à beaucoup de choses. Les facteurs les plus courants pour calculer le coût de déplacement sont :

* La quantité d’état ou de données que le service doit déplacer.
* Le coût de déconnexion des clients. Le coût de déplacement d’un réplica principal est généralement plus élevé que celui du déplacement d’un réplica secondaire.
* Le coût d’interruption d’une opération en cours. Certaines opérations au niveau du magasin de données ou en réponse à un appel du client sont coûteuses. Après un certain point, il est préférable de ne pas les arrêter si vous n’êtes pas obligé de le faire. Par conséquent, pendant l’opération, vous augmentez le coût du déplacement de cet objet de service afin de réduire la probabilité qu’il se déplace. Lorsque l’opération se termine, vous rétablissez le coût normal.

## <a name="next-steps"></a>Étapes suivantes
* Le Gestionnaire des ressources de cluster Service Fabric utilise des métriques pour gérer la consommation et la capacité du cluster. Pour en savoir plus sur les métriques et leur configuration, consultez [Gestion de la charge et de la consommation des ressources dans Service Fabric avec des métriques](service-fabric-cluster-resource-manager-metrics.md).
* Pour en savoir plus sur la façon dont le Gestionnaire des ressources de cluster gère et équilibre la charge du cluster, consultez [Équilibrage de votre cluster Service Fabric](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png



<!--HONumber=Jan17_HO1-->


