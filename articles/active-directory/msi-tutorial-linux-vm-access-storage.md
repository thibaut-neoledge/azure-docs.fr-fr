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
ms.date: 09/19/2017
ms.author: elkuzmen
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 8e4b3466143dc8b19df399913baca864b0af9bc0
ms.contentlocale: fr-fr
ms.lasthandoff: 09/20/2017

---


# <a name="use-a-linux-vm-managed-service-identity-to-access-azure-storage"></a>Utiliser l’identité du service administré d’une machine virtuelle Linux pour accéder au stockage Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Ce didacticiel vous montre comment activer des identités du service administré (MSI) pour une machine virtuelle Linux et ensuite utiliser cette identité pour accéder aux clés de stockage. Vous pouvez utiliser les clés de stockage comme d’habitude lors de l’exécution d’opérations de stockage, par exemple lors de l’utilisation du stockage de Kits de développement logiciel (SDK). Pour ce didacticiel, nous chargeons et téléchargeons des objets blob à l’aide de Azure CLI. Vous apprendrez à :


> [!div class="checklist"]
> * Activer l’identité du service administré sur une machine virtuelle Linux 
> * Accorder l’accès à votre machine virtuelle à des clés de stockage dans le Gestionnaire des ressources
> * Obtenir un jeton d’accès à l’aide de l’identité de la machine virtuelle et l’utiliser pour récupérer des clés de stockage à partir du Gestionnaire des ressources 


Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="sign-in-to-azure"></a>Connexion à Azure
Connectez-vous au portail Azure depuis l’adresse [https://portal.azure.com](https://portal.azure.com).


## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Créer une machine virtuelle Linux dans un nouveau groupe de ressources

Pour ce didacticiel, nous créons une machine virtuelle Linux. Vous pouvez également activer l’identité du service administré sur une machine virtuelle existante.

1. Cliquez sur le bouton **Nouveau** dans le coin supérieur gauche du portail Azure.
2. Sélectionnez **Compute**, puis sélectionnez **Ubuntu Server 16.04 LTS**.
3. Saisissez les informations de la machine virtuelle. Dans **Type d’authentification**, sélectionnez **Clé publique SSH** ou **Mot de passe**. Les informations d’identification créées vous permettent de vous connecter à la machine virtuelle.

    ![Texte de remplacement d’image](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Choisissez un **Abonnement** pour la machine virtuelle dans la liste déroulante.
5. Pour sélectionner un nouveau **Groupe de ressources** dans lequel vous souhaitez créer la machine virtuelle, choisissez **Créer nouveau**. Lorsque vous avez terminé, cliquez sur **OK**.
6. Choisissez la taille de la machine virtuelle. Pour voir plus de tailles, sélectionnez **Afficher tout** ou modifiez le filtre de type de disque pris en charge. Conservez les valeurs par défaut dans le panneau des paramètres et cliquez sur **OK**.

## <a name="enable-msi-on-your-vm"></a>Activer l’identité du service administré sur votre machine virtuelle

L’identité du service administré d’une machine virtuelle permet d’obtenir des jetons d’accès d’Azure AD sans avoir à placer des informations d’identification dans votre code. En arrière-plan, l’activation de l’identité du service administré entraîne deux actions : elle installe l’extension de l’identité du service administré de la machine virtuelle et active l’identité du service administré pour la machine virtuelle.  

1. Accédez au groupe de ressources de votre nouvelle machine virtuelle et sélectionnez la machine virtuelle que vous avez créée à l’étape précédente.
2. Dans les paramètres de la machine virtuelle sur la gauche, cliquez sur **Configuration**.
3. Pour enregistrer et activer l’identité du service administré, sélectionnez **Oui**. Si vous souhaitez la désactiver, sélectionnez Non.
4. Assurez-vous d’avoir cliqué sur **Enregistrer** pour enregistrer la configuration.

    ![Texte de remplacement d’image](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Si vous souhaitez vérifier les extensions présentes sur la machine virtuelle, cliquez sur **Extensions**. Si l’identité du service administré est activée, **ManagedIdentityExtensionforLinux** apparaît dans la liste.

    ![Texte de remplacement d’image](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>Créez un compte de stockage. 

Si vous n’en avez pas déjà un, vous allez maintenant créer un compte de stockage.  Vous pouvez également ignorer cette étape et accorder l’accès MSI de votre machine virtuelle aux clés d’un compte de stockage existant. 

1. Cliquez sur le bouton **Nouveau** dans le coin supérieur gauche du portail Azure.
2. Cliquez sur **Stockage**, puis **Compte de stockage**, et un nouveau panneau « Créer un compte de stockage » s’affiche.
3. Saisissez un **nom** pour le compte de stockage, vous l’utiliserez ultérieurement.  
4. **Modèle de déploiement** et **Type de compte** doivent être respectivement définis sur « Gestionnaire de ressources » et « Usage général ». 
5. Assurez-vous que les champs **Abonnement** et **Groupe de ressources** correspondent à ceux que vous avez spécifiés lorsque vous avez créé votre machine virtuelle à l’étape précédente.
6. Cliquez sur **Create**.

    ![Créer un nouveau compte de stockage](media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Création d’un conteneur d’objets blob dans le compte de stockage

Plus tard, nous chargerons et téléchargerons un fichier vers le nouveau compte de stockage. Étant donné que les fichiers nécessitent un stockage d’objets blob, nous devons créer un conteneur d’objets blob dans lequel stocker le fichier.

1. Revenez à votre compte de stockage nouvellement créé.
2. Cliquez sur le lien **Conteneurs** dans la barre de navigation de gauche, sous « Service BLOB ».
3. Cliquez sur **+ Conteneur** en haut de la page et un panneau « Nouveau conteneur » apparait.
4. Nommez le conteneur, sélectionnez un niveau d’accès, puis cliquez sur **OK**. Le nom spécifié sera utilisé plus loin dans le didacticiel. 

    ![Créer un conteneur de stockage](media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-identity-access-to-use-storage-keys"></a>Accorder l’accès pour utiliser les clés de stockage à l’identité de votre machine virtuelle 

Le stockage Azure ne prend pas en charge l’authentification Azure AD en mode natif.  Toutefois, vous pouvez utiliser un MSI pour récupérer les clés de stockage du Gestionnaire des ressources et utiliser ces clés pour accéder au stockage.  À cette étape, vous accordez l’accès MSI de votre machine virtuelle aux clés pour votre compte de stockage.   

1. Accédez à l’onglet **Stockage**.  
2. Sélectionnez le **Compte de stockage** spécifique que vous avez préalablement créé.   
3. Accédez au **Contrôle d’accès (IAM)** dans le volet de gauche.  
4. Puis, choisissez d’**Ajouter** une nouvelle attribution de rôle pour votre machine virtuelle et sélectionnez **Rôle** en tant que **Rôle de service opérateur de clé de compte de stockage**.  
5. Dans la liste déroulante suivante, sélectionnez **Attribuer un accès** à la ressource **Machine virtuelle**.  
6. Ensuite, vérifiez que l’abonnement approprié est répertorié dans la liste déroulante **Abonnement**. Et pour **Groupe de ressources**, sélectionnez **Tous les groupes de ressources**.  
7. Enfin, dans **Sélectionner**, choisissez votre machine virtuelle Linux dans la liste déroulante et cliquez sur **Enregistrer**. 
    ![Texte de remplacement d’image](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Obtenir un jeton d’accès à l’aide de l’identité de la machine virtuelle et l’utiliser pour appeler Azure Resource Manager

Pour effectuer cette procédure, vous avez besoin d'un client SSH. Si vous utilisez Windows, vous pouvez utiliser le client SSH dans le [Sous-système Windows pour Linux](https://msdn.microsoft.com/commandline/wsl/install_guide).

1. Dans le portail, accédez à votre machine virtuelle Linux, et dans la **Vue d’ensemble**, cliquez sur **Se connecter**. Vous recevrez une invite pour utiliser Bash, puis vous noterez votre SSH et l’adresse IP de votre machine virtuelle dans le message d’alerte. 
2. Ouvrez et connectez-vous à Bash.  
3. Dans le terminal, entrez votre **SSH** et la **machine virtuelle** que vous souhaitez connecter, par exemple, « ssh admin@12.61.219.35 ».  
4. Ensuite, vous serez invité à entrer le **Mot de passe** que vous avez ajouté à la création de la **machine virtuelle Linux**. Vous devez être connecté.  
5. Utilisez CURL pour obtenir un jeton d’accès à partir de Azure Resource Manager.  

    Vous trouverez la requête CURL pour le jeton d’accès ci-dessous :
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true    
    ```
    
    > [!NOTE]
    > La valeur du paramètre « ressource » doit correspondre exactement à ce qui est attendu par Azure AD. Lorsque vous utilisez l’ID de ressource Azure Resource Manager, vous devez inclure la barre oblique de fin à l’URI.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyIsImtpZCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsImlhdCI6MTUwNDEyNjYyNywibmJmIjoxNTA0MTI2NjI3LCJleHAiOjE1MDQxMzA1MjcsImFpbyI6IlkyRmdZTGg2dENWSzRkSDlGWGtuZzgyQ21ZNVdBZ0E9IiwiYXBwaWQiOiI2ZjJmNmU2OS04MGExLTQ3NmEtOGRjZi1mOTgzZDZkMjUxYjgiLCJhcHBpZGFjciI6IjIiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwib2lkIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwic3ViIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoib0U5T3JVZFJMMHVKSEw4UFdvOEJBQSIsInZlciI6IjEuMCJ9.J6KS7b9kFgDkegJ-Vfff19LMnu3Cfps4dL2uNGucb5M76rgDM5f73VO-19wZSRhQPxWmZLETzN3SljnIMQMkYWncp79MVdBud_xqXYyLdQpGkNinpKVJhTo1j1dY27U_Cjl4yvvpBTrtH3OX9gG0GtQs7PBFTTLznqcH3JR9f-bTSEN4wUhalaIPHPciVDtJI9I24_vvMfVqxkXOo6gkL0mEP"}
     ```
    
## <a name="use-curl-to-get-storage-keys-from-azure-resource-manager"></a>Utiliser CURL pour obtenir les clés de stockage à partir de Azure Resource Manager  

Maintenant, nous allons utiliser CURL pour effectuer un appel auprès du Gestionnaire des ressources à l’aide du jeton d’accès récupéré dans la section précédente, afin de récupérer la clé d’accès de stockage. Une fois la clé d’accès de stockage récupérée, nous pouvons appeler des opérations de chargement/téléchargement de stockage.

> [!NOTE]
> Le texte de l’URL respecte la casse, assurez-vous d’utiliser les majuscules et les minuscules de manière appropriée pour vos groupes de ressources. En outre, il est important de savoir qu’il s’agit d’une demande POST et non d’une demande GET. Assurez-vous donc que vous transmettez une valeur pour capturer une limite de longueur avec -d qui peut être zéro.  

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 –-request POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

La réponse CURL vous donne la liste des clés :  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNtXcluBiFSV2Fs+JTj6c+VK5wWB4RWUVYw2Y5BkRQu21cRpAztt3S19bAowhGPqJrAfMr3gn89Ui9VPbtA=="},{"keyName":"key2","permissions":"Full","value":"U+uI0B1Iygw1bDK6Ic1VWKMMY77ac0VV3kqYaCW7FcUFiuw4UkLfEKbnF2sZpIfFR9zUWniLBhlt+eUn/TeSJg=="}]} 
```


Créez un exemple de fichier blob à charger dans votre conteneur de stockage d’objets blob. Ceci peut se faire sur une machine virtuelle Linux avec la commande suivante : 

```bash
echo "This is a test file." > test.txt
```

Ensuite, chargez le fichier à l’aide de Azure CLI et authentifiez-vous avec la clé de stockage.
 

```azurecli-interactive
az storage blob upload -c <CONTAINER NAME> -n test.txt -f test.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
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
az storage blob download -c <CONTAINER NAME> -n test.txt -f test-download.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Réponse : 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "test.txt",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 21,
    "contentRange": "bytes 0-20/21",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "LSghAvpnElYyfUdn7CO8aw==",
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
    "etag": "\"0x8D5067F30D0C283\"",
    "lastModified": "2017-09-28T14:42:49+00:00",
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


