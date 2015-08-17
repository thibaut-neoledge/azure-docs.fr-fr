<properties
   pageTitle="Architecture de l'équilibreur de ressources"
   description="Un aperçu architectural de l'équilibreur de ressources de Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="abhic"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/27/2015"
   ms.author="abhic"/>

# Présentation de l'architecture de l'équilibreur de ressources

![Architecture de l'équilibreur de ressources][Image1]

L'équilibreur de ressources de Service Fabric se compose d'un seul service d'équilibrage de ressources centralisé et d'un composant qui existe sur chaque nœud, qui sont colocalisés de façon conceptuelle avec le Failover Manager de Service Fabric et le nœud local de Service Fabric, respectivement.

L'agent local est responsable de la collecte et la mise en mémoire tampon des rapports de charge à partir des services qui sont exécutés sur le nœud local, de leur envoi au service d'équilibrage de ressources, ainsi que du signalement des échecs et autres événements au Gestionnaire de basculement et à l'équilibreur de ressources (1 et 2 ci-dessus). Le service d'équilibreur de ressources et le Failover Manager collaborent pour répondre aux événements de charge et autres événements dans le système, comme les défaillances de nœud ou de réplica, les rapports de charge, les services et les applications qui sont créés et supprimés. Par exemple, lorsqu'un réplica échoue, le Failover Manager demande à l'équilibreur de ressources de Service Fabric de suggérer un emplacement de remplacement qui est basé sur des données de charge de nœuds différents. De même, lorsqu'une nouvelle requête de service est reçue, le Failover Manager demande des recommandations à l'équilibreur de ressources sur l'emplacement de ce service. Dans tous ces cas, l'équilibreur de ressources répond par des modifications apportées à différentes configurations de service (3), qui sont ensuite mises en œuvre par le Failover Manager. Dans l'exemple précédent, le Failover Manager crée un nouveau réplica sur le nœud, ce qui entraîne le meilleur équilibre global (4).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

Fonctionnalités d'équilibrage de ressources :

- [Décrire le cluster](service-fabric-resource-balancer-cluster-description.md)
- [Décrire les services](service-fabric-resource-balancer-service-description.md)
- [Indiquer la charge dynamique](service-fabric-resource-balancer-dynamic-load-reporting.md)
- [Compression de mesure proactive](service-fabric-resource-balancer-proactive-metric-packing.md)
- [Pourcentage de mémoire tampon de nœud](service-fabric-resource-balancer-node-buffer-percentage.md)

[Image1]: media/service-fabric-resource-balancer-architecture/Service-Fabric-Resource-Balancer-Architecture.png
 

<!---HONumber=August15_HO6-->