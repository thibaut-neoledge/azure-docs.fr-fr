---
title: "Gérer des solutions Azure avec PowerShell | Microsoft Docs"
description: "Utilisez Azure PowerShell et Resource Manager pour gérer vos ressources."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: b33b7303-3330-4af8-8329-c80ac7e9bc7f
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: c201ac12d06ffc8097615517ae09422b037eba6b
ms.contentlocale: fr-fr
ms.lasthandoff: 07/21/2017

---
# <a name="manage-resources-with-azure-powershell-and-resource-manager"></a>Gérer les ressources avec Azure PowerShell et Resource Manager
> [!div class="op_single_selector"]
> * [Portail](resource-group-portal.md)
> * [Interface de ligne de commande Azure](xplat-cli-azure-resource-manager.md)
> * [Azure PowerShell](powershell-azure-resource-manager.md)
> * [API REST](resource-manager-rest-api.md)
>
>

Dans cet article, vous allez apprendre à gérer vos solutions avec Azure PowerShell et Azure Resource Manager. Si vous n’êtes pas familiarisé avec Resource Manager, consultez la page [Vue d’ensemble de Resource Manager](resource-group-overview.md). Cette rubrique se concentre sur les tâches de gestion. Vous allez :

1. Créer un groupe de ressources
2. Ajouter une ressource au groupe de ressources
3. Ajouter une balise à la ressource
4. Interroger les ressources selon des noms ou des valeurs de balise
5. Appliquer et supprimer un verrou sur la ressource
6. Supprimer un groupe de ressources

Cet article n’indique pas comment déployer un modèle Resource Manager sur votre abonnement. Pour plus d’informations, voir [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](resource-group-template-deploy.md).

## <a name="get-started-with-azure-powershell"></a>Prise en main de Microsoft Azure PowerShell

Si Azure PowerShell n’est pas installé sur votre système, consultez la page [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

Si vous avez déjà installé Azure PowerShell mais ne l’avez pas mis à jour récemment, envisagez d’installer la version la plus récente. Vous pouvez mettre à jour la version en appliquant la même méthode que lors de l’installation. Par exemple, si vous avez utilisé le programme Web Platform Installer, relancez-le et recherchez si une mise à jour est disponible.

Pour vérifier votre version du module de ressources Azure, utilisez l’applet de commande suivante :

```powershell
Get-Module -ListAvailable -Name AzureRm.Resources | Select Version
```

Cette rubrique a été mise à jour pour la version 3.3.0. Si vous possédez une version antérieure, les étapes présentées dans cette rubrique ne correspondront peut-être pas à votre situation. Pour plus d’informations sur les applets de commande pour cette version, consultez [AzureRM.Resources Module](/powershell/module/azurerm.resources) (Module AzureRM.Resources).

## <a name="log-in-to-your-azure-account"></a>Connexion à votre compte Azure
Avant de travailler sur votre solution, vous devez vous connecter à votre compte.

Pour vous connecter à votre compte Azure, utilisez l’applet de commande **Login-AzureRmAccount**.

```powershell
Login-AzureRmAccount
```

Les applets de commande vous invitent à entrer les informations d’identification de connexion pour votre compte Azure. Une fois que vous êtes connecté, l’applet de commande télécharge vos paramètres de compte pour qu’ils soient reconnus par Azure PowerShell.

L’applet de commande retourne des informations sur votre compte et l’abonnement à utiliser pour les tâches.

```powershell
Environment           : AzureCloud
Account               : example@contoso.com
TenantId              : {guid}
SubscriptionId        : {guid}
SubscriptionName      : Example Subscription One
CurrentStorageAccount :

```

Si vous avez plusieurs abonnements, vous pouvez en choisir un autre. Tout d’abord, nous allons voir tous les abonnements de votre compte.

```powershell
Get-AzureRmSubscription
```

Les abonnements activés et désactivés sont retournés.

```powershell
SubscriptionName : Example Subscription One
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Enabled

SubscriptionName : Example Subscription Two
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Enabled

SubscriptionName : Example Subscription Three
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Disabled
```

Pour choisir un autre abonnement, indiquez le nom de l’abonnement avec l’applet de commande **Set-AzureRmContext**.

```powershell
Set-AzureRmContext -SubscriptionName "Example Subscription Two"
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Avant de déployer des ressources dans votre abonnement, vous devez créer un groupe de ressources qui contiendra ces ressources.

Pour créer un groupe de ressources, utilisez l’applet de commande **New-AzureRmResourceGroup** . La commande utilise le paramètre **Name** pour attribuer un nom au groupe de ressources et le paramètre **Location** pour indiquer son emplacement.

```powershell
New-AzureRmResourceGroup -Name TestRG1 -Location "South Central US"
```

La sortie est au format suivant :

```powershell
ResourceGroupName : TestRG1
Location          : southcentralus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1
```

Si vous avez besoin de récupérer le groupe de ressources ultérieurement, utilisez l’applet de commande suivante :

```powershell
Get-AzureRmResourceGroup -ResourceGroupName TestRG1
```

Pour obtenir tous les groupes de ressources de votre abonnement, n’indiquez aucun nom :

```powershell
Get-AzureRmResourceGroup
```

## <a name="add-resources-to-a-resource-group"></a>Ajouter des ressources à un groupe de ressources
Pour ajouter une ressource au groupe de ressources, vous pouvez utiliser l’applet de commande **New-AzureRmResource** ou une applet de commande spécifique au type de ressource que vous créez (comme **New-AzureRmStorageAccount**). Il est peut-être plus facile d’utiliser une applet de commande spécifique à un type de ressource, car elle inclut les paramètres relatifs aux propriétés requises pour la nouvelle ressource. Pour utiliser **New-AzureRmResource**, vous devez connaître toutes les propriétés à définir, même si vous n’êtes pas invité à les entrer.

Cependant, l’ajout d’une ressource à l’aide d’applets de commande risque de créer une confusion par la suite, car la nouvelle ressource n’existe pas dans un modèle Resource Manager. Microsoft recommande de définir l’infrastructure de votre solution Azure dans un modèle Resource Manager. Les modèles vous permettent de déployer votre solution plusieurs fois de manière fiable. Dans le cadre de cette rubrique, vous créez un compte de stockage avec une applet de commande PowerShell et générerez plus tard un modèle à partir de votre groupe de ressources.

L’applet de commande suivante permet de créer un compte de stockage. Au lieu d’utiliser le nom indiqué dans l’exemple, entrez un nom unique pour le compte de stockage. Le nom doit comprendre entre 3 et 24 caractères et comporter uniquement des lettres en minuscules et des nombres. Si vous utilisez le nom indiqué dans l’exemple, vous recevez une erreur, car ce nom est déjà en cours d’utilisation.

```powershell
New-AzureRmStorageAccount -ResourceGroupName TestRG1 -AccountName mystoragename -Type "Standard_LRS" -Location "South Central US"
```

Si vous avez besoin de récupérer cette ressource ultérieurement, utilisez l’applet de commande suivante :

```powershell
Get-AzureRmResource -ResourceName mystoragename -ResourceGroupName TestRG1
```

## <a name="add-a-tag"></a>Ajouter une balise

Les balises vous permettent d’organiser vos ressources en fonction de différentes propriétés. Par exemple, vous pouvez disposer de plusieurs ressources incluses dans différents groupes de ressources appartenant au même service. Vous pouvez appliquer une valeur et une balise de service à ces ressources pour les marquer comme appartenant à la même catégorie. Vous pouvez également indiquer si une ressource est utilisée dans un environnement de production ou de test. Dans le cadre de cette rubrique, vous appliquez des balises à une seule ressource, mais il conviendra probablement d’appliquer des balises à toutes vos ressources dans votre environnement.

L’applet de commande suivante applique deux balises à votre compte de stockage :

```powershell
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName mystoragename -ResourceGroupName TestRG1 -ResourceType Microsoft.Storage/storageAccounts
 ```

Les balises sont mises à jour en tant qu’objet unique. Pour ajouter une balise à une ressource qui inclut déjà des balises, commencez par récupérer les balises existantes. Ajoutez la nouvelle balise à l’objet qui contient les balises existantes et appliquez de nouveau toutes les balises à la ressource.

```powershell
$tags = (Get-AzureRmResource -ResourceName mystoragename -ResourceGroupName TestRG1).Tags
$tags += @{Status="Approved"}
Set-AzureRmResource -Tag $tags -ResourceName mystoragename -ResourceGroupName TestRG1 -ResourceType Microsoft.Storage/storageAccounts
```

## <a name="search-for-resources"></a>Rechercher des ressources

Utilisez l’applet de commande **Find-AzureRmResource** pour récupérer les ressources selon différentes conditions de recherche.

* Pour obtenir une ressource par son nom, indiquez le paramètre **ResourceNameContains** :

  ```powershell
  Find-AzureRmResource -ResourceNameContains mystoragename
  ```

* Pour obtenir toutes les ressources d’un groupe de ressources, indiquez le paramètre **ResourceGroupNameContains** :

  ```powershell
  Find-AzureRmResource -ResourceGroupNameContains TestRG1
  ```

* Pour obtenir toutes les ressources ayant une valeur et un nom de balise, indiquez les paramètres **TagName** et **TagValue** :

  ```powershell
  Find-AzureRmResource -TagName Dept -TagValue IT
  ```

* Pour obtenir toutes les ressources d’un type particulier, indiquez le paramètre **ResourceType** :

  ```powershell
  Find-AzureRmResource -ResourceType Microsoft.Storage/storageAccounts
  ```

## <a name="lock-a-resource"></a>Verrouiller une ressource

Pour vous assurer qu’une ressource critique ne sera pas accidentellement supprimée ou modifiée, appliquez un verrou à la ressource. Vous pouvez spécifier le niveau **CanNotDelete** ou **ReadOnly**.

Pour créer ou supprimer des verrous de gestion, vous devez avoir accès à des actions `Microsoft.Authorization/*` ou `Microsoft.Authorization/locks/*`. Parmi les rôles prédéfinis, seuls les rôles Propriétaire et Administrateur de l'accès utilisateur peuvent effectuer ces actions.

Pour appliquer un verrou, utilisez l’applet de commande suivante :

```powershell
New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockStorage -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
```

La ressource verrouillée de l’exemple précédent ne peut pas être effacée tant que le verrou n’est pas supprimé. Pour supprimer un verrou, utilisez :

```powershell
Remove-AzureRmResourceLock -LockName LockStorage -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
```

Pour plus d’informations sur la définition des verrous, consultez [Verrouiller des ressources avec Azure Resource Manager](resource-group-lock-resources.md).

## <a name="remove-resources-or-resource-group"></a>Supprimer des ressources ou un groupe de ressources
Vous pouvez supprimer une ressource ou un groupe de ressources. Lorsque vous supprimez un groupe de ressources, vous supprimez également toutes les ressources qu’il contient.

* Pour supprimer une ressource du groupe de ressources, utilisez l’applet de commande **Remove-AzureRmResource** . Cette applet de commande supprime la ressource, mais pas le groupe de ressources.

  ```powershell
  Remove-AzureRmResource -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
  ```

* Pour supprimer un groupe de ressources et toutes les ressources qu’il contient, utilisez l’applet de commande **Remove-AzureRmResourceGroup**.

  ```powershell
  Remove-AzureRmResourceGroup -Name TestRG1
  ```

Pour les deux applets de commande, vous êtes invité à confirmer que vous souhaitez supprimer la ressource ou le groupe de ressources. Si l’opération supprime avec succès la ressource ou le groupe de ressources, elle retourne la valeur **True**.

## <a name="run-resource-manager-scripts-with-azure-automation"></a>Exécuter des scripts Resource Manager avec Azure Automation

Cette rubrique montre comment effectuer des opérations de base sur vos ressources avec Azure PowerShell. Pour les scénarios de gestion plus avancés, vous souhaiterez généralement créer un script et le réutiliser en fonction de vos besoins ou selon une planification. [Azure Automation](../automation/automation-intro.md) vous offre un moyen d’automatiser les scripts fréquemment utilisés, qui gèrent vos solutions Azure.

Les rubriques suivantes vous montrent comment utiliser Azure Automation, Resource Manager et PowerShell pour exécuter efficacement des tâches de gestion :

- Pour plus d’informations sur la création d’un runbook, consultez [Mon premier Runbook PowerShell](../automation/automation-first-runbook-textual-powershell.md).
- Pour plus d’informations sur l’utilisation des galeries de scripts, consultez [Galeries de runbooks et de modules pour Azure Automation](../automation/automation-runbook-gallery.md).
- Pour les runbooks qui démarrent et arrêtent des machines virtuelles, consultez [Scénario Azure Automation : utilisation de balises au format JSON afin de créer une planification pour le démarrage et l’arrêt de machines virtuelles Azure](../automation/automation-scenario-start-stop-vm-wjson-tags.md).
- Pour les runbooks qui démarrent et arrêtent des machines virtuelles durant les heures creuses, consultez [Solution Start/Stop VMs during off-hours (Démarrer/arrêter des machines virtuelles durant les heures creuses) dans Automation](../automation/automation-solution-vm-management.md).

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur la création de modèles Resource Manager, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour savoir comment déployer des modèles, consultez [Déployer une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).
* Vous pouvez déplacer des ressources existantes vers un nouveau groupe de ressources. Pour obtenir des exemples, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](resource-group-move-resources.md).
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).


