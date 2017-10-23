---
title: "Résoudre les problèmes de synchronisation de fichiers Azure (préversion) | Microsoft Docs"
description: "Résoudre les problèmes courants de synchronisation de fichiers Azure"
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
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 1ea7956e92dbc85f62383e4b041c4c830599f765
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-file-sync-preview"></a>Résoudre les problèmes de synchronisation de fichiers Azure (préversion)
La synchronisation de fichiers Azure (préversion) vous permet de centraliser les partages de fichiers de votre organisation dans Azure Files sans perdre la flexibilité, le niveau de performance et la compatibilité d’un serveur de fichiers local. Pour cela, elle transforme vos serveurs Windows Server en un cache rapide de votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible sur Windows Server pour accéder à vos données localement (y compris SMB, NFS et FTPS) et vous pouvez avoir autant de caches que nécessaire dans le monde entier.

Cet article est conçu pour vous aider à dépanner et à résoudre les problèmes rencontrés avec le déploiement de la synchronisation de fichiers Azure. Sinon, ce guide montre comment collecter les journaux importants sur le système pour permettre un examen plus approfondi des problèmes. Les options suivantes sont disponibles pour obtenir un support concernant la synchronisation de fichiers Azure :

- Support Microsoft : pour créer une nouvelle demande de support, accédez à l’onglet « Aide + support » sur le Portail Azure et cliquez sur « Nouvelle demande de support ».
- [Forum du Stockage Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=windowsazuredata)

## <a name="how-to-troubleshoot-agent-installation-failures"></a>Résoudre les problèmes de défaillance lors de l’installation de l’agent
Si l’installation de l’agent de synchronisation de fichiers Azure est défaillant, exécutez la commande suivante dans une invite de commandes avec élévation de privilèges pour activer la journalisation lors de l’installation de l’agent :

```
StorageSyncAgent.msi /l*v Installer.log
```

Après l’échec de l’installation, vérifiez installer.log pour en déterminer la cause. 

> [!Note]  
> L’installation de l’agent échoue si vous choisissez d’utiliser Microsoft Update et que le service Windows Update n’est pas en cours d’exécution.

## <a name="cloud-endpoint-creation-fails-with-the-following-error-the-specified-azure-fileshare-is-already-in-use-by-a-different-cloudendpoint"></a>La création du point de terminaison cloud échoue avec l’erreur suivante : « Le partage de fichiers Azure spécifié est déjà en cours d’utilisation par un autre point de terminaison cloud ».
Cette erreur se produit si le partage de fichiers Azure est déjà en cours d’utilisation par un autre point de terminaison cloud. 

Si vous obtenez cette erreur et que le partage de fichiers Azure n’est pas en cours d’utilisation par un point de terminaison cloud, effectuez les étapes ci-dessous pour effacer les métadonnées de synchronisation de fichiers Azure sur le partage de fichiers Azure :

> [!Warning]  
> La suppression des métadonnées sur un partage de fichiers Azure en cours d’utilisation par un point de terminaison cloud entraîne l’échec des opérations de synchronisation de fichiers Azure. 

1. Accédez à votre partage de fichiers Azure dans le Portail Azure.  
2. Cliquez avec le bouton droit sur le partage de fichiers Azure, puis sélectionnez **Modifier les métadonnées**.
3. Cliquez avec le bouton droit sur SyncService, puis sélectionnez **Supprimer**.

## <a name="server-is-not-listed-under-registered-servers-in-the-azure-portal"></a>Le serveur n’est pas listé sous Serveurs inscrits sur le Portail Azure
Si un serveur n’est pas listé sous Serveurs inscrits pour un service de synchronisation du stockage, suivez les étapes ci-dessous :
1. Connectez-vous au serveur que vous souhaitez inscrire.
2. Ouvrez l’Explorateur de fichiers et accédez au répertoire d’installation de l’Agent de synchronisation du stockage (emplacement par défaut : `C:\Program Files\Azure\StorageSyncAgent`). 
3. Exécutez ServerRegistration.exe et suivez l’Assistant pour inscrire le serveur auprès d’un service de synchronisation du stockage.

## <a name="server-registration-displays-the-following-message-after-installing-the-azure-file-sync-agent-this-server-is-already-registered"></a>L’inscription du serveur affiche le message suivant après l’installation de l’Agent de synchronisation de fichiers Azure : « Ce serveur est déjà inscrit ».
![Capture d’écran de la boîte de dialogue d’inscription du serveur avec le message d’erreur « Ce serveur est déjà inscrit »](media/storage-sync-files-troubleshoot/server-registration-1.png)

Ce message s’affiche si le serveur a déjà été inscrit auprès d’un service de synchronisation du stockage. Pour désinscrire le serveur auprès du service de synchronisation du stockage actuel et l’inscrire auprès d’un nouveau service de synchronisation du stockage, suivez les étapes de la page [Désinscrire un serveur de la synchronisation de fichiers Azure](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Si le serveur n’est pas répertorié sous Serveurs inscrits dans le service de synchronisation de stockage, exécutez les commandes PowerShell suivantes sur le serveur dont vous souhaitez annuler l’inscription :

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Si le serveur fait partie d’un cluster, vous disposez d’un paramètre `Reset-StorageSyncServer -CleanClusterRegistration` facultatif pour supprimer également l’inscription du cluster. Pensez à utiliser ce commutateur quand vous annulez l’inscription du dernier nœud du cluster.

## <a name="how-to-troubleshoot-sync-not-working-on-a-server"></a>Résoudre les problèmes de synchronisation sur un serveur
Si la synchronisation échoue sur un serveur, effectuez les opérations ci-dessous :
- Vérifiez qu’il existe un serveur de point de terminaison sur le Portail Azure pour le répertoire que vous voulez synchroniser sur un partage de fichiers Azure :
    
    ![Capture d’écran d’un groupe de synchronisation avec un point de terminaison de serveur et cloud sur le Portail Azure](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

- Vérifiez les journaux des événements opérationnels et des événements de diagnostic avec l’observateur d’événements, situés sous `Applications and Services\Microsoft\FileSync\Agent`.
- Vérifiez que le serveur possède une connexion Internet.
- Vérifiez que le service de synchronisation de fichiers Azure est en cours d’exécution sur le serveur en ouvrant le composant logiciel enfichable MMC des services, et vérifiez que le service de l’Agent de synchronisation du stockage (FileSyncSvc) est en cours d’exécution.

## <a name="how-to-troubleshoot-individual-files-failing-to-sync"></a>Résoudre les problèmes de synchronisation de fichiers isolés 
Si la synchronisation échoue sur des fichiers isolés, effectuez les opérations ci-dessous :
- Vérifiez les journaux des événements opérationnels et des événements de diagnostic sous `Applications and Services\Microsoft\FileSync\Agent` dans l’observateur d’événements.
- Vérifiez que le fichier ne contient pas de descripteurs ouverts.
    - Remarque : La synchronisation de fichiers Azure prend régulièrement des instantanés VSS pour synchroniser les fichiers avec descripteurs ouverts.

## <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>Résoudre les problèmes de hiérarchisation de fichiers
Si la hiérarchisation d’un ou plusieurs fichiers échoue sur Azure Files, effectuez les opérations ci-dessous :

- Vérifiez que le ou les fichiers se trouvent dans le partage de fichiers Azure.
    - Remarque : Un fichier doit être synchronisé à un partage de fichiers Azure avant d’être hiérarchisé.
- Vérifiez les journaux des événements opérationnels et des événements de diagnostic, situés sous `Applications and Services\Microsoft\FileSync\Agent` dans l’observateur d’événements.
- Vérifiez que le serveur possède une connexion Internet. 
- Vérifiez que les pilotes de filtrage de la synchronisation de fichiers Azure (StorageSync.sys & StorageSyncGuard.sys) sont en cours d’exécution.
    - Ouvrez une invite de commandes avec élévation de privilèges, exécutez `fltmc` et vérifiez que les pilotes de filtrage du système de fichiers StorageSync.sys et StorageSyncGuard.sys sont listés.

## <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>Résoudre les problèmes de rappel de fichiers
Si le rappel de certains fichiers échoue, effectuez les opérations ci-dessous :
- Vérifiez les journaux des événements opérationnels et des événements de diagnostic, situés sous `Applications and Services\Microsoft\FileSync\Agent` dans l’observateur d’événements.
- Vérifiez que le ou les fichiers se trouvent dans le partage de fichiers Azure.
- Vérifiez que le serveur possède une connexion Internet. 
- Vérifiez que les pilotes de filtrage de la synchronisation de fichiers Azure (StorageSync.sys & StorageSyncGuard.sys) sont en cours d’exécution.
    - Ouvrez une invite de commandes avec élévation de privilèges, exécutez `fltmc` et vérifiez que les pilotes de filtrage du système de fichiers StorageSync.sys et StorageSyncGuard.sys sont listés.

## <a name="how-to-troubleshoot-files-being-unexpectedly-recalled-on-a-server"></a>Résoudre les problèmes de rappel de fichiers inattendu sur un serveur
Les antivirus, applications de sauvegarde et autres applications qui lisent un grand nombre de fichiers provoquent des rappels indésirables, sauf s’ils respectent l’attribut hors connexion et ignorent la lecture du contenu de ces fichiers. Ignorer les fichiers hors connexion pour les produits compatibles permet d’éviter les rappels indésirables lors de l’exécution d’opérations comme des analyses antivirus ou des travaux de sauvegarde.

Contactez votre éditeur de logiciels pour savoir comment configurer leur solution de façon à ignorer la lecture des fichiers hors connexion.

D’autres rappels indésirables peuvent se produire dans d’autres scénarios, comme la recherche de fichiers dans l’Explorateur de fichiers. L’ouverture d’un dossier avec des fichiers cloud hiérarchisés dans l’Explorateur de fichiers sur le serveur peut entraîner des rappels indésirables, surtout si un antivirus est activé sur le serveur.

## <a name="general-troubleshooting"></a>Résolution générale des problèmes
Si vous rencontrez des problèmes avec la synchronisation de fichiers Azure sur un serveur, commencez par effectuer les opérations suivantes :
- Vérifiez les journaux des événements de diagnostic et des événements opérationnels dans l’observateur d’événements.
    - Les problèmes de synchronisation, de hiérarchisation et de rappel sont enregistrés dans les journaux des événements de diagnostic et des événements opérationnels sous `Applications and Services\Microsoft\FileSync\Agent`.
    - Les problèmes de gestion d’un serveur (par exemple, les paramètres de configuration) sont enregistrés dans les journaux des événements de diagnostic et des événements opérationnels sous `Applications and Services\Microsoft\FileSync\Management`.
- Vérifiez que le service de synchronisation de fichiers Azure est en cours d’exécution sur le serveur.
    - Ouvrez le composant logiciel enfichable MMC des services et vérifiez que le service de l’Agent de synchronisation du stockage (FileSyncSvc) est en cours d’exécution.
- Vérifiez que les pilotes de filtrage de la synchronisation de fichiers Azure (StorageSync.sys & StorageSyncGuard.sys) sont en cours d’exécution.
    - Ouvrez une invite de commandes avec élévation de privilèges, exécutez fltmc et vérifiez que les pilotes de filtrage du système de fichiers StorageSync.sys et StorageSyncGuard.sys sont listés.

Si le problème n’est pas résolu après avoir effectué les opérations ci-dessus, exécutez l’outil AFSDiag en suivant les étapes ci-dessous :
1. Créez un répertoire qui servira à enregistrer la sortie d’AFSDiag (par exemple, C:\output).
2. Ouvrez une fenêtre PowerShell avec élévation de privilèges et exécutez les commandes suivantes (appuyez sur ENTRÉE après chaque commande) :

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: use the path created in step 1
    ```

3. Choisissez le niveau de trace 1 du mode noyau de la synchronisation de fichiers Azure (sauf indication contraire pour créer des traces plus détaillées) et appuyez sur ENTRÉE.
4. Choisissez le niveau de trace 1 du mode utilisateur de la synchronisation de fichiers Azure (sauf indication contraire pour créer des traces plus détaillées) et appuyez sur ENTRÉE.
5. Reproduisez le problème, puis appuyez sur D lorsque c’est fait.
6. Un fichier .zip contenant les journaux et les fichiers de trace sera créé dans le répertoire de sortie spécifié.