---
title: Forum Aux Questions (FAQ) sur Azure Files | Microsoft Docs
description: "Trouvez des réponses aux questions fréquemment posées sur Azure Files."
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/13/2017
ms.author: renash
ms.openlocfilehash: 91c46ea0897f83811cfa5c1a8b67677caff14569
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2017
---
# <a name="frequently-asked-questions-about-azure-files"></a>Forum Aux Questions (FAQ) sur Azure Files
[Azure Files](storage-files-introduction.md) offre des partages de fichiers managés dans le cloud qui sont accessibles via le [protocole SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) standard du secteur (également connu sous le nom de Common Internet File System ou CIFS). Azure Files peut être monté simultanément sur des déploiements cloud ou locaux de Windows, macOS et Linux. En outre, les partages de fichiers Azure peuvent être mis en cache sur les serveurs Windows avec Azure File Sync (préversion) pour un accès rapide à proximité de l’endroit où les données sont utilisées.

Cet article couvre certaines des questions fréquemment posées sur les fonctionnalités d’Azure Files, notamment concernant Azure File Sync. Si vous ne trouvez pas de réponse à votre question ici, n’hésitez pas à nous contacter via les méthodes suivantes (par ordre de priorité) :

1. Dans la section Commentaires de cet article.
2. [Forum du Stockage Azure](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredata)
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 
4. Support Microsoft : pour créer une nouvelle demande de support, accédez à l’onglet « Aide + support » sur le Portail Azure et cliquez sur « Nouvelle demande de support ».

## <a name="general"></a>Généralités
* <a id="why-files-useful"></a>**Pourquoi Azure Files est-il utile ?**  
   Azure Files vous permet de créer des partages de fichiers dans le cloud sans avoir à gérer la charge d’un appareil, d’une appliance ou d’un serveur physique. Cela signifie que vous pouvez passer moins de temps à appliquer les mises à jour du système d’exploitation et à remplacer les disques défectueux, travail monotone que nous faisons entièrement pour vous. Pour en savoir plus sur les scénarios qui se prêtent à l’utilisation d’Azure Files, consultez [Pourquoi Azure Files est-il utile ?](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>**Quelles sont les différentes façons d’accéder aux fichiers dans Azure Files ?**  
    Vous pouvez monter le partage de fichiers sur votre ordinateur local à l’aide du protocole SMB 3.0, ou vous servir d’outils tels que l’[Explorateur de stockage](http://storageexplorer.com/) pour accéder aux fichiers dans votre partage de fichiers. À partir de votre application, vous pouvez utiliser des bibliothèques clientes de stockage, des API REST, PowerShell ou des interfaces CLI pour accéder à vos fichiers dans le partage de fichiers Azure.

* <a id="what-is-afs"></a>**Qu’est-ce qu’Azure File Sync ?**  
    Azure File Sync vous permet de centraliser les partages de fichiers de votre organisation dans Azure Files sans perdre la flexibilité, le niveau de performance et la compatibilité d’un serveur de fichiers local. Pour cela, elle transforme vos serveurs Windows Server en un cache rapide de votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible sur Windows Server pour accéder à vos données localement (y compris SMB, NFS et FTPS) et vous pouvez avoir autant de caches que nécessaire dans le monde entier.

* <a id="files-versus-blobs"></a>**Pourquoi utiliser un partage de fichiers Azure plutôt que le stockage blob Azure pour mes données ?**  
    Azure Files et le stockage Blob Azure permettent tous les deux de stocker de grandes quantités de données dans le cloud, mais sont utiles à des fins légèrement différentes. Le stockage Blob Azure est utile pour les applications cloud à grande échelle qui doivent stocker des données non structurées. Le stockage Blob Azure étant une abstraction de stockage plus simple qu’un véritable système de fichiers, le niveau de performance et la mise à l’échelle s’en trouvent optimisés. En outre, le stockage Blob Azure peut n’être accessible que via des bibliothèques clientes basées sur REST (ou directement via le protocole basé sur REST).

    De son côté, Azure Files a pour vocation d’être un système de fichiers, avec tous les fichiers abstraits issus des systèmes d’exploitation locaux et que vous appréciez depuis des années. Comme le stockage Blob Azure, Azure Files offre une interface REST et des bibliothèques clientes basées sur REST ; toutefois, il s’en différencie en proposant également un accès SMB aux partages de fichiers Azure. Cela signifie que vous pouvez monter un partage de fichiers Azure directement sur des machines virtuelles cloud ou locales Windows, Linux ou macOS, sans avoir à écrire du code ou à joindre des pilotes spéciaux au système de fichiers. En outre, les partages de fichiers Azure peuvent être mis en cache sur les serveurs de fichiers locaux à l’aide de la synchronisation de fichiers Azure pour un accès rapide à proximité de l’endroit où les données sont utilisées. 
   
    Pour une explication plus approfondie des différences entre Azure Files et le stockage Blob Azure, consultez [Quand utiliser le stockage Blob Azure, Azure Files ou Disques Azure](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Pour en savoir plus sur le stockage Blob Azure, consultez [Présentation du Stockage Blob](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Pourquoi utiliser un partage de fichiers Azure plutôt que des disques Azure ?**  
    Un disque Azure est tout simplement un disque. Un disque autonome n’est pas en soi très utile : pour tirer parti d’un disque Azure, vous devez le joindre à la machine virtuelle s’exécutant dans Azure. Vous pouvez utiliser un disque Azure pour toute opération pour laquelle vous utiliseriez un disque sur un serveur local : en tant que disque de système d’exploitation, espace d’échange pour un système d’exploitation ou stockage dédié à une application. Vous pouvez notamment recourir à des disques Azure pour créer un serveur de fichiers dans le cloud et l’utiliser là ou vous utiliseriez un partage de fichiers Azure. Déployer un serveur de fichiers dans des machines virtuelles Azure est un moyen très efficace pour obtenir un stockage de fichiers dans Azure quand vous avez besoin d’options de déploiement qui ne sont pas prises en charge par Azure Files (telles que la prise en charge du protocole NFS ou le stockage premium). 

    En revanche, exécuter un serveur de fichiers avec des disques Azure en tant que stockage backend est généralement beaucoup plus onéreux qu’utiliser un partage de fichiers Azure, et ce pour plusieurs raisons. Tout d’abord, outre le stockage sur disque, vous devez payer les dépenses liées à l’exécution d’une ou plusieurs machines virtuelles Azure. En second lieu, vous devez gérer les machines virtuelles utilisées pour exécuter le serveur de fichiers, entre autres en prenant en charge les mises à niveau du système d’exploitation. Enfin, si des données doivent être mises en cache localement, il vous appartient de configurer et de gérer des technologies de réplication (telles que la réplication DFS).

    Une approche intéressante pour tirer parti d’Azure Files et d’un serveur de fichiers hébergé sur des machines virtuelles Azure avec des disques Azure comme stockage backend consiste à installer la synchronisation de fichiers Azure sur votre serveur de fichiers hébergé sur les machines virtuelles cloud. Si le partage de fichiers Azure se trouve dans la même région que votre serveur de fichiers, vous pouvez activer la hiérarchisation cloud et définir le pourcentage d’espace libre du volume sur la valeur maximale (99 %). Cela garantit une duplication minimale des données tout en vous permettant d’utiliser les applications que vous souhaitez par rapport à vos serveurs de fichiers, telles qu’une application exigeant la prise en charge du protocole NFS.

    Pour plus d’informations sur la manière d’obtenir un serveur de fichiers hautes performances et à disponibilité élevée dans Azure, consultez [Déploiement de clusters invités de machine virtuelle IaaS dans Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Pour une explication plus approfondie des différences entre Azure Files et Disques Azure, consultez [Quand utiliser le stockage Blob Azure, Azure Files ou Disques Azure](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Pour en savoir plus sur Disques Azure, consultez [Vue d’ensemble d’Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>**Comment commencer à utiliser Azure Files ?**  
    Pour rendre en main Azure Files, il vous suffit de [créer un partage de fichiers](storage-how-to-create-file-share.md) et de le monter sur le système d’exploitation de votre choix : 

    - [Montage sur Windows](storage-how-to-use-files-windows.md)
    - [Montage sur Linux](storage-how-to-use-files-linux.md)
    - [Montage sur macOS](storage-how-to-use-files-mac.md)

    Pour obtenir un guide détaillé sur le déploiement d’un partage de fichiers Azure afin de remplacer des partages de fichiers de production dans votre organisation, consultez [Planification d’un déploiement Azure Files](storage-files-planning.md).

* <a id="redundancy-options"></a>**Quelles sont les options de redondance de stockage prises en charge par Azure Files ?**  
    Azure Files ne prend en charge que le stockage localement redondant (LRS) et le stockage géoredondant (GRS). Nous envisageons la prise en charge du stockage redondant dans une zone (ZRS) et du stockage géographiquement redondant avec accès en lecture (RA-GRS), mais aucun calendrier n’est arrêté pour l’instant.

* <a id="tier-options"></a>**Quels niveaux de stockage sont actuellement pris en charge par Azure Files ?**  
    Actuellement, Azure Files prend uniquement en charge le niveau de stockage standard. Nous ne sommes pas en mesure de vous indiquer quand les niveaux de stockage Premium et à froid seront pris en charge. Vous ne pouvez pas créer des partages de fichiers Azure à partir de comptes de stockage d’objets blob uniquement ou de comptes de stockage Premium.

* <a id="give-us-feedback"></a>**Je souhaite vraiment que la fonctionnalité *x* soit ajoutée à Azure Files. Pouvez-vous l’ajouter ?**  
    Absolument, l’équipe Azure Files est à l’écoute de tous vos commentaires sur notre service. Veuillez voter pour les demandes de fonctionnalités sur le [forum UserVoice consacré à Azure Files](https://feedback.azure.com/forums/217298-storage/category/180670-files) ! Nous sommes impatients de mettre à votre disposition de nombreuses nouvelles fonctionnalités.

## <a name="azure-file-sync"></a>Azure File Sync
* <a id="afs-region-availability"></a>**Quelles sont les régions prises en charge par Azure File Sync (préversion) ?**  
    La synchronisation de fichiers Azure est disponible dans l’Ouest des États-Unis, l’Europe de l’Ouest, l’Est de l’Australie et l’Asie du Sud-Est. Nous ajouterons la prise en charge pour d’autres régions à mesure que nous progresserons vers la disponibilité générale. Pour plus d’informations, consultez [Disponibilité des régions](storage-sync-files-planning.md#region-availability).

* <a id="cross-domain-sync"></a>**Un même groupe de synchronisation peut-il contenir des serveurs joints à un domaine et des serveurs non joints à un domaine ?**  
    Oui, un groupe de synchronisation peut contenir des points de terminaison de serveur qui ont des appartenances Active Directory différentes, y compris comme le fait de ne pas être joints à un domaine. Bien que cette configuration fonctionne sur le plan technique, nous la déconseillons comme configuration standard, car les listes de contrôle d’accès (ACL, access-control list) définies pour les fichiers/dossiers sur un serveur peuvent ne pas être applicables par d’autres serveurs dans le groupe de synchronisation. Pour de meilleurs résultats, nous vous recommandons d’effectuer une synchronisation entre des serveurs au sein d’une même forêt Active Directory, des serveurs dans des forêts Active Directory différentes avec des relations d’approbation établies ou des serveurs n’appartenant pas un domaine, mais pas une combinaison de ces différentes configurations.

* <a id="afs-change-detection"></a>**J’ai créé un fichier directement dans mon partage de fichiers Azure sur SMB ou par le biais du portail. Combien de temps la synchronisation du fichier prend-elle sur les serveurs dans le groupe de synchronisation ?** 
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Quand un même fichier est modifié sur deux serveurs à peu près au même moment, que se passe-t-il ?**  
    La synchronisation de fichiers Azure utilise une stratégie de résolution de conflit simple : nous conservons les deux modifications. Le fichier le plus récemment écrit conserve son nom d’origine. Concernant l’ancien fichier, la machine « source » et le numéro de conflit sont ajoutés à son nom selon le format suivant : 
   
    `<FileNameWithoutExtension>-<MachineName>[-#].<ext>`  

    Par exemple, le premier conflit de `CompanyReport.docx` deviendrait `CompanyReport-CentralServer.docx` si `CentralServer` est l’endroit où l’écriture antérieure s’est produite. Le deuxième conflit serait identifié sous la forme `CompanyReport-CentralServer-1.docx`.

* <a id="afs-storage-redundancy"></a>**Le stockage géoredondant (GRS) est-il pris en charge par Azure File Sync ?**  
    Oui, le stockage localement redondant (LRS) et le stockage géoredondant (GRS) sont tous les deux pris en charge par la synchronisation de fichiers Azure. Dans le cas d’un basculement GRS entre des régions associées, nous vous recommandons de traiter la nouvelle région en tant que sauvegarde des données uniquement. La synchronisation de fichiers Azure ne démarre pas automatiquement la synchronisation avec la nouvelle région principale. 

* <a id="sizeondisk-versus-size"></a>**Pourquoi la propriété *Taille sur le disque* pour un fichier ne correspond-elle pas à la propriété *Taille* après l’utilisation d’Azure File Sync ?**  
    L’Explorateur de fichiers Windows expose deux propriétés, **Taille** et **Taille sur le disque** pour représenter la taille d’un fichier. Le sens de ces propriétés diffère légèrement ; **Taille** représente la taille complète du fichier, tandis que **Taille sur le disque** représente la taille du flux de fichier réellement stocké sur le disque. Ces tailles peuvent différer pour diverses raisons, telles que la compression, l’utilisation de la déduplication des données ou, dans le cas présent, la hiérarchisation cloud avec la synchronisation de fichiers Azure. Si un fichier est hiérarchisé sur un partage Azure Files, la taille sur le disque est égale à zéro, car le flux de fichier est stocké dans votre partage Azure File, pas sur le disque. Un fichier peut également être partiellement hiérarchisé (ou partiellement rappelé) ; dans ce cas, une partie du fichier est sur le disque. Cette opération peut se produire quand un fichier est partiellement lu par une application telle qu’un lecteur multimédia ou par un utilitaire de compression. 

* <a id="is-my-file-tiered"></a>**Comment puis-je savoir si un fichier a été hiérarchisé ?**  
    Vous pouvez déterminer de plusieurs façons si un fichier a été hiérarchisé sur votre partage de fichiers Azure :
    
    1. **Vérifier les attributs du fichier.** Pour ce faire, cliquez avec le bouton droit sur le fichier, accédez à **Détails** et faites défiler jusqu’à la propriété **Attributs**. Un fichier hiérarchisé a les attributs suivants définis :     
        
        | Lettre de l’attribut | Attribut | Définition |
        |:----------------:|-----------|------------|
        | A | Archivage | Indique que le fichier doit être sauvegardé par un logiciel de sauvegarde. Cet attribut est toujours défini, que le fichier soit hiérarchisé ou entièrement stocké sur le disque. |
        | P | Fichier partiellement alloué | Indique que le fichier est un fichier partiellement alloué ; offert par NTFS, ce type spécialisé de fichier est efficace quand le flux sur disque du fichier est pratiquement vide. La synchronisation de fichiers Azure utilise des fichiers partiellement alloués quand un fichier est entièrement hiérarchisé (son flux de fichier est stocké uniquement dans le cloud) ou partiellement rappelé (une partie du fichier est déjà sur le disque). Si un fichier est entièrement rappelé sur le disque, la synchronisation de fichiers Azure le convertit d’un fichier partiellement alloué en fichier normal. |
        | L | Point d’analyse | Indique que le fichier comporte un point d’analyse, pointeur spécial destiné à être utilisé par un filtre de système de fichiers. La synchronisation de fichiers Azure utilise des points d’analyse pour définir dans le filtre de système de fichiers de la synchronisation de fichiers Azure (StorageSync.sys) à quel endroit du cloud le fichier est stocké. Votre utilisateur final bénéficie ainsi d’un accès fluide sans qu’il ait même besoin de savoir que la synchronisation de fichiers Azure est utilisée ou comment accéder au fichier dans votre partage de fichiers Azure. Quand un fichier est entièrement rappelé, la synchronisation de fichiers Azure supprime le point d’analyse du fichier. |
        | O | Hors ligne | Indique qu’une partie ou la totalité du contenu du fichier n’est pas stockée sur le disque. Quand un fichier est entièrement rappelé, la synchronisation de fichiers Azure supprime cet attribut. |

        ![Boîte de dialogue Propriétés d’un fichier dans laquelle l’onglet Détails est sélectionné](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Vous pouvez également voir les attributs de tous les fichiers d’un dossier en ajoutant le champ **Attributs** à l’affichage sous forme de tableau de l’Explorateur de fichiers. Pour ce faire, cliquez avec le bouton droit sur une colonne (par exemple, Taille), sélectionnez **Autres**, puis sélectionnez **Attributs** dans la liste déroulante.
        
    2. **Utiliser `fsutil` pour rechercher les points d’analyse sur un fichier.** Comme indiqué dans l’option précédente, un point d’analyse, ou pointeur spécial pour le filtre de système de fichiers de la synchronisation de fichiers Azure (StorageSync.sys), est toujours défini pour un fichier hiérarchisé. Vous pouvez vérifier si un fichier a un point d’analyse avec l’utilitaire `fsutil` depuis une invite de commandes avec élévation de privilèges ou une session PowerShell :
    
        ```PowerShell
        fsutil reparsepoint query <your-file-name>
        ```

        Si le fichier a un point d’analyse, vous devez voir **Valeur de la balise d’analyse : 0x8000001e**. Cette valeur hexadécimale est la valeur de point d’analyse détenue par la synchronisation de fichiers Azure. La sortie contient également les données d’analyse représentant le chemin du fichier dans votre partage de fichiers Azure.

        > [!Warning]  
        > La commande d’utilitaire `fsutil reparsepoint` permet également de supprimer un point d’analyse. Cette commande pouvant entraîner la perte de données, ne l’exécutez que si l’équipe d’ingénieurs d’Azure File Sync vous le demande. 

* <a id="afs-recall-file"></a>**Un fichier que je souhaite utiliser a été hiérarchisé... Comment puis-je le rappeler sur le disque pour l’utiliser localement ?**  
    Pour rappeler un fichier sur le disque, le plus simple consiste à l’ouvrir. Le filtre de système de fichiers de la synchronisation de fichiers Azure (StorageSync.sys) télécharge le fichier à partir de votre partage de fichiers Azure de façon fluide et automatique. Pour les types de fichiers qui peuvent être partiellement lus, tels que les fichiers multimédias ou zip, l’ouverture d’un fichier n’entraîne pas le téléchargement du fichier entier.

    Vous pouvez également imposer le rappel d’un fichier à l’aide de PowerShell. Cela peut s’avérer utile pour rappeler de nombreux fichiers en même temps (par exemple, tous les fichiers d’un dossier). Ouvrez une session PowerShell sur le nœud serveur sur lequel la synchronisation de fichiers Azure est installée, puis exécutez les commandes PowerShell suivantes :
    
    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

* <a id="afs-force-tiering"></a>**Comment puis-je imposer la hiérarchisation d’un fichier ou répertoire ?**  
    Quand elle est activée, la fonctionnalité de hiérarchisation cloud hiérarchise automatiquement les fichiers en fonction de la date du dernier accès et de la dernière modification afin d’atteindre le pourcentage d’espace libre du volume spécifié sur le point de terminaison cloud ; cependant, vous pouvez être amené à imposer une hiérarchisation manuelle d’un fichier. Cette opération peut s’avérer utile si vous enregistrez un fichier volumineux que vous n’envisagez pas de réutiliser pendant un certain temps et que vous souhaitez dédier dans l’immédiat l’espace libre sur le volume à d’autres fichiers/dossiers. Vous pouvez forcer la hiérarchisation avec les commandes PowerShell suivantes :

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

* <a id="afs-files-excluded"></a>**Quels fichiers ou dossiers sont automatiquement exclus par Azure File Sync ?**
    Par défaut, la synchronisation de fichiers Azure exclut les fichiers suivants :
    
    - desktop.ini
    - thumbs.db
    - ehthumbs.db
    - ~$\*.\*
    - \*.laccdb
    - \*.tmp
    - 635D02A9D91C401B97884B82B3BCDAEA.\*

    Les dossiers suivants sont également exclus par défaut :

    - \System Volume Information
    - \$RECYCLE.BIN
    - \SyncShareState

* <a id="afs-os-support"></a>**Je souhaite utiliser Azure File Sync avec Windows Server 2008 R2, avec Linux ou avec mon périphérique NAS. Azure File Sync prend-il en charge cette utilisation ?**
    Aujourd’hui, la synchronisation de fichiers Azure prend uniquement en charge Windows Server 2016 et Windows Server 2012 R2. À ce stade, nous n’avons pas d’autres plans à partager, mais nous sommes ouverts à l’idée de prendre en charge des plateformes supplémentaires en fonction de la demande des clients. Indiquez-nous les plateformes que vous souhaiteriez voir prises en charge sur le [forum UserVoice consacré à Azure Files](https://feedback.azure.com/forums/217298-storage/category/180670-files).

## <a name="security-authentication-and-access-control"></a>Sécurité, authentification et contrôle d'accès
* <a id="ad-support"></a>**Azure Files prend-il en charge le contrôle d’accès et l’authentification Active Directory ?**  
    Dans Azure Files, vous pouvez gérer le contrôle d’accès de deux manières :

    - À l’aide des SAP, vous pouvez générer des jetons assortis d’autorisations spécifiques, qui restent valides pendant une période définie. Par exemple, vous pouvez générer un jeton offrant un accès en lecture seule à un fichier donné, valide pendant 10 minutes. Toute personne possédant ce jeton pendant sa période de validité dispose d’un accès en lecture seule à ce fichier pendant 10 minutes. Les clés SAP étant uniquement prises en charge par le biais des bibliothèques clientes ou de l’API REST, vous devez monter le partage de fichiers Azure sur SMB avec les clés de compte de stockage.

    - La synchronisation de fichiers Azure conserve toutes les ACL (basées sur Active Directory ou locales) et les réplique sur tous les points de terminaison de serveur avec lesquels elle effectue une synchronisation. Étant donné que Windows Server peut déjà s’authentifier auprès d’Active Directory, vous pouvez efficacement utiliser la synchronisation de fichiers Azure en attendant que l’authentification Active Directory et les ACL soient totalement prises en charge.

    Azure Files ne prend pas en charge Active Directory directement à ce stade.

* <a id="encryption-at-rest"></a>**Comment puis-je m’assurer que mon partage de fichiers Azure est chiffré au repos ?**  
    Le chiffrement au repos est en cours d’activation par défaut dans toutes les régions. Pour ces régions, vous n’avez rien à faire pour activer le chiffrement. Pour les autres régions, consultez [Chiffrement côté serveur](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>**Comment donner accès à un fichier spécifique via un navigateur web ?**  
    À l’aide des SAP, vous pouvez générer des jetons assortis d’autorisations spécifiques, qui restent valides pendant une période définie. Par exemple, vous pouvez générer un jeton avec un accès en lecture seule à un fichier spécifique pendant une période donnée. Toute personne possédant cette URL peut accéder au fichier directement à partir de n’importe quel navigateur web tant que le jeton est valide. Les clés SAP peuvent être facilement générées à partir d’une interface utilisateur telle que Storage Explorer.

* <a id="file-level-permissions"></a>**Est-il possible de spécifier des autorisations en lecture seule ou en écriture seule sur des dossiers au sein du partage ?**  
    Vous ne bénéficiez pas de ce niveau de contrôle sur les autorisations si vous montez le partage de fichiers par le biais de SMB. Toutefois, vous pouvez y parvenir en créant une signature d’accès partagé (SAP) via l’API REST ou les bibliothèques clientes.

* <a id="ip-restrictions"></a>**Puis-je implémenter des restrictions d’IP pour un partage de fichiers Azure ?**  
    Oui, les accès à votre partage de fichiers Azure peuvent être limités au niveau du compte de stockage. Pour plus d’informations, consultez [Configurer Pare-feu et réseaux virtuels dans Stockage Azure](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>**Quelles sont les stratégies de conformité des données Azure Files prises en charge ?**  
   Azure Files s’exécute sur la même architecture de stockage que d’autres services de stockage dans Stockage Azure, et applique les mêmes stratégies de conformité des données. Pour plus d’informations sur la conformité des données de Stockage Azure, vous pouvez télécharger et consulter le document sur la [protection des données dans Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409) et le [Centre de gestion de la confidentialité Microsoft](https://www.microsoft.com/TrustCenter/default.aspx).

## <a name="on-premises-access"></a>Accès local
* <a id="expressroute-not-required"></a>**Dois-je utiliser Azure ExpressRoute pour me connecter à Azure Files ou pour utiliser Azure File Sync localement ?**  
    Non, ExpressRoute n’est pas nécessaire pour accéder à un partage de fichiers Azure. Si vous montez un partage de fichiers Azure directement localement, la seule contrainte est que le port 445 (TCP sortant) soit ouvert pour l’accès à Internet (il s’agit du port sur lequel SMB communique). Si vous utilisez la synchronisation de fichiers Azure, seul est nécessaire le port 443 (TCP sortant) pour l’accès HTTPS (aucun protocole SMB requis). Toutefois, vous pouvez utiliser ExpressRoute avec ces deux options d’accès si vous le souhaitez.

* <a id="mount-locally"></a>**Comment monter un partage de fichiers Azure sur mon ordinateur local ?**  
    Vous pouvez monter le partage de fichiers par le biais du protocole SMB tant que le port 445 (TCP sortant) est ouvert et que votre client prend en charge le protocole SMB 3.0 (par exemple, vous utilisez Windows 10 ou Windows Server 2016). Si le port 445 est bloqué par la stratégie de votre organisation ou par votre fournisseur de services Internet, vous pouvez utiliser la synchronisation de fichiers Azure pour accéder à votre partage de fichiers Azure.

## <a name="backup"></a>Sauvegarde
* <a id="backup-share"></a>**Comment sauvegarder mon partage de fichiers Azure ?**  
    Vous pouvez utiliser des [instantanés de partage périodiques (préversion)](storage-how-to-use-files-snapshots.md) pour la protection contre les suppressions accidentelles. Vous pouvez utiliser AzCopy, RoboCopy ou un outil de sauvegarde tiers capable de sauvegarder un partage de fichiers monté. 

## <a name="share-snapshots"></a>Instantanés de partage
### <a name="share-snapshots---general"></a>Instantanés de partage - Généralités
* <a id="what-are-snaphots"></a>**Qu’est-ce qu’un instantané de partage de fichiers ?**  
    Les instantanés de partage de fichiers Azure vous permettent de créer une version en lecture seule de vos partages de fichiers. Vous pouvez également les utiliser pour copier une version antérieure de votre contenu sur le même partage ou à un autre emplacement dans Azure ou localement en vue d’effectuer d’autres modifications. Pour en savoir plus sur les instantanés de partage, consultez [Vue d’ensemble des instantanés de partage](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>**Où sont stockés mes instantanés de partage ?**  
    Les instantanés de partage sont stockés dans le même compte de stockage que celui du partage de fichiers.

* <a id="snapshot-perf-impact"></a>**Le niveau de performance est-il affecté ?**  
    Les instantanés de partage n’affectent pas le niveau de performance.

* <a id="snapshot-consistency"></a>**Les instantanés de partage sont-ils cohérents par rapport aux applications ?**  
    Non. Les instantanés de partage ne sont pas cohérents par rapport aux applications. L’utilisateur doit vider les écritures depuis l’application vers le partage avant de prendre un instantané de partage.

* <a id="snapshot-limits"></a>**Existe-t-il des limites quant au nombre d’instantanés de partage ?**  
    Azure Files peut conserver au maximum 200 instantanés de partage. Les instantanés de partage n’étant pas comptabilisés dans le quota de partages, il n’y a pas de limite par partage quant à l’espace total utilisé par tous les instantanés de partage. Les limites de compte de stockage continuent de s’appliquer. Au-delà de 200 instantanés de partage, vous devez supprimer les anciens instantanés pour en créer d’autres.

### <a name="create-share-snapshots"></a>Créer des instantanés de partage
* <a id="file-snaphsots"></a>**Puis-je créer des instantanés de partage de fichiers individuels ?**  
    Les instantanés de partage sont créés au niveau du partage de fichiers. Vous pouvez restaurer des fichiers individuels à partir de l’instantané de partage de fichiers, mais vous ne pouvez pas créer des instantanés de partage au niveau du fichier. Toutefois, si vous avez pris un instantané de partage au niveau du partage et que vous souhaitez répertorier les instantanés de partage où un fichier particulier a changé, vous pouvez effectuer cette opération à partir de l’expérience « Versions précédentes » sur un partage monté sur Windows. N’hésitez pas à nous indiquer si vous avez besoin d’une fonctionnalité de capture d’instantanés de fichiers sur [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encypted-snapshots"></a>**Puis-je créer des instantanés de partage de fichiers chiffré ?**  
    Vous pouvez prendre un instantané de partages de fichiers Azure pour lequel le chiffrement est activé au repos. Vous pouvez restaurer des fichiers depuis un instantané de partage vers un partage de fichiers chiffré. Si votre partage est chiffré, l’instantané de partage l’est également.

* <a id="geo-redundant-snaphsots"></a>**Mes instantanés de partage sont-ils géoredondants ?**
    Les instantanés de partage ont la même redondance que le partage de fichiers Azure auquel ils sont destinés. Si vous avez sélectionné un stockage géoredondant (GRS) pour votre compte, votre instantané de partage est également stocké de façon redondante dans la région associée.

### <a name="manage-share-snapshots"></a>Gérer les instantanés de partage
* <a id="browse-snapshots-linux"></a>**Puis-je parcourir mes instantanés de partage à partir de Linux ?**  
    Vous pouvez utiliser Azure CLI 2.0 pour effectuer les opérations de création, affichage, exploration et restauration sur Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>**Puis-je copier les instantanés de partage vers un autre compte de stockage ?**  
    Vous pouvez copier des fichiers depuis les instantanés de partage vers un autre emplacement, mais pas les instantanés de partage eux-mêmes.

### <a name="restore-data-from-share-snapshots"></a>Restaurer des données à partir d’instantanés de partage
* <a id="promote-share-snapshot"></a>**Puis-je promouvoir un instantané de partage vers le partage de base ?**  
    Vous pouvez uniquement copier des données depuis un instantané de partage vers la destination de votre choix. Toutefois, vous ne pouvez pas promouvoir un instantané de partage vers le partage de base.

* <a id="restore-snapshotted-file-to-other-share"></a>**Puis-je restaurer des données depuis mon instantané de partage vers un autre compte de stockage ?**  
    Oui. Vous pouvez copier les fichiers d’un instantané de partage depuis l’emplacement d’origine ou un autre emplacement qui inclut le même compte de stockage ou un autre compte de stockage dans des régions identiques ou différentes. Vous pouvez également copier les fichiers localement ou sur un cloud.    
  
### <a name="cleanup-share-snapshot"></a>Nettoyer les instantanés de partage
* <a id="delete-share-keep-snapshots"></a>**Puis-je supprimer mon partage, mais pas les instantanés de partage ?**
    Vous ne pouvez pas supprimer votre partage s’il comporte des instantanés de partage actifs. Une API est disponible pour supprimer les instantanés de partage, ainsi que le partage. Vous pouvez également effectuer cette opération à partir du portail Azure.

* <a id="delete-share-with-snapshots"></a>**Qu’advient-il de mes instantanés de partage si je supprime mon compte de stockage ?**
    Si vous supprimez votre compte de stockage, les instantanés de partage sont également supprimés.

## <a name="billing-and-pricing"></a>Facturation et la tarification
* <a id="vm-file-share-network-traffic"></a>**Le trafic réseau entre une machine virtuelle Azure et un partage de fichiers Azure est-il considéré comme de la bande passante externe facturée dans le cadre de l’abonnement ?**  
    Si le partage de fichiers et la machine virtuelle se trouvent dans la même région Azure, le trafic entre eux est gratuit. S’ils se trouvent dans des régions différentes, le trafic entre eux est facturé en tant que bande passante externe.

* <a id="share-snapshot-price"></a>**Quel est le coût des instantanés de partage ?**
     Pendant la préversion, la fonctionnalité d’instantané de partage n’est pas facturée. Les coûts de sortie standard et de transaction liés au stockage continuent de s’appliquer. Après la disponibilité générale, la fonctionnalité et les transactions sur les instantanés de partage seront facturées.
     
     Les instantanés de partage sont incrémentiels par nature. L’instantané de partage de base est le partage lui-même. Tous les instantanés de partage suivants étant incrémentiels, chacun ne stocke que la différence par rapport à l’instantané de partage précédent. Vous n’êtes facturé que pour le contenu changé. Si vous disposez d’un partage de 100 Go de données, mais que seulement 5 Go ont changé depuis le dernier instantané de partage, l’instantané de partage consomme seulement 5 Go supplémentaires ; ainsi, seuls 105 Go vous sont facturés. Pour plus d’informations sur les frais de sortie standard et de transaction, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Extensibilité et niveau de performance
* <a id="files-scale-limits"></a>**Quelles sont les limites d’extensibilité d’Azure Files ?**  
    Pour plus d’informations sur les objectifs d’extensibilité et de performance d’Azure Files, consultez [Azure Files scalability and performance targets](storage-files-scale-targets.md) (Objectifs de performance et d’extensibilité d’Azure Files).

* <a id="need-larger-share"></a>**J’ai besoin d’un partage de fichiers plus volumineux que ce que propose actuellement Azure Files... Puis-je augmenter la taille de mon partage de fichiers Azure ?**  
    Non, la taille maximale d’un partage de fichiers Azure est 5 To. Il s’agit actuellement d’une limite inconditionnelle que nous ne pouvons pas ajuster. Nous travaillons sur une solution permettant d’augmenter la taille de partage à 100 To, mais nous ne sommes pas en mesure de vous indiquer quand elle sera disponible pour l’instant.

* <a id="open-handles-quota"></a>**Combien de clients peuvent accéder simultanément au même fichier ?**  
    Il existe un quota de 2 000 handles ouverts sur un seul fichier. Une fois que vous avez atteint 2 000 handles ouverts, vous obtiendrez une erreur indiquant que le quota est atteint.

* <a id="zip-slow-performance"></a>**Les performances étaient lentes quand j’ai essayé de décompresser des fichiers dans Azure Files. Que dois-je faire ?**  
    Pour transférer un grand nombre de fichiers dans Azure Files, nous vous recommandons d’utiliser AzCopy (Windows, préversion pour Linux/Unix) ou Azure Powershell, car ces outils ont été optimisés pour le transfert réseau.

* <a id="slow-perf-windows-81-2012r2"></a>**Pourquoi les performances sont-elles lentes depuis que j’ai monté mon partage de fichiers Azure sur Windows Server 2012 R2 ou Windows 8.1 ?**  
    Un problème connu lié au montage d’un partage de fichiers Azure sur Windows Server 2012 R2 et Windows 8.1 a été corrigé dans la mise à jour cumulative d’avril 2014 pour Windows 8.1 et Windows Server 2012 R2. Pour optimiser les performances, vérifiez que ce correctif a été appliqué à toutes les instances de Windows Server 2012 R2 et Windows 8.1 (bien entendu, nous conseillons toujours de récupérer tous les correctifs Windows par le biais de Windows Update). Pour plus d’informations, consultez l’article de la base de connaissances associé [Slow performance when you access Azure Files from Windows 8.1 or Server 2012 R2](https://support.microsoft.com/kb/3114025) (Ralentissement des performances lors de l’accès à Azure Files depuis Windows 8.1 ou Server 2012 R2).

## <a name="features-and-interoperability-with-other-services"></a>Fonctionnalités et interopérabilité avec d’autres services
* <a id="cluster-witness"></a>**Puis-je utiliser mon partage de fichiers Azure en tant que *témoin de partage de fichiers* pour mon cluster de basculement Windows Server ?**  
    Ce cas n’est pas pris en charge pour un partage de fichiers Azure. Pour plus d’informations sur la façon d’obtenir cette configuration pour le stockage Blob Azure, consultez [Déployer un témoin cloud pour un cluster de basculement](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>**Puis-je monter un partage de fichiers Azure dans une instance Azure Container ?**  
    Oui, les partages de fichiers Azure constituent une méthode idéale pour conserver les informations au-delà de la durée de vie d’une instance de conteneur. Pour plus d’informations, consultez [Montage d’un partage de fichiers Azure avec Azure Container Instances](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>**Existe-t-il une opération de renommage dans l’API REST ?**  
    Pas pour l'instant.

* <a id="nested-shares"></a>**Est-il possible d’avoir des partages imbriqués, autrement dit, un partage sous un partage ?**  
    Non. Le partage de fichiers est le pilote virtuel que vous pouvez monter et les partages imbriqués ne sont donc pas pris en charge.

* <a id="ibm-mq"></a>**Utilisation d’Azure Files avec IBM MQ**  
    IBM a publié un document visant à guider les clients IBM MQ lors de la configuration d’Azure Files avec leur service. Pour plus d’informations, consultez l’article [How to setup IBM MQ Multi instance queue manager with Microsoft Azure File Service](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service)(Comment configurer le gestionnaire de file d’attente multi-instance IBM MQ avec le service de fichiers Microsoft Azure).

## <a name="see-also"></a>Voir aussi
* [Résoudre les problèmes liés à Azure Files sous Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Résoudre les problèmes liés à Azure Files dans Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Résoudre les problèmes de synchronisation de fichiers Azure (préversion)](storage-sync-files-troubleshoot.md)