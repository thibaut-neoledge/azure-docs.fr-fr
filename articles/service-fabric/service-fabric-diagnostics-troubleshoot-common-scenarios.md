<properties
   pageTitle="Résolution des problèmes courants | Microsoft Azure"
   description="Problèmes les plus courants rencontrés lors du déploiement de services sur Microsoft Azure Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/10/2015"
   ms.author="mattrow"/>


# Résolution des problèmes courants
Lors de l'exécution de services sur votre ordinateur de développement, il est facile d’utiliser les [outils de dépannage de Visual Studio](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Pour les clusters à distance, [les rapports d'intégrité](service-fabric-view-entities-aggregated-health.md) sont toujours un bon point de départ. Le plus simple pour accéder à ces rapports est de passer par PowerShell ou [SFX](service-fabric-visualizing-your-cluster.md). Cet article suppose que vous résolvez les problèmes d’un cluster à distance et que vous avez des notions de base concernant l'utilisation de l’un de ces outils.

##Blocage d'application
Le rapport « La partition présente un nombre de réplicas ou d’instances inférieur à la valeur cible » est un bon moyen de savoir que votre service se bloque. Pour savoir où votre service se bloque, cela demande un peu plus d’investigation. Lors d’une exécution de votre service à grande l'échelle, votre meilleur ami est un ensemble de traces bien planifiées. Nous vous suggérons d'essayer les [diagnostics Azure](service-fabric-diagnostics-how-to-setup-wad-operational-insights.md) pour collecter et afficher ces traces.

![Intégrité de la partition SFX](./media/service-fabric-diagnostics-troubleshoot-common-scenarios/crashNewApp.png)

###Au cours de l’initialisation du service ou de l’acteur
Toutes les exceptions qui surviennent avant l’initialisation du type de service entraînent le blocage du processus. Pour ces types de blocage, le journal d’événements des applications affiche l'erreur à partir de votre service. Il s'agit des exceptions les plus courantes qui se produisent avant l’initialisation du service.

| Erreur | Description |
| --- | --- |
| System.IO.FileNotFoundException | Cette erreur est souvent due à des dépendances d'assembly manquantes. Vérifiez la propriété CopyLocal dans Visual Studio ou dans le Global Assembly Cache (GAC) pour le nœud.
| System.Runtime.InteropServices.COMException at System.Fabric.Interop.NativeRuntime+IFabricRuntime.RegisterStatefulServiceFactory(IntPtr, IFabricStatefulServiceFactory)|Cette erreur indique que le nom du type de service inscrit ne correspond pas au manifeste du service. |

Les [diagnostics Azure](service-fabric-diagnostics-how-to-setup-wad-operational-insights.md) peuvent être configurés pour télécharger le journal d’événements des applications pour tous les nœuds automatiquement.

###RunAsync() or OnActivateAsync()
Si le blocage se produit pendant l'initialisation ou l'exécution de votre type de service ou acteur inscrit, l'exception est interceptée par Azure Service Fabric. Vous pouvez l’afficher à partir des fournisseurs EventSource détaillés dans la section « Étapes suivantes ».

## Étapes suivantes

En savoir plus sur les diagnostics existants fournis par Service Fabric :

* [Diagnostics Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Diagnostics Reliable Services](service-fabric-reliable-services-diagnostics.md)

<!---HONumber=AcomDC_1217_2015-->