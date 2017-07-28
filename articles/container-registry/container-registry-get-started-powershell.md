---
title: "Créer des référentiels Azure Container Registry | Microsoft Docs"
description: "Utiliser des référentiels Azure Container Registry pour gérer des images Docker"
services: container-registry
documentationcenter: 
author: cristy
manager: balans
editor: dlepow
ms.service: container-registry
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2017
ms.author: cristyg
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 2fb060749c203a445196530bb7711d50d83c2923
ms.contentlocale: fr-fr
ms.lasthandoff: 06/09/2017


---

# <a name="create-a-private-docker-container-registry-using-the-azure-powershell"></a>Créer un registre de conteneurs Docker privé à l’aide d’Azure PowerShell
Utilisez les commandes dans [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview) pour créer un registre de conteneurs et gérer ses paramètres à partir de votre ordinateur Windows. Vous pouvez également créer et gérer des registres de conteneurs à l’aide du [portail Azure](container-registry-get-started-portal.md), d’[Azure CLI](container-registry-get-started-azure-cli.md) ou par programmation avec [l’API REST](https://go.microsoft.com/fwlink/p/?linkid=834376) de registre de conteneurs.


* Pour plus d’informations, notamment sur les concepts, consultez la [page de présentation](container-registry-intro.md)
* Pour obtenir une liste complète des applets de commande pris en charge, consultez [Applets de commande de gestion d’Azure Container Registry](https://docs.microsoft.com/en-us/powershell/module/azurerm.containerregistry/).


## <a name="prerequisites"></a>Prérequis
* **Azure PowerShell** : pour installer et découvrir Azure PowerShell, consultez les [instructions d’installation](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps). Connectez-vous à votre abonnement Azure en exécutant `Login-AzureRMAccount`. Pour plus d’informations, consultez [Bien démarrer avec Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/get-started-azurep).
* **Groupe de ressources** : créez un [groupe de ressources](../azure-resource-manager/resource-group-overview.md#resource-groups) avant de créer un registre de conteneur, ou utilisez un groupe de ressources existant. Assurez-vous que le groupe de ressources est dans un emplacement où le service Container Registry est [disponible](https://azure.microsoft.com/regions/services/). Pour créer un groupe de ressources à l’aide d’Azure PowerShell, consultez [la documentation de référence de Powershell](https://docs.microsoft.com/en-us/powershell/azure/get-started-azureps#create-a-resource-group).
* **Compte de stockage** (facultatif) : créez un [compte de stockage](../storage/storage-introduction.md) Azure standard pour prendre en charge le registre de conteneur dans le même emplacement. Si vous ne spécifiez pas de compte de stockage lors de la création d’un registre avec `New-AzureRMContainerRegistry`, la commande en crée un pour vous. Pour créer un compte de stockage à l’aide de PowerShell, consultez [la documentation de référence de PowerShell](https://docs.microsoft.com/en-us/powershell/module/azure/new-azurestorageaccount). Pour l’instant, l’offre Stockage Premium n’est pas prise en charge.
* **Principal de service** (facultatif) : lorsque vous créez un registre avec PowerShell, par défaut son accès n’est pas configuré. Selon vos besoins, vous pouvez assigner un principal de service Azure Active Directory existant à un registre, ou en créer un et l’y affecter. Vous pouvez également activer le compte de l’utilisateur administrateur du registre. Consultez les sections disponibles plus loin dans cet article. Pour plus d’informations sur l’accès au registre, consultez la section relative à [l’authentification auprès d’un conteneur](container-registry-authentication.md).

## <a name="create-a-container-registry"></a>Créer un registre de conteneur
Exécutez la commande `New-AzureRMContainerRegistry` pour créer un registre de conteneur.

> [!TIP]
> Lorsque vous créez un registre, spécifiez un nom de domaine global unique de niveau supérieur, contenant uniquement des chiffres et des lettres. Dans cet exemple, le nom de registre est `MyRegistry`, mais vous pouvez le remplacer par un nom unique de votre choix.
>
>

La commande suivante utilise les paramètres minimum pour créer le registre de conteneur `MyRegistry` dans le groupe de ressources `MyResourceGroup` à l’emplacement Sud du centre des États-Unis :

```PowerShell
$Registry = New-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry"
```

* `-StorageAccountName` est facultatif. S’il n’est pas spécifié, un compte de stockage est créé avec un nom comprenant le nom de Registre et un horodatage dans le groupe de ressources spécifié.

## <a name="assign-a-service-principal"></a>Affecter un principal de service
Utilisez les commandes PowerShell pour affecter un [principal de service](../azure-resource-manager/resource-group-authenticate-service-principal.md) Azure Active Directory à un registre. Le principal du service dans ces exemples est affecté au rôle de propriétaire, mais vous pouvez attribuer [d’autres rôles](../active-directory/role-based-access-control-configure.md) si vous le souhaitez.

### <a name="create-a-service-principal"></a>Créer un principal du service
Dans la commande suivante, un principal de service est créé. Spécifiez un mot de passe fort avec le paramètre `-Password`.

```PowerShell
$ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName ApplicationDisplayName -Password "MyPassword"
```

### <a name="assign-a-new-or-existing-service-principal"></a>Affecter un principal de service existant ou nouveau
Vous pouvez affecter un principal de service nouveau ou existant à un registre. Pour attribuer l’accès du rôle Propriétaire au registre, exécutez une commande similaire à l’exemple suivant :

```PowerShell
New-AzureRMRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Registry.Id
```

##<a name="sign-in-to-the-registry-with-the-service-principal"></a>Se connecter au registre avec le principal de service
Après avoir affecté le principal de service au registre, vous pouvez vous connecter à l’aide de la commande suivante :

```PowerShell
docker login -u $ServicePrincipal.ApplicationId -p myPassword
```

## <a name="manage-admin-credentials"></a>Gérer les informations d’identification de l’administrateur
Un compte d’administrateur est automatiquement créé pour chaque registre de conteneur et est désactivé par défaut. Les exemples suivants montrent les commandes PowerShell permettant de gérer les informations d’identification d’administrateur pour votre registre de conteneurs.

### <a name="obtain-admin-user-credentials"></a>Obtenir les informations d’identification de l’utilisateur Admin
```PowerShell
Get-AzureRMContainerRegistryCredential -ResourceGroupName "MyResourceGroup" -Name "MyRegistry"
```

### <a name="enable-admin-user-for-an-existing-registry"></a>Activer l’utilisateur Admin pour un registre existant
```PowerShell
Update-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry" -EnableAdminUser
```

### <a name="disable-admin-user-for-an-existing-registry"></a>Désactiver l’utilisateur Admin pour un registre existant
```PowerShell
Update-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry" -DisableAdminUser
```

## <a name="next-steps"></a>Étapes suivantes
* [Effectuer un push de votre première image à l’aide de l’interface CLI Docker](container-registry-get-started-docker-cli.md)

