---
title: "Résoudre les problèmes de suppression de comptes de stockage Azure, de conteneurs ou de disques durs virtuels dans un déploiement classique | Microsoft Docs"
description: "Résoudre les problèmes de suppression de comptes de stockage Azure, de conteneurs ou de disques durs virtuels dans un déploiement classique"
services: storage
documentationcenter: 
author: genlin
manager: felixwu
editor: tysonn
tags: storage
ms.assetid: 0f7a8243-d8dc-432a-9d37-1272a0cb3a5c
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 9f3e824414ad6c1a0aba98a3d549ee63ddc7272f
ms.contentlocale: fr-fr
ms.lasthandoff: 06/29/2017


---
# <a name="troubleshoot-deleting-azure-storage-accounts-containers-or-vhds-in-a-classic-deployment"></a>Résoudre les problèmes de suppression de comptes de stockage Azure, de conteneurs ou de disques durs virtuels dans un déploiement classique
[!INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

Il est possible que vous receviez des erreurs quand vous essayez de supprimer le compte de stockage Azure, le conteneur ou le disque dur virtuel dans le [portail Azure](https://portal.azure.com/) ou le [portail Azure Classic](https://manage.windowsazure.com/). Ces problèmes peuvent être causés par les circonstances suivantes :

* Lorsque vous supprimez une machine virtuelle, le disque et le disque dur virtuel ne sont pas automatiquement supprimés. Cela peut être la cause de l'échec de la suppression du compte de stockage. Nous ne supprimons pas le disque pour que vous puissiez l’utiliser pour monter une autre machine virtuelle.
* Il existe toujours un bail sur un disque ou sur l’objet blob associé au disque.
* Il existe toujours une image de machine virtuelle qui utilise un compte de stockage, un conteneur ou un objet blob.

Si le problème lié à Azure n’est pas traité dans cet article, parcourez les forums Azure sur [MSDN et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez publier votre problème sur ces forums ou sur @AzureSupport sur Twitter. Vous pouvez également créer une demande de support Azure en sélectionnant **Obtenir de l’aide** sur le site du [support Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptoms"></a>Symptômes
La section suivante répertorie les erreurs courantes que vous pourriez recevoir quand vous tentez de supprimer les comptes de stockage Azure, les conteneurs ou les disques durs virtuels.

### <a name="scenario-1-unable-to-delete-a-storage-account"></a>Scénario 1 : Impossible de supprimer un compte de stockage
Lorsque vous accédez au compte de stockage classique dans le [portail Azure](https://portal.azure.com/) et que vous sélectionnez **Supprimer**, une liste des objets qui empêchent la suppression du compte de stockage peut s’afficher :

  ![Image de l’erreur qui se produit lors de la suppression du compte de stockage](./media/storage-cannot-delete-storage-account-container-vhd/newerror.png)

Lorsque vous accédez au compte de stockage dans le [portail Azure Classic](https://manage.windowsazure.com/) et que vous sélectionnez **Supprimer**, l’une des erreurs suivantes peut s’afficher :

- *Le compte de stockage StorageAccountName contient des images de machine virtuelle. Supprimez ces images de machine virtuelle avant de supprimer ce compte de stockage.*

- *Échec de suppression du compte de stockage <vm-storage-account-name>. Impossible de supprimer le compte de stockage <vm-storage-account-name> : Storage account <vm-storage-account-name> contient des images et/ou des disques actifs. Supprimez ces images et/ou disques avant de supprimer ce compte de stockage. ».*

- *Le compte de stockage <vm-storage-account-name> contient des images et/ou des disques actifs, par exemple, xxxxxxxxx- xxxxxxxxx-O-209490240936090599. Supprimez ces images et/ou disques avant de supprimer ce compte de stockage.*

- *Le compte de stockage <vm-storage-account-name> contient 1 ou plusieurs conteneurs avec une image active et/ou des artefacts de disque. Supprimez ces artefacts du dépôt d’images avant de supprimer ce compte de stockage*.

- *Échec d’envoi : le compte de stockage <vm-storage-account-name> contient 1 ou plusieurs conteneurs avec une image active et/ou des artefacts de disque. Supprimez ces artefacts du dépôt d’images avant de supprimer ce compte de stockage. Quand vous essayez de supprimer un compte de stockage auquel sont associés des disques toujours actifs, un message vous informe que des disques actifs doivent être supprimés*.

### <a name="scenario-2-unable-to-delete-a-container"></a>Scénario 2 : Impossible de supprimer un conteneur
Quand vous essayez de supprimer le conteneur de stockage, il est possible que vous rencontriez l’erreur suivante :

*Échec de la suppression du conteneur de stockage <container name>. Erreur : « Il existe actuellement un bail sur le conteneur et aucun ID de bail n’a été spécifié dans la demande »*.

Ou

*Les disques de machines virtuelles suivants utilisent actuellement des objets blob de ce conteneur. Par conséquent, le conteneur ne peut pas être supprimé : VirtualMachineDiskName1, VirtualMachineDiskName2, ...*

### <a name="scenario-3-unable-to-delete-a-vhd"></a>Scénario 3 : Impossible de supprimer un disque dur virtuel
Après avoir supprimé une machine virtuelle, puis tenté de supprimer les objets blob des disques durs virtuels associés, il est possible que vous receviez le message suivant :

*Échec de la suppression de l’objet blob « path/XXXXXX-XXXXXX-os-1447379084699.vhd ». Erreur : « Il existe actuellement un bail sur l’objet blob et aucun ID de bail n’a été spécifié dans la demande »*.

Ou

*L’objet blob « BlobName.vhd » est en cours d’utilisation en tant que disque de la machine virtuelle « VirtualMachineDiskName », donc il ne peut pas être supprimé.*

## <a name="solution"></a>Solution
Pour résoudre les problèmes les plus courants, essayez la méthode suivante :

### <a name="step-1-delete-any-disks-that-are-preventing-deletion-of-the-storage-account-container-or-vhd"></a>Étape 1 : Supprimer les disques qui empêchent la suppression du compte de stockage, du conteneur ou du disque dur virtuel
1. Connectez-vous au [Portail Azure Classic](https://manage.windowsazure.com/).
2. Sélectionnez **MACHINES VIRTUELLES** > **DISQUES**.

    ![Image de disques sur des machines virtuelles sur le portail Azure Classic.](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)
3. Recherchez les disques associés au compte de stockage, au conteneur ou au disque dur virtuel à supprimer. En vérifiant l’emplacement du disque, vous trouverez le compte de stockage, le conteneur et le disque dur virtuel associés.

    ![Image qui affiche des informations d'emplacement sur les disques sur le portail Azure Classic](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)
4. Supprimez les disques en utilisant l’une des méthodes suivantes :

  - Si aucune machine virtuelle n’est indiquée dans le champ **Attaché à** des disques, vous pouvez supprimer le disque directement.

  - Si le disque est un disque de données, procédez comme suit :

    1. Vérifiez le nom de la machine virtuelle à laquelle le disque est attaché.
    2. Accédez à **Machines virtuelles** > **Instances**, puis recherchez la machine virtuelle.
    3. Assurez-vous que le disque n’est pas activement utilisé.
    4. Sélectionnez **Détacher le disque** au bas du portail pour détacher le disque.
    5. Accédez à **Machines virtuelles** > **Disques** et attendez que le champ **Attaché à** soit vide. Cela indique que le disque a été correctement détaché de la machine virtuelle.
    6. Sélectionnez **Supprimer** sous **Machines virtuelles** > **Disques** pour supprimer le disque.

  - Si le disque est un disque du système d’exploitation (le champ **Contient le système d’exploitation** a une valeur telle que Windows) et qu’il est attaché à une machine virtuelle, procédez comme suit pour supprimer la machine virtuelle. Le disque du système d’exploitation ne peut pas être détaché. Nous devons donc supprimer la machine virtuelle pour libérer l’attribution.

    1. Vérifiez le nom de la machine virtuelle à laquelle le disque de données est attaché.  
    2. Accédez à **Machines virtuelles** > **Instances**, puis sélectionnez la machine virtuelle à laquelle le disque est attaché.
    3. Assurez-vous qu’aucun élément n’utilise activement la machine virtuelle, et que vous n’avez plus besoin de la machine virtuelle.
    4. Sélectionnez la machine virtuelle à laquelle le disque est attaché, puis sélectionnez **Supprimer** > **Supprimer les disques attachés**.
    5. Accédez à **Machines virtuelles** > **Disques** et attendez que le disque ne s’affiche plus.  Cela peut prendre plusieurs minutes. Vous devrez peut-être actualiser la page.
    6. Si le disque ne disparaît pas, attendez que le champ **Attaché à** soit vide. Cela indique que le disque a été entièrement détaché de la machine virtuelle.  Ensuite, sélectionnez le disque et sélectionnez **Supprimer** en bas de la page pour supprimer le disque.


   > [!NOTE]
   > Si un disque est attaché à une machine virtuelle, vous ne pouvez pas le supprimer. Les disques sont détachés de façon asynchrone d’une machine virtuelle supprimée. L’effacement de ce champ peut prendre quelques minutes après la suppression de la machine virtuelle.
   >
   >


### <a name="step-2-delete-any-vm-images-that-are-preventing-deletion-of-the-storage-account-or-container"></a>Étape 2 : Supprimer les images de machine virtuelle qui empêchent la suppression du compte de stockage ou du conteneur
1. Connectez-vous au [Portail Azure Classic](https://manage.windowsazure.com/).
2. Sélectionnez **MACHINE VIRTUELLE** > **IMAGES**, puis supprimez les images associées au compte de stockage, au conteneur ou au disque dur virtuel.

    Ensuite, réessayez de supprimer le compte de stockage, le conteneur ou le disque dur virtuel.

> [!WARNING]
> Veillez à sauvegarder tout ce que vous souhaitez conserver avant de supprimer le compte. Lorsque vous supprimez un VHD, un blob, une table, une file d’attente ou un fichier, il est définitivement supprimé. Vérifiez que la ressource n’est pas en cours d’utilisation.
>
>

## <a name="about-the-stopped-deallocated-status"></a>À propos de l’état Arrêté (Désalloué)
Les machines virtuelles créées dans le modèle de déploiement classique et conservées auront le statut **Arrêté (Désalloué)** soit sur le [portail Azure](https://portal.azure.com/), soit sur le [portail Azure Classic](https://manage.windowsazure.com/).

**Portail Azure Classic**:

![Statut Arrêté (désalloué) pour les machines virtuelles sur le portail Azure.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)

**Portail Azure**:

![Statut Arrêté (désalloué) pour les machines virtuelles sur le portail Azure Classic.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

Le statut « Arrêté (désalloué) » libère les ressources de l’ordinateur (processeur, mémoire et réseau). Les disques, cependant, sont toujours conservés de façon à vous permettre de recréer rapidement la machine virtuelle si nécessaire. Ces disques sont créés sur les disques durs virtuels sauvegardés par Azure Storage. Le compte de stockage contient ces disques durs virtuels et les disques ont des baux sur ces disques durs virtuels.

## <a name="next-steps"></a>Étapes suivantes
* [Suppression d'un compte de stockage](storage-create-storage-account.md#delete-a-storage-account)

