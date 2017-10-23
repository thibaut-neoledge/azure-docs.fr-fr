---
title: "Déploiement de la synchronisation de fichiers Azure (préversion) | Microsoft Docs"
description: "Découvrez comment déployer la synchronisation de fichiers Azure du début à la fin."
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
ms.openlocfilehash: 9f9ff0674fd4e3f9b0598a982d81681eaa6d1997
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-deploy-azure-file-sync-preview"></a>Déploiement de la synchronisation de fichiers Azure (préversion)
La synchronisation de fichiers Azure (préversion) vous permet de centraliser les partages de fichiers de votre organisation dans Azure Files sans perdre la flexibilité, le niveau de performance et la compatibilité d’un serveur de fichiers local. Pour cela, elle transforme vos serveurs Windows Server en un cache rapide de votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible sur Windows Server pour accéder à vos données localement (y compris SMB, NFS et FTPS) et vous pouvez avoir autant de caches que nécessaire dans le monde entier.

Nous recommandons vivement de lire [Planification d’un déploiement de fichiers Azure](storage-files-planning.md) et [Planification d’un déploiement de synchronisation de fichiers Azure](storage-sync-files-planning.md) avant de suivre les étapes décrites dans ce guide.

## <a name="prerequisites"></a>Composants requis
* Un compte de stockage et un partage de fichiers Azure dans la même région où vous souhaitez déployer la synchronisation de fichiers Azure. Pour plus d'informations, consultez les pages suivantes :
    - [Disponibilité des régions](storage-sync-files-planning.md#region-availability) de la synchronisation de fichiers Azure
    - [Créer un compte de stockage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) pour obtenir des instructions pas à pas pour créer un compte de stockage
    - [Créer un partage de fichiers](storage-how-to-create-file-share.md) pour obtenir des instructions pas à pas pour créer un partage de fichiers
* Au moins un serveur Windows Server ou un cluster Windows Server pris en charge pour la synchronisation de fichiers Azure. Consultez [Interopérabilité avec Windows Server](storage-sync-files-planning.md#azure-file-sync-interoperability) pour plus d’informations sur les versions prises en charge de Windows Server.

## <a name="deploy-the-storage-sync-service"></a>Déployer le service de synchronisation de stockage 
Le service de synchronisation de stockage est la ressource Azure de niveau supérieur qui représente la synchronisation de fichiers Azure. Pour déployer un service de synchronisation de stockage, accédez au [portail Azure](https://portal.azure.com/) et recherchez l’option de synchronisation de fichiers Azure. Après avoir sélectionné « Synchronisation de fichiers Azure (préversion) » dans les résultats de la recherche, sélectionnez « Créer » pour ouvrir l’onglet « Déployer la synchronisation du stockage ».

Le panneau qui s’affiche nécessite les informations suivantes :

- **Nom** : nom unique (par abonnement) du service de synchronisation de stockage.
- **Abonnement** : abonnement dans lequel créer le service de synchronisation de stockage. Selon la stratégie de configuration de votre organisation, vous avez accès à un ou plusieurs abonnements. Un abonnement Azure est le conteneur de base pour la facturation de chaque service cloud (par exemple, Azure Files).
- **Groupe de ressources** : un groupe de ressources est un groupe logique de ressources Azure, tel qu’un compte de stockage ou un service de synchronisation de stockage. Vous pouvez créer un nouveau groupe de ressources ou utiliser un groupe de ressources existant pour la synchronisation de fichiers Azure (nous recommandons d’utiliser des groupes de ressources comme des conteneurs servant à isoler logiquement les ressources de votre organisation, en regroupant par exemple les ressources humaines ou les ressources pour un projet particulier).
- **Emplacement** : région dans laquelle vous souhaitez déployer la synchronisation de fichiers Azure. Seuls les régions prises en charge sont disponibles dans cette liste.

Quand le formulaire « Déployer la synchronisation du stockage » est rempli, cliquez sur « Créer » pour déployer le service de synchronisation de stockage.

## <a name="prepare-windows-servers-for-use-with-azure-file-sync"></a>Préparer des serveurs Windows Server à la synchronisation de fichiers Azure
Procédez comme suit pour chaque serveur sur lequel vous souhaitez utiliser la synchronisation de fichiers Azure, y compris les nœuds de serveur dans un cluster de basculement :

Procédez comme suit pour chaque serveur, y compris les nœuds de serveur dans un cluster de basculement, sur lequel vous souhaitez utiliser la synchronisation de fichiers Azure :

1. Désactivez la configuration de la sécurité renforcée d’Internet Explorer. Cette action est uniquement requise pour l’inscription du serveur initial. La configuration peut être réactivée une fois le serveur inscrit.
    1. Ouvrez le Gestionnaire de serveurs.
    2. Cliquez sur **Serveur Local** :  
        ![« Serveur local » sur le côté gauche de l’interface utilisateur du Gestionnaire de serveur](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
    3. Sélectionnez le lien vers la **Configuration de sécurité renforcée d’Internet Explorer** dans le sous-volet Propriétés :  
        ![« Configuration de sécurité renforcée d’Internet Explorer » dans l’interface utilisateur du Gestionnaire de serveur](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
    4. Dans la fenêtre indépendante Configuration de sécurité renforcée d’Internet Explorer, sélectionnez **Activé** aussi bien pour les administrateurs que les utilisateurs :  
        ![Fenêtre indépendante Configuration de sécurité renforcée d’Internet Explorer avec l’option « Désactivé » sélectionnée](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

2. Assurez-vous d’exécuter au moins PowerShell 5.1.\* (PowerShell 5.1 est la valeur par défaut sur Windows Server 2016). Vous pouvez vérifier que vous utilisez PowerShell 5.1.\* en consultant la valeur de la propriété PSVersion de l’objet $PSVersionTable :

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    - Si cette valeur est inférieure à 5.1.\*, comme cela sera le cas avec la plupart des installations de Windows Server 2012 R2, vous pouvez facilement effectuer la mise à niveau en téléchargeant et en installant [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). Le package correct à télécharger et à installer pour Windows Server 2012 R2 est **Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu**.

3. [Installez et configurez Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Nous recommandons toujours d’utiliser la dernière version des modules Azure PowerShell.

## <a name="install-the-azure-file-sync-agent"></a>Installer l’agent de synchronisation de fichiers Azure
L’agent de synchronisation de fichiers Azure est un package téléchargeable qui permet à un serveur Windows Server de se synchroniser avec un partage de fichiers Azure. L’agent est téléchargeable sur le [Centre de téléchargement Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Une fois téléchargé, double-cliquez sur le package MSI pour démarrer l’installation de l’agent de synchronisation de fichiers Azure.

> [!Important]  
> Si vous souhaitez utiliser la synchronisation de fichiers Azure avec un cluster de basculement, l’agent de synchronisation de fichiers Azure doit être installé sur chaque nœud du cluster.

L’installation du package d’installation de l’agent de synchronisation de fichiers Azure devrait s’effectuer relativement vite et sans trop d’invites supplémentaires. Nous recommandons de :
- Conserver le chemin d’installation par défaut `C:\Program Files\Azure\StorageSyncAgent` pour simplifier la résolution des problèmes et la maintenance des serveurs.
- Activer Microsoft Update pour garantir la mise à jour de la synchronisation de fichiers Azure. Toutes les mises à jour, y compris les correctifs logiciels et les mises à jour de fonctionnalités, pour l’agent de synchronisation de fichiers Azure seront effectuées à partir de Microsoft Update. Nous vous recommandons d’utiliser la dernière mise à jour de la synchronisation de fichiers Azure. Consultez [Stratégie de mise à jour de la synchronisation de fichiers Azure](storage-sync-files-planning.md#azure-file-sync-agent-update-policy) pour plus d’informations.

À l’issue de l’installation de l’agent de synchronisation de fichiers Azure, l’interface utilisateur d’inscription de serveur démarrera automatiquement. Consultez la section suivante pour savoir comment inscrire ce serveur à la synchronisation de fichiers Azure.

## <a name="register-windows-server-with-storage-sync-service"></a>Inscrire un serveur Windows Server au service de synchronisation de stockage
L’inscription de votre serveur Windows Server à un service de synchronisation de stockage établit une relation d’approbation entre votre serveur (ou cluster) et le service de synchronisation de stockage. L’interface utilisateur d’inscription de serveur devrait démarrer automatiquement après l’installation de l’agent de synchronisation de fichiers Azure. Si n’est pas le cas, vous pouvez l’ouvrir manuellement à partir de son emplacement : `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`. Une fois l’interface utilisateur d’inscription de serveur ouverte, cliquez sur **Se connecter** pour commencer.

Après la connexion, les informations suivantes vous sont demandées :

![Capture d’écran de l’interface utilisateur d’inscription de serveur](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Abonnement Azure** : abonnement qui inclut le service de synchronisation de stockage (consultez [Déployer le service de synchronisation de stockage](#deploy-the-storage-sync-service) ci-dessus). 
- **Groupe de ressources** : groupe de ressources qui inclut le service de synchronisation de stockage.
- **Service de synchronisation de stockage** : nom du service de synchronisation de stockage auquel vous souhaitez vous inscrire.

Après avoir sélectionné les informations appropriées dans les listes déroulantes, cliquez sur **S’inscrire** pour terminer l’inscription du serveur. Dans le cadre du processus d’inscription, vous êtes invité à effectuer une nouvelle connexion.

## <a name="create-a-sync-group"></a>Créer un groupe de synchronisation
Un groupe de synchronisation définit la topologie de synchronisation d’un ensemble de fichiers. Les points de terminaison dans un groupe de synchronisation sont synchronisés entre eux. Un groupe de synchronisation doit contenir au moins un point de terminaison cloud, qui représente un partage de fichiers Azure, et un point de terminaison de serveur, qui représente un chemin d’accès sur un serveur Windows Server. Pour créer un groupe de synchronisation, accédez à votre service de synchronisation de stockage dans le [portail Azure](https://portal.azure.com/), puis cliquez sur **+ Groupe de synchronisation** :

![Créer un nouveau groupe de synchronisation dans le portail Azure](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

Le volet qui s’affiche nécessite les informations suivantes pour créer un groupe de synchronisation avec un point de terminaison cloud :

- **Nom du groupe de synchronisation** : nom du groupe de synchronisation à créer. Ce nom doit être unique au sein du service de synchronisation de stockage, mais vous pouvez choisir tout nom ayant un sens pour vous.
- **Abonnement** : abonnement pour lequel vous avez déployé le service de synchronisation de stockage en suivant la procédure[Déployer le service de synchronisation de stockage](#deploy-the-storage-sync-service) ci-dessus.
- **Compte de stockage** : cliquez sur « Sélectionner le compte de stockage ». Le nouveau volet qui s’affiche permet de sélectionner le compte de stockage contenant le partage de fichiers Azure avec lequel vous souhaitez vous synchroniser.
- **Partage de fichiers Azure** : nom du partage de fichiers Azure avec lequel vous souhaitez synchroniser.

Pour ajouter un point de terminaison de serveur, accédez au nouveau groupe de synchronisation créé, puis cliquez sur « Ajouter un point de terminaison de serveur ».

![Ajouter un nouveau point de terminaison de serveur dans le volet Groupe de synchronisation](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

Le volet « Ajouter un point de terminaison de serveur » qui s’affiche nécessite les informations suivantes pour créer un point de terminaison de serveur :

- **Serveur inscrit** : nom du serveur ou du cluster sur lequel créer le point de terminaison de serveur.
- **Chemin d’accès** : chemin d’accès sur le serveur Windows Server à synchroniser en tant qu’élément du groupe de synchronisation.
- **Hiérarchisation cloud** : commutateur activant ou désactivant la synchronisation cloud, qui permet de hiérarchiser les fichiers rarement ouverts ou utilisés dans Azure Files.
- **Espace libre du volume** : quantité d’espace libre à réserver sur le volume sur lequel se trouve le point de terminaison de serveur. Par exemple, si l’espace libre du volume est définie sur 50 % sur un volume avec un point de terminaison de serveur unique, environ la moitié de la quantité de données sera hiérarchisée dans Azure Files. Que la hiérarchisation cloud soit activée ou non, le partage de fichiers Azure dispose toujours d’une copie complète des données dans le groupe de synchronisation.

Cliquez sur « Créer » pour ajouter le point de terminaison de serveur. Vos fichiers seront désormais synchronisés sur le partage de fichiers Azure et sur le serveur Windows Server. 

## <a name="next-steps"></a>Étapes suivantes
- [Ajouter/supprimer un point de terminaison de serveur pour la synchronisation de fichiers Azure](storage-sync-files-server-endpoint.md)
- [Inscrire/désinscrire un serveur de la synchronisation de fichiers Azure](storage-sync-files-server-registration.md)