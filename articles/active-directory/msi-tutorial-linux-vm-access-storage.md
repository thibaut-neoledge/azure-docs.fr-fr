---
title: "Utiliser une MSI de machine virtuelle Linux pour accéder au stockage Azure"
description: "Ce didacticiel vous guide tout au long de l’utilisation d’une identité du service administré (MSI) de machine virtuelle Linux pour accéder au stockage Azure."
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
ms.openlocfilehash: 9fec51528d1203a7107558408ced42fa2b2b884a
ms.contentlocale: fr-fr
ms.lasthandoff: 09/14/2017

---


# <a name="use-managed-service-identity-msi-with-a-linux-vm-to-access-storage-credentials"></a>Utiliser l’identité du service administré (MSI) avec une machine virtuelle Linux pour accéder aux informations d'identification de stockage

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Ce didacticiel vous montre comment activer des identités du service administré (MSI) pour une machine virtuelle Linux et ensuite utiliser cette identité pour accéder aux clés de stockage. Vous pouvez utiliser les clés de stockage comme d’habitude lors de l’exécution d’opérations de stockage, par exemple lors de l’utilisation du stockage de Kits de développement logiciel (SDK). Pour ce didacticiel, nous chargerons et téléchargerons des objets blob à l’aide d’Azure CLI. Vous apprendrez à :


> [!div class="checklist"]
> * Activer l’identité du service administré sur une machine virtuelle Linux 
> * Créer un compte de stockage
> * Accorder l’accès au stockage à votre machine virtuelle
> * Obtenir un jeton d’accès au compte de stockage à l’aide de l’identité de machine virtuelle pour accéder aux clés de stockage 


Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="sign-in-to-azure"></a>Connexion à Azure
Connectez-vous au portail Azure à l’adresse [https://portal.azure.com](https://portal.azure.com).


## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Créer une machine virtuelle Linux dans un nouveau groupe de ressources

Pour ce didacticiel, nous allons créer une machine virtuelle Linux. Vous pouvez également activer l’identité du service administré sur une machine virtuelle existante.

1. Cliquez sur le bouton **Nouveau** dans le coin supérieur gauche du portail Azure.
2. Sélectionnez **Compute**, puis sélectionnez **Ubuntu Server 16.04 LTS**.
3. Saisissez les informations de la machine virtuelle. Dans **Type d’authentification**, sélectionnez **Clé publique SSH** ou **Mot de passe**. Les informations d’identification créées vous permettent de vous connecter à la machine virtuelle.

    ![Texte de remplacement d’image](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Choisissez un **Abonnement** pour la machine virtuelle dans la liste déroulante.
5. Pour sélectionner un nouveau **Groupe de ressources** dans lequel vous souhaitez créer la machine virtuelle, choisissez **Créer un nouveau**. Lorsque vous avez terminé, cliquez sur **OK**.
6. Choisissez la taille de la machine virtuelle. Pour voir plus de tailles, sélectionnez **Afficher tout** ou modifiez le filtre de type de disque pris en charge. Conservez les valeurs par défaut dans le panneau des paramètres et cliquez sur **OK**.

## <a name="enable-msi-on-your-vm"></a>Activer l’identité du service administré sur votre machine virtuelle

L’identité du service administré d’une machine virtuelle permet d’obtenir des jetons d’accès d’Azure AD sans avoir à placer des informations d’identification dans votre code. En arrière-plan, l’activation de l’identité du service administré entraîne deux actions : elle installe l’extension de l’identité du service administré de la machine virtuelle et active l’identité du service administré pour la machine virtuelle.  

1. Sélectionnez la **machine virtuelle** de votre choix pour activer MSI.
2. Dans la barre de navigation gauche, cliquez sur **Configuration**.
3. **Identité du service administré** s’affiche. Pour enregistrer et activer l’identité du service administré, sélectionnez **Oui**. Si vous souhaitez la désactiver, sélectionnez Non.
4. Assurez-vous d’avoir cliqué sur **Enregistrer** pour enregistrer la configuration.

    ![Texte de remplacement d’image](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Si vous souhaitez vérifier les extensions présentes sur cette **Machine virtuelle Linux**, cliquez sur **Extensions**. Si l’identité du service administré est activée, **ManagedIdentityExtensionforLinux** apparaît dans la liste.

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
7. Enfin, dans **Sélectionner**, choisissez votre machine virtuelle Linux dans la liste déroulante et cliquez sur **Enregistrer**. 
    ![Texte de remplacement d’image](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>Obtenir un jeton d’accès à l’aide de l’identité de machine virtuelle et l’utiliser pour appeler Azure Resource Manager

Vous devez utiliser le terminal Bash, puis sélectionner votre distribution Linux et la [télécharger ici](https://msdn.microsoft.com/commandline/wsl/install_guide).


1. Dans le portail, accédez à votre machine virtuelle Linux, et dans la **Vue d’ensemble**, cliquez sur **Se connecter**. Vous recevrez une invite pour utiliser Bash, puis vous noterez votre SSH et l’adresse IP de votre machine virtuelle dans le message d’alerte. 
2. Ouvrez et connectez-vous à Bash.  
3. Dans le terminal, entrez votre **SSH** et la **machine virtuelle** que vous souhaitez connecter, par exemple, « ssh admin@12.61.219.35 ».  
4. Ensuite, vous serez invité à entrer le **Mot de passe** que vous avez ajouté à la création de la **machine virtuelle Linux**. Vous devez être connecté.  
5. Ensuite, vous pouvez envoyer une requête à l’aide de CURL pour obtenir le jeton d’autorisation pour la machine virtuelle Linux avec laquelle vous êtes connecté. Le point de terminaison **Azure Resource Manager** est https://management.azure.com.  

    Vous trouverez la requête CURL pour le jeton d’accès ci-dessous :
    
    ```bash
    curl --data "authority= https://login.microsoftonline.com/<TENANT ID>&&resource=https://management.azure.com/"  -H Metadata:true http://localhost:50432/oauth2/token   
    ```
    
    > [!NOTE]
    > Assurez-vous que l’URL de la ressource pour laquelle vous essayez d’envoyer une requête d’accès contient la mise en forme correcte avec une barre oblique à la fin, par exemple « https:<RESOURCE>/ ».
    
    ```powershell
    {"access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyIsImtpZCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsImlhdCI6MTUwNDEyNjYyNywibmJmIjoxNTA0MTI2NjI3LCJleHAiOjE1MDQxMzA1MjcsImFpbyI6IlkyRmdZTGg2dENWSzRkSDlGWGtuZzgyQ21ZNVdBZ0E9IiwiYXBwaWQiOiI2ZjJmNmU2OS04MGExLTQ3NmEtOGRjZi1mOTgzZDZkMjUxYjgiLCJhcHBpZGFjciI6IjIiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwib2lkIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwic3ViIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoib0U5T3JVZFJMMHVKSEw4UFdvOEJBQSIsInZlciI6IjEuMCJ9.J6KS7b9kFgDkegJ-Vfff19LMnu3Cfps4dL2uNGucb5M76rgDM5f73VO-19wZSRhQPxWmZLETzN3SljnIMQMkYWncp79MVdBud_xqXYyLdQpGkNinpKVJhTo1j1dY27U_Cjl4yvvpBTrtH3OX9gG0GtQs7PBFTTLznqcH3JR9f-bTSEN4wUhalaIPHPciVDtJI9I24_vvMfVqxkXOo6gkL0mEP"}
     ```
    
## <a name="the-curl-request-to-get-storage-keys-from-azure-resource-manager"></a>Requête CURL pour obtenir les clés de stockage à partir d’Azure Resource Manager  

> [!NOTE]
> Le texte de l’URL respecte la casse, assurez-vous d’utiliser les majuscules et les minuscules de manière appropriée pour vos groupes de ressources. En outre, il est important de savoir qu’il s’agit d’une demande POST et non d’une demande GET. Assurez-vous donc que vous transmettez une valeur pour capturer une limite de longueur avec -d qui peut être zéro.  

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 –request POST -d"" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

La réponse CURL vous donne la liste des clés :  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNtXcluBiFSV2Fs+JTj6c+VK5wWB4RWUVYw2Y5BkRQu21cRpAztt3S19bAowhGPqJrAfMr3gn89Ui9VPbtA=="},{"keyName":"key2","permissions":"Full","value":"U+uI0B1Iygw1bDK6Ic1VWKMMY77ac0VV3kqYaCW7FcUFiuw4UkLfEKbnF2sZpIfFR9zUWniLBhlt+eUn/TeSJg=="}]} 
```


Créez un fichier chargeable qui correspond à un exemple de fichier blob que vous pouvez charger avec vos clés de stockage sur votre compte de stockage dans le conteneur que vous avez créé. 

Ceci peut se faire sur une machine virtuelle Linux avec la commande suivante : 

```bash
echo "This is a test file." > test.txt
```
 Ensuite, chargez le fichier à l’aide d’Azure CLI et authentifiez-vous avec la clé de stockage.
 

```azurecli-interactive
 az storage blob upload --container-name 
                        --file 
                        --name 
                        [--account-name] 
                        [--account-key] 
```

Réponse : 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-12T03:58:56+00:00"
}
```

Vous pouvez également télécharger le fichier à l’aide d’Azure CLI et vous authentifier avec la clé de stockage. 

Demande : 

```azurecli-interactive
az storage blob download --container-name
                         --file 
                         --name 
                         [--account-name]
                         [--account-key]  
```

Réponse : 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "testblob",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 16,
    "contentRange": "bytes 0-15/16",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "Aryr///Rb+D8JQ8IytleDA==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D4F9929765C139\"",
    "lastModified": "2017-09-12T03:58:56+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="related-content"></a>Contenu connexe

Pour une vue d’ensemble de l’identité du service administré, consultez [Vue d’ensemble de l’identité du service administré](../active-directory/msi-overview.md).

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.


