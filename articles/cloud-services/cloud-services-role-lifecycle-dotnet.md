---
title: "Gérer les événements de cycle de vie du service cloud | Microsoft Docs"
description: "Découvrez comment utiliser les méthodes de cycle de vie d'un rôle de service cloud dans .NET"
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 39b30acd-57b9-48b7-a7c4-40ea3430e451
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: eb78c05df3b3cdf3887334c11bdabd5cebb74747
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Personnalisation du cycle de vie d'un rôle Web ou de travail dans .NET
Lorsque vous créez un rôle de travail, vous étendez la classe [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) qui vous fournit des méthodes à substituer, vous permettant de répondre aux événements de cycle de vie. Cette classe est facultative pour les rôles Web. Vous devez donc l’utiliser pour répondre aux événements de cycle de vie.

## <a name="extend-the-roleentrypoint-class"></a>Extension de la classe RoleEntryPoint
La classe [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) inclut des méthodes qui sont appelées par Azure lorsqu’il **démarre**, **exécute** ou **arrête** un rôle web ou de travail. Vous pouvez éventuellement substituer ces méthodes pour gérer l'initialisation, les séquences d'arrêt ou le thread d'exécution du rôle. 

Lors de l'extension de **RoleEntryPoint**, vous devez tenir compte des comportements de méthodes suivants :

* Les méthodes [OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) et [OnStop](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx) retournent une valeur booléenne, ce qui veut dire qu’elles peuvent retourner la valeur **false**.
  
   Si votre code retourne la valeur **false**, le processus de rôle se termine soudainement, sans exécuter de séquence d'arrêt. En général, vous devez éviter de retourner la valeur **false** à partir de la méthode **OnStart**.
* Une exception non interceptée dans la surcharge d'une méthode **RoleEntryPoint** est traitée comme une exception non gérée.
  
   Si une exception se produit dans une des méthodes de cycle de vie, Azure déclenche l’événement [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) et le processus prend fin. Une fois mis hors connexion, votre rôle sera redémarré par Azure. Lorsqu'une exception non gérée se produit, l’événement [Stopping](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx) n'est pas déclenché et la méthode **OnStop** n'est pas appelée.

Si votre rôle ne démarre pas ou qu’il est recyclé entre les états Initialisation, Occupé et Arrêté, votre code peut lever une exception non gérée dans l'un des événements du cycle de vie chaque fois que le rôle redémarre. Utilisez, dans ce cas, l’événement [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) pour déterminer la cause de l'exception et la gérer correctement. Votre rôle peut également être retourné depuis la méthode [Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) , ce qui entraîne ainsi son redémarrage. Pour plus d'informations sur les états de déploiement, consultez la rubrique [Problèmes courants qui entraînent le recyclage des rôles](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [!NOTE]
> Si vous utilisez **Azure Tools pour Microsoft Visual Studio** pour développer votre application, les modèles de projet de rôle étendent automatiquement la classe **RoleEntryPoint** pour vous dans les fichiers *WebRole.cs* et *WorkerRole.cs*.
> 
> 

## <a name="onstart-method"></a>Méthode OnStart
La méthode **OnStart** est appelée une fois votre instance de rôle mise en ligne par Azure. Pendant l'exécution du code OnStart, l'instance de rôle est désignée comme **Occupée** et aucun trafic externe n'est dirigé vers elle via l'équilibrage de charge. Vous pouvez substituer cette méthode pour exécuter des tâches d'initialisation, telles que l'implémentation de gestionnaires d'événements et le démarrage d’ [Azure Diagnostics](cloud-services-how-to-monitor.md).

Si **OnStart** retourne la valeur **true**, l’instance est initialisée correctement et Azure appelle la méthode **RoleEntryPoint.Run**. Si **OnStart** retourne la valeur **false**, le rôle prend fin immédiatement, sans exécuter de séquence d’arrêt planifié.

L'exemple de code suivant montre comment substituer la méthode **OnStart** . Cette méthode configure et démarre un moniteur de diagnostic lorsque l'instance de rôle démarre et définit un transfert de données de journalisation vers un compte de stockage :

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## <a name="onstop-method"></a>Méthode OnStop
La méthode **OnStop** est appelée lorsqu'une instance de rôle a été mise hors connexion par Azure, avant la fin du processus. Vous pouvez substituer cette méthode pour appeler le code requis qui vous permet d’arrêter correctement votre instance de rôle.

> [!IMPORTANT]
> L’exécution du code dans la méthode **OnStop** est limitée dans le temps lorsqu’il est appelé pour des raisons autres qu'un arrêt initié par l'utilisateur. Une fois ce délai écoulé, le processus est terminé ; vous devez donc veiller à ce que le code de la méthode **OnStop** puisse s'exécuter rapidement ou qu’il accepte de ne pas être exécuté jusqu’à la fin. La méthode **OnStop** est appelée une fois l’événement **Stopping** déclenché.
> 
> 

## <a name="run-method"></a>Méthode Run
Vous pouvez substituer la méthode **Run** pour implémenter un thread de longue durée à votre instance de rôle.

Substituer la méthode **Run** n’est pas obligatoire ; l'implémentation par défaut démarre un thread qui ne se met jamais en veille. Si vous substituez la méthode **Run** , votre code se bloquera indéfiniment. Si la méthode **Run** est retournée, le rôle est automatiquement recyclé ; en d’autres termes, Azure déclenche l’événement **Stopping** et appelle la méthode **OnStop** pour que vos séquences d’arrêt puissent être exécutées avant que le rôle soit mis hors connexion.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implémentation des méthodes de cycle de vie ASP.NET pour un rôle Web
Vous pouvez utiliser les méthodes de cycle de vie ASP.NET, en plus de celles fournies par la classe **RoleEntryPoint** , pour gérer des séquences d'initialisation et d'arrêt d’un rôle Web. Celles-ci peuvent être utiles à des fins de compatibilité si vous portez une application ASP.NET existante vers Azure. Les méthodes de cycle de vie ASP.NET sont appelées depuis les méthodes **RoleEntryPoint** . La méthode **Application\_Start** est appelée une fois la méthode **RoleEntryPoint.OnStart** terminée. La méthode **Application\_End** est appelée avant que la méthode **RoleEntryPoint.OnStop** ne soit appelée.

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment [créer un package de service cloud](cloud-services-model-and-package.md).

