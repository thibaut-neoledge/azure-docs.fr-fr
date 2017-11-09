---
title: "Résoudre les erreurs lorsque vous supprimez des comptes de stockage Azure, des conteneurs ou des disques durs virtuels | Microsoft Docs"
description: "Résoudre les erreurs lorsque vous supprimez des comptes de stockage Azure, des conteneurs ou des disques durs virtuels"
services: storage
documentationcenter: 
author: passaree
manager: cshepard
editor: na
tags: storage
ms.assetid: 17403aa1-fe8d-45ec-bc33-2c0b61126286
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 7a3c9422887592c29c2eae8bd75cf960865808fd
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-storage-delete-errors-in-resource-manager-deployment"></a>Résoudre les erreurs de suppression de stockage dans un déploiement Resource Manager
Cet article fournit des conseils pour résoudre les erreurs suivantes lorsque vous essayez de supprimer un compte de stockage Azure, un conteneur ou un blob dans un déploiement Azure Resource Manager.

>**Échec de la suppression du compte de stockage « StorageAccountName ». Erreur : Le compte de stockage ne peut pas être supprimé en raison de ses artefacts en cours d’utilisation.**

>**Failed to delete # out of # container(s):<br>vhds (Impossible de supprimer # conteneur(s) sur # : disques durs virtuels) :il existe actuellement un bail sur le conteneur et aucun ID de bail n’a été spécifié dans la demande.**

>**Failed to delete # out of # blobs:<br>BlobName.vhd (Impossible de supprimer # blob(s) sur # : BlobName.vhd) : il existe actuellement un bail sur le blob et aucun ID de bail n’a été spécifié dans la demande.**

Les disques durs virtuels utilisés dans les machines virtuelles Azure sont des fichiers .vhd stockés comme objets blob de pages dans un compte de stockage Standard ou Premium dans Azure.  Vous trouverez plus d’informations sur les disques Azure [ici](../../virtual-machines/windows/about-disks-and-vhds.md). Azure empêche la suppression d’un disque qui est joint à une machine virtuelle pour éviter une altération des données. Il empêche également la suppression de conteneurs et de comptes de stockage qui ont un objet blob de pages joint à une machine virtuelle. 

## <a name="solution"></a>Solution
Le processus de suppression d’un compte de stockage, d’un conteneur ou d’un blob lorsque vous recevez l’une des erreurs ci-dessus est le suivant : 
1. [Identifier les blobs joints à une machine virtuelle](#step-1-identify-blobs-attached-to-a-vm)
2. [Supprimer les machines virtuelles avec un **disque de système d’exploitation** joint](#step-2-delete-vm-to-detach-os-disk)
3. [Détacher tous les**disques de données** des autres machines virtuelles](#step-3-detach-data-disk-from-the-vm)

Effectuez une nouvelle tentative de suppression du compte de stockage, du conteneur ou du blob après avoir terminé les étapes précédents.

### <a name="step-1-identify-blob-attached-to-a-vm"></a>Étape 1 : Identifier les blobs joints à une machine virtuelle

#### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>Scénario 1 : Suppression d’un blob - identifier la machine virtuelle jointe
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu Hub, sélectionnez **Toutes les ressources**. Accédez au compte de stockage, sous **Service BLOB** sélectionnez **Conteneurs** et accédez au blob à supprimer.
3. Si le paramètre **État du bail** du blob est défini sur **Alloué**, cliquez avec le bouton droit et sélectionnez **Modifier les métadonnées** pour ouvrir le volet Métadonnées d’objet blob. 

    ![Capture d’écran du portail, avec les blobs du compte de stockage et clic droit sur « Modifier les métadonnées » mis en surbrillance](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-edit-metadata-sm.png)

4. Dans le volet Métadonnées d’objet blob, vérifiez et enregistrez la valeur de **MicrosoftAzureCompute_VMName**. Cette valeur correspond au nom de la machine virtuelle à laquelle le disque dur virtuel est joint. (Consultez la section **Important** si ce champ n’existe pas.)
5. Dans le volet Métadonnées d’objet blob, vérifiez et enregistrez la valeur de **MicrosoftAzureCompute_DiskType**. Cela permet d’identifier si le disque joint est un disque de système d’exploitation ou un disque de données (consultez la section **Important** si ce champ n’existe pas). 

     ![Capture d’écran du portail, avec le volet « Métadonnées d’objet Blob » de stockage ouvert](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-blob-metadata-sm.png)

6. Si le type de disque de blob est **OSDisk**, suivez [Étape 2 : Supprimer une machine virtuelle pour détacher le disque de système d’exploitation](#step-2-delete-vm-to-detach-os-disk). Si le type de disque de blob est **DataDisk**, suivez la procédure décrite dans [Étape 3 : détacher un disque de données de la machine virtuelle](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Si **MicrosoftAzureCompute_VMName** et **MicrosoftAzureCompute_DiskType** n’apparaissent pas dans les métadonnées du blob, cela signifie que le blob est explicitement alloué et qu’il n’est pas joint à une machine virtuelle. Les blobs alloués ne peuvent pas être supprimés tant que le bail n’a pas été résilié. Pour résilier le bail, cliquez avec le bouton droit sur le blob et sélectionnez **Résilier le bail**. Les blobs alloués qui ne sont pas joints à une machine virtuelle empêchent la suppression du blob, mais n’empêchent pas la suppression d’un conteneur ou d’un compte de stockage.

#### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>Scénario 2 : Suppression d’un conteneur - identifier tous les blobs du conteneur qui sont joints aux machines virtuelles
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu Hub, sélectionnez **Toutes les ressources**. Accédez au compte de stockage, sous **Service BLOB** sélectionnez **Conteneurs** et accédez au conteneur à supprimer.
3. Cliquez pour ouvrir le conteneur, et la liste des blobs qu’il contient s’affiche. Identifiez tous les blobs avec Type d’objet blob = **Objet blob de pages** et État du bail = **Alloué** dans cette liste. Suivez le [scénario 1](#step-1-identify-blobs-attached-to-a-vm) pour identifier la machine virtuelle associée à chacun de ces blobs.

    ![Capture d’écran du portail, avec les blobs du compte de stockage et « État du bail » avec « Alloué » mis en surbrillance](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-disks-sm.png)

4. Suivez [l’étape 2](#step-2-delete-vm-to-detach-os-disk) et [l’étape 3](#step-3-detach-data-disk-from-the-vm) pour supprimer des machines virtuelles avec **OSDisk** et détacher **DataDisk**. 

#### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>Scénario 3 : Suppression du stockage du compte - identifier tous les blobs du compte de stockage qui sont joints aux machines virtuelles
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu Hub, sélectionnez **Toutes les ressources**. Accédez au compte de stockage, sous **Service BLOB** sélectionnez **Conteneurs**.

    ![Capture d’écran du portail, avec les conteneurs du compte de stockage et « État du bail » avec « Alloué » mis en surbrillance](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-containers-sm.png)

3. Dans le volet **Conteneurs**, identifiez tous les conteneurs dans lesquels le paramètre **État du bail** est défini sur **Alloué** et suivez le [scénario 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) pour chaque conteneur **alloué**.
4. Suivez [l’étape 2](#step-2-delete-vm-to-detach-os-disk) et [l’étape 3](#step-3-detach-data-disk-from-the-vm) pour supprimer des machines virtuelles avec **OSDisk** et détacher **DataDisk**. 

### <a name="step-2-delete-vm-to-detach-os-disk"></a>Étape 2 : Supprimer une machine virtuelle pour détacher le disque de système d’exploitation
Si le disque dur virtuel est un disque de système d’exploitation, vous devez supprimer la machine virtuelle avant de pouvoir supprimer le disque dur virtuel joint. Aucune action supplémentaire ne sera nécessaire pour les disques de données joints à la même machine virtuelle une fois ces étapes effectuées :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu Hub, sélectionnez **Machines virtuelles**.
3. Sélectionnez la machine virtuelle à laquelle le disque dur virtuel est joint.
4. Assurez-vous qu’aucun élément n’utilise activement la machine virtuelle, et que vous n’avez plus besoin de la machine virtuelle.
5. En haut du volet **Détails de la machine virtuelle**, sélectionnez **Supprimer**, puis cliquez sur **Oui** pour confirmer.
6. La machine virtuelle doit être supprimée, mais le disque dur virtuel peut être conservé. Toutefois, le disque dur virtuel ne devrait plus être joint à une machine virtuelle, ni avoir de bail associé. La publication du bail peut nécessiter quelques minutes. Pour vérifier que le bail est résilié, accédez à l’emplacement du blob et dans le volet **Propriétés de l’objet blob**, le paramètre **État du bail** doit être défini sur **Disponible**.

### <a name="step-3-detach-data-disk-from-the-vm"></a>Étape 3 : Détacher un disque de données de la machine virtuelle
Si le disque dur virtuel est un disque de données, détachez le disque dur virtuel de la machine virtuelle pour supprimer le bail :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu Hub, sélectionnez **Machines virtuelles**.
3. Sélectionnez la machine virtuelle à laquelle le disque dur virtuel est joint.
4. Sélectionnez **Disques** dans le volet **Détails de la machine virtuelle**.
5. Sélectionnez le disque de données à supprimer auquel le disque dur virtuel est joint. Vous pouvez déterminer le blob joint dans le disque en vérifiant l’URL du disque dur virtuel.
6. Vous pouvez vérifier l’emplacement du blob en cliquant sur le disque pour vérifier le chemin d’accès dans le champ **URI VHD**.
7. Sélectionnez **Modifier** en haut du volet **Disques**.
8. Cliquez sur **l’icône Détacher** du disque de données à supprimer.

     ![Capture d’écran du portail, avec le volet « Métadonnées d’objet Blob » de stockage ouvert](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-vm-disks-edit.png)

9. Sélectionnez **Enregistrer**. Le disque doit maintenant être détaché de la machine virtuelle, et le disque dur virtuel ne doit plus avoir de bail. La publication du bail peut nécessiter quelques minutes. Pour vérifier que le bail a été résilié, accédez à l’emplacement du blob et dans le volet **Propriétés de l’objet blob**, le paramètre **État du bail** doit être défini sur **Déverrouillé** ou **Disponible**.

## <a name="next-steps"></a>Étapes suivantes
Nouvelle tentative de suppression d’objet de stockage qui avait échoué précédemment.

