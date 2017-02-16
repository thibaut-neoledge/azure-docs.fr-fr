---
title: Azure Government Backup| Microsoft Docs
description: "Cet article fournit une vue d’ensemble des fonctionnalités Azure Backup disponibles dans Azure Government."
services: azure-government
documentationcenter: 
author: markgalioto
manager: carmonm
ms.assetid: a7622135-8790-4be4-a02a-7b9ac8a4996f
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 1/5/2017
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: fa00142a9e89c5ad2630f688ea9771a1a542c052
ms.openlocfilehash: e5f89f845302ecb890caa50dd8f86503b29f1154


---
# <a name="azure-government-backup"></a>Azure Government Backup

Cet article fournit une vue d’ensemble du service Azure Backup et répertorie les fonctionnalités de sauvegarde disponibles dans Azure Government. Azure Backup est le service Azure qui vous permet de sauvegarder (ou de protéger) vos données dans le cloud Microsoft. La protection de vos données dans Azure ne consiste pas seulement à les sauvegarder dans le cloud, mais aussi à restaurer les données dans le cloud ou sur une installation locale. Azure Backup offre les principaux avantages suivants :

- Gestion automatique du stockage
- Mise à l’échelle illimitée
- Diverses options de stockage
- Transfert de données illimité
- Chiffrement des données
- Sauvegarde cohérente avec les applications
- Rétention à long terme

Si vous découvrez Azure Backup et que vous souhaitez une vue d’ensemble des fonctionnalités disponibles, consultez l’article [Qu’est-ce qu’Azure Backup](../backup/backup-introduction-to-azure-backup.md).

[!INCLUDE [learn-about-backup-deployment models](../../includes/backup-deployment-models.md)]

## <a name="azure-backup-components-available-in-azure-government-backup"></a>Composants Azure Backup disponibles dans Azure Government Backup

Vous pouvez utiliser Azure Backup pour protéger : les fichiers, les dossiers, les volumes, les machines virtuelles, les applications et les charges de travail. En fonction de ce que vous souhaitez protéger, et d’où se trouvent les données, vous utilisez différents composants Azure Backup. Les sections suivantes comportent des liens vers des articles de la documentation publique d’Azure Backup pour chaque composant. Les sections sont réparties par portail classique ou Azure. Utilisez la version du portail Azure si vous envisagez d’utiliser des déploiements Resource Manager.

### <a name="using-windows-server-and-windows-computers-in-azure-portal"></a>Utilisation de Windows Server et des ordinateurs Windows dans le portail Azure

- [Sauvegarder Windows Server et les ordinateurs clients Windows](../backup/backup-configure-vault.md)
- [Restaurer Windows Server et les ordinateurs clients Windows](../backup/backup-azure-restore-windows-server.md)
- [Gérer les sauvegardes Windows Server et des ordinateurs clients Windows](../backup/backup-azure-manage-windows-server.md)
- [Utilisation de PowerShell pour sauvegarder Windows Server](../backup/backup-client-automation.md)

### <a name="using-windows-server-and-windows-computers-in-classic-portal"></a>Utilisation de Windows Server et des ordinateurs Windows dans le portail classique

- [Sauvegarder Windows Server et les ordinateurs clients Windows](../backup/backup-configure-vault-classic.md)
- [Restaurer Windows Server et les ordinateurs clients Windows](../backup/backup-azure-restore-windows-server-classic.md)
- [Gérer les sauvegardes Windows Server et des ordinateurs clients Windows](../backup/backup-azure-manage-windows-server-classic.md)
- [Utilisation de PowerShell pour sauvegarder Windows Server](../backup/backup-client-automation-classic.md)

### <a name="using-virtual-machines-in-azure-portal"></a>Utilisation des machines virtuelles dans le portail Azure

- [Préparation de l’environnement de la machine virtuelle](../backup/backup-azure-arm-vms-prepare.md)
- [Sauvegarde de machines virtuelles](../backup/backup-azure-vms-first-look-arm.md)
- [Restauration des machines virtuelles](../backup/backup-azure-arm-restore-vms.md)
- [Gestion des machines virtuelles](../backup/backup-azure-manage-vms.md)
- [Utilisation de PowerShell pour sauvegarder les machines virtuelles](../backup/backup-azure-vms-automation.md)

### <a name="using-virtual-machines-in-classic-portal"></a>Utilisation de machines virtuelles dans le portail classique

- [Préparation de l’environnement de la machine virtuelle](../backup/backup-azure-vms-prepare.md)
- [Sauvegarde de machines virtuelles](../backup/backup-azure-vms-first-look.md)
- [Restauration des machines virtuelles](../backup/backup-azure-restore-vms.md)
- [Gestion des machines virtuelles](../backup/backup-azure-manage-vms-classic.md)
- [Utilisation de PowerShell pour sauvegarder les machines virtuelles](../backup/backup-azure-vms-classic-automation.md)

### <a name="using-system-center-data-protection-manager-in-azure-portal"></a>Utilisation de System Center Data Protection Manager (DPM) dans le portail Azure

- [Sauvegarde de System Center Data Protection Manager (DPM)](../backup/backup-azure-dpm-introduction.md)

### <a name="using-system-center-data-protection-manager-in-classic-portal"></a>Utilisation de System Center Data Protection Manager (DPM) dans le portail classique

- [Sauvegarde de System Center Data Protection Manager (DPM)](../backup/backup-azure-dpm-introduction-classic.md)

### <a name="using-azure-backup-server-in-azure-portal"></a>Utilisation du serveur de sauvegarde Azure dans le portail Azure

Le serveur de sauvegarde Azure est un composant Azure Backup qui fonctionne de la même façon que System Center Data Protection Manager (DPM), à une différence près : le serveur de sauvegarde Azure ne peut pas enregistrer de données sur bande. Le serveur de sauvegarde Azure peut protéger des charges de travail d’application telles que les machines virtuelles Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange et les clients Windows dans le cloud à partir d’une console unique. Le serveur de sauvegarde Azure ne nécessite pas de licence System Center.

- [Azure Backup Server](../backup/backup-azure-microsoft-azure-backup.md)

### <a name="using-azure-backup-server-in-classic-portal"></a>Utilisation du serveur de sauvegarde Azure dans le portail classique

- [Azure Backup Server](../backup/backup-azure-microsoft-azure-backup-classic.md)


## <a name="next-steps"></a>Étapes suivantes

Si vous ne savez pas comment vous y prendre, commencez par lire l’article [Sauvegarder un client Windows ou un serveur Windows Server dans Azure à l’aide du modèle de déploiement classique](../backup/backup-configure-vault-classic.md). Ce didacticiel vous guide au fil des étapes de configuration d’un projet de sauvegarde sur un ordinateur Windows ou un serveur Windows Server.

Si vous savez déjà que vous pouvez utiliser Azure Backup, mais souhaitez connaître le tarif, consultez la [page de tarification Azure Backup](http://azure.microsoft.com/pricing/details/backup/). Un forum aux questions est disponible et peut vous fournir des informations utiles. Notez également qu’il existe deux régions Azure Government dans le menu déroulant **Région**.



<!--HONumber=Jan17_HO1-->


