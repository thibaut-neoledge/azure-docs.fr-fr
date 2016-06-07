<properties
   pageTitle="Service Fabric Cluster Resource Manager - Coût du déplacement | Microsoft Azure"
   description="Présentation du coût du mouvement pour les services Service Fabric"
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

# Coût lié au déplacement des services pour influencer les choix de Resource Manager
Un autre facteur important que nous prenons en compte lorsque nous essayons de déterminer les modifications à apporter à un cluster et le score d’une solution donnée est le coût global de la réalisation de cette solution.

Le déplacement de réplicas ou d’instances de service coûte du temps en termes de processeur et de bande passante réseau au minimum ; et pour les services avec état, cela coûte également la quantité d’espace disque dont vous avez besoin pour créer une copie de l’état avant d’arrêter les anciens réplicas. Bien entendu, vous voulez réduire le coût de toute solution issue de Resource Manager, mais vous ne voulez pas non plus ignorer les solutions qui permettraient d’améliorer considérablement l’allocation des ressources du cluster.

Service Fabric Resource Manager a deux façons de calculer les coûts et de les limiter. La première est lorsque Resource Manager envisage une nouvelle disposition pour le cluster, il compte chaque déplacement unique qui serait effectué. Dans un cas simple, si vous obtenez deux solutions avec le même équilibre global (score) à la fin, alors choisissez celle avec le coût le plus bas (nombre total de déplacements). Cela fonctionne très bien, jusqu’à ce que nous soyons confrontés au même problème que nous avions avec les charges par défaut ou statiques : il est peu probable que dans un système complexe tous les déplacements soient égaux. Certains peuvent être beaucoup plus coûteux.

## Modification du coût lié au déplacement d’un réplica et facteurs à prendre en compte
Comme lors de la création de rapports sur les charges (autre fonctionnalité de Cluster Resource Manager), nous fournissons au service une façon de créer automatiquement des rapports sur le coût lié au déplacement du service à tout moment.

Code

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

MoveCost a quatre niveaux : zéro, faible, moyen et élevé. Ces niveaux sont uniquement relatifs entre eux, à l’exception du niveau zéro, signifie que le déplacement d’un réplica est gratuit et ne sera pas inclus dans le score de la solution. La définition du coût du déplacement sur le niveau élevé ne garantit *pas* que le réplica ne sera pas déplacé. Il signifie uniquement qu’il ne sera déplacé que s’il existe une bonne raison de le faire.

![Coût du mouvement comme facteur de sélection des réplicas pour les déplacements][Image1]

MoveCost nous permet de rechercher les solutions qui provoquent globalement le moins d’interruption tout en obtenant un équilibre équivalent. La notion de coût d’un service peut être liée à beaucoup de choses, mais les plus courantes pour calculer le coût d’un déplacement sont les suivantes :

1.	La quantité d’état ou de données que le service doit déplacer
2.	Le coût de la déconnexion des clients (le coût du déplacement d’un réplica principal est plus élevé que celui du déplacement d’un réplica secondaire)
3.	Le coût de l’interruption d’une opération en cours (certaines opérations au niveau du magasin de données sont coûteuses et après un certain point, vous ne souhaitez pas les abandonner si vous n’y êtes pas obligé). Par conséquent, pendant la durée de l’opération, vous augmentez le coût pour réduire la probabilité que le réplica ou l’instance de service ne se déplace, et lorsque l’opération se termine, vous rétablissez l’état normal.

## Étapes suivantes
- Les métriques représentent la façon dont Service Fabric Cluster Resource Manager gère la consommation et la capacité du cluster. Pour en savoir plus sur ces métriques et la façon de les configurer, consultez [cet article](service-fabric-cluster-resource-manager-metrics.md)
- Pour en savoir plus sur la façon dont Cluster Resource Manager gère et équilibre la charge du cluster, consultez l’article sur l’[équilibrage de la charge](service-fabric-cluster-resource-manager-balancing.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png

<!---HONumber=AcomDC_0525_2016-->