<properties
   pageTitle="Architecture de l'équilibreur de ressources | Microsoft Azure"
   description="Un aperçu architectural de l’équilibreur de ressources de Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="masnider"/>

# Présentation de l’architecture de l’équilibreur de ressources

![Architecture de l'équilibreur de ressources][Image1]

L’équilibreur de ressources dans Azure Service Fabric se compose d’un service d’équilibrage des ressources centralisées unique et d’un composant qui existe sur chaque nœud. Ceux-ci sont conceptuellement colocalisés avec Service Fabric Failover Manager et le nœud Service Fabric local, respectivement.

L’agent local est responsable de la collecte et la mise en mémoire tampon des rapports de charge à partir des services qui sont exécutés sur le nœud local, de leur envoi au service d’équilibrage de ressources, ainsi que du signalement des échecs et autres événements à Failover Manager et à l’équilibreur de ressources (1 et 2 ci-dessus). L’équilibreur de ressources et Failover Manager collaborent pour répondre aux événements de charge et autres événements dans le système. Ces événements peuvent inclure les défaillances de nœud ou de réplica, les rapports de charge et les services et applications créés et supprimés.

Par exemple, lorsqu’un réplica échoue, Failover Manager demande à l’équilibreur de ressources de suggérer un emplacement de remplacement, basé sur des données de charge de nœuds différents. De même, lorsqu’une nouvelle requête de service est reçue, Failover Manager demande des recommandations à l’équilibreur de ressources sur l’emplacement de ce service. Dans tous ces cas, l’équilibreur de ressources répond par des modifications apportées aux différentes configurations de service (3). Ces modifications sont ensuite mises en œuvre par Failover Manager. Dans l’exemple précédent, Failover Manager crée un nouveau réplica sur le nœud, ce qui entraîne l’ équilibre global optimal (4).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

Fonctionnalités d’équilibrage des ressources :

- [Décrire le cluster](service-fabric-resource-balancer-cluster-description.md)
- [Décrire les services](service-fabric-resource-balancer-service-description.md)
- [Indiquer la charge dynamique](service-fabric-resource-balancer-dynamic-load-reporting.md)
- [Compression de mesure proactive](service-fabric-resource-balancer-proactive-metric-packing.md)
- [Pourcentage de mémoire tampon de nœud](service-fabric-resource-balancer-node-buffer-percentage.md)

[Image1]: media/service-fabric-resource-balancer-architecture/Service-Fabric-Resource-Balancer-Architecture.png

<!---HONumber=AcomDC_1223_2015-->