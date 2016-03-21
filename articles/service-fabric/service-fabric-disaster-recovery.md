<properties
   pageTitle="Récupération d’urgence Azure Service Fabric | Microsoft Azure"
   description="Azure Service Fabric offre les fonctionnalités nécessaires pour gérer tous les types d’incidents. Cet article décrit les types d’incidents qui peuvent se produire et comment les gérer."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/03/2016"
   ms.author="seanmck"/>

# Récupération d’urgence dans Azure Service Fabric

Pour obtenir une application cloud avec une haute disponibilité, vous devez vous assurer qu’elle est capable de survivre à tous les différents types de défaillances, notamment à celles qui sont complètement en dehors de votre contrôle. Cet article décrit la disposition physique d’un cluster Azure Service Fabric dans le contexte d’incidents potentiels et fournit des conseils sur la façon de faire face à ces incidents pour limiter ou éliminer le risque de perte de données ou d’interruption de service.

## Disposition physique des clusters Service Fabric dans Azure

Pour comprendre le risque posé par les différents types de défaillances, il est utile de savoir comment les clusters sont physiquement disposés dans Azure.

Quand vous créez un cluster Service Fabric dans Azure, vous devez choisir une région où il sera hébergé. L’infrastructure Azure approvisionne ensuite les ressources de ce cluster dans la région, notamment le nombre de machines virtuelles demandées. Examinons plus en détail comment et où ces machines virtuelles sont approvisionnées.

### Domaines d'erreur

Par défaut, les machines virtuelles du cluster sont réparties uniformément dans des groupes logiques appelés domaines d’erreur, qui segmentent les machines en fonction des pannes pouvant affecter le matériel hôte. En particulier, si deux machines virtuelles résident dans deux domaines d’erreur distincts, vous pouvez être sûr qu’elles ne partagent pas le même commutateur réseau ou la même source d’alimentation. Ainsi, une défaillance du réseau local ou une panne d’alimentation qui affecte une machine virtuelle n’affectera pas l’autre, ce qui permet à Service Fabric de rééquilibrer la charge de travail de la machine qui ne répond pas au sein du cluster.

Vous pouvez visualiser la disposition de votre cluster dans les domaines d’erreur à l’aide de la carte de cluster fournie dans [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) :

![Nœuds répartis dans les domaines d’erreur dans Service Fabric Explorer][sfx-cluster-map]

>[AZURE.NOTE] L’autre axe de la carte de cluster montre les domaines de mise à niveau, qui regroupent logiquement les nœuds en fonction des activités de maintenance planifiées. Les clusters Service Fabric dans Azure sont toujours répartis dans cinq domaines de mise à niveau.

### Répartition géographique

Il existe actuellement 22 régions Azure dans le monde, et cinq autres sont annoncées. Une région peut contenir un ou plusieurs centres de données physiques en fonction de la demande et de la disponibilité des emplacements appropriés (entre autres facteurs). Notez toutefois que même dans les régions qui comptent plusieurs centres de données physiques, il n’existe aucune garantie que les machines virtuelles de votre cluster soient réparties uniformément parmi ces emplacements physiques. En effet, actuellement, toutes les machines virtuelles d’un cluster donné sont approvisionnées sur un site physique unique.

## Gestion des défaillances

Plusieurs types de défaillances peuvent avoir un impact sur votre cluster, chacune avec sa propre atténuation. Nous allons les examiner par ordre de probabilité.

### Défaillances d’une machine spécifique

Comme mentionné, les défaillances d’une machine spécifique, au niveau de la machine virtuelle ou du matériel ou logiciel qui l’héberge au sein d’un domaine d’erreur, ne posent en soi aucun problème. Service Fabric détecte généralement la défaillance en quelques secondes et répond de manière adéquate en fonction de l’état du cluster. Par exemple, si le nœud héberge les réplicas principaux d’une partition, un nouveau réplica principal est choisi parmi les réplicas secondaires de la partition. Quand Azure restaure la machine défaillante, celle-ci rejoint automatiquement le cluster et reprend sa part de charge de travail.

### Défaillances simultanées de plusieurs machines

Même si les domaines d’erreur réduisent considérablement les risques de défaillances de machines simultanées, il y a toujours un risque que plusieurs défaillances aléatoires provoquent l’arrêt simultané de plusieurs machines dans un cluster.

En règle générale, tant que la majorité des nœuds restent disponibles, le cluster continue à fonctionner. Sa capacité est toutefois inférieure, car les réplicas avec état sont regroupés dans un plus petit groupe de machines et moins d’instances sans état sont disponibles pour répartir la charge.

#### Perte de quorum

Si une majorité des réplicas de la partition d’un service avec état tombe en panne, cette partition bascule dans un état appelé « perte de quorum ». À ce stade, Service Fabric cesse d’autoriser les écritures sur cette partition pour s’assurer que son état reste cohérent et fiable. En effet, nous choisissons d’accepter une période d’indisponibilité pour nous assurer que les clients ne seront pas informés que leurs données ont été enregistrées alors qu’elles ne l’ont pas été. Notez que si vous avez choisi d’autoriser les lectures à partir des réplicas secondaires pour ce service avec état, vous pouvez continuer à effectuer ces opérations de lecture dans cet état. Une partition reste à l’état de perte de quorum jusqu’à ce qu’un nombre suffisant de réplicas soient restaurés ou jusqu’à ce que l’administrateur du cluster force le système à continuer à l’aide de l’[API Repair-ServiceFabricPartition](repair-partition-ps). Si vous effectuez cette action quand le réplica principal est hors service, des données sont perdues.

Les services système peuvent également subir une perte de quorum, l’impact étant propre au service en question. Par exemple, une perte de quorum dans le service d’affectation de noms affecte la résolution de noms, tandis qu’une perte de quorum dans le service Failover Manager bloque la création de services et les basculements. Notez que contrairement à vos propres services, il est *déconseillé* d’essayer de réparer les services système. Il est préférable d’attendre le retour des réplicas défaillants.

#### Réduction du risque de perte de quorum

Vous pouvez réduire le risque de perte de quorum en augmentant la taille du jeu de réplicas cible pour votre service. Il est utile de penser au nombre de réplicas dont vous avez besoin en termes de nombre de nœuds non disponibles que vous pouvez tolérer à tout moment mais restant disponibles pour les écritures, en gardant à l’esprit que les mises à niveau d’application ou de cluster, en plus des défaillances matérielles, peuvent rendre les nœuds temporairement indisponibles.

Prenons les exemples suivants, en supposant que vous avez configuré vos services avec un paramètre MinReplicaSetSize égal à trois, le plus petit nombre recommandé pour les services de production. Avec un paramètre TargetReplicaSetSize égal à trois (un principal et deux secondaires), une défaillance matérielle pendant une mise à niveau (deux réplicas en panne) entraîne une perte de quorum et le basculement en lecture seule de votre service. Si vous avez cinq réplicas, vous pouvez résister à deux défaillances pendant la mise à niveau (trois réplicas en panne), car les deux réplicas restants peuvent encore constituer un quorum conforme au jeu de réplicas minimal.

### Pannes ou destruction de centre de données

Dans de rares cas, les centres de données physiques peuvent devenir temporairement indisponibles à cause d’une perte d’alimentation ou de connectivité réseau. Si ce problème se pose, vos clusters et applications Service Fabric sont indisponibles, mais vos données sont conservées. Pour les clusters exécutés dans Azure, vous pouvez afficher les mises à jour concernant les pannes dans la [page d’état Azure](azure-status-dashboard).

En cas de destruction d’un centre de données physique entier (très peu probable), les clusters Service Fabric hébergés à cet emplacement sont perdus, ainsi que leur état.

Pour vous protéger contre cette éventualité, il est essentiel de [sauvegarder régulièrement votre état](service-fabric-reliable-services-backup-restore.md) dans un magasin géo-redondant et de valider votre capacité à le restaurer. La fréquence de vos sauvegardes dépend de votre objectif de point de récupération. Même si vous n’avez pas encore totalement implémenté de sauvegarde et de restauration, vous devez implémenter un gestionnaire pour l’événement `OnDataLoss` visant à consigner ses occurrences dans un journal comme suit :

```c#
protected virtual Task<bool> OnDataLoss(CancellationToken cancellationToken)
{
  ServiceEventSource.Current.ServiceMessage(this, "OnDataLoss event received.");
  return Task.FromResult(true);
}
```

>[AZURE.NOTE] La sauvegarde et la restauration ne sont actuellement disponibles que pour l’API Reliable Services. La sauvegarde et la restauration pour Reliable Actors seront disponibles dans une version ultérieure.

### Défaillances logicielles et autres sources de perte de données

Parmi les causes de perte de données, les erreurs de code dans les services, les erreurs humaines et les failles de sécurité sont plus courantes que les défaillances de centre de données à grande échelle. Toutefois, la stratégie de récupération reste toujours la même : effectuer des sauvegardes régulières de tous les services avec état et exercer votre capacité à restaurer cet état.

## Étapes suivantes

- Découvrez comment simuler différentes défaillances à l’aide de l’[infrastructure de testabilité](service-fabric-testability-overview.md).
- Consultez les autres ressources de récupération d’urgence et de haute disponibilité. Microsoft a publié de nombreuses rubriques d’aide sur ces sujets. Même si certains de ces documents font référence à des techniques propres à d’autres produits, ils contiennent un grand nombre de bonnes pratiques générales que vous pouvez appliquer aussi dans le contexte de Service Fabric :
 - [Liste de contrôle de disponibilité](azure-availability-checklist)
 - [Exécution d’un exercice de récupération d’urgence](disaster-recovery-drill)
 - [Récupération d’urgence et haute disponibilité pour les applications Azure](dr-ha-guide)


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/fr-FR/library/mt163522.aspx
[azure-status-dashboard]: https://azure.microsoft.com/fr-FR/status/
[azure-availability-checklist]: https://azure.microsoft.com/fr-FR/documentation/articles/best-practices-availability-checklist/
[disaster-recovery-drill]: https://azure.microsoft.com/fr-FR/documentation/articles/sql-database-disaster-recovery-drills/

<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png

<!---HONumber=AcomDC_0309_2016-->