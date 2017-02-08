---
title: "Résoudre les erreurs lorsque vous supprimez des comptes de stockage Azure, des conteneurs ou des disques durs virtuels dans un déploiement Resource Manager | Microsoft Docs"
description: "Résoudre les erreurs lorsque vous supprimez des comptes de stockage Azure, des conteneurs ou des disques durs virtuels dans un déploiement Resource Manager"
services: storage
documentationcenter: 
author: genlin
manager: felixwu
editor: na
tags: storage
ms.assetid: 17403aa1-fe8d-45ec-bc33-2c0b61126286
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 75d5e82e2f4e747747f24376239e23f6512f916a


---
# <a name="troubleshoot-errors-when-you-delete-azure-storage-accounts-containers-or-vhds-in-a-resource-manager-deployment"></a>Résoudre les erreurs lorsque vous supprimez des comptes de stockage Azure, des conteneurs ou des disques durs virtuels dans un déploiement Resource Manager
[!INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

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

### <a name="step-1-identify-the-problem-vhd-and-the-associated-vm"></a>Étape 1 : Identifier le problème de disque dur virtuel et la machine virtuelle associée
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu **Hub**, sélectionnez **Toutes les ressources**. Accédez au compte de stockage que vous souhaitez supprimer, puis sélectionnez **Blobs** > **vhds**.

    ![Capture d’écran du portail, où le compte de stockage et le conteneur « vhds » apparaissent en surbrillance](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/opencontainer.png)
3. Vérifiez les propriétés de chaque disque dur virtuel du conteneur. Recherchez le disque dur virtuel à l’état **Loué**. Ensuite, déterminez quelle machine virtuelle utilise le disque dur virtuel. En règle générale, vous pouvez déterminer les machines virtuelles que contient le disque dur virtuel en vérifiant le nom de ce dernier :

   * Généralement, les disques de système d’exploitation respectent la convention d’affectation de noms suivante : NomMachineVirtuelleAAAAMMJJHHMMSS.vhd
   * Généralement, les disques de données respectent la convention d’affectation de noms suivante : NomMachineVirtuelle-AAAAMMJJ-HHMMSS.vhd

     ![Capture d’écran de l’info de conteneur dans le portail, avec le nom de la machine Virtuelle, l’état de « Verrouillé » et l’état du bail de « Loué » mis en surbrillance](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/locatevm.png)

### <a name="step-2-remove-the-lease-from-the-vhd"></a>Étape 2 : Supprimer le bail du disque dur virtuel
Pour supprimer la machine virtuelle qui utilise le disque dur virtuel (pour les disques de système d’exploitation) :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu **Hub**, sélectionnez **Machines virtuelles**.
3. Sélectionnez la machine virtuelle qui détient un bail sur le disque dur virtuel.
4. Assurez-vous qu’aucun élément n’utilise activement la machine virtuelle, et que vous n’avez plus besoin de la machine virtuelle.
5. En haut du panneau **Détails de la machine virtuelle**, sélectionnez **Supprimer**, puis cliquez sur **Oui** pour confirmer.
6. La machine virtuelle doit être supprimée, mais le disque dur virtuel doit être conservé. Toutefois, le disque dur virtuel ne devrait plus avoir de bail associé. La publication du bail peut nécessiter quelques minutes. Pour vérifier que le bail est publié, accédez à **Toutes les ressources** > **Nom du compte de stockage** > **Blobs** > **vhds**. Dans le volet **Propriétés Blob**, la valeur **Statut du bail** doit être **Déverrouillé**.

Pour détacher le disque dur virtuel à partir de la machine virtuelle qui l’utilise (pour les disques de données) :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu **Hub**, sélectionnez **Machines virtuelles**.
3. Sélectionnez la machine virtuelle qui détient un bail sur le disque dur virtuel.
4. Sélectionnez **Disques** sur le panneau **Détails de la machine virtuelle**.
5. Sélectionnez le disque de données qui détient un bail sur le disque dur virtuel. Vous pouvez déterminer le disque dur virtuel attaché dans le disque en vérifiant son URL.
6. Déterminez avec certitude que rien n’utilise le disque de données.
7. Cliquez sur **Détacher** sur le panneau **Détails concernant le disque**.
8. Le disque doit maintenant être détaché de la machine virtuelle, et le disque dur virtuel ne doit plus avoir de bail. La publication du bail peut nécessiter quelques minutes. Pour vérifier que le bail a été publié, accédez à **Toutes les ressources** > **Nom du compte de stockage** > **Blobs** > **vhds**. Dans le volet **Propriétés Blob**, la valeur **Statut du bail** doit être **Déverrouillé**.

## <a name="what-is-a-lease"></a>Qu’est-ce qu’un bail ?
Un bail est un verrou qui peut être utilisé pour contrôler l’accès à un objet blob (par exemple, un disque dur virtuel). Quand un objet blob est loué, seuls les propriétaires du bail peuvent accéder à l’objet blob. Un bail est important pour les raisons suivantes :

* Il empêche la corruption des données si plusieurs propriétaires essaient d’écrire sur la même partie de l’objet blob en même temps.
* Il empêche la suppression de l’objet blob d’être supprimé si un élément l’utilise activement (par exemple, une machine virtuelle).
* Il empêche la suppression du compte de stockage si un élément l’utilise activement (par exemple, une machine virtuelle).

## <a name="next-steps"></a>Étapes suivantes
* [Suppression d'un compte de stockage](storage-create-storage-account.md#delete-a-storage-account)
* [How to break the locked lease of blob storage in Microsoft Azure (PowerShell) (Interruption du bail verrouillé du stockage d’objets blob dans Microsoft Azure (PowerShell))](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)



<!--HONumber=Nov16_HO3-->


