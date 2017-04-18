---
title: "Créer un compte Batch dans le portail Azure | Microsoft Docs"
description: "Apprenez à créer un compte Azure Batch dans le portail Azure pour exécuter des charges de travail parallèles à grande échelle dans le cloud"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 11f8c3f37e56e0b5c566c4abdb60697c5279e72a
ms.lasthandoff: 04/06/2017


---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Créer un compte Batch avec le portail Azure

> [!div class="op_single_selector"]
> * [Portail Azure](batch-account-create-portal.md)
> * [Gestion de lots .NET](batch-management-dotnet.md)
> 
> 

Apprenez à créer un compte Azure Batch dans le [portail Azure][azure_portal] et choisissez les propriétés du compte qui correspondent à votre scénario de calcul. Découvrez où se trouvent les propriétés du compte importantes, telles que les clés d’accès et les URL du compte. 

Pour plus d’informations sur les comptes et les scénarios Batch, consultez la [présentation des fonctionnalités](batch-api-basics.md).



## <a name="create-a-batch-account"></a>Création d’un compte Batch

Utilisez le portail pour créer un compte Batch dans l’un des deux *modes d’allocation de pool* : le mode**Service Batch** ou le mode plus récent **Abonnement utilisateur**, qui requiert davantage de configuration. Pour plus d’informations sur ces deux modes, consultez la [présentation des fonctionnalités](batch-api-basics.md#account). Pour les fonctionnalités du mode Abonnement utilisateur, consultez également le [billet de blog](https://blogs.technet.microsoft.com/windowshpc/2017/03/17/azure-batch-vnet-and-custom-image-support-for-virtual-machine-pools/).

## <a name="batch-service-mode"></a>Mode service Batch



1. Connectez-vous au [portail Azure][azure_portal].
2. Cliquez sur **Nouveau** > **Calcul** > **Service Batch**.
   
    ![Batch dans Marketplace][marketplace_portal]
3. Le panneau **Nouveau compte Batch** s’affiche. Consultez les descriptions ci-dessous de chaque élément du panneau.
   
    ![Création d’un compte Batch][account_portal]
   
    a. **Nom du compte** : le nom du compte Batch que vous choisissez doit être unique dans la région Azure dans laquelle le compte est créé (voir **Emplacement** ci-dessous). Le nom de compte peut contenir uniquement des caractères minuscules ou des chiffres et doit comporter 3 à 24 caractères.
   
    b. **Abonnement** : l’abonnement dans lequel créer le compte Batch. Si vous n’avez qu’un seul abonnement, il est sélectionné par défaut.

    c. **Mode d’allocation de pool** : sélectionnez **Service Batch**.
   
    c. **Groupe de ressources** : sélectionnez un groupe de ressources pour votre nouveau compte Batch (vous pouvez également en créer un).
   
    d. **Emplacement** : la région Azure dans laquelle créer le compte Batch. Seules les régions prises en charge par votre abonnement et votre groupe de ressources sont affichées.
   
    e. **Compte de stockage** (facultatif) : compte de stockage Azure à usage général à associer à votre compte Batch. Cela est recommandé pour la plupart des comptes Batch. Pour plus d’informations, consultez la section [Compte Azure Storage lié](#linked-azure-storage-account) disponible plus loin dans cet article.

4. Cliquez sur **Créer** pour créer le compte.
   
   Le portail indique que le déploiement est en cours. Une fois l’opération terminée, la notification **Déploiements réussis** s’affiche dans **Notifications**.
   
## <a name="user-subscription-mode"></a>Mode Abonnement utilisateur

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Autoriser Azure Batch à accéder à l’abonnement (opération ponctuelle)
Lorsque vous créez votre premier compte Batch en mode Abonnement utilisateur, procédez comme suit pour inscrire votre abonnement auprès de Batch. (Si vous l’avez déjà fait, passez à la section suivante.)

1. Connectez-vous au [portail Azure][azure_portal].

2. Cliquez sur **Plus de services** > **Abonnements**, puis cliquez sur l’abonnement que vous souhaitez utiliser pour le compte Batch. 

3. Dans le panneau **Abonnement**, cliquez sur **Contrôle d’accès (IAM)** > **Ajouter**.

    ![Contrôle d’accès à l’abonnement][subscription_access]

4. Dans le panneau **Ajouter des autorisations**, sélectionnez le rôle **Collaborateur** et recherchez **MicrosoftAzureBatch** (sans espace). Sélectionnez **MicrosoftAzureBatch**, puis cliquez sur **Enregistrer**.

    ![Ajouter des autorisations Batch][add_permission]

### <a name="create-a-key-vault"></a>Création d’un coffre de clés
En mode Abonnement utilisateur, un coffre de clés Azure appartenant au même groupe de ressources que le compte Batch est requis. Assurez-vous que le groupe de ressources se trouve dans une région prise en charge par votre abonnement et dans laquelle Batch est [disponible](https://azure.microsoft.com/regions/services/).

1. Dans le [portail Azure][azure_portal], cliquez sur **Nouveau** > **Sécurité et identité** > **Key Vault**. 

2. Dans le panneau **Création d’un coffre de clés**, entrez un nom pour le coffre de clés et créez un groupe de ressources dans la région souhaitée pour votre compte Batch. Conservez les autres paramètres par défaut, puis cliquez sur **Créer**.

### <a name="create-a-batch-account"></a>Création d’un compte Batch

1. Dans le [portail Azure][azure_portal], cliquez sur **Nouveau** > **Calcul** > **Service Batch**.
   
    ![Batch dans Marketplace][marketplace_portal]
3. Le panneau **Nouveau compte Batch** s’affiche. Consultez les descriptions ci-dessous de chaque élément du panneau.
   
    ![Création d’un compte Batch][account_portal_byos]
   
    a. **Nom du compte** : le nom du compte Batch que vous choisissez doit être unique dans la région Azure dans laquelle le compte est créé (voir **Emplacement** ci-dessous). Le nom de compte peut contenir uniquement des caractères minuscules ou des chiffres et doit comporter 3 à 24 caractères.
   
    b. **Abonnement** : si vous avez plusieurs abonnements, sélectionnez celui que vous avez inscrit auprès du service Batch.

    c. **Mode d’allocation de pool** : sélectionnez **Abonnement utilisateur**.

    d. **Coffre de clés** : sélectionnez le coffre de clés que vous avez créé pour votre compte Batch dans la section précédente. Vous pouvez éventuellement créer un coffre de clés. Après avoir sélectionné le coffre, activez la case à cocher pour accorder l’accès Azure Batch au coffre de clés.
   
    c. **Groupe de ressources** : sélectionnez le groupe de ressources dans lequel vous avez créé le coffre de clés.
   
    d. **Emplacement** : région Azure dans laquelle vous avez créé le coffre de clés pour le compte Batch. 
   
    e. **Compte de stockage** (facultatif) : compte de stockage Azure à usage général à associer à votre compte Batch. Cela est recommandé pour la plupart des comptes Batch. Pour plus d’informations, consultez la section [Compte Azure Storage lié](#linked-azure-storage-account) ci-dessous.

4. Cliquez sur **Créer** pour créer le compte.
   
   Le portail indique que le déploiement est en cours. Une fois l’opération terminée, la notification **Déploiements réussis** s’affiche dans **Notifications**.



## <a name="view-batch-account-properties"></a>Afficher les propriétés du compte Batch
Une fois le compte créé, vous pouvez ouvrir le **Panneau du compte Batch** pour accéder à ses propriétés et paramètres. Vous avez accès à tous les paramètres et propriétés du compte dans le menu de gauche du panneau du compte Batch.

![Panneau de compte Batch dans le portail Azure][account_blade]

* **URL de compte Batch** : lorsque vous développez une application avec des [API Batch](batch-apis-tools.md#batch-development-apis), vous avez besoin d’une URL de compte pour accéder aux ressources de votre Batch. Une URL de compte Batch a le format suivant :
  
    `https://<account_name>.<region>.batch.azure.com`

![URL de compte Batch dans le portail][account_url]

* **Clés d’accès** (mode Service Batch) : pour authentifier l’accès à votre compte Batch à partir de votre application, vous avez besoin d’une clé d’accès. (Ce paramètre n’est pas disponible en mode Abonnement utilisateur, dans lequel vous utilisez l’authentification Azure Active Directory.)

    Pour afficher ou régénérer les clés d’accès de votre compte Batch, entrez `keys` dans la zone **Recherche** du menu de gauche dans le volet du compte Batch, puis sélectionnez **Clés**. 
  
    ![Clés de compte Batch dans le portail Azure][account_keys]

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="linked-azure-storage-account"></a>Compte Azure Storage lié

Vous pouvez éventuellement lier un compte de stockage à usage général à votre compte Batch. La fonctionnalité de [packages d’application](batch-application-packages.md) de Batch utilise le stockage Azure Blob Azure comme la bibliothèque [Batch File Conventions .NET](batch-task-output.md). Ces fonctionnalités facultatives vous aident à déployer les applications exécutées par vos tâches Batch et à conserver les données qu’elles produisent.

Nous vous recommandons de créer un compte de stockage dédié à votre compte Batch.

![Création d’un compte de stockage « à usage général »][storage_account]

> [!NOTE] 
> Azure Batch prend actuellement en charge uniquement le type de compte de stockage à usage général. Ce type de compte est décrit à l’étape 5, [Créez un compte de stockage] (../storage/storage-create-storage-account.md#create-a-storage-account), dans [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md).
>
>

> [!WARNING]
> Soyez prudent lorsque vous régénérez les clés d’accès d’un compte de stockage lié. Ne régénérez qu’une clé du compte de stockage et cliquez sur **Synchroniser les clés** dans le panneau du compte de stockage lié. Attendez cinq minutes que les clés se propagent aux nœuds de calcul de vos pools, puis régénérez et synchronisez l’autre clé si nécessaire. Si vous régénérez les deux clés en même temps, les nœuds de calcul ne pourront pas les synchroniser, car elles perdront l’accès au compte de stockage.
> 
> 

![Régénération de clés de compte de stockage][4]

## <a name="batch-service-quotas-and-limits"></a>Quotas et limites du service Batch
Sachez qu’à l’image de votre abonnement Azure et d’autres services Azure, certains [quotas et limites](batch-quota-limit.md) s’appliquent aux comptes Batch. Les quotas actuels d’un compte Batch sont indiqués dans les **propriétés**du compte sur le portail.

![Quotas de compte Batch dans le portail Azure][quotas]



De plus, il est possible de relever la plupart de ces quotas simplement en envoyant une demande de support produit gratuit dans le portail Azure. Pour plus d’informations sur les demandes d’augmentation de quotas, consultez [Quotas et limites pour le service Azure Batch](batch-quota-limit.md) .

## <a name="other-batch-account-management-options"></a>Autres options de gestion de comptes Batch
Vous pouvez créer et gérer des comptes Batch à l’aide du portail Azure, mais aussi avec les éléments suivants :

* [Applets de commande PowerShell pour Batch](batch-powershell-cmdlets-get-started.md)
* [Interface de ligne de commande Azure](batch-cli-get-started.md)
* [Gestion de lots .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur les concepts et fonctionnalités du service Batch, consultez [Présentation des fonctionnalités du service Batch pour les développeurs](batch-api-basics.md). L’article traite des principales ressources Batch, notamment des pools, des nœuds de calcul, des travaux et des tâches et fournit une vue d’ensemble des fonctionnalités du service qui permettent l’exécution de la charge de travail de calcul à grande échelle.
* Découvrez les bases du développement d’une application Batch à l’aide de la [bibliothèque Azure Batch pour .NET](batch-dotnet-get-started.md) ou [Python](batch-python-tutorial.md). Cette présentation vous décrit la création d’une application opérationnelle qui utilise le service Batch pour exécuter une charge de travail sur plusieurs nœuds de calcul, et inclut l’utilisation d’Azure Storage pour la mise en attente et la récupération de fichiers de la charge de travail.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "Régénération de clés de compte de stockage"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[account_portal_byos]: ./media/batch-account-create-portal/batch_acct_portal_byos.png
