---
title: "Résolution des problèmes avec le suivi d’événements | Microsoft Docs"
description: "Problèmes les plus courants rencontrés lors du déploiement de services sur Microsoft Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: mattrowmsft
manager: timlt
editor: 
ms.assetid: 5eb8ef21-da04-4ac8-8b9a-5f7ff1e0a180
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/31/2016
ms.author: mattrow
translationtype: Human Translation
ms.sourcegitcommit: c9730b553e59d12b8720bbf3a06cc956912e27de
ms.openlocfilehash: c920a206d6f3288f6cba0d2658d556749fc4d574


---
# <a name="troubleshoot-common-issues-when-you-deploy-services-on-azure-service-fabric"></a>Résoudre les problèmes courants rencontrés pendant le déploiement de services dans Azure Service Fabric
Lors de l'exécution de services sur votre ordinateur de développement, il est facile d’utiliser les [outils de dépannage de Visual Studio](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Pour les clusters à distance, [les rapports d'intégrité](service-fabric-view-entities-aggregated-health.md) sont toujours un bon point de départ. Le plus simple pour accéder à ces rapports est de passer par PowerShell ou [SFX](service-fabric-visualizing-your-cluster.md). Cet article suppose que vous résolvez les problèmes d’un cluster à distance et que vous avez des notions de base concernant l'utilisation de l’un de ces outils.

## <a name="application-crash"></a>Blocage d'application
Le rapport « La partition présente un nombre de réplicas ou d’instances inférieur à la valeur cible » est un bon moyen de savoir que votre service se bloque. Pour savoir où votre service se bloque, cela demande un peu plus d’investigation. Lors d’une exécution de votre service à grande l'échelle, votre meilleur ami est un ensemble de traces bien planifiées.  Nous vous suggérons d’essayer les [diagnostics Azure](service-fabric-diagnostics-how-to-setup-wad.md) pour collecter ces traces et d’utiliser une solution comme [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) pour afficher et rechercher les traces.

![Intégrité de la partition SFX](./media/service-fabric-diagnostics-troubleshoot-common-scenarios/crashNewApp.png)

### <a name="during-service-or-actor-initialization"></a>Au cours de l’initialisation du service ou de l’acteur
Toutes les exceptions qui surviennent avant l’initialisation du type de service entraînent le blocage du processus. Pour ces types de blocage, le journal d’événements des applications affiche l'erreur à partir de votre service.
Il s'agit des exceptions les plus courantes qui se produisent avant l’initialisation du service.

***System.IO.FileNotFoundException***

Cette erreur est souvent due à des dépendances d'assembly manquantes. Vérifiez la propriété CopyLocal dans Visual Studio ou dans le Global Assembly Cache (GAC) pour le nœud.

***System.Runtime.InteropServices.COMException***
 *at System.Fabric.Interop.NativeRuntime+IFabricRuntime.RegisterStatefulServiceFactory(IntPtr, IFabricStatefulServiceFactory)*

 Cette erreur indique que le nom du type de service inscrit ne correspond pas au manifeste du service.

[diagnostics Azure](service-fabric-diagnostics-how-to-setup-wad.md) peuvent être configurés pour télécharger le journal d’événements des applications pour tous les nœuds automatiquement.

### <a name="runasync-or-onactivateasync"></a>RunAsync() or OnActivateAsync()
Si le blocage se produit pendant l'initialisation ou l'exécution de votre type de service ou acteur inscrit, l'exception est interceptée par Azure Service Fabric. Vous pouvez l’afficher à partir des fournisseurs EventSource détaillés dans la section « Étapes suivantes ».

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur les diagnostics existants fournis par Service Fabric :

* [Diagnostics Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Diagnostics Reliable Services](service-fabric-reliable-services-diagnostics.md)




<!--HONumber=Feb17_HO3-->


