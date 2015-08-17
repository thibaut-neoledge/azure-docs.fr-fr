<properties
   pageTitle="Diagnostiquer et résoudre les problèmes d'un service Service Fabric"
   description="Informations conceptuelles et didacticiels qui vous aideront à diagnostiquer, surveiller et dépanner un service Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/21/2015"
   ms.author="ryanwi"/>

# Diagnostiquer et surveiller un service Service Fabric
L'analyse, la détection, le diagnostic et la résolution des problèmes permettent aux services de fonctionner avec une interruption minimale de l'expérience utilisateur. Pour plus d'informations, consultez les articles suivants :

- [Comment analyser et diagnostiquer des services localement](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
- [Configuration d'Application Insights pour votre application Service Fabric](service-fabric-diagnostics-application-insights-setup.md)
- [Résolution des problèmes de mise à niveau des applications](service-fabric-application-upgrade-troubleshooting.md)
- [Résolution de l'échec d'une mise à niveau d'application surveillée](../service-fabric-application-monitored-upgrade-troubleshooting.md)
- [Diagnostics et surveillance des performances pour les acteurs fiables](service-fabric-reliable-actors-diagnostics.md)
- [Diagnostics et surveillance des performances pour les services fiables](service-fabric-reliable-services-diagnostics.md)

## Dépannage d'un cluster
Les informations suivantes vous aideront à résoudre les problèmes de votre cluster de développement local :

- [Résoudre les problèmes d'installation de votre cluster de développement local](service-fabric-troubleshoot-local-cluster-setup.md)

## Modèle d'intégrité
Service Fabric introduit un modèle d'intégrité qui fournit une fonctionnalité d'évaluation et de création de rapports riche, flexible et extensible pour les entités Service Fabric. Les composants Service Fabric signalent l'intégrité de toutes les entités, sans configuration préalable. Les services d'utilisateurs peuvent enrichir les données d'intégrité avec des informations propres à leur logique, collectées sur eux-mêmes ou sur d'autres entités du cluster. Pour plus d'informations, consultez les articles suivants :

- [Présentation du contrôle d'intégrité de Service Fabric](service-fabric-health-introduction.md)
- [Comment afficher les rapports d'intégrité de Service Fabric](service-fabric-view-entities-aggregated-health.md)
- [Utilisation des rapports d'intégrité système pour la résolution des problèmes](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Ajout de rapports d'intégrité de Service Fabric personnalisés](service-fabric-report-health.md)
 

<!---HONumber=August15_HO6-->