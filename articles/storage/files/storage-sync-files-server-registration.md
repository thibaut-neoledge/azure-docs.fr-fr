---
title: "Inscrire/désinscrire un serveur à Azure File Sync (préversion) | Microsoft Docs"
description: "Découvrez comment inscrire un serveur Windows Server au service de synchronisation de stockage Azure File Sync et le désinscrire."
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: wgries
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 0acf183fbaea99e4316b668a3da28d79b20b7bef
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="registerunregister-a-server-with-azure-file-sync-preview"></a>Inscrire/désinscrire un serveur à Azure File Sync (préversion)
Avec Azure File Sync (préversion), les partages peuvent être répliqués localement ou dans Azure, et sont accessibles via des partages SMB ou NFS sur Windows Server. Azure File Sync est utile dans les scénarios où les données doivent être consultées et modifiées loin d’un centre de données Azure, par exemple, dans une succursale. Les données peuvent être répliquées entre plusieurs points de terminaison Windows Server, c’est-à-dire entre plusieurs succursales.

L’article suivant décrit comment inscrire un serveur au service de synchronisation de stockage et le désinscrire. Vous pouvez en avoir besoin quand un serveur doit être mis hors service ou que vous voulez introduire un nouveau point de terminaison de serveur dans un groupe de synchronisation. Consultez [Guide pratique pour déployer Azure File Sync (préversion)](storage-sync-files-deployment-guide.md) pour plus d’informations le déploiement de bout en bout d’Azure File Sync.

## <a name="prerequisites"></a>Prérequis
Pour inscrire un serveur Windows Server à un service de synchronisation de stockage, vous devez d’abord préparer un serveur Windows Server avec les prérequis nécessaires :

* Vérifiez qu’un service de synchronisation de stockage a été déployé. Pour plus d’informations sur le déploiement d’un service de synchronisation de stockage, consultez [Guide pratique pour déployer Azure File Sync (préversion)](storage-sync-files-deployment-guide.md).
* Vérifiez que le serveur est connecté à Internet et qu’Azure est accessible.
* Désactivez la Configuration de sécurité renforcée d’Internet Explorer pour les administrateurs avec l’interface utilisateur du Gestionnaire de serveur.
    
    ![Interface utilisateur du Gestionnaire de serveur avec la Configuration de sécurité renforcée d’Internet Explorer en surbrillance](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Vérifiez que le module AzureRM PowerShell est installé sur votre serveur. Si votre serveur est membre d’un cluster de basculement, chaque nœud du cluster nécessite le module AzureRM. Plus d’informations sur l’installation du module AzureRM sont disponibles dans [Installer et configurer Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

    > [!Note]  
    > Nous vous recommandons d’utiliser la version la plus récente du module AzureRM PowerShell pour inscrire/désinscrire un serveur. Si le package AzureRM a été installé précédemment sur ce serveur (et que la version de PowerShell sur ce serveur est 5.* ou supérieure), vous pouvez utiliser l’applet de commande `Update-Module` pour mettre à jour ce package. 

## <a name="register-a-server-with-storage-sync-service"></a>Inscrire un serveur au service de synchronisation de stockage
Pour utiliser un serveur Windows Server comme *point de terminaison de serveur* dans un *groupe de synchronisation* d’Azure File Sync, celui-ci doit être inscrit à un *service de synchronisation de stockage*. Un serveur peut être inscrit à un seul *service de synchronisation de stockage* à la fois.

### <a name="install-the-azure-file-sync-agent"></a>Installer l’agent Azure File Sync
1. [Téléchargez l’agent Azure File Sync](https://go.microsoft.com/fwlink/?linkid=858257).
2. Démarrez le programme d’installation de l’agent Azure File Sync.
    
    ![Première page du programme d’installation de l’agent Azure File Sync](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Vous devez activer les mises à jour de l’agent Azure File Sync à l’aide de Microsoft Update. Cette étape est importante, car les correctifs de sécurité critiques et les améliorations de fonctionnalités pour le package du serveur sont fournis via Microsoft Update.

    ![Vérifiez que Microsoft Update est activé dans le volet Microsoft Update du programme d’installation de l’agent Azure File Sync](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Si le serveur n’est pas déjà inscrit, l’interface utilisateur d’inscription de serveur s’affiche immédiatement une fois que l’installation est terminée.

> [!Important]  
> Si le serveur est membre d’un cluster de basculement, l’agent Azure File Sync doit être installé sur chaque nœud du cluster.

### <a name="register-the-server-using-the-server-registration-ui"></a>Inscrire le serveur à l’aide de l’interface utilisateur d’inscription de serveur
1. Si l’interface utilisateur d’inscription de serveur n’a pas démarré immédiatement après la fin de l’installation de l’agent Azure File Sync, elle peut être démarrée manuellement en exécutant `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`.
2. Cliquez sur *Connexion* pour accéder à votre abonnement Azure. 

    ![Ouverture de la boîte de dialogue de l’interface utilisateur d’inscription de serveur](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Choisissez un abonnement, un groupe de ressources et un service de synchronisation de stockage dans la boîte de dialogue.

    ![Informations du service de synchronisation de stockage](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. Dans la préversion, vous devez vous connecter une nouvelle fois pour terminer le processus. 

    ![Boîte de dialogue de connexion](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Si le serveur est membre d’un cluster de basculement, chaque serveur doit exécuter l’inscription de serveur. Quand vous affichez les serveurs inscrits dans le portail Azure, Azure File Sync reconnaît automatiquement chaque nœud comme un membre du même cluster de basculement et les regroupe en conséquence.

## <a name="unregister-the-server-with-storage-sync-service"></a>Désinscrire un serveur du service de synchronisation de stockage
Plusieurs étapes sont nécessaires pour désinscrire un serveur du service de synchronisation de stockage. Voyons comment désinscrire correctement un serveur.

### <a name="optional-recall-all-tiered-data"></a>(Facultatif) Rappeler toutes les données hiérarchisées
Quand la hiérarchisation cloud est activée pour un point de terminaison de serveur, elle *hiérarchise* les fichiers dans des partages de fichiers Azure. Cela permet aux partages de fichiers locaux de faire office de cache, au lieu d’effectuer une copie complète du jeu de données, et d’utiliser efficacement l’espace sur le serveur de fichiers. Toutefois, si un point de terminaison de serveur est supprimé alors que des fichiers hiérarchisés sont toujours localement sur le serveur, ces fichiers deviennent inaccessibles. Par conséquent, si vous voulez un accès continu aux fichiers, vous devez rappeler tous les fichiers hiérarchisés dans Azure Files avant de poursuivre la désinscription. 

Cette opération peut être effectuée avec l’applet de commande PowerShell comme indiqué ci-dessous :

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> Si le volume local qui héberge le serveur n’a pas assez d’espace libre pour rappeler toutes les données hiérarchisées, l’applet de commande `Invoke-StorageSyncFileRecall` échoue.  

### <a name="remove-the-server-from-all-sync-groups"></a>Supprimer le serveur de tous les groupes de synchronisation
Avant de désinscrire le serveur du service de synchronisation de stockage, tous les points de terminaison de serveur doivent être supprimés pour ce serveur. Vous pouvez le faire dans le portail :

1. Accédez au service de synchronisation de stockage auquel votre serveur est inscrit.
2. Supprimez tous les points de terminaison de serveur pour ce serveur dans chaque groupe de synchronisation du service de synchronisation de stockage. Pour ce faire, vous pouvez cliquer avec le bouton droit sur le point de terminaison de serveur concerné dans le volet Groupe de synchronisation.

    ![Suppression d’un point de terminaison de serveur dans un groupe de synchronisation](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Cette opération peut également être effectuée avec un script PowerShell simple :

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"

$accountInfo = Login-AzureRmAccount
Login-AzureRmStorageSync -SubscriptionId $accountInfo.Context.Subscription.Id -TenantId $accountInfo.Context.Tenant.Id -ResourceGroupName "<your-resource-group>"

$StorageSyncService = "<your-storage-sync-service>"

Get-AzureRmStorageSyncGroup -StorageSyncServiceName $StorageSyncService | ForEach-Object { 
    $SyncGroup = $_; 
    Get-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name | Where-Object { $_.DisplayName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name -ServerEndpointName $_.Name 
    } 
}
```

### <a name="unregister-the-server"></a>Désinscrire le serveur
À présent que toutes les données ont été rappelées et que le serveur a été supprimé de tous les groupes de synchronisation, il peut être désinscrit. 

1. Dans le portail Azure, accédez à la section « Serveurs inscrits » du service de synchronisation de stockage.
2. Cliquez avec le bouton droit sur le serveur que vous voulez désinscrire et cliquez sur « Désinscrire le serveur ».

    ![Désinscrire le serveur](media/storage-sync-files-server-registration/unregister-server-1.png)
