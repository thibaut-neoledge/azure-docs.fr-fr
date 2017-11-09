---
title: "Planification d’un déploiement Azure File Sync (préversion) | Microsoft Docs"
description: "Découvrez les éléments à prendre en compte lors de la planification d’un déploiement Azure Files."
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
ms.openlocfilehash: d626f71aa21cea562ef6c9554c05e6de027e7f4d
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2017
---
# <a name="planning-for-an-azure-file-sync-preview-deployment"></a>Planification d’un déploiement Azure File Sync (préversion)
La synchronisation de fichiers Azure (préversion) vous permet de centraliser les partages de fichiers de votre organisation dans Azure Files sans perdre la flexibilité, le niveau de performance et la compatibilité d’un serveur de fichiers local. Pour cela, elle transforme vos serveurs Windows Server en un cache rapide de votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible sur Windows Server pour accéder à vos données localement (y compris SMB, NFS et FTPS) et vous pouvez avoir autant de caches que nécessaire dans le monde entier.

Ce guide décrit les actions à prendre en compte pour déployer Azure File Sync. Nous vous recommandons de lire les tests du guide [Planification d’un déploiement Azure Files](storage-files-planning.md). 

## <a name="understanding-azure-file-sync-terminology"></a>Présentation de la terminologie Azure File Sync
Avant d’aborder les détails d’Azure File Sync, il est important de connaître la terminologie.

### <a name="storage-sync-service"></a>Service de synchronisation de stockage
Le service de synchronisation de stockage est la ressource Azure de niveau supérieur qui représente Azure File Sync. La ressource de service de synchronisation de stockage est un pair de la ressource de compte de stockage. Elle peut être déployée de la même façon dans des groupes de ressources Azure. Une ressource de niveau supérieur distincte est nécessaire pour le compte de stockage, car le service de synchronisation de stockage peut créer des relations de synchronisation avec plusieurs comptes de stockage via plusieurs groupes de synchronisation. Un abonnement peut avoir plusieurs ressources de service de synchronisation de stockage déployées.

### <a name="sync-group"></a>Groupe de synchronisation
Un groupe de synchronisation définit la topologie de synchronisation d’un ensemble de fichiers. Les points de terminaison dans un groupe de synchronisation sont synchronisés entre eux. Par exemple, si vous avez deux ensembles distincts de fichiers que vous voulez gérer avec un partage de fichiers Azure, vous créez deux groupes de synchronisation et leur ajoutez à chacun des points de terminaison différents. Un service de synchronisation de stockage peut héberger autant de groupes de synchronisation que nécessaire.  

### <a name="registered-server"></a>Serveur inscrit
L’objet Serveur inscrit représente une relation d’approbation entre votre serveur (ou cluster) et le service de synchronisation de stockage. Vous pouvez inscrire autant de serveurs à une instance de service de synchronisation de stockage que vous le souhaitez. Toutefois, un serveur (ou cluster) ne peut être inscrit qu’à un seul service de synchronisation de stockage à un instant donné.

### <a name="azure-file-sync-agent"></a>Agent Azure File Sync
L’agent Azure File Sync est un package téléchargeable qui permet à un serveur Windows Server de se synchroniser avec un partage de fichiers Azure. L’agent Azure File Sync est constitué de trois composants principaux : 
- **FileSyncSvc.exe** : Service Windows en arrière-plan responsable de la surveillance des changements intervenant sur les points de terminaison de serveur et du lancement des sessions de synchronisation sur Azure.
- **StorageSync.sys** : Filtre du système de fichiers Azure File Sync, responsable de la hiérarchisation des fichiers dans Azure Files (quand la hiérarchisation cloud est activée).
- **Applets de commande de gestion PowerShell** : Applets de commande PowerShell permettant d’interagir avec le fournisseur de ressources Azure Microsoft.StorageSync. Ces composants se trouvent aux emplacements suivants (par défaut) :
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Point de terminaison de serveur
Un point de terminaison de serveur représente un emplacement spécifique sur un serveur inscrit, comme un dossier sur un volume de serveur ou la racine du volume. Plusieurs points de terminaison de serveur peuvent se trouver sur le même volume si leurs espaces de noms ne se chevauchent pas (par ex., F:\sync1 et F:\sync2). Vous pouvez configurer des stratégies de hiérarchisation cloud individuellement pour chaque point de terminaison de serveur. Si vous ajoutez un emplacement de serveur avec un ensemble de fichiers existants comme point de terminaison de serveur à un groupe de synchronisation, ces fichiers sont fusionnés avec tout autre fichier déjà présent sur les autres points de terminaison dans le groupe de synchronisation.

### <a name="cloud-endpoint"></a>Point de terminaison cloud
Un point de terminaison cloud est un partage de fichiers Azure qui fait partie d’un groupe de synchronisation. L’intégralité du partage de fichiers Azure se synchronise. Un partage de fichiers Azure peut uniquement être membre d’un seul point de terminaison cloud et, par conséquent, d’un seul groupe de synchronisation. Si vous ajoutez un partage de fichiers Azure avec un ensemble de fichiers existants comme point de terminaison de serveur à un groupe de synchronisation, ces fichiers sont fusionnés avec tout autre fichier déjà présent sur les autres points de terminaison dans le groupe de synchronisation.

> [!Important]  
> Azure File Sync prend en charge les modifications apportées directement au partage de fichiers Azure. Cependant, toute modification apportée directement au partage de fichiers Azure doit être détectée au préalable par une tâche de détection de modification d’Azure File Sync, qui est lancée pour un point de terminaison cloud seulement une fois toutes les 24 heures. Visitez le [FAQ d’Azure Files](storage-files-faq.md#afs-change-detection) pour plus d’informations.

### <a name="cloud-tiering"></a>Hiérarchisation cloud 
La hiérarchisation cloud est une fonctionnalité facultative d’Azure File Sync, qui permet aux fichiers rarement utilisés d’être hiérarchisés dans Azure Files. Quand un fichier est hiérarchisé, le filtre du système de fichiers Azure File Sync (StorageSync.sys) remplace le fichier localement par un pointeur, ou point d’analyse, qui représente une URL vers le fichier dans Azure Files. Un fichier hiérarchisé a l’attribut « hors connexion » défini dans NTFS, pour que les applications tierces puissent l’identifier. Quand un utilisateur ouvre un fichier hiérarchisé, Azure File Sync rappelle sans interruption les données de fichier dans Azure Files sans que l’utilisateur ait besoin de savoir que le fichier n’est pas stocké localement sur le système. Cette fonctionnalité est également appelée Gestion hiérarchique du stockage.

## <a name="azure-file-sync-interoperability"></a>Interopérabilité d’Azure File Sync 
Cette section traite de l’interopérabilité de la synchronisation de fichiers Azure avec les fonctionnalités et rôles de Windows Server et les solutions tierces.

### <a name="supported-versions-of-windows-server"></a>Versions de Windows Server prises en charge
Pour le moment, les versions prises en charge de Windows Server par Azure File Sync sont les suivantes :

| Version | Références prises en charge | Options de déploiement prises en charge |
|---------|----------------|------------------------------|
| Windows Server 2016 | Datacenter et Standard | Complète (serveur avec une interface utilisateur) |
| Windows Server 2012 R2 | Datacenter et Standard | Complète (serveur avec une interface utilisateur) |

Les futures versions de Windows Server seront ajoutées à mesure de leur publication, et les versions antérieures de Windows peuvent être ajoutées en fonction des commentaires des utilisateurs.

> [!Important]  
> Nous vous recommandons de mettre à jour avec les dernières mises à jour de Windows Update tous les serveurs Windows Server que vous utilisez avec la synchronisation de fichiers Azure. 

### <a name="file-system-features"></a>Fonctionnalités du système de fichiers
| Fonctionnalité | État de la prise en charge | Remarques |
|---------|----------------|-------|
| Listes de contrôle d’accès (ACL) | Entièrement pris en charge | Les listes ACL Windows sont conservées par Azure File Sync et appliquées par Windows Server sur les points de terminaison de serveur. Les listes ACL Windows ne sont pas (encore) prises en charge par Azure Files si les fichiers sont accessibles directement dans le cloud. |
| Liens physiques | Ignoré | |
| Liens symboliques | Ignoré | |
| Points de montage | Partiellement pris en charge | Les points de montage peuvent être la racine d’un point de terminaison de serveur, mais il sont ignorés s’ils sont contenus dans l’espace de noms du point de terminaison de serveur. |
| Jonctions | Ignoré | |
| Points d’analyse | Ignoré | |
| Compression NTFS | Entièrement pris en charge | |
| Fichiers partiellement alloués | Entièrement pris en charge | Les fichiers partiellement alloués sont synchronisés (ils ne sont pas bloqués), mais ils sont synchronisés dans le cloud sous forme de fichier complet. Si le contenu du fichier change dans le cloud (ou sur un autre serveur), le fichier n’est plus partiellement alloué quand le changement est téléchargé |
| Autres flux de données | Conservés, mais pas synchronisés | |

> [!Note]  
> Seuls les volumes NTFS sont pris en charge.

### <a name="failover-clustering"></a>Clustering de basculement
Le clustering de basculement Windows Server est pris en charge par Azure File Sync pour l’option de déploiement « Serveur de fichiers pour une utilisation générale ». Le clustering de basculement n’est pas pris en charge sur le « serveur de fichiers avec montée en puissance parallèle pour les données d’application » (SOFS) ou sur les volumes partagés de cluster.

> [!Note]  
> L’agent Azure File Sync doit être installé sur chaque nœud d’un cluster de basculement pour que la synchronisation fonctionne correctement.

### <a name="windows-server-data-deduplication"></a>Déduplication des données Windows Server
Pour les volumes pour lesquels la hiérarchisation cloud n’est pas activée, Azure File Sync prend en charge la déduplication des données quand elle est activée sur le volume. Pour le moment, nous ne prenons pas en charge l’interopérabilité entre Azure File Sync avec hiérarchisation cloud et la déduplication des données.

### <a name="anti-virus-solutions"></a>Solutions antivirus
Parce que les antivirus fonctionnent en recherchant dans les fichiers du code malveillant connu, ils peuvent entraîner le rappel des fichiers hiérarchisés. Comme les fichiers hiérarchisés ont l’attribut « hors connexion » défini, nous vous recommandons de consulter votre fournisseur de logiciels concernant la configuration de leur solution pour ignorer la lecture des fichiers hors connexion. 

Les solutions suivantes peuvent ignorer les fichiers hors connexion :

- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [McAfee EndPoint Security](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) (consultez la section « Scan only what you need to » à la page 90)
- [Kaspersky Anti-Virus](https://support.kaspersky.com/4684)
- [Sophos Endpoint Protection](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>Solutions de sauvegarde
Tout comme les solutions antivirus, les solutions de sauvegarde peuvent entraîner le rappel des fichiers hiérarchisés. Nous vous recommandons d’utiliser une solution de sauvegarde cloud pour sauvegarder le partage de fichiers Azure plutôt qu’un produit de sauvegarde locale.

### <a name="encryption-solutions"></a>Solutions de chiffrement
La prise en charge des solutions de chiffrement dépend de la façon dont elles sont implémentées. Azure File Sync est connu pour fonctionner avec les solutions suivantes :

- Chiffrement BitLocker
- Azure RMS (et Active Directory RMS hérité)

Azure File Sync est connu pour ne pas fonctionner avec les solutions suivantes :

- Système de fichiers chiffrés NTFS (EFS)

En général, Azure File Sync doit pouvoir fonctionner avec les solutions de chiffrement qui se trouvent sous le système de fichiers, comme BitLocker, et les solutions implémentées dans le format de fichier, comme BitLocker, mais aucune interopérabilité en particulier n’a été effectuée pour interagir avec les solutions qui se trouvent au-dessus du système de fichiers (par exemple, le système de fichiers chiffrés NTFS).

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Autres solutions de gestion hiérarchique du stockage (HSM)
Aucune autre solution de gestion hiérarchique du stockage ne doit être utilisée avec Azure File Sync.

## <a name="region-availability"></a>Disponibilité des régions
Azure File Sync est disponible en préversion uniquement dans les régions suivantes :

| Région | Emplacement du centre de données |
|--------|---------------------|
| Ouest des États-Unis | Californie, États-Unis |
| Europe de l'Ouest | Pays-bas |
| Asie du Sud-Est | Singapour |
| Est de l’Australie | Nouvelle-Galles du Sud, Australie |

Dans la préversion, nous prenons uniquement en charge la synchronisation avec un partage de fichiers Azure dans la même région que le service de synchronisation de stockage.

## <a name="azure-file-sync-agent-update-policy"></a>Stratégie de mise à jour de l’agent Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Étapes suivantes
* [Planification d’un déploiement Azure Files](storage-files-planning.md)
* [Déploiement d’Azure Files](storage-files-deployment-guide.md)
* [Déploiement d’Azure File Sync](storage-sync-files-deployment-guide.md)
