---
title: FAQ sur le coffre Recovery Services | Microsoft Docs
description: "Cette version de la FAQ prend en charge la version préliminaire publique du service Azure Backup. Réponses aux questions fréquemment posées sur l’agent de sauvegarde, la sauvegarde et la rétention, la récupération, la sécurité et d’autres questions courantes sur la solution Azure Backup."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "solution de sauvegarde ; service de sauvegarde"
ms.assetid: 5f55b500-1ee9-4f64-9306-02d6f7a8eded
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/21/2016
ms.author: markgal;trinadhk;
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: e5ef305d926a57e32cdebd44f3dbe2185c735dd4
ms.lasthandoff: 04/15/2017


---
# <a name="recovery-services-vault---faq"></a>FAQ du coffre Recovery Services
Cet article fournit des informations spécifiques au coffre Recovery Services et il vient compléter la [FAQ Azure Backup](backup-azure-backup-faq.md). La FAQ Azure Backup fournit l’ensemble des questions et réponses sur le service Azure Backup.  

Vous pouvez poser des questions sur Azure Backup dans la section Disques de cet article ou d’un article associé. Vous pouvez également publier des questions sur le service Azure Backup dans le [forum de discussion](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-classic-mode-still-supported-br"></a>Les coffres Recovery Services sont basés sur Resource Manager. Les coffres Azure Backup (mode Classic) sont-ils toujours gérés ? <br/>
Oui, les coffres Azure Backup sont toujours pris en charge. Créez des coffres Azure Backup dans le [portail Azure Classic](https://manage.windowsazure.com). Créez des coffres Azure Recovery Services dans le [portail Azure](https://portal.azure.com). Toutefois, il est vivement recommandé de créer un coffre Recovery Services, car toutes les futures améliorations ne seront disponibles que dans c type de coffre.

## <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>Puis-je migrer un coffre Azure Backup dans un coffre Recovery Services ? <br/>
Malheureusement non, à ce stade vous ne pouvez pas migrer le contenu d’un coffre Azure Backup dans un coffre Azure Recovery Services. Nous travaillons sur l’ajout de cette fonctionnalité, mais elle n’est pas disponible dans la version préliminaire publique.

## <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Les coffres Recovery Services prennent-ils en charge les machines virtuelles Azure Classic ou Resource Manager ? <br/>
Les coffres Recovery Services prennent en charge les deux modèles.  Vous pouvez sauvegarder une machine virtuelle créée dans le portail Azure Classic (en mode Classic) ou une machine virtuelle créée dans le portail Azure (en mode Resource Manager) dans un coffre Recovery Services.

## <a name="i-have-backed-up-my-classic-vms-in-backup-vault-now-i-want-to-migrate-my-vms-from-classic-mode-to-resource-manager-mode--how-can-i-backup-them-in-recovery-services-vault"></a>J’ai sauvegardé mes machines virtuelles en mode Classic dans le coffre Azure Backup. Maintenant, je souhaite les migrer du mode Classic vers le mode Resource Manager.  Comment faire pour les sauvegarder dans le coffre Recovery Services ?
Les sauvegardes de machines virtuelles en mode Classic dans le code Azure Backup ne sont pas migrées automatiquement vers le coffre Recovery Services, lorsque vous migrez les machines virtuelles du mode Classic vers le mode Resource Manager. Pour migrer des sauvegardes de machines virtuelles, procédez comme suit :

1. Dans le coffre Azure Backup, accédez à l’onglet **Éléments protégés** et sélectionnez la machine virtuelle. Cliquez sur [Arrêter la protection](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines). Laissez l’option *Supprimer les données de sauvegarde associées***non cochée**.
2. Dans le [portail Azure](https://portal.azure.com), accédez au menu **Extensions** de la machine virtuelle et désinstallez l’extension **VMSnapshot/VMSnapshotLinux**.
3. Migrez la machine virtuelle du mode Classic vers le mode Resource Manager. Vérifiez que le stockage et le réseau correspondant à la machine virtuelle sont également migrés vers le mode Resource Manager.
4. Créez un coffre Recovery Services et configurez la sauvegarde sur la machine virtuelle migrée, à l’aide de l’action **Sauvegarder** en haut du tableau de bord du coffre. En savoir plus sur la [sauvegarde dans le coffre Recovery Services](backup-azure-vms-first-look-arm.md)

