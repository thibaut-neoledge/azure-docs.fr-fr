---
title: "Obtenir des valeurs pour l’authentification de l’application - Azure SQL Database | Microsoft Docs"
description: "Créez un principal du service pour l’accès à la base de données SQL à partir du code."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
tags: 
ms.assetid: b43e43bb-6660-49e6-b069-abde97eb5770
ms.service: sql-database
ms.custom: develop apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/30/2016
ms.author: sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: ec6256e9c5bb0d9c8d15d0f673cea70b3915eb34
ms.contentlocale: fr-fr
ms.lasthandoff: 06/22/2017


---
# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>Obtenir l’ID client et la clé pour la connexion à une base de données SQL à partir du code
Pour créer et gérer une base de données SQL à partir du code, vous devez inscrire votre application dans le domaine Azure Active Directory (AAD) de l’abonnement où vos ressources Azure ont été créées.

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>Créer un principal du service pour accéder aux ressources à partir d’une application
La dernière version [d’Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx) doit être installée et en cours d’exécution. Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).

Le script PowerShell suivant crée l’application Active Directory (AD) et le principal du service dont nous avons besoin pour authentifier notre application C#. Le script génère les valeurs dont nous avons besoin pour l’exemple C# précédent. Pour plus de détails, consultez la page [Créer un principal du service pour accéder aux ressources à l’aide d’Azure PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md).

    # Sign in to Azure.
    Add-AzureRmAccount

    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId

    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.

    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"

    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15

    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid


    # Output the values we need for our C# application to successfully authenticate

    Write-Output "Copy these values into the C# sample app"

    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




## <a name="see-also"></a>Voir aussi
* [Créer une base de données SQL avec C#](sql-database-get-started-csharp.md)
* [Connexion à SQL Database avec l’authentification Azure Active Directory](sql-database-aad-authentication.md)


