---
title: Azure Government Backup| Microsoft Docs
description: "Cet article fournit une vue d’ensemble des fonctionnalités Azure Backup disponibles dans Azure Government."
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
ms.assetid: a7622135-8790-4be4-a02a-7b9ac8a4996f
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/11/2016
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: 0095a95afc14de42c1160a73139a0f059cd758dd
ms.openlocfilehash: 3b3ecaef33aefc89e5a5c1cd351566d21246d0b8


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

> [!IMPORTANT]
> Actuellement, Azure Government Backup prend en charge les déploiements Service Manager, également appelé modèle de déploiement classique. Les déploiements Resource Manager ne sont pour le moment pas pris en charge. Consultez l’article suivant pour connaître la [différence entre Azure Resource Manager et les modèles de déploiement classiques](../resource-manager-deployment-model.md).

[!INCLUDE [learn-about-backup-deployment models](../../includes/backup-deployment-models.md)]

## <a name="azure-backup-components-available-in-azure-government-backup"></a>Composants Azure Backup disponibles dans Azure Government Backup

Vous pouvez utiliser Azure Backup pour protéger : les fichiers, les dossiers, les volumes, les machines virtuelles, les applications et les charges de travail. En fonction de ce que vous souhaitez protéger, et d’où se trouvent les données, vous utilisez différents composants Azure Backup. Les sections suivantes comportent des liens vers des articles de la documentation publique d’Azure Backup pour chaque composant.

Chaque article explique comment utiliser le composant Azure Backup sur le portail de la version classique.

### <a name="windows-server-and-windows-computers"></a>Windows Server et ordinateurs Windows

- [Sauvegarder Windows Server et les ordinateurs clients Windows](../backup/backup-configure-vault-classic.md)
- [Restaurer Windows Server et les ordinateurs clients Windows](../backup/backup-azure-restore-windows-server.md)
- [Gérer les sauvegardes Windows Server et des ordinateurs clients Windows](../backup/backup-azure-manage-windows-server.md)
- [Utilisation de PowerShell pour sauvegarder Windows Server](../backup/backup-client-automation-classic.md)

### <a name="virtual-machines"></a>Machines virtuelles

- [Préparation de l’environnement de la machine virtuelle](../backup/backup-azure-vms-prepare.md)
- [Sauvegarde de machines virtuelles](../backup/backup-azure-vms-first-look.md)
- [Restauration des machines virtuelles](../backup/backup-azure-restore-vms.md)
- [Gestion des machines virtuelles](../backup/backup-azure-manage-vms-classic.md)
- [Utilisation de PowerShell pour sauvegarder les machines virtuelles](../backup/backup-azure-vms-classic-automation.md)

### <a name="system-center-data-protection-manager"></a>System Center Data Protection Manager (DPM)

- [Sauvegarde de System Center Data Protection Manager (DPM)](../backup/backup-azure-dpm-introduction-classic.md)

### <a name="azure-backup-server"></a>Azure Backup Server

- [Azure Backup Server](../backup/backup-azure-microsoft-azure-backup-classic.md)

Le serveur de sauvegarde Azure est un composant Azure Backup qui fonctionne de la même façon que System Center Data Protection Manager (DPM). Le serveur de sauvegarde Azure peut protéger des charges de travail d’application telles que les machines virtuelles Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange et les clients Windows dans le cloud à partir d’une console unique.

## <a name="next-steps"></a>Étapes suivantes

Si vous ne savez pas comment vous y prendre, commencez par lire l’article [Sauvegarder un client Windows ou un serveur Windows Server dans Azure à l’aide du modèle de déploiement classique](../backup/backup-configure-vault-classic.md). Ce didacticiel vous guide au fil des étapes de configuration d’un projet de sauvegarde sur un ordinateur Windows ou un serveur Windows Server.

Si vous savez déjà que vous pouvez utiliser Azure Backup, mais souhaitez connaître le tarif, consultez la [page de tarification Azure Backup](http://azure.microsoft.com/pricing/details/backup/). Un forum aux questions est disponible et peut vous fournir des informations utiles. Notez également qu’il existe deux régions Azure Government dans le menu déroulant **Région**.



<!--HONumber=Nov16_HO3-->


