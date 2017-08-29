---
title: "Présentation du Stockage Fichier Azure | Microsoft Docs"
description: "Présentation du Stockage Fichier Azure, qui permet le partage de fichiers réseau dans Microsoft Cloud"
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
ms.topic: get-started-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 498af5cffb76e026c9a87127cab238f0f23b668a
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---

# <a name="introduction-to-azure-file-storage"></a>Présentation du Stockage Fichier Azure

Le stockage de fichiers Azure propose le partage de fichiers réseau sur cloud grâce au [protocole Server Message Block (SMB)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx), et au protocole [Common Internet File System (CIFS)](https://technet.microsoft.com/library/cc939973.aspx). Les partages de fichiers Azure peuvent être montés simultanément par les machines virtuelles Azure et les déploiements locaux sous Windows, macOS ou Linux. Un compte de stockage à usage général vous donne accès au Stockage Fichier Azure, au Stockage Blob Azure et au Stockage de files d’attente Azure.

## <a name="videos"></a>Vidéos
| Présentation du Stockage Fichier Azure (27 min) | Didacticiel sur le Stockage Fichier Azure (5 minutes)  |
|-|-|
| [![Capture vidéo de la Présentation du Stockage Fichier Azure – Cliquez pour lancer la vidéo !](./media/storage-files-introduction/azure-files-introduction-video-snapshot1.png)](https://www.youtube.com/watch?v=zlrpomv5RLs) | [![Capture vidéo du didacticiel Stockage Fichier Azure – Cliquez pour lancer la vidéo !](./media/storage-files-introduction/azure-files-introduction-video-snapshot2.png)](https://channel9.msdn.com/Blogs/Azure/Azure-File-storage-with-Windows/) |

## <a name="why-azure-file-storage-is-useful"></a>Avantages du Stockage Fichier Azure

Le Stockage Fichier Azure vous permet de remplacer les serveurs de fichiers Windows Server, Linux ou NAS hébergés localement ou sur le cloud grâce à un partage de fichiers sur cloud sans système d’exploitation. Le Stockage Fichier Azure possède les avantages suivants :

* **Un accès partagé** : les partages de fichiers Azure prennent en charge les protocoles standards SMB. Cela signifie que vous pouvez facilement remplacer vos partages de fichiers locaux par des partages de fichiers Azure, sans vous soucier de la compatibilité des applications. Être en mesure d’accéder à un partage de fichier sur plusieurs machines et applications / instances peut s’avérer très avantageux avec le Stockage Fichier Azure.

* **Entièrement gérés** : les partages de fichiers Azure peuvent être créés sans avoir à gérer le matériel ou un système d’exploitation. Vous n’êtes donc pas obligé d’appliquer au serveur du système d’exploitation des mises à jour de sécurité critiques, ni de remplacer les disques durs défaillants.

* **Scripts et outils** : les cmdlets PowerShell et Azure CLI peuvent créer, monter et gérer les partages de fichier Azure dans le cadre de l’administration des applications Azure. Vous pouvez créer et gérer les partages de fichiers Azure à l’aide du [portail Azure](https://portal.azure.com) et de l’[Explorateur de stockage Azure](https://storageexplorer.com). 

* **Résilience** : le Stockage Fichier Azure a été entièrement conçu de manière à être toujours disponible. L’utilisation du Stockage Fichier Azure au lieu des partages de fichiers locaux signifie que vous n’avez plus à vous soucier des pannes de courant ou des problèmes de réseau. 

* **Programmabilité familière** : les applications exécutées dans Azure peuvent accéder aux données dans le partage via les [API d’E/S du système de fichier](https://msdn.microsoft.com/library/system.io.file.aspx). Les développeurs peuvent ainsi tirer profit de leur code et compétences actuels pour migrer les applications existantes. En plus des API d’E/S du système, vous pouvez utiliser n’importe quelle bibliothèque de client du stockage Azure, comme c’est le cas pour [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet) ou l’[API REST du stockage Azure](/rest/api/storageservices/file-service-rest-api).

Les partages de fichiers Azure peuvent être utilisés pour :

* **Remplacer des serveurs de fichiers locaux** : le Stockage Fichier Azure peut être utilisé pour remplacer complètement les partages de fichiers sur les serveurs de fichiers locaux traditionnels ou les périphériques NAS. Les systèmes d’exploitation courants tels que Windows, Mac OS et Linux facilement peuvent facilement un partage de fichiers Azure, n’importe où dans le monde.

* **Migration « lift-and-shift » des applications**

    Le Stockage Fichier Azure permet d’effectuer facilement la migration « lift-and-shift » vers le cloud des applications qui utilisent des partages de fichiers locaux pour partager des données entre plusieurs parties d’une application. Pour implémenter cela, chaque machine virtuelle se connecte au partage de fichiers, après quoi elle peut lire et écrire dans les fichiers comme sur un partage de fichiers local.

* **Simplifier le développement cloud**
    
    Le Stockage Fichier Azure peut être utilisé de plusieurs manières pour simplifier les nouveaux projets de développement cloud.
    
    * **Paramètres d’application partagés**
    
        Pour les applications distribuées, les fichiers de configuration sont souvent centralisés à un emplacement accessible par différentes machines virtuelles. Ces fichiers de configuration peuvent désormais être stockés dans un partage de fichiers Azure, puis lus par toutes les instances de l’application. Ces paramètres peuvent également être gérés via l’interface REST, ce qui offre un accès mondial aux fichiers de configuration.

    * **Partage de diagnostic**
    
        Un partage de fichiers Azure peut également servir à enregistrer les fichiers de diagnostic tels que les journaux, les indicateurs de performance et les vidages sur incident. Le fait que des fichiers de partage soient à la fois disponibles via SMB et l’interface REST permet aux applications de créer ou d’utiliser différents outils d’analyse pour traiter et analyser les données de diagnostic.

    * **Développement / test / débogage**

        Lorsque les développeurs ou les administrateurs travaillent sur des machines virtuelles situées dans le cloud, ils ont souvent besoin de différents outils ou utilitaires. Les procédures d’installation et de distribution de ces utilitaires sur chaque machine virtuelle, à l’endroit requis, peuvent prendre du temps. Avec le Stockage Fichier Azure, le développeur ou l’administrateur peut stocker ses outils préférés sur un partage de fichiers et les relier facilement à n’importe quelle machine virtuelle.
        
## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

La gestion des partages de fichiers Azure est beaucoup plus simple que la gestion des partages de fichiers locaux. Le diagramme ci-après illustre la structure de gestion du Stockage Fichier Azure :

![Structure de fichiers](./media/storage-files-introduction/files-concepts.png)

* **Compte de stockage** : tout accès au stockage Azure s’effectue via un compte de stockage. Pour plus d’informations sur la capacité du compte de stockage, consultez la page [Objectifs de performance et évolutivité](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* **Partage** : un partage de stockage de fichiers est un partage de fichiers SMB dans Azure. Tous les répertoires et fichiers doivent être créés dans un partage parent. Un compte peut contenir un nombre illimité de partages, et un partage peut stocker un nombre illimité de fichiers, dans la limite de la capacité totale de 5 To du partage de fichiers.

* **Répertoire** : hiérarchie facultative de répertoires.

* **Fichier** : fichier du partage. Un fichier peut avoir une taille de 1 To maximum.

* **Format d’URL** : les fichiers sont adressables à l’aide du format d’URL suivant :  

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory/directories>/<file>
    ```

## <a name="next-steps"></a>Étapes suivantes

* [Création d’un partage de fichiers Azure](storage-how-to-create-file-share.md)
* [Connexion et montage sous Windows](storage-how-to-use-files-windows.md)
* [Connexion et montage sous Linux](storage-how-to-use-files-linux.md)
* [Connexion et montage sous macOS](storage-how-to-use-files-mac.md)
* [FAQ](../storage-files-faq.md)
* [Résolution des problèmes sur Windows](storage-troubleshoot-windows-file-connection-problems.md)   
* [Résolution des problèmes sur Linux](storage-troubleshoot-linux-file-connection-problems.md)   

<!-- Rena I would remove any articles from here that are more than a year old. - Robin-->
### <a name="conceptual-articles-and-videos"></a>Vidéos et articles conceptuels
* [Stockage Fichier Azure : un système de fichiers SMB dans le cloud sans friction pour Windows et Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)

### <a name="tooling-support-for-azure-file-storage"></a>Outils pour le Stockage Fichier Azure
* [Utilisation de AzCopy avec Microsoft Azure Storage](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Utilisation de la CLI Microsoft Azure avec Microsoft Azure Storage](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)

### <a name="blog-posts"></a>Billets de blog :
* [Le stockage de fichiers Azure est désormais mis à la disposition générale](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Dans le Stockage Fichier Azure](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Présentation de Microsoft Azure File Service](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Migration de données vers le fichier Azure](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)

### <a name="reference"></a>Référence
* [Référence de la bibliothèque cliente de stockage pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Référence de l’API REST du service de fichiers](http://msdn.microsoft.com/library/azure/dn167006.aspx)

