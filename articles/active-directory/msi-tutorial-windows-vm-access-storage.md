---
title: "Utiliser une MSI de machine virtuelle Windows pour accéder au stockage Azure"
description: "Ce didacticiel vous guide tout au long de l’utilisation d’une identité du service administré (MSI) de machine virtuelle Windows pour accéder au stockage Azure."
services: active-directory
documentationcenter: 
author: elkuzmen
manager: mbaldwin
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: elkuzmen
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 86d88e3d44f442171f69d0baea5e7d689b963277
ms.contentlocale: fr-fr
ms.lasthandoff: 09/14/2017

---

# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-storage"></a>Utiliser une identité du service administré (MSI) de machine virtuelle Windows pour accéder au stockage Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Ce didacticiel vous montre comment activer des identités du service administré (MSI) pour une machine virtuelle Linux et ensuite utiliser cette identité pour accéder aux clés de stockage. Vous pouvez utiliser les clés de stockage comme d’habitude lors de l’exécution d’opérations de stockage, par exemple lors de l’utilisation du stockage de Kits de développement logiciel (SDK). Pour ce didacticiel, nous chargerons et téléchargerons des objets blob à l’aide d’Azure CLI. Vous apprendrez à :


> [!div class="checklist"]
> * Activer MSI sur une machine virtuelle Windows 
> * Créer un compte de stockage
> * Accorder l’accès au stockage à votre machine virtuelle 
> * Obtenir un jeton d’accès à votre compte de stockage à l’aide de l’identité de machine virtuelle 


Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure à l’adresse [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Création d'une machine virtuelle Windows dans un nouveau groupe de ressources

Pour ce didacticiel, nous allons créer une machine virtuelle Windows. Vous pouvez également activer l’identité du service administré sur une machine virtuelle existante.

1.  Cliquez sur le bouton **Nouveau** dans le coin supérieur gauche du portail Azure.
2.  Sélectionnez **Compute**, puis **Windows Server 2016 Datacenter**. 
3.  Saisissez les informations de la machine virtuelle. Le **Nom d’utilisateur** et le **Mot de passe** créés ici sont les informations d’identification nécessaires pour vous connecter à la machine virtuelle.
4.  Choisissez un **Abonnement** approprié pour la machine virtuelle dans la liste déroulante.
5.  Pour sélectionner un nouveau **Groupe de ressources** dans lequel vous souhaitez créer une machine virtuelle, choisissez **Créer un nouveau**. Lorsque vous avez terminé, cliquez sur **OK**.
6.  Choisissez la taille de la machine virtuelle. Pour voir plus de tailles, sélectionnez **Afficher tout** ou modifiez le filtre **Type de disque pris en charge**. Conservez les valeurs par défaut dans le panneau des paramètres et cliquez sur **OK**.

    ![Texte de remplacement d’image](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Activer l’identité du service administré sur votre machine virtuelle

L’identité du service administré d’une machine virtuelle permet d’obtenir des jetons d’accès d’Azure AD sans avoir à placer des informations d’identification dans votre code. En arrière-plan, activer MSI effectue deux opérations : cela installe l’extension MSI VM sur votre machine virtuelle et cela active MSI pour la machine virtuelle.  

1. Sélectionnez la **machine virtuelle** de votre choix pour activer MSI.
2. Dans la barre de navigation gauche, cliquez sur **Configuration**.
3. **Identité du service administré** s’affiche. Pour enregistrer et activer l’identité du service administré, sélectionnez **Oui**. Si vous souhaitez la désactiver, sélectionnez Non.
4. Assurez-vous d’avoir cliqué sur **Enregistrer** pour enregistrer la configuration.

    ![Texte de remplacement d’image](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Si vous souhaitez vérifier quelles sont les extensions sur cette **machine virtuelle Windows**, cliquez sur **Extensions**. Si MSI est activée, l’extension **ManagedIdentityExtensionforWindows** s’affiche dans la liste.

    ![Texte de remplacement d’image](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-new-storage-account"></a>Créer un compte de stockage 

Vous pouvez utiliser des clés de stockage comme d’habitude lors d’opérations de stockage. Dans cet exemple, nous allons nous concentrer sur le chargement et le téléchargement d’objets blob à l’aide d’Azure CLI. 

1. Accédez à la barre latérale et sélectionnez **Stockage**.  
2. Créez un **Compte de stockage**.  
3. Dans **Modèle de déploiement**, entrez dans le **Gestionnaire des ressources** et **Type de compte** avec **Usage général**.  
4. Assurez-vous que l’**Abonnement** et le **Groupe de ressources** sont ceux que vous avez utilisés lorsque vous avez créé votre **machine virtuelle Linux** à l’étape précédente.

    ![Texte de remplacement d’image](media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="grant-your-vm-identity-access-to-use-storage-keys"></a>Accorder l’accès pour utiliser les clés de stockage à votre machine virtuelle 

À l’aide de l’identité de service administré, votre code peut obtenir des jetons d’accès pour vous authentifier sur des ressources prenant en charge l’authentification Azure AD.   

1. Accédez à l’onglet **Stockage**.  
2. Sélectionnez le **Compte de stockage** spécifique que vous avez préalablement créé.   
3. Accédez au **Contrôle d’accès (IAM)** dans le volet de gauche.  
4. Puis, choisissez d’**Ajouter** une nouvelle attribution de rôle pour votre machine virtuelle et sélectionnez **Rôle** en tant que **Rôle de service opérateur de clé de compte de stockage**.  
5. Dans la liste déroulante suivante, sélectionnez **Attribuer un accès** à la ressource **Machine virtuelle**.  
6. Ensuite, vérifiez que l’abonnement approprié est répertorié dans la liste déroulante **Abonnement**. Et pour **Groupe de ressources**, sélectionnez **Tous les groupes de ressources**.  
7. Enfin, choisissez votre machine virtuelle Windows dans la liste déroulante dans **Sélectionner** et cliquez sur **Enregistrer**. 

    ![Texte de remplacement d’image](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>Obtenir un jeton d’accès à l’aide de l’identité de machine virtuelle et l’utiliser pour appeler Azure Resource Manager 

Vous devez utiliser **PowerShell** dans cette partie.  Si vous ne l’avez pas installé, vous pouvez le [télécharger ici](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1). 

1. Dans le portail, accédez à **Machines virtuelles** et accédez à votre machine virtuelle Windows. Puis, dans **Vue d’ensemble**, cliquez sur **Connecter**. 
2. Entrez le **Nom d’utilisateur** et le **Mot de passe** que vous avez ajoutés lorsque vous avez créé la machine virtuelle Windows. 
3. Maintenant que vous avez créé une **Connexion au Bureau à distance** avec la machine virtuelle, ouvrez **PowerShell** dans la session à distance. 
4. À l’aide de Invoke-WebRequest de Powershell, envoyez une requête au point de terminaison MSI local pour obtenir un jeton d’accès pour Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost/50342/oauth2/token -Method GET -Body @resource="https://management.azure.com/"} -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > La valeur du paramètre « ressource » doit correspondre exactement à ce qui est attendu par Azure AD. Lorsque vous utilisez l’ID de ressource Azure Resource Manager, vous devez inclure la barre oblique de fin à l’URI.
    
    Ensuite, extrayez la réponse complète qui est stockée sous forme de chaîne au format JavaScript Objet Notation (JSON) dans l’objet $response. 
    
    ```powershell
    $content = $repsonse.Content | ConvertFrom-Json
    ```
    Ensuite, extrayez le jeton d’accès de la réponse.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
    
    Enfin, appelez Azure Resource Manager à l’aide du jeton d’accès. Dans cet exemple, nous utilisons également le Invoke-WebRequest de PowerShell pour appeler Azure Resource Manager et inclure le jeton d’accès dans l’en-tête d’autorisation.
    
    ```powershell
    (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $ArmToken"}).content
    ```
    > [!NOTE]
    > L’URL respecte la casse, par conséquent, assurez-vous que la casse est identique à celle utilisée précédemment lorsque vous avez nommé le groupe de ressources, et que la majuscule « G » dans « resourceGroup » est correcte.
    
## <a name="get-the-storage-keys-from-azure-resource-manager"></a>Obtenir les clés de stockage à partir d’Azure Resource Manager 

```powershell
PS C:\> $keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/97f51385-2edc-4b69-bed8-7778dd4cb761/resourceGroups/SKwan_Test/providers/Microsoft.Storage/storageAccounts/skwanteststorage/listKeys/?api-version=2016-12-01 -Method POST$ -Headers @{Authorization="Bearer $ARMToken"}
```

```powershell
PS C:\> $keysContent = $keysResponse.Content | ConvertFrom-Json
```

```powershell
PS C:\> $key = $keysContent.keys[0].value
```

**Créer un fichier à charger à l’aide d’Azure CLI**

```bash
echo "This is a test text file." > test.txt
```

**Charger le fichier à l’aide d’Azure CLI et s’authentifier avec la clé de stockage**

> [!NOTE]
> Vous devez d’abord installer les applets de commande de stockage Azure « Install-Module Azure.Storage ». 

Requête PowerShell :


```powershell
PS C:\> $ctx = New-AzureStorageContext -StorageAccountName skwanteststorage -StorageAccountKey $key
PS C:\> Set-AzureStorageBlobContent -File test.txt -Container testcontainer -Blob testblob -Context $ctx
```

Réponse :

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

**Télécharger le fichier à l’aide d’Azure CLI et s’authentifier avec la clé de stockage**

Requête PowerShell :

```powershell
PS C:\> Get-AzureStorageBlobContent -Blob <blob name> -Container <container name> -Destination <file> -Context $ctx
```

Réponse :

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```





