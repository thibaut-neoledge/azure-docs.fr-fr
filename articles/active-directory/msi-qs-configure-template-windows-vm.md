---
title: "Configurer l’identité du service administré sur une machine virtuelle Azure à l’aide d’un modèle"
description: "Instructions détaillées sur la configuration d’une identité du service administré (MSI) sur une machine virtuelle Azure, à l’aide d’un modèle Azure Resource Manager."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.translationtype: HT
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: 8b599c3e0e7d4fa3ae5bdb156191bff0553249ee
ms.contentlocale: fr-fr
ms.lasthandoff: 09/22/2017

---

# <a name="configure-a-vm-managed-service-identity-msi-using-a-template"></a>Configurer une identité du service administré (MSI) de machine virtuelle à l’aide d’un modèle

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

L’identité du service administré fournit des services Azure avec une identité gérée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous apprenez à activer et supprimer l’identité du service administré pour une machine virtuelle Azure, à l’aide d’un modèle de déploiement Azure Resource Manager.

## <a name="prerequisites"></a>Composants requis

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Activer l’identité du service administré lors de la création d’une machine virtuelle Azure, ou sur une machine virtuelle existante

Comme pour le portail Azure et le script, les modèles Azure Resource Manager offrent la possibilité de déployer des ressources nouvelles/modifiées définies par un groupe de ressources Azure. Plusieurs options sont disponibles pour la modification du modèle et le déploiement, à la fois localement et sur le portail/web. En voici quelques-unes :

   - Utiliser un [modèle personnalisé à partir d’Azure Marketplace](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), lequel vous permet de créer un modèle à partir de zéro, ou à partir d’un modèle commun existant ou d’un [modèle de démarrage rapide](https://azure.microsoft.com/documentation/templates/).
   - Dériver à partir d’un groupe de ressources existant, en exportant un modèle à partir du [déploiement d’origine](../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), ou à partir de l’[état actuel du déploiement](../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Utiliser un éditeur local [JSON (VS Code, par exemple)](../azure-resource-manager/resource-manager-create-first-template.md), puis télécharger/déployer à l’aide de PowerShell ou Azure CLI.
   - Utiliser le [projet de groupe de ressources Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) de Visual Studio pour créer et déployer un modèle.  

Quel que soit le chemin que vous prenez, la syntaxe du modèle est la même pendant le déploiement initial et le redéploiement. L’activation de l’identité du service administré sur une machine virtuelle nouvelle ou existante s’effectue donc de la même manière. En outre, par défaut, Azure Resource Manager effectue une [mise à jour incrémentielle](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) au niveau des déploiements :

1. Si vous vous connectez à Azure localement ou via le portail Azure, utilisez un compte associé à l’abonnement Azure qui contient l’ordinateur virtuel. Vous devez également vérifier que votre compte appartient à un rôle qui vous donne des autorisations en écriture sur la machine virtuelle, comme « Contributeur de machines virtuelles ».

2. Après le chargement du modèle dans un éditeur, localisez la ressource d’intérêt `Microsoft.Compute/virtualMachines` dans la section `resources`. La vôtre peut différer légèrement de cette capture d’écran, selon l’éditeur que vous utilisez et si vous modifiez un modèle pour un déploiement nouveau ou existant :

   >[!NOTE] 
   > Cet exemple suppose que des variables telles que `vmName`, `storageAccountName` et `nicName` ont été définies dans le modèle.
   >

   ![Modèle avant capture d’écran - localiser une machine virtuelle](./media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. Ajoutez la propriété `"identity"` au même niveau que la propriété `"type": "Microsoft.Compute/virtualMachines"` à l’aide de la syntaxe suivante :

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. Puis ajoutez l’extension de l’identité du service administré de la machine virtuelle en tant qu’élément `resources` à l’aide de la syntaxe suivante :

   >[!NOTE] 
   > L’exemple suivant suppose qu’une extension de la machine virtuelle Windows (`ManagedIdentityExtensionForWindows`) est en cours de déploiement. À la place, vous pouvez également configurer pour Linux à l’aide de `ManagedIdentityExtensionForLinux`, pour les éléments `"name"` et `"type"`.
   >

   ```JSON
   { 
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
       "apiVersion": "2016-03-30",
       "location": "[resourceGroup().location]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.ManagedIdentity",
           "type": "ManagedIdentityExtensionForWindows",
           "typeHandlerVersion": "1.0",
           "autoUpgradeMinorVersion": true,
           "settings": {
               "port": 50342
           },
           "protectedSettings": {}
       }
   }
   ```

5. Lorsque vous avez terminé, votre modèle doit ressembler à l’exemple suivant :

   ![Modèle après capture](./media/msi-qs-configure-template-windows-vm/template-file-after.png) 

## <a name="remove-msi-from-an-azure-vm"></a>Supprimer l’identité du service administré d’une machine virtuelle Azure

Si vous disposez d’une machine virtuelle qui ne nécessite plus d’identité du service administré :

1. Si vous vous connectez à Azure localement ou via le portail Azure, utilisez un compte associé à l’abonnement Azure qui contient l’ordinateur virtuel. Vérifiez également que votre compte appartient à un rôle qui vous donne des autorisations en écriture sur la machine virtuelle, comme « Contributeur de machines virtuelles ».

2. Supprimez les deux éléments qui ont été ajoutés dans la section précédente : la propriété `"identity"` de la machine virtuelle et la ressource `"Microsoft.Compute/virtualMachines/extensions"`.

## <a name="related-content"></a>Contenu connexe

- [Vue d’ensemble de l’identité du service administré](msi-overview.md)

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.


