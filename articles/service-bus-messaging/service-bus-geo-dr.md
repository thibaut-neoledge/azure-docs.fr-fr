---
title: "Géorécupération d’urgence Azure Service Bus | Microsoft Docs"
description: "Utiliser les régions géographiques pour le basculement et la récupération d’urgence dans Azure Service Bus"
services: service-bus-messaging
documentationcenter: 
author: christianwolf42
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: sethm
ms.openlocfilehash: 49f2992245d694f85b7b1f1c34339f1445c9d699
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2017
---
# <a name="azure-service-bus-geo-disaster-recovery-preview"></a>Géorécupération d’urgence Azure Service Bus (préversion)

Si un centre de données régional connaît un temps d’arrêt, il est essentiel que le traitement des données puisse continuer dans les autres régions ou centres de données. Pour cette raison, la *géorécupération d’urgence* et la *géoréplication* sont des fonctionnalités importantes pour les entreprises. Azure Service Bus prend en charge la géorécupération d’urgence et la géoréplication au niveau de l’espace de noms. 

La préversion de la fonctionnalité de géorécupération d’urgence est disponible uniquement dans deux régions (**Nord-Centre des États-Unis** et **Sud-Centre des États-Unis)**.

## <a name="outages-and-disasters"></a>Pannes et sinistres

L’article [Meilleures pratiques pour protéger les applications contre les pannes et les sinistres de Service Bus](service-bus-outages-disasters.md) établit une distinction entre les « pannes » et les « sinistres ». Cette distinction a son importance. Une *panne* se définit comme l’indisponibilité temporaire d’Azure Service Bus. La panne impacte certains composants du service, comme une banque de messages, ou le centre de données entier. Toutefois, une fois le problème résolu, Service Bus redevient disponible. En règle générale, une panne ne provoque aucune perte de messages ou d’autres données. Une coupure de courant dans le centre de données est un exemple de panne.

Un *sinistre* se définit comme une perte définitive ou à long terme d’une [unité d’échelle](service-bus-architecture.md#service-bus-scale-units) ou d’un centre de données Service Bus. Le centre de données peut redevenir disponible ou pas, et rester arrêté pendant plusieurs heures ou jours. Les incendies, les inondations ou les tremblements de terre sont des exemples de sinistres. Un sinistre qui devient permanent peut entraîner la perte de certains messages ou d’autres données. Toutefois, dans la plupart des cas, il ne doit y avoir aucune perte de données et les messages peuvent être récupérés une fois que le centre de données est sauvegardé.

La fonctionnalité de géorécupération d’urgence d’Azure Service Bus est une solution de récupération d’urgence. Les concepts et le workflow décrits dans cet article concernent des scénarios de sinistres, pas des pannes temporaires ou transitoires.  

## <a name="basic-concepts-and-terms"></a>Concepts et terminologie de base

La fonctionnalité de récupération d’urgence implémente la récupération d’urgence des métadonnées, en s’appuyant sur les espaces de noms de récupération d’urgence principal et secondaire. Notez que la fonctionnalité de géorécupération d’urgence est disponible uniquement pour les [espaces de noms Premium](service-bus-premium-messaging.md). Vous n’avez pas besoin de modifier la chaîne de connexion, car la connexion est établie à l’aide d’un alias.

Cet article emploie les termes suivants :

-  *Alias* : votre chaîne de connexion principale.

-  *Espace de noms principal/secondaire* : décrit les espaces de noms qui correspondent à l’alias. L’espace de noms principal est « actif » et reçoit les messages, alors que l’espace de noms est « passif » et ne reçoit pas de messages. Les métadonnées sont synchronisées entre ces deux espaces de noms, qui peuvent ainsi accepter facilement les messages sans aucune modification du code d’application.

-  *Métadonnées* : votre représentation des objets dans Azure Service Bus. Seules les métadonnées sont prises en charge pour le moment.

-  *Basculement* : processus d’activation de l’espace de noms secondaire. Vous devez tirer (pull) les messages à partir de votre espace de noms qui était le principal une fois qu’il est redevenu disponible, puis supprimer l’espace de noms. Pour créer un autre basculement, ajoutez un nouvel espace de noms secondaire à l’association. Si vous souhaitez réutiliser l’ancien espace de noms principal après un basculement, vous devez d’abord supprimer toutes les entités existantes de l’espace de noms. Assurez-vous de recevoir tous les messages avant de procéder.

## <a name="failover-workflow"></a>Workflow du basculement

La section suivante présente une vue d’ensemble de tout le processus de configuration du basculement initial et explique les étapes à faire à partir de ce point.

![1][]

Vous devez d’abord configurer un espace de noms principal et un espace de noms secondaire, puis créer une association. Cette association crée un alias qui vous servira à vous connecter. Étant donné que vous utilisez un alias, vous n’avez pas besoin de modifier les chaînes de connexion existantes. Vous pouvez uniquement ajouter de nouveaux espaces de noms à votre association de basculement. Enfin, vous devez ajouter une logique de déclencheur (par exemple, une logique métier qui détecte quand l’espace de noms n’est pas disponible et démarre le basculement). Vous pouvez vérifier la disponibilité de l’espace de noms à l’aide de la fonctionnalité de [parcours des messages](message-browsing.md) de Service Bus.

Une fois que vous avez configuré la surveillance et la récupération d’urgence, vous pouvez passer au processus de basculement. Si le déclencheur démarre un basculement, ou si vous le démarrez manuellement, vous devez effectuer deux étapes :

1. Vous voulez être sûr de pouvoir refaire un basculement en cas de nouvelle panne. Pour cela, configurez un deuxième espace de noms passif et mettez à jour l’association. 
2. Tirez (pull) les messages à partir de l’ancien espace de noms principal une fois que le nouvel espace de noms est disponible. Après cela, vous pouvez réutiliser l’ancien espace de noms principal, ou le supprimer.

![2][]

## <a name="set-up-disaster-recovery"></a>Configurer une récupération d'urgence

Cette section décrit comment créer votre propre code de géorécupération d’urgence dans Service Bus. Pour ce faire, vous avez besoin de deux espaces de noms situés dans des régions distinctes (par exemple, Sud des États-Unis et Nord-Centre des États-Unis). L’exemple suivant utilise Visual Studio 2017.

1.  Créez un projet **Console App(.Net Framework)** dans Visual Studio et donnez-lui un nom, **SBGeoDR**, par exemple.

2.  Installez les packages NuGet suivants :
    1.  Microsoft.IdentityModel.Clients.ActiveDirectory
    2.  Microsoft.Azure.Management.ServiceBus

3. Assurez-vous d’utiliser la version 10.0.3 du package Newtonsoft.Json NuGet.

3.  Ajoutez les instructions `using` suivantes dans votre code :

    ```csharp
    using System.Threading;
    using Microsoft.Azure.Management.ServiceBus;
    using Microsoft.Azure.Management.ServiceBus.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

4. Ajoutez deux espaces de noms Premium dans votre méthode `main()` existante :

    ```csharp
    // 1. Create primary namespace (optional).

    var namespaceParams = new SBNamespace()
    {
        Location = "South Central US",
        Sku = new SBSku()
        {
            Name = SkuName.Premium,
            Capacity = 1
        }

    };

    var namespace1 = client.Namespaces.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, namespaceParams);

    // 2. Create secondary namespace (optional if you already have an empty namespace available).

    var namespaceParams2 = new SBNamespace()
    {
        Location = "North Central US",
        Sku = new SBSku()
        {
            Name = SkuName.Premium,
            Capacity = 1
        }

    };

    // If you re-run this program while namespaces are still paired this operation will fail with a bad request.
    // This is because we block all updates on secondary namespaces once it is paired.

    var namespace2 = client.Namespaces.CreateOrUpdate(resourceGroupName, geoDRSecondaryNS, namespaceParams2);
    ```

5. Activez l’association des deux espaces de noms et obtenez l’alias que vous utiliserez ultérieurement pour vous connecter à vos entités :

    ```csharp
    // 3. Pair the namespaces to enable DR.

    ArmDisasterRecovery drStatus = client.DisasterRecoveryConfigs.CreateOrUpdate(
        resourceGroupName,
        geoDRPrimaryNS,
        alias,
        new ArmDisasterRecovery { PartnerNamespace = geoDRSecondaryNS });

    // A similar loop can be used to check if other operations (Failover, BreakPairing, Delete) 
    // mentioned below have been successful.
    while (drStatus.ProvisioningState != ProvisioningStateDR.Succeeded)
    {
        Console.WriteLine("Waiting for DR to be set up. Current state: " +
        drStatus.ProvisioningState);
        drStatus = client.DisasterRecoveryConfigs.Get(
        resourceGroupName,
        geoDRPrimaryNS,
        alias);

        Thread.CurrentThread.Join(TimeSpan.FromSeconds(30));
    }
    ```

Vous avez terminé la configuration des deux espaces de noms associés. Vous pouvez maintenant créer des entités pour observer la synchronisation des métadonnées. Si vous souhaitez effectuer un basculement immédiatement après, vous devez prévoir le temps nécessaire pour la synchronisation des métadonnées. Vous pouvez ajouter une courte durée de veille, par exemple :

```csharp
client.Topics.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, "myTopic", new SBTopic());
client.Subscriptions.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, "myTopic", "myTopic-Sub1", new SBSubscription());

// sleeping to allow metadata to sync across primary and secondary
Thread.Sleep(1000 * 60);
```

À ce stade, vous êtes prêt à ajouter des entités par le biais du portail ou d’Azure Resource Manager, et vérifier que la synchronisation se déroule correctement. Sauf si vous prévoyez d’effectuer le basculement manuellement, vous devez créer une application qui surveille l’espace de noms principal et démarre le basculement en cas d’indisponibilité de cet espace de noms. 

## <a name="initiate-a-failover"></a>Initialisation d’un basculement

Le code suivant montre comment démarrer un basculement :

```csharp
// Note that this failover operation is always run against the secondary namespace 
// (because primary might be down at time of failover).

client.DisasterRecoveryConfigs.FailOver(resourceGroupName, geoDRSecondaryNS, alias);
```

Une fois que vous avez déclenché le basculement, ajoutez un nouvel espace de noms passif et recréez l’association. Le code pour créer une nouvelle association est fourni dans la section précédente. De plus, vous devez supprimer les messages de l’ancien espace de noms principal à la fin du basculement. Pour obtenir des exemples montrant comment recevoir les messages d’une file d’attente, consultez [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md).

## <a name="how-to-disable-geo-disaster-recovery"></a>Comment désactiver la géorécupération d’urgence

Le code suivant montre comment désactiver une association d’espaces de noms :

```csharp
client.DisasterRecoveryConfigs.BreakPairing(resourceGroupName, geoDRPrimaryNS, alias);
```

Le code ci-dessous supprime l’alias que vous avez créé :

```csharp
// Delete the DR config (alias).
// Note that this operation must run against the namespace to which the alias is currently pointing.
// If you break the pairing and want to delete the namespaces afterwards, you must delete the alias first.

client.DisasterRecoveryConfigs.Delete(resourceGroupName, geoDRPrimaryNS, alias);
```

## <a name="steps-after-a-failover-failback"></a>Étapes après un basculement (restauration automatique)

Après un basculement, effectuez les deux étapes suivantes :

1.  Créez un autre espace de noms secondaire passif. Le code nécessaire est fourni dans la section précédente.
2.  Supprimez les messages restants dans votre file d’attente.

## <a name="alias-connection-string-and-test-code"></a>Code de test et d’obtention de la chaîne de connexion d’alias

Si vous souhaitez tester le processus de basculement, créez un exemple d’application qui envoie (push) les messages à l’espace de noms principal à l’aide de l’alias. Pour ce faire, vous devez obtenir la chaîne de connexion d’alias à partir d’un espace de noms actif. Dans la préversion actuelle, il n’existe pas d’autre interface pour obtenir directement la chaîne de connexion. L’exemple de code suivant établit une connexion avant et après le basculement :

```csharp
var accessKeys = client.Namespaces.ListKeys(resourceGroupName, geoDRPrimaryNS, "RootManageSharedAccessKey");
var aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
var aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;

if(aliasPrimaryConnectionString == null)
{
    accessKeys = client.Namespaces.ListKeys(resourceGroupName, geoDRSecondaryNS, "RootManageSharedAccessKey");
    aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
    aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;
}
```

## <a name="next-steps"></a>Étapes suivantes

- Consultez les [informations de référence de l’API REST](/rest/api/servicebus/disasterrecoveryconfigs) sur la géorécupération d’urgence.
- Exécutez [l’exemple de géorécupération d’urgence sur GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- Consultez [l’exemple d’envoi de messages à un alias](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) pour la géorécupération d’urgence.

Pour en savoir plus sur la messagerie Service Bus, consultez les articles suivants :

* [Concepts de base de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Files d’attente, rubriques et abonnements Service Bus](service-bus-queues-topics-subscriptions.md)
* [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Utilisation des rubriques et abonnements Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [API REST](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geo1.png
[2]: ./media/service-bus-geo-dr/geo2.png
