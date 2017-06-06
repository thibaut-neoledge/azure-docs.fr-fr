---
title: "Bibliothèques de gestion Azure Event Hubs | Microsoft Docs"
description: "Gérer les entités et espaces de noms des Event Hubs à partir de .NET"
services: event-hubs
cloud: na
documentationcenter: na
author: sethmanheim
manager: timlt
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 4/10/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: a9023448c4ced1edf54c84bb103454cbd76fbfba
ms.contentlocale: fr-fr
ms.lasthandoff: 04/12/2017


---

# <a name="event-hubs-management-libraries"></a>Bibliothèque de gestion des Event Hubs

Les bibliothèques de gestion des Event Hubs peuvent approvisionner dynamiquement des entités et des espaces de noms d’Event Hubs. Ceci permet des déploiements et des scénarios de messagerie complexes, et de déterminer ainsi par programmation les entités à approvisionner. Ces bibliothèques sont actuellement disponibles pour .NET.

## <a name="supported-functionality"></a>Fonctionnalités prises en charge

* Création, mise à jour et suppression d’espaces de noms
* Création, mise à jour et suppression d’Event Hubs
* Création, mise à jour et suppression de groupes de consommateurs

## <a name="prerequisites"></a>Composants requis

Pour commencer à utiliser les bibliothèques de gestion d’Event Hubs, vous devez vous authentifier avec Azure Active Directory (AAD). AAD vous oblige à vous authentifier en tant que principal du service pour pouvoir accéder à vos ressources Azure. Pour plus d’informations sur la création d’un principal du service, consultez ces articles :  

* [Utiliser le portail Azure pour créer une application et un principal du service Active Directory pouvant accéder aux ressources](../azure-resource-manager/resource-group-create-service-principal-portal.md)
* [Créer un principal du service pour accéder aux ressources à l’aide d’Azure PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [Créer un principal du service pour accéder aux ressources à l’aide de l’interface de ligne de commande (CLI) Azure](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

Ces didacticiels vous fourniront un `AppId` (ID client), un `TenantId` (ID de locataire) et un `ClientSecret` (clé d’authentification), qui sont utilisés pour l’authentification par les bibliothèques de gestion. Vous devez disposer des autorisations « Propriétaire » pour le groupe de ressources à utiliser pour l’exécution.

## <a name="programming-pattern"></a>Modèle de programmation

Le modèle pour manipuler une ressource Event Hubs quelconque suit un protocole commun :

1. Obtenir un jeton à partir d’Azure Active Directory à l’aide de la bibliothèque `Microsoft.IdentityModel.Clients.ActiveDirectory`.
    ```csharp
    var context = new AuthenticationContext($"https://login.windows.net/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Créer l’objet `EventHubManagementClient`.
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Définissez les paramètres `CreateOrUpdate` sur vos propres valeurs.
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. Exécuter l’appel.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>Étapes suivantes
* [Exemple de gestion .NET](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Espace de noms Microsoft.Azure.Management.EventHub](/dotnet/api/Microsoft.Azure.Management.EventHub) 

