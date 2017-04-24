---
title: "Prise en main de l’explorateur de stockage (version préliminaire) | Microsoft Docs"
description: "Gérer les ressources de stockage Azure avec l’Explorateur de stockage (version préliminaire)"
services: storage
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 07b62cd6f6deb0cf3ff1c806204ebc26c773a164
ms.lasthandoff: 04/13/2017


---
# <a name="getting-started-with-storage-explorer-preview"></a>Prise en main de l’explorateur de stockage (version préliminaire)
## <a name="overview"></a>Vue d’ensemble
L’explorateur de stockage Microsoft Azure (version préliminaire) est une application autonome qui vous permet d’utiliser facilement les données Stockage Azure sur Windows, macOS et Linux. Dans cet article, vous découvrez les différentes façons de vous connecter à vos comptes de stockage Azure et de les gérer.

![Explorateur de stockage Microsoft Azure (version préliminaire)][15]

## <a name="prerequisites"></a>Configuration requise
* [Télécharger et installer l’explorateur de stockage (version préliminaire)](http://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Connexion à un service ou un compte de stockage
L’explorateur de stockage (version préliminaire) offre plusieurs façons de se connecter à des comptes de stockage. Il vous permet notamment de vous connecter aux comptes de stockage associés à vos abonnements Azure, de vous connecter à des services et comptes de stockage partagés à partir d’autres abonnements Azure, et même de vous connecter au stockage local et de le gérer à l’aide de l’Émulateur de stockage Azure. En outre, vous pouvez utiliser des comptes de stockage Azure à l’échelle internationale et nationale :

* [Connexion à un abonnement Azure](#connect-to-an-azure-subscription) : gérez les ressources de stockage appartenant à votre abonnement Azure.
* [Utilisation du stockage de développement local](#work-with-local-development-storage) : gérez le stockage local à l’aide de l’émulateur de stockage Azure.
* [Attachement à un stockage externe](#attach-or-detach-an-external-storage-account) : gérez les ressources de stockage appartenant à un autre abonnement Azure ou figurant sous des clouds Azure nationaux à l’aide du nom, de la clé et des points de terminaison du compte de stockage.
* [Attachement d’un compte de stockage à l’aide d’une SAP](#attach-storage-account-using-sas) : gérez les ressources de stockage appartenant à un autre abonnement Azure à l’aide d’une SAP.
* [Attachement d’un service à l’aide d’une SAP](#attach-service-using-sas) : gérez un service de stockage spécifique (conteneur d’objets blob, file d’attente ou table) appartenant à un autre abonnement Azure à l’aide d’une SAP.

## <a name="connect-to-an-azure-subscription"></a>Connexion à un abonnement Azure
> [!NOTE]
> Si vous ne possédez pas de compte Azure, vous pouvez [vous inscrire à un essai gratuit](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [activer les avantages de votre abonnement Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
>
>

1. Dans l’Explorateur de stockage (version préliminaire), sélectionnez les **paramètres de compte Azure**.

    ![paramètres de compte Azure][0]
2. Le volet gauche affiche tous les comptes Microsoft auxquels vous vous êtes connecté. Pour vous connecter à un autre compte, sélectionnez **Ajouter un compte**et suivez les indications des boîtes de dialogue pour vous connecter avec un compte Microsoft associé à un ou plusieurs abonnements Azure actifs.
> [!NOTE]
>La connexion aux comptes de stockage Azure nationaux tels que Black Forest Azure, Fairfax Azure et Mooncake Azure par le biais d’une connexion n’est pas prise en charge pour l’instant. Pour plus d’informations sur la procédure de connexion aux comptes de stockage Azure nationaux, consultez la section **Attacher ou détacher un compte de stockage externe**.

3. Une fois que vous êtes connecté avec un compte Microsoft, le volet gauche indique les abonnements Azure associés à ce compte. Sélectionnez les abonnements Azure que vous souhaitez utiliser, puis sélectionnez **Appliquer**. (La case à cocher **Tous les abonnements** permet de sélectionner ou de désélectionner l’ensemble des abonnements Azure répertoriés.)

    ![Sélectionner les abonnements Azure][3]
4. Le volet gauche affiche les comptes de stockage associés aux abonnements Azure sélectionnés.

    ![Abonnements Azure sélectionnés][4]

## <a name="connect-to-an-azure-stack-subscription"></a>Connexion à un abonnement Azure Stack

1. Une connexion VPN est nécessaire pour que l’explorateur de stockage puisse accéder à distance à l’abonnement Azure Stack. Pour savoir comment configurer la connexion VPN à Azure Stack, reportez-vous à [Connect to Azure Stack](azure-stack/azure-stack-connect-azure-stack.md#connect-with-vpn) (Se connecter à Azure Stack avec un VPN).

2. Pour Azure Stack POC, vous devez exporter le certificat racine d’autorité Azure Stack. Ouvrez `mmc.exe` sur MAS-CON01, la machine hôte Azure Stack ou la machine locale avec la connexion VPN à Azure Stack. Dans **Fichier**, sélectionnez **Ajouter/Supprimer un composant logiciel enfichable**, ajoutez **Certificats** pour gérer le **compte d’ordinateur** d’un **ordinateur local**.

   ![Charger le certificat racine Azure Stack via mmc.exe][25]   

   Recherchez **AzureStackCertificationAuthority** sous **Racine de la console\Certificats (ordinateur local)\Autorités de certification racines de confiance\Certificats**. Cliquez avec le bouton droit sur l’élément, puis sélectionnez **All Tasks (Toutes les tâches) -> Exporter**. Suivez ensuite les boîtes de dialogue pour exporter le certificat avec **X.509 encodé en base 64 (*.cer)**. Le certificat exporté sera utilisé à l’étape suivante.   

   ![Exporter le certificat racine d’autorité Azure Stack][26]   

3. Dans l’Explorateur de stockage (version préliminaire), sélectionnez le menu **Modifier**, puis **Certificats SSL**, puis **Importer les certificats**. Utilisez la boîte de dialogue du sélecteur de fichier pour rechercher et ouvrir le certificat que vous avez découvert à l’étape précédente. Vous serez invité à redémarrer l’explorateur de stockage suite à l’importation.

   ![Importer le certificat dans l’explorateur de stockage (version préliminaire)][27]

4. Après le redémarrage de l’explorateur de stockage (version préliminaire), sélectionnez le menu **Modifier** et vérifiez que l’option **Target Azure Stack** (Cibler Azure Stack). Dans le cas contraire, cochez l’option et redémarrez l’explorateur de stockage pour appliquer la modification. Cette configuration est requise pour la compatibilité avec votre environnement Azure Stack.

   ![S’assurer que l’option Target Azure Stack (Cibler Azure Stack) est sélectionnée][28]

5. Dans la barre latérale gauche, sélectionnez **Gérer les comptes**. Le volet de gauche affiche tous les comptes Microsoft auxquels vous êtes connecté. Pour vous connecter au compte Azure Stack, sélectionnez **Ajouter un compte**.

   ![Ajouter un compte Azure Stack][29]

6. Choisissez **Create Custom Environment** (Créer un environnement personnalisé) sous **Environnement Azure** dans la boîte de dialogue **Ajouter un compte**, puis cliquez sur **Suivant**.

7. Entrez toutes les informations requises de l’environnement personnalisé Azure Stack, puis cliquez sur **Se connecter**.  Renseignez la boîte de dialogue **Sign in to a Custom Cloud environment** (Se connecter à un environnement cloud personnalisé) pour vous connecter au compte Azure Stack associé à un abonnement Azure Stack actif minimum. Les détails de chaque champ de la boîte de dialogue sont les suivants :

    * **Nom de l’environnement** : le champ peut être personnalisé par l’utilisateur.
    * **Autorité** : la valeur doit être https://login.windows.net. Pour Azure en Chine (Mooncake), utilisez https://login.chinacloudapi.cn.
    * **Sign in resource id (ID de ressource de connexion)** : récupérez la valeur en exécutant l’instance PowerShell suivante :

    Si vous êtes administrateur de cloud :

    ```powershell
    PowerShell (Invoke-RestMethod -Uri https://adminmanagement.local.azurestack.external/metadata/endpoints?api-version=1.0 -Method Get).authentication.audiences[0]
    ```

    Si vous êtes client :

    ```powershell
    PowerShell (Invoke-RestMethod -Uri https://management.local.azurestack.external/metadata/endpoints?api-version=1.0 -Method Get).authentication.audiences[0]
    ```

    * **Point de terminaison Graph** : la valeur doit être https://graph.windows.net. Pour Azure en Chine (Mooncake), utilisez https://graph.chinacloudapi.cn.
    * **ID de ressource ARM** : utilisez la même valeur que l’ID de ressource de connexion.
    * **ARM resource endpoint (Point de terminaison de ressource ARM)** : les exemples de point de terminaison de ressource ARM :

    Pour l’administrateur de cloud : https://adminmanagement.local.azurestack.external   
    Pour le client : https://management.local.azurestack.external
 
    * **ID de locataire** : facultatif. La valeur est fournie uniquement lorsque le répertoire doit être spécifié.

8. Une fois que vous êtes connecté avec un compte Azure Stack, le volet de gauche indique les abonnements Azure Stack associés à ce compte. Sélectionnez les abonnements Azure Stack que vous souhaitez utiliser, puis sélectionnez **Appliquer**. (La case à cocher **Tous les abonnements** permet de sélectionner ou de désélectionner l’ensemble des abonnements Azure Stack répertoriés.)

   ![Sélectionner les abonnements Azure Stack après avoir renseigné la boîte de dialogue Custom Cloud Environment (Environnement cloud personnalisé)][30]

9. Le volet de gauche affiche les comptes de stockage associés aux abonnements Azure Stack sélectionnés.

   ![Liste des comptes de stockage, y compris les comptes d’abonnement Azure Stack][31]

## <a name="work-with-local-development-storage"></a>Utilisation du stockage de développement local
L’explorateur de stockage (version préliminaire) vous permet de travailler sur le stockage local à l’aide de l’émulateur de stockage Azure. Vous pouvez ainsi écrire du code pour le stockage et le tester sans nécessairement disposer d’un compte de stockage déployé sur Azure (étant donné que le compte de stockage est émulé par l’émulateur de stockage Azure).

> [!NOTE]
> Actuellement, l’émulateur de stockage Azure est pris en charge uniquement pour Windows.
>
>

1. Dans le volet gauche de l’Explorateur de stockage (version préliminaire), développez le nœud **(Local et attaché** > **Comptes de stockage** > **(Développement)**.

    ![Nœud de développement local][21]
2. Si vous n’avez pas encore installé l’émulateur de stockage Azure, vous êtes invité à le faire par le biais d’une barre d’informations. Si la barre d’informations s’affiche, sélectionnez **Télécharger la dernière version**et installez l’émulateur.

    ![Invite de téléchargement de l’émulateur de stockage Azure][22]
3. Une fois que l’émulateur est installé, vous pouvez créer et utiliser des tables, des files d’attente et des objets blob locaux. Pour apprendre à utiliser chaque type de compte de stockage, sélectionnez l’un des liens suivants :

   * [Manage Azure blob storage resources (Gérer les ressources Azure Blob Storage)](vs-azure-tools-storage-explorer-blobs.md)
   * Manage Azure file share storage resources (Gérer les ressources de stockage de partage de fichiers Azure) - *Bientôt disponible*
   * Manage Azure queue storage resources (Gérer les ressources Azure Queue Storage) - *Bientôt disponible*
   * Manage Azure table storage resources (Gérer les ressources Azure Table Storage) - *Bientôt disponible*

## <a name="attach-or-detach-an-external-storage-account"></a>Attacher ou détacher un compte de stockage externe
L’explorateur de stockage (version préliminaire) permet l’attachement aux comptes de stockage externes, pour un partage simplifié des comptes de stockage. Cette section explique la procédure d’attachement à des comptes de stockage externes (et la procédure de détachement).

### <a name="get-the-storage-account-credentials"></a>Obtention des informations d’identification du compte de stockage
Pour partager un compte de stockage externe, le propriétaire du compte doit d’abord obtenir les informations d’identification du compte (nom et clé du compte), puis les partager avec les personnes souhaitant effectuer un attachement à ce compte (externe). Vous pouvez obtenir les informations d’identification du compte de stockage par le biais du Portail Azure en suivant ces étapes :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Parcourir**.
3. Sélectionnez **Comptes de stockage**.
4. Dans le panneau **Comptes de stockage** , sélectionnez le compte de stockage souhaité.
5. Dans le panneau **Paramètres** du compte de stockage sélectionné, sélectionnez **Clés d’accès**.

   ![Option Clés d’accès][5]
6. Dans le panneau **Clés d’accès**, copiez les valeurs **NOM DU COMPTE DE STOCKAGE** et **CLÉ 1** à utiliser pour l’attachement au compte de stockage.

   ![Clés d’accès][6]

### <a name="attach-to-an-external-storage-account"></a>Attachement à un compte de stockage externe
Pour attacher à un compte de stockage externe, vous avez besoin du nom et de la clé du compte. La section *Obtention des informations d’identification du compte de stockage* explique comment obtenir ces valeurs à partir du portail Azure. Toutefois, notez que dans le portail, la clé du compte est appelée « clé 1», donc lorsque l’Explorateur de stockage (version préliminaire) demande une clé de compte, vous devrez entrer (ou coller) la valeur « clé 1 ».

1. Dans l’Explorateur de stockage (version préliminaire), sélectionnez **Se connecter à Azure Storage**.

   ![Option Se connecter à Azure Storage][23]
2. Dans la boîte de dialogue **Se connecter à Azure Storage**, spécifiez la clé de compte (valeur « clé 1 » du portail Azure), puis sélectionnez **Suivant**.
> [!NOTE]
> Vous pouvez entrer une chaîne de connexion de stockage appartenant à un compte de stockage Azure national. Par exemple, pour vous connecter aux comptes de stockage Azure Black Forest, entrez des chaînes de connexion semblables à ce qui suit : DefaultEndpointsProtocol=https;AccountName=cawatest03;AccountKey=<storage_account_key>;EndpointSuffix=core.cloudapi.de; Vous pouvez obtenir la chaîne de connexion par le biais du Portail Azure, comme décrit à la section **Obtention des informations d’identification du compte de stockage**.

   ![Boîte de dialogue Se connecter à Azure Storage][24]

3. Dans la boîte de dialogue **Attacher un stockage externe**, entrez le nom du compte de stockage dans la zone **Nom du compte**, spécifiez tout autre paramètre souhaité, puis sélectionnez **Suivant** lorsque vous avez terminé.

   ![Boîte de dialogue Attacher un stockage externe][8]
4. Vérifiez les informations de la boîte de dialogue **Résumé de la connexion** . Si vous souhaitez modifier quoi que ce soit, sélectionnez **Précédent** et saisissez de nouveau les paramètres souhaités. Une fois que vous avez terminé, sélectionnez **Se connecter**.
5. Une fois connecté, le compte de stockage externe s’affiche avec le texte **(Externe)** ajouté au nom.

   ![Résultat de la connexion à un compte de stockage externe][9]

### <a name="detach-from-an-external-storage-account"></a>Détachement d’un compte de stockage externe
1. Cliquez avec le bouton droit sur le compte de stockage externe que vous souhaitez détacher puis, dans le menu contextuel, sélectionnez **Détacher**.

   ![Option Détacher d’un compte de stockage][10]
2. Lorsque le message de confirmation s’affiche, sélectionnez **Oui** pour confirmer le détachement du compte de stockage externe.

## <a name="attach-storage-account-using-sas"></a>Attachement d’un compte de stockage à l’aide d’une SAP
Une [SAP (signature d’accès partagé)](storage/storage-dotnet-shared-access-signature-part-1.md) permet à l’administrateur d’un abonnement Azure d’accorder un accès temporaire à un compte de stockage sans avoir à fournir les informations d’identification de l’abonnement Azure.

Pour illustrer cela, supposons que l’utilisateur A est l’administrateur d’un abonnement Azure et qu’il souhaite autoriser l’utilisateur B à accéder à un compte de stockage pour une durée limitée, avec certaines autorisations :

1. L’utilisateur A génère une SAP (composée de la chaîne de connexion du compte de stockage) pour une période spécifique, avec les autorisations souhaitées.
2. L’utilisateur A partage la SAP avec la personne souhaitant accéder au compte de stockage, en l’occurrence l’utilisateur B.  
3. L’utilisateur B utilise l’explorateur de stockage (version préliminaire) pour effectuer l’attachement au compte appartenant à l’utilisateur A à l’aide de la SAP fournie.

### <a name="get-a-sas-for-the-account-you-want-to-share"></a>Obtention d’une SAP pour le compte à partager
1. Dans l’Explorateur de stockage (version préliminaire), cliquez avec le bouton droit sur le compte de stockage que vous souhaitez partager puis, dans le menu contextuel, sélectionnez **Obtenir une signature d’accès partagé**.

   ![Option de menu contextuel Obtenir une signature d’accès partagé][13]
2. Dans la boîte de dialogue **Signature d’accès partagé**, spécifiez la période et les autorisations souhaitées pour le compte, puis sélectionnez **Créer**.

    ![Boîte de dialogue Obtenir une signature d’accès partagé][14]
3. Une seconde boîte de dialogue **Signature d’accès partagé** affiche la SAP. Sélectionnez **Copier** en regard de la **Chaîne de connexion** pour la copier dans le Presse-papiers. Sélectionnez **Fermer** pour fermer la boîte de dialogue.

### <a name="attach-to-the-shared-account-using-the-sas"></a>Attachement au compte partagé à l’aide de la SAP
1. Dans l’Explorateur de stockage (version préliminaire), sélectionnez **Se connecter à Azure Storage**.

   ![Option Se connecter à Azure Storage][23]
2. Dans la boîte de dialogue **Se connecter à Azure Storage**, spécifiez la chaîne de connexion, puis sélectionnez **Suivant**.

   ![Boîte de dialogue Se connecter à Azure Storage][24]
3. Vérifiez les informations de la boîte de dialogue **Résumé de la connexion** . Si vous souhaitez modifier quoi que ce soit, sélectionnez **Précédent** et saisissez de nouveau les paramètres souhaités. Une fois que vous avez terminé, sélectionnez **Se connecter**.
4. Une fois attaché, le compte de stockage s’affiche avec le texte (SAP) ajouté au nom que vous avez fourni.

   ![Résultat de l’attachement à un compte à l’aide d’une SAP][17]

## <a name="attach-service-using-sas"></a>Attachement d’un service à l’aide d’une SAP
La section [Attachement d’un compte de stockage à l’aide d’une SAP](#attach-storage-account-using-sas) illustre la façon dont l’administrateur d’un abonnement Azure peut accorder un accès temporaire à un compte de stockage en générant (et en partageant) une SAP pour le compte de stockage. De la même façon, une SAP peut être générée pour un service spécifique (conteneur d’objets blob, file d’attente ou table) dans un compte de stockage.  

### <a name="generate-a-sas-for-the-service-you-want-to-share"></a>Génération d’une SAP pour le service à partager
Dans ce contexte, un service peut être un conteneur d’objets blob, une file d’attente ou une table. Les sections suivantes expliquent comment générer la SAP pour le service répertorié :

* [Obtenir la signature d’accès partagé pour un conteneur d’objets blob](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
* Get the SAS for a file share (Obtenir la signature d’accès partagé pour un partage de fichiers) - *Bientôt disponible*
* Get the SAS for a queue (Obtenir la signature d’accès partagé pour une file d’attente) - *Bientôt disponible*
* Get the SAS for a table (Obtenir la signature d’accès partagé pour une table) - *Bientôt disponible*

### <a name="attach-to-the-shared-account-service-using-the-sas"></a>Attachement au service de compte partagé à l’aide de la SAP
1. Dans l’Explorateur de stockage (version préliminaire), sélectionnez **Se connecter à Azure Storage**.

   ![Option Se connecter à Azure Storage][23]
2. Dans la boîte de dialogue **Se connecter à Azure Storage**, spécifiez l’URI de signature d’accès partagé, puis sélectionnez **Suivant**.

   ![Boîte de dialogue Se connecter à Azure Storage][24]
3. Vérifiez les informations de la boîte de dialogue **Résumé de la connexion** . Si vous souhaitez modifier quoi que ce soit, sélectionnez **Précédent** et saisissez de nouveau les paramètres souhaités. Une fois que vous avez terminé, sélectionnez **Se connecter**.
4. Une fois attaché, le nouveau service s’affiche sous le nœud **(Service SAS)** (SAP de service).

   ![Résultat de l’attachement à un service partagé à l’aide d’une SAP][20]

## <a name="search-for-storage-accounts"></a>Recherche de comptes de stockage
Si vous avez une longue liste de comptes de stockage, la zone de recherche située en haut du volet gauche offre un moyen rapide de rechercher un compte spécifique.

Lorsque vous tapez dans la zone de recherche, le volet gauche affiche uniquement les comptes de stockage qui correspondent à la valeur en cours de saisie. La capture d’écran suivante montre un exemple dans lequel je recherche tous les comptes de stockage dont le nom contient le texte « tarcher ».

![Recherche de compte de stockage][11]

Pour effacer la recherche, sélectionnez le bouton **x** dans la zone de recherche.

## <a name="next-steps"></a>Étapes suivantes
* [Gérer les ressources Azure Blob Storage avec l’explorateur de stockage (version préliminaire)](vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-icon.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-account-link.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/subscriptions-list.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-accounts-list.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/mase.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-icon.png
[24]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-next.png
[25]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-certificate-azure-stack.png
[26]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/export-root-cert-azure-stack.png
[27]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/import-azure-stack-cert-storage-explorer.png
[28]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/select-target-azure-stack.png
[29]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-azure-stack-account.png
[30]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/select-accounts-azure-stack.png
[31]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/azure-stack-storage-account-list.png

