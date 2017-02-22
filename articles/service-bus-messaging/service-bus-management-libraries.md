---
title: "Bibliothèques de gestion Azure Service Bus | Microsoft Docs"
description: "Gérer les entités et les espaces de noms Service Hub à partir de .NET"
services: service-bus-messaging
cloud: na
documentationcenter: na
author: jtaubensee
manager: timlt
ms.assetid: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 1/6/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: dfd1ae52cc56a4d4b4c7ee3f69f0c454be607401
ms.openlocfilehash: 7b04227327235f788ecf4018b6c159d33113b63d


---

# <a name="service-bus-management-libraries"></a>Bibliothèques de gestion Service Bus

Les bibliothèques de gestion Service Bus peuvent approvisionner dynamiquement des entités et des espaces de noms Service Bus. Cela permet de mettre en œuvre des déploiements et des scénarios de messagerie complexes, et de déterminer ainsi par programmation les entités à approvisionner. Ces bibliothèques sont actuellement disponibles pour .NET.

## <a name="supported-functionality"></a>Fonctionnalités prises en charge

* Création, mise à jour et suppression d’espaces de noms
* Création, mise à jour et suppression de files d’attente
* Création, mise à jour et suppression de rubriques
* Création, mise à jour et suppression d’abonnements

## <a name="prerequisites"></a>Composants requis

Pour commencer à utiliser les bibliothèques de gestion Service Bus, vous devez vous authentifier auprès d’Azure Active Directory (AAD). AAD vous oblige à vous authentifier en tant que principal du service pour pouvoir accéder à vos ressources Azure. Pour plus d’informations sur la création d’un principal du service, consultez ces articles :  

* [Utiliser le portail Azure pour créer une application et un principal du service Active Directory pouvant accéder aux ressources](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Créer un principal du service pour accéder aux ressources à l’aide d’Azure PowerShell](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Créer un principal du service pour accéder aux ressources à l’aide de l’interface de ligne de commande (CLI) Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

Ces didacticiels vous fourniront un `AppId` (ID client), un `TenantId` (ID de locataire) et un `ClientSecret` (clé d’authentification), qui sont utilisés pour l’authentification par les bibliothèques de gestion. Vous devez disposer des autorisations « Propriétaire » pour le groupe de ressources à utiliser pour l’exécution.

## <a name="programming-pattern"></a>Modèle de programmation

Le modèle pour manipuler une ressource Service Bus quelconque suit un protocole commun :

1. Obtenir un jeton auprès d’Azure Active Directory à l’aide de la bibliothèque `Microsoft.IdentityModel.Clients.ActiveDirectory`.
    ```csharp
    var context = new AuthenticationContext($"https://login.windows.net/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Créer l’objet `ServiceBusManagementClient`.
    ```csharp
    var creds = new TokenCredentials(token);
    var sbClient = new ServiceBusManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Configurer les paramètres CreateOrUpdate avec vos propres valeurs.
    ```csharp
    var queueParams = new QueueCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"],
        EnablePartitioning = true
    };
    ```

1. Exécuter l’appel.
    ```csharp
    await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
    ```

## <a name="next-steps"></a>Étapes suivantes
* [Exemple de gestion .NET](https://github.com/Azure-Samples/service-bus-dotnet-management/)
* [Informations de référence sur Microsoft.Azure.Management.ServiceBus](/dotnet/api/Microsoft.Azure.Management.ServiceBus)



<!--HONumber=Jan17_HO3-->


