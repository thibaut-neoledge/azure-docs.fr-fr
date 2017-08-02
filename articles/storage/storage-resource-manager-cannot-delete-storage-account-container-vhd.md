---
title: "Résoudre les erreurs lorsque vous supprimez des comptes de stockage Azure, des conteneurs ou des disques durs virtuels | Microsoft Docs"
description: "Résoudre les erreurs lorsque vous supprimez des comptes de stockage Azure, des conteneurs ou des disques durs virtuels"
services: storage
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: storage
ms.assetid: 17403aa1-fe8d-45ec-bc33-2c0b61126286
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: c3ea7cfba9fbf1064e2bd58344a7a00dc81eb148
ms.openlocfilehash: 318d7146ea53a806baf813ff7de2fe91f18becc8
ms.contentlocale: fr-fr
ms.lasthandoff: 07/19/2017

---
# <a name="troubleshoot-errors-when-you-delete-azure-storage-accounts-containers-or-vhds"></a>Résoudre les erreurs lorsque vous supprimez des comptes de stockage Azure, des conteneurs ou des disques durs virtuels

Il est possible que vous receviez des erreurs quand vous essayez de supprimer un compte de stockage Azure, un conteneur ou un disque dur virtuel dans le [portail Azure](https://portal.azure.com). Cet article fournit des conseils de dépannage pour vous aider à résoudre le problème dans un déploiement Azure Resource Manager.

Si cet article ne traite pas de votre problème avec Azure, parcourez les forums Azure sur [MSDN et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez publier votre problème sur ces forums ou sur @AzureSupport sur Twitter. Vous pouvez également créer une demande de support Azure en sélectionnant **Obtenir de l’aide** sur le site du [support Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptoms"></a>Symptômes
### <a name="scenario-1"></a>Scénario 1
Lorsque vous essayez de supprimer un disque dur virtuel d’un compte de stockage dans un déploiement Resource Manager, le message d’erreur suivant s’affiche :

**Échec de la suppression de l’objet blob « vhds/BlobName.vhd » Erreur : Il existe actuellement un bail sur l’objet blob et aucun ID de bail n’a été spécifié dans la demande.**

Ce problème peut survenir lorsqu’une machine virtuelle dispose d’un bail sur le disque dur virtuel que vous voulez supprimer.

### <a name="scenario-2"></a>Scénario 2
Lorsque vous essayez de supprimer un conteneur d’un compte de stockage dans un déploiement Resource Manager, le message d’erreur suivant s’affiche :

**Échec de la suppression du conteneur de stockage « vhds ». Erreur : Il existe actuellement un bail sur le conteneur et aucun ID de bail n’a été spécifié dans la demande.**

Ce problème peut se produire lorsque le conteneur possède un disque dur virtuel qui est verrouillé à l’état de bail.

### <a name="scenario-3"></a>Scénario 3
Lorsque vous essayez de supprimer un compte de stockage dans un déploiement Resource Manager, le message d’erreur suivant s’affiche :

**Échec de la suppression du compte de stockage « StorageAccountName ». Erreur : Le compte de stockage ne peut pas être supprimé en raison de ses artefacts en cours d’utilisation.**

Ce problème peut se produire lorsque le compte de stockage contient un disque dur virtuel qui est à l’état du bail.

## <a name="solution"></a>Solution 
Pour résoudre ces problèmes, vous devez identifier le disque dur virtuel qui a provoqué l’erreur et la machine virtuelle associée. Ensuite, détachez le disque dur virtuel de la machine virtuelle (pour les disques de données) ou supprimez la machine virtuelle qui utilise le disque dur virtuel (pour les disques de système d’exploitation). Cela supprime le bail du disque dur virtuel et permet la suppression de ce dernier. 

Pour ce faire, utilisez l’une des méthodes suivantes :

### <a name="method-1---use-azure-storage-explorer"></a>Méthode 1 - Utiliser l’Explorateur de stockage Azure

### <a name="step-1-identify-the-vhd-that-prevent-deletion-of-the-storage-account"></a>Étape 1 : Identifier le disque dur virtuel qui empêche la suppression du compte de stockage

1. Lorsque vous supprimez le compte de stockage, une boîte de dialogue contenant le type de message suivant s’affiche : 

    ![message lors de la suppression du compte de stockage](media/storage-resource-manager-cannot-delete-storage-account-container-vhd/delete-storage-error.png) 

2. Vérifiez **l’URL du disque** pour identifier le compte de stockage et le disque dur virtuel qui vous empêchent de supprimer le compte de stockage. Dans l’exemple suivant, la chaîne précédant « .blob.core.windows.net » correspond au nom du compte de stockage et « SCCM2012-2015-08-28.vhd » est le nom du disque dur virtuel.  

        https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd

### <a name="step-2-delete-the-vhd-by-using-azure-storage-explorer"></a>Étape 2 : Supprimer le disque dur virtuel à l’aide de l’Explorateur de stockage Azure

1. Téléchargez et installez la version la plus récente de [l’Explorateur de stockage Azure](http://storageexplorer.com/). Cet outil est une application autonome de Microsoft qui vous permet d’utiliser facilement des données du stockage Azure sur Windows, macOS et Linux.
2. Ouvrez l’Explorateur de stockage Azure, sélectionnez ![l’icône de compte](media/storage-resource-manager-cannot-delete-storage-account-container-vhd/account.png) dans la barre de gauche, sélectionnez ensuite votre environnement Azure, puis connectez-vous.

3. Sélectionnez tous les abonnements ou l’abonnement qui contient le compte de stockage que vous souhaitez supprimer.

    ![ajouter un abonnement](media/storage-resource-manager-cannot-delete-storage-account-container-vhd/addsub.png)

4. Accédez au compte de stockage que nous avons précédemment obtenu à partir de l’URL du disque, sélectionnez **Conteneurs d’objets blob** > **Disques durs virtuels** et recherchez le disque dur virtuel qui vous empêche de supprimer le compte de stockage.
5. Si le disque dur virtuel est trouvé, consultez la colonne **Nom de la machine virtuelle** pour rechercher la machine virtuelle utilisant ce disque dur virtuel.

    ![Rechercher la machine virtuelle](media/storage-resource-manager-cannot-delete-storage-account-container-vhd/check-vm.png)

6. Supprimez le bail du disque dur virtuel à l’aide du portail Azure. Pour plus d’informations, consultez [Remove the lease from the VHD](#remove-the-lease-from-the-vhd) (Supprimer le bail du disque dur virtuel). 

7. Accédez à l’Explorateur de stockage Azure, cliquez avec le bouton droit sur le disque dur virtuel, puis sélectionnez Supprimer.

8. Supprime le compte de stockage.

### <a name="method-2---use-azure-portal"></a>Méthode 2 - Utiliser le portail Azure 

#### <a name="step-1-identify-the-vhd-that-prevent-deletion-of-the-storage-account"></a>Étape 1 : Identifier le disque dur virtuel qui empêche la suppression du compte de stockage

1. Lorsque vous supprimez le compte de stockage, une boîte de dialogue contenant le type de message suivant s’affiche : 

    ![message lors de la suppression du compte de stockage](media/storage-resource-manager-cannot-delete-storage-account-container-vhd/delete-storage-error.png) 

2. Vérifiez **l’URL du disque** pour identifier le compte de stockage et le disque dur virtuel qui vous empêchent de supprimer le compte de stockage. Dans l’exemple suivant, la chaîne précédant « .blob.core.windows.net » correspond au nom du compte de stockage et « SCCM2012-2015-08-28.vhd » est le nom du disque dur virtuel.  

        https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd

2. Connectez-vous au [portail Azure](https://portal.azure.com).
3. Dans le menu Hub, sélectionnez **Toutes les ressources**. Accédez au compte de stockage, puis sélectionnez **Objets blob** > **Disques durs virtuels**.

    ![Capture d’écran du portail, où le compte de stockage et le conteneur « vhds » apparaissent en surbrillance](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/opencontainer.png)

4. Recherchez le disque dur virtuel que nous avons précédemment obtenu à partir de l’URL du disque. Ensuite, déterminez quelle machine virtuelle utilise le disque dur virtuel. En règle générale, vous pouvez déterminer les machines virtuelles que contient le disque dur virtuel en vérifiant le nom de ce dernier :

Machine virtuelle dans le modèle de développement Resource Manager

   * Généralement, les disques de système d’exploitation respectent la convention d’affectation de noms suivante : NomMachineVirtuelle-AAAA-MM-JJ-HHMMSS.vhd
   * Généralement, les disques de données respectent la convention d’affectation de noms suivante : NomMachineVirtuelle-AAAA-MM-JJ-HHMMSS.vhd

Machine virtuelle dans le modèle de développement classique

   * Généralement, les disques de système d’exploitation respectent la convention d’affectation de noms suivante : NomServiceCloud-NomMachineVirtuelle-AAAA-MM-JJ-HHMMSS.vhd
   * Généralement, les disques de données respectent la convention d’affectation de noms suivante : NomServiceCloud-NomMachineVirtuelle-AAAA-MM-JJ-HHMMSS.vhd

#### <a name="step-2-remove-the-lease-from-the-vhd"></a>Étape 2 : Supprimer le bail du disque dur virtuel

[Supprimez le bail du disque dur virtuel](#remove-the-lease-from-the-vhd), puis supprimez le compte de stockage.

## <a name="what-is-a-lease"></a>Qu’est-ce qu’un bail ?
Un bail est un verrou qui peut être utilisé pour contrôler l’accès à un objet blob (par exemple, un disque dur virtuel). Quand un objet blob est loué, seuls les propriétaires du bail peuvent accéder à l’objet blob. Un bail est important pour les raisons suivantes :

* Il empêche la corruption des données si plusieurs propriétaires essaient d’écrire sur la même partie de l’objet blob en même temps.
* Il empêche la suppression de l’objet blob d’être supprimé si un élément l’utilise activement (par exemple, une machine virtuelle).
* Il empêche la suppression du compte de stockage si un élément l’utilise activement (par exemple, une machine virtuelle).

### <a name="remove-the-lease-from-the-vhd"></a>Supprimer le bail du disque dur virtuel
Si le disque dur virtuel est un disque de système d’exploitation, vous devez supprimer la machine virtuelle pour supprimer le bail :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu **Hub**, sélectionnez **Machines virtuelles**.
3. Sélectionnez la machine virtuelle qui détient un bail sur le disque dur virtuel.
4. Assurez-vous qu’aucun élément n’utilise activement la machine virtuelle, et que vous n’avez plus besoin de la machine virtuelle.
5. En haut du panneau **Détails de la machine virtuelle**, sélectionnez **Supprimer**, puis cliquez sur **Oui** pour confirmer.
6. La machine virtuelle doit être supprimée, mais le disque dur virtuel peut être conservé. Toutefois, le disque dur virtuel ne devrait plus avoir de bail associé. La publication du bail peut nécessiter quelques minutes. Pour vérifier que le bail est publié, accédez à **Toutes les ressources** > **Nom du compte de stockage** > **Blobs** > **vhds**. Dans le volet **Propriétés Blob**, la valeur **Statut du bail** doit être **Déverrouillé**.

Si le disque dur virtuel est un disque de données, détachez le disque dur virtuel de la machine virtuelle pour supprimer le bail :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu **Hub**, sélectionnez **Machines virtuelles**.
3. Sélectionnez la machine virtuelle qui détient un bail sur le disque dur virtuel.
4. Sélectionnez **Disques** sur le panneau **Détails de la machine virtuelle**.
5. Sélectionnez le disque de données qui détient un bail sur le disque dur virtuel. Vous pouvez déterminer le disque dur virtuel attaché dans le disque en vérifiant son URL.
6. Déterminez avec certitude que rien n’utilise le disque de données.
7. Cliquez sur **Détacher** sur le panneau **Détails concernant le disque**.
8. Le disque doit maintenant être détaché de la machine virtuelle, et le disque dur virtuel ne doit plus avoir de bail. La publication du bail peut nécessiter quelques minutes. Pour vérifier que le bail a été publié, accédez à **Toutes les ressources** > **Nom du compte de stockage** > **Blobs** > **vhds**. Dans le volet **Propriétés Blob**, la valeur **Statut du bail** doit être **Déverrouillé**.

## <a name="next-steps"></a>Étapes suivantes
* [Suppression d'un compte de stockage](storage-create-storage-account.md#delete-a-storage-account)
* [How to break the locked lease of blob storage in Microsoft Azure (PowerShell) (Interruption du bail verrouillé du stockage d’objets blob dans Microsoft Azure (PowerShell))](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)

