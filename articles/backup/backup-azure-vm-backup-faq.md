
---
title: Sauvegarde de machine virtuelle Azure - Forum aux questions | Microsoft Docs
description: "Réponses aux questions courantes sur : le fonctionnement de la sauvegarde de machine virtuelle Azure, ses limitations et ce qu’il se passe lors d’un changement de stratégie"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
keywords: "sauvegarde de la machine virtuelle azure, restauration de la machine virtuelle azure, stratégie de sauvegarde"
ms.assetid: c4cd7ff6-8206-45a3-adf5-787f64dbd7e1
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/18/2017
ms.author: trinadhk;pullabhk;
ms.translationtype: HT
ms.sourcegitcommit: 0425da20f3f0abcfa3ed5c04cec32184210546bb
ms.openlocfilehash: 8c06a90f58cf56ebb4e75e7567e237de7414a300
ms.contentlocale: fr-fr
ms.lasthandoff: 07/20/2017

---
# <a name="questions-about-the-azure-vm-backup-service"></a>Questions sur le service de sauvegarde de machine virtuelle Azure
Cet article comporte les réponses aux questions fréquentes pour vous aider à comprendre rapidement les composants de la sauvegarde de machine virtuelle Azure. Certaines réponses comportent des liens vers les articles présentant des informations complètes. Vous pouvez également publier des questions sur le service Azure Backup dans le [forum de discussion](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="configure-backup"></a>Configurer une sauvegarde
### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Les coffres Recovery Services prennent-ils en charge les machines virtuelles Azure Classic ou Resource Manager ? <br/>
Les coffres Recovery Services prennent en charge les deux modèles.  Vous pouvez sauvegarder une machine virtuelle classique (créée dans le portail Classic) ou une machine virtuelle Azure Resource Manager (créée dans le portail Azure) dans un coffre Azure Recovery Services.

### <a name="what-configurations-are-not-supported-by-azure-vm-backup-"></a>Quelles configurations ne sont pas prises en charge par la sauvegarde de machine virtuelle Azure ?
Consultez [Systèmes d’exploitation pris en charge](backup-azure-arm-vms-prepare.md#supported-operating-system-for-backup) et [Limitations des sauvegardes de machine virtuelle](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm)

### <a name="why-cant-i-see-my-vm-in-configure-backup-wizard"></a>Pourquoi je ne peux pas voir ma machine virtuelle dans l’assistant de configuration de la sauvegarde ?
Dans l’assistant de configuration de la sauvegarde, Azure Backup répertorie uniquement les machines virtuelles qui sont :
* Actuellement non protégées : vous pouvez vérifier le statut de la sauvegarde d’une machine virtuelle en accédant au panneau de la machine virtuelle et en vérifiant le Statut de la sauvegarde à partir du Menu Paramètres du panneau. En savoir plus sur comment [Vérifier l’état de la sauvegarde de machine virtuelle](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-management-blade)
* Appartient à la même région que la machine virtuelle

## <a name="backup"></a>Sauvegarde
### <a name="will-on-demand-backup-job-follow-same-retention-schedule-as-scheduled-backups"></a>Est-ce qu’un travail de sauvegarde à la demande suit le même planning de rétention que les autres sauvegardes planifiées ?
Non. Vous devez spécifier la plage de rétention pour un travail de sauvegarde à la demande. Par défaut, il sera conservé pendant 30 jours s’il a été déclenché à partir du portail. 

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>J’ai récemment activé Azure Disk Encryption sur certaines machines virtuelles. Est-ce que mes sauvegardes continueront de fonctionner ?
Vous devez accorder l’autorisation au service Azure Backup d’accéder au Key Vault. Vous pouvez fournir ces autorisations dans PowerShell à l’aide des étapes présentées dans la section *Sauvegarde des machines virtuelles Azure* de la documentation [PowerShell](backup-azure-vms-automation.md).

### <a name="i-migrated-disks-of-a-vm-to-managed-disks-will-my-backups-continue-to-work"></a>J’ai effectué une migration des disques d’une machine virtuelle sur des disques gérés. Est-ce que mes sauvegardes continueront de fonctionner ?
Oui, les sauvegardes fonctionnent de façon transparente et sans devoir de nouveau configurer la sauvegarde. 

## <a name="restore"></a>Restauration
### <a name="how-do-i-decide-between-restoring-disks-versus-full-vm-restore"></a>Comment choisir entre la restauration des disques et la restauration complète de la machine virtuelle ?
Vous devez considérer la restauration complète de la machine virtuelle Azure comme une manière rapide de créer une option pour la machine virtuelle restaurée. L’option de restauration de la machine virtuelle changera les noms des disques, des conteneurs utilisés par les disques, des adresses IP publiques et des noms d’interface réseau pour l’unicité des ressources créées en tant qu’éléments de la création de la machine virtuelle. De plus, cela n’ajoutera pas de groupe à haute responsabilité à la machine virtuelle. 

Utilisez des disques de restauration pour :
* Personnaliser la machine virtuelle qui est créée à partir de la configuration dans le temps ou même changer la taille de la configuration de sauvegarde
* Ajouter des configurations qui ne sont pas présentes au moment de la sauvegarde 
* Contrôler la convention d’affectation de noms pour les ressources créées
* Ajouter des machines virtuelles aux groupes à haute disponibilité
* Vous disposez de n’importe quelle configuration pouvant être obtenue uniquement à l’aide de PowerShell/d’une définition de modèle déclaratif

## <a name="manage-vm-backups"></a>Gérer les sauvegardes de machine virtuelle
### <a name="what-happens-when-i-change-a-backup-policy-on-vms"></a>Que se passe-t-il lorsque je modifie une stratégie de sauvegarde sur des machines virtuelles ?
Lorsqu’une nouvelle stratégie est appliquée sur les machines virtuelles, le planning et la rétention de la nouvelle stratégie seront suivis. Si la rétention est étendue, les points de récupération existants seront marqués comme à conserver afin qu’ils soient conformes à la nouvelle stratégie. Si la rétention est réduite, ils seront marqués comme à nettoyer lors de la prochaine tâche de nettoyage et seront supprimés. 

