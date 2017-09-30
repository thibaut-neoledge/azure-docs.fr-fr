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
ms.date: 09/19/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: eb0b4d7cde568ab1809daa8025120828e75f5d76
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="frequently-asked-questions-about-azure-files"></a>Forum Aux Questions (FAQ) sur Azure Files

## <a name="general"></a>Généralités
* **Q. Qu’est-ce qu’Azure Files ?**  
   
    Azure Files est un système de fichiers distribué dans Azure. Il fournit une interface de protocole SMB qui permet aux utilisateurs de monter le stockage en tant que partage natif sur une machine virtuelle Azure prise en charge ou un ordinateur local.

* **Q. Pourquoi Azure Files est-il utile ?**  
   
    Azure Files fournit un accès aux données partagé entre plusieurs machines virtuelles et plateformes. Consultez [Pourquoi Azure Files est-il utile ?](storage-files-introduction.md#why-azure-files-is-useful).

* **Q. Quand utiliser Stockage Fichier Azure, Stockage Blob Azure ou un stockage sur disque Azure ?**  
   
    Microsoft Azure offre plusieurs moyens de stocker des données et d’y accéder dans le cloud.  
   
    Azure Files : fournit une interface SMB, des bibliothèques clientes et une interface REST qui permet un accès aisé en tout lieu aux fichiers stockés.  
   
    Stockage Blob Azure : fournit des bibliothèques clientes et une interface REST qui permet de stocker des données non structurées et d’y accéder à grande échelle dans des objets blob de blocs.  
   
    Disques de données Azure : fournit des bibliothèques clientes et une interface REST qui permet de stocker des données de manière permanente, et d’y accéder à partir d’un disque dur virtuel attaché.  
   
    Pour plus d’informations, voir [Quand utiliser des objets blob Azure, des fichiers Azure ou des disques de données Azure](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* **Q. Comment commencer à utiliser Azure Files ?**  
   
    Commencez par créer un partage de fichiers Azure. Vous pouvez créer des partages de fichiers Azure à l’aide du portail Azure, des applets de commande PowerShell pour Stockage Azure, des bibliothèques clientes Stockage Azure ou de l’API REST de Stockage Azure. Ce didacticiel contient des informations concernant les procédures suivantes :

    * [Création d’un partage de fichiers via le portail](storage-how-to-use-files-portal.md)
    * [Création d’un partage de fichiers via PowerShell](storage-how-to-create-file-share.md#create-file-share-through-powershell)
    * [Création d’un partage de fichiers via l’Interface de ligne de commande (CLI)](storage-how-to-create-file-share.md#create-file-share-through-command-line-interface-cli)

* **Q. Quelles réplications Azure Files prend-il en charge ?**  
   
    Azure Files ne prend en charge actuellement que les stockages LRS ou GRS. Nous prévoyons de prendre en charge RA-GRS, mais nous n’avons pas encore de calendrier bien défini.
    
## <a name="scale-targetslimits"></a>Objectifs/limites de l’extensibilité

* **Q. Combien de clients peuvent accéder simultanément au même fichier ?**

    Il existe un quota de 2 000 handles ouverts sur un seul fichier. Une fois que vous avez atteint 2 000 handles ouverts, vous obtiendrez une erreur indiquant que le quota est atteint.

## <a name="security-authentication-and-access-control"></a>Sécurité, authentification et contrôle d'accès

* **Q. Quelles sont les différentes façons d’accéder aux fichiers dans Azure Files ?**
    
    Vous pouvez monter le partage de fichiers sur votre ordinateur local à l’aide du protocole SMB 3.0, ou vous servir d’outils tels que l’[Explorateur de stockage](http://storageexplorer.com/) pour accéder aux fichiers dans votre partage de fichiers. À partir de votre application, vous pouvez utiliser des bibliothèques clientes de stockage, des API REST, ou Powershell pour accéder à vos fichiers dans le partage de fichiers Azure.

* **Q. Comment donner accès à un fichier spécifique via un navigateur web ?**
    
    À l’aide des SAP, vous pouvez générer des jetons assortis d’autorisations spécifiques, qui restent valides pendant une période définie. Par exemple, vous pouvez générer un jeton avec un accès en lecture seule à un fichier spécifique pendant une période donnée. Toute personne possédant cette URL peut accéder au fichier directement à partir de n’importe quel navigateur web tant que le jeton est valide. Les clés SAP peuvent être facilement générées à partir d’une interface utilisateur telle que Storage Explorer.

* **Q. Est-il possible de spécifier des autorisations en lecture seule ou en écriture seule sur des dossiers au sein du partage ?**
    
    Vous ne bénéficiez pas de ce niveau de contrôle sur les autorisations si vous montez le partage de fichiers par le biais de SMB. Toutefois, vous pouvez y parvenir en créant une signature d’accès partagé (SAP) via l’API REST ou les bibliothèques clientes.  

* **Q. Comment activer le chiffrement côté serveur pour Azure Files ?**

    Le [chiffrement côté serveur](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) pour Azure Files est généralement disponible dans toutes les régions et tous les clouds publics et nationaux. Vous pouvez activer SSE pour Azure Files à l’aide du [portail Azure](https://portal.azure.com/), de [l’API du fournisseur de ressources de Stockage Microsoft Azure](/rest/api/storagerp/storageaccounts), [d’Azure PowerShell](https://msdn.microsoft.com/library/azure/mt607151.aspx) ou [d’Azure CLI](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
    
    Après l’activation de SSE sur Azure Files, toutes les nouvelles données écrites dans le stockage de fichiers de ce compte de stockage sont automatiquement chiffrées. Cette fonctionnalité est disponible pour toutes les nouvelles données écrites dans les partages existants ou nouveaux, dans un compte de stockage existant ou nouveau. L’activation de cette fonction sera sans frais supplémentaires. En savoir plus sur [l’activation de SSE sur Azure Files](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* **Q. Azure Files prend-il en charge l’authentification Azure Active Directory ?**
   
    À l’heure actuelle, l’authentification ou les listes de contrôle d’accès basées sur Active Directory ne sont pas prises en charge, mais elles figurent dans notre liste de demandes de fonctionnalités. Pour l’instant, les clés de compte de stockage Azure sont utilisées pour l’authentification auprès du partage de fichiers. Nous offrons néanmoins une solution de contournement utilisant les signatures d’accès partagé (SAP) via l’API REST ou les bibliothèques clientes. À l’aide des SAP, vous pouvez générer des jetons assortis d’autorisations spécifiques, qui restent valides pendant une période définie. Par exemple, vous pouvez générer un jeton offrant un accès en lecture seule à un fichier donné, valide pendant 10 minutes. Toute personne possédant ce jeton pendant sa période de validité dispose d’un accès en lecture seule à ce fichier pendant 10 minutes.
   
    Les SAP sont uniquement prises en charge via l’API REST ou les bibliothèques clientes. Lorsque vous montez le partage de fichiers par le biais du protocole SMB, vous ne pouvez pas utiliser de SAP pour déléguer l’accès à son contenu. 
    
* **Q. Quelles stratégies de conformité des données Azure Files prend-il en charge ?**

   Azure Files s’exécute sur la même architecture de stockage que d’autres services de stockage dans Stockage Azure, et applique les mêmes stratégies de conformité des données. Pour plus d’informations sur la conformité des données de Stockage Azure, vous pouvez télécharger et consulter le document sur la [protection des données dans Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409).

## <a name="on-premises-access"></a>Accès local

* **Q. Dois-je utiliser Azure ExpressRoute pour me connecter à Azure Files à partir d’une machine virtuelle locale ?**
   
    Non. Si vous ne disposez pas d’ExpressRoute, vous pouvez toujours accéder au partage de fichiers à partir de votre site à condition que le port 445 (TCP sortant) soit ouvert pour l’accès Internet. Vous pouvez toutefois utiliser ExpressRoute avec Azure Files si vous le souhaitez.

* **Q. Comment monter un partage de fichiers Azure sur mon ordinateur local ?** 
    
    Vous pouvez monter le partage de fichiers par le biais du protocole SMB tant que le port 445 (TCP sortant) est ouvert et que votre client prend en charge le protocole SMB 3.0 (par exemple, vous utilisez Windows 10 ou Windows Server 2012). Contactez votre fournisseur d’accès Internet pour débloquer le port. Entre-temps, vous pouvez afficher vos fichiers à l’aide de l’[Explorateur de stockage](../../vs-azure-tools-storage-explorer-files.md#view-a-file-shares-contents).


## <a name="billing-and-pricing"></a>Facturation et la tarification
* **Q. Le trafic réseau entre une machine virtuelle Azure et un partage de fichiers est-il considéré comme de la bande passante externe est facturée dans le cadre de l’abonnement ?**
   
    Si le partage de fichiers et la machine virtuelle se trouvent dans la même région Azure, le trafic entre eux est gratuit. S’ils se trouvent dans des régions différentes, le trafic entre eux est facturé en tant que bande passante externe.

## <a name="backup"></a>Sauvegarde

* **Q. Comment sauvegarder les mon partage de fichiers Azure ?**
    
    Vous pouvez utiliser AzCopy, RoboCopy ou un outil de sauvegarde tiers capable de sauvegarder un partage de fichiers monté. Nous prévoyons d’offrir la possibilité de prendre des captures instantanées de partages de fichiers dans un futur proche. Vous pourrez alors utiliser cette fonctionnalité pour sauvegarder vos partages de fichiers Azure.

## <a name="performance"></a>Performances

* **Q. Quelles sont les limites d’extensibilité d’Azure Files ?**
    Pour plus d’informations sur les objectifs d’extensibilité et de performance d’Azure Files, consultez [Objectifs de performance et évolutivité de Stockage Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#scalability-targets-for-blobs-queues-tables-and-files).

* **Q. Les performances étaient lentes lorsque j’ai essayé de décompresser des fichiers dans Azure Files. Que dois-je faire ?**
    
    Pour transférer un grand nombre de fichiers dans Azure Files, nous vous recommandons d’utiliser AzCopy (Windows, préversion pour Linux/Unix) ou Azure Powershell, car ces outils ont été optimisés pour le transfert réseau.

* **Q. Quels correctifs ont été publiés pour résoudre les problèmes de lenteur des performances d’Azure Files ?**
    
    L’équipe Windows a récemment publié un correctif permettant de résoudre un problème de ralentissement des performances lorsque le client accède à Azure Files à partir de Windows 8.1 ou de Windows Server 2012 R2. Pour plus d’informations, consultez l’article de la base de connaissances associé [Slow performance when you access Azure Files from Windows 8.1 or Server 2012 R2](https://support.microsoft.com/kb/3114025) (Ralentissement des performances lors de l’accès à Azure Files depuis Windows 8.1 ou Server 2012 R2).

## <a name="features-and-interoperability-with-other-services"></a>Fonctionnalités et interopérabilité avec d’autres services
* **Q. Un « témoin de partage de fichiers » pour un cluster de basculement constitue-t-il un des cas d’utilisation d’Azure Files ?**
   
    Cela n’est actuellement pas pris en charge.

* **Q. Existe-t-il une opération de changement de nom dans l’API REST ?**
   
    Pas pour l'instant.

* **Q. Est-il possible d’avoir des partages imbriqués, autrement dit, un partage sous un partage ?**
    
    Non. Le partage de fichiers est le pilote virtuel que vous pouvez monter et les partages imbriqués ne sont donc pas pris en charge.

* **Q. Utilisation d’Azure Files avec IBM MQ**
    
    IBM a publié un document visant à guider les clients IBM MQ lors de la configuration d’Azure Files avec leur service. Pour plus d’informations, consultez l’article [How to setup IBM MQ Multi instance queue manager with Microsoft Azure File Service](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service)(Comment configurer le gestionnaire de file d’attente multi-instance IBM MQ avec le service de fichiers Microsoft Azure).


## <a name="troubleshooting"></a>Résolution de problèmes
* **Q. Comment résoudre les erreurs d’Azure Files ?**
    
    Vous pouvez vous référer à [l’article Résolution des problèmes relatifs aux fichiers Azure](storage-troubleshoot-windows-file-connection-problems.md) pour obtenir une aide de bout en bout. 


## <a name="see-also"></a>Voir aussi
Consultez ces liens pour plus d’informations sur Azure Files.

### <a name="conceptual-articles-and-videos"></a>Vidéos et articles conceptuels
* [Azure Files : un système de fichiers SMB cloud transparent pour Windows et Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Comment utiliser Azure Files avec Linux](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Outils pris en charge pour le stockage de fichiers
* [Utilisation de AzCopy avec Microsoft Azure Storage](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Utilisation de la CLI Microsoft Azure avec Microsoft Azure Storage](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Résolution des problèmes liés à Azure Files](storage-troubleshoot-linux-file-connection-problems.md)

### <a name="blog-posts"></a>Billets de blog :
* [Azure Files est désormais mis à la disposition générale](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Dans Azure Files](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Présentation de Microsoft Azure File Service](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Migration de données vers Azure Files](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)

### <a name="reference"></a>Référence
* [Référence de la bibliothèque cliente de stockage pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Référence de l’API REST du service de fichiers](http://msdn.microsoft.com/library/azure/dn167006.aspx)

