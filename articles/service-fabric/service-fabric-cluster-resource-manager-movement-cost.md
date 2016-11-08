---
title: 'Gestionnaire des ressources de cluster Service Fabric : coût du déplacement | Microsoft Docs'
description: Présentation du coût du mouvement pour les services Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''

ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider

---
# Coût lié au déplacement des services pour influencer les choix du Gestionnaire des ressources de cluster
Un facteur important à prendre en compte lorsque vous essayez de déterminer les modifications à apporter à un cluster et le score d’une solution est le coût global de la réalisation de cette solution.

Le déplacement de réplicas ou d’instances de service coûte au minimum du temps d’UC et de la bande passante réseau. Pour les services avec état, il coûte également la quantité d’espace disque dont vous avez besoin pour créer une copie de l’état avant d’arrêter les anciens réplicas. Vous souhaiterez de toute évidence réduire le coût des solutions associées au Gestionnaire des ressources de cluster Azure Service Fabric. Mais vous ne voulez pas non plus ignorer des solutions qui amélioreraient considérablement l’allocation de ressources dans le cluster.

Le Gestionnaire des ressources de cluster dispose de deux modes de calcul et de limitation des coûts, même lorsqu’il tente de gérer le cluster en fonction de ses autres objectifs. Le premier est utilisé lorsque le Gestionnaire des ressources de cluster envisage une nouvelle disposition pour le cluster : il compte chaque déplacement qui serait effectué. Dans un cas simple, si vous obtenez deux solutions avec le même équilibre global (score) à la fin, choisissez celle avec le coût le plus bas (nombre total de déplacements).

Cela fonctionne plutôt bien. Mais, comme avec les charges par défaut ou statiques, il est peu probable dans n’importe quel système complexe que tous les déplacements soient égaux. Certains sont susceptibles d’être beaucoup plus coûteux.

## Modification du coût lié au déplacement d’un réplica et facteurs à prendre en compte
Comme avec la création de rapports sur les charges (autre fonctionnalité du Gestionnaire des ressources de cluster), vous fournissez au service une façon de créer automatiquement des rapports sur le coût lié au déplacement du service à un moment donné.

Code :

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

MoveCost a quatre niveaux : zéro, faible, moyen et élevé. À l’exception de zéro, ils sont liés les uns aux autres. Zéro signifie que le déplacement d’un réplica est gratuit et ne doit pas compter dans le score de la solution. La définition du coût du déplacement vers le niveau élevé ne garantit *pas* que le réplica ne sera pas déplacé. Il signifie simplement qu’il ne sera déplacé que s’il existe une bonne raison de le faire.

![Coût du mouvement comme facteur de sélection des réplicas pour les déplacements][Image1]

MoveCost vous permet de rechercher les solutions qui provoquent globalement le moins de perturbations et qui sont les plus faciles à mettre en place tout en obtenant un équilibre équivalent. La notion de coût d’un service peut être liée à beaucoup de choses. Les facteurs les plus courants pour calculer le coût de déplacement sont :

* La quantité d’état ou de données que le service doit déplacer.
* Le coût de déconnexion des clients. Le coût de déplacement d’un réplica principal est généralement plus élevé que celui du déplacement d’un réplica secondaire.
* Le coût d’interruption d’une opération en cours. Certaines opérations au niveau du magasin de données ou en réponse à un appel du client sont coûteuses. Après un certain point, il est préférable de ne pas les arrêter si vous n’êtes pas obligé de le faire. Par conséquent, pendant la durée de l’opération, vous augmentez le coût pour réduire la probabilité que le réplica ou l’instance de service ne se déplace. Lorsque l’opération se termine, vous rétablissez l’état normal.

## Étapes suivantes
* Le Gestionnaire des ressources de cluster Service Fabric utilise des métriques pour gérer la consommation et la capacité du cluster. Pour en savoir plus sur les métriques et leur configuration, consultez [Gestion de la charge et de la consommation des ressources dans Service Fabric avec des métriques](service-fabric-cluster-resource-manager-metrics.md).
* Pour en savoir plus sur la façon dont le Gestionnaire des ressources de cluster gère et équilibre la charge du cluster, consultez [Équilibrage de votre cluster Service Fabric](service-fabric-cluster-resource-manager-balancing.md).

[Image1]: ./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png

<!---HONumber=AcomDC_0831_2016-->