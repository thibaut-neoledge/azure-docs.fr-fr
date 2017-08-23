---
title: "Utiliser le service Azure Batch Rendering pour créer des rendus dans le cloud | Microsoft Docs"
description: "Créez des travaux de rendu sur des machines virtuelles Azure directement à partir de Maya et sur une base de paiement à l’utilisation."
services: batch
author: tamram
manager: timlt
ms.service: batch
ms.topic: hero-article
ms.date: 07/31/2017
ms.author: tamram
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: 4d22f92cafdbceee5213361d6d2b2f38904d12c6
ms.contentlocale: fr-fr
ms.lasthandoff: 08/01/2017

---

# <a name="get-started-with-the-batch-rendering-service"></a>Prise en main du service Batch Rendering

Le service Azure Batch Rendering offre des fonctionnalités de création de rendus à l’échelle du cloud, sur une base de paiement à l’utilisation. Le service Batch Rendering traite la planification des travaux et leur mise en file d’attente, la gestion des échecs et des nouvelles tentatives et la mise à l’échelle automatique de votre travail de rendu. Il prend en charge Autodesk Maya, 3ds Max, et Arnold. La prise en charge d’autres applications est prévue prochainement. Le plug-in Batch pour Maya 2017 facilite le lancement d’un travail de rendu sur Azure directement à partir de votre bureau. 

## <a name="supported-applications"></a>Applications prises en charge

Le service Batch Rendering prend actuellement en charge les applications suivantes :

- Autodesk Maya
- Autodesk 3ds Max
- Autodesk Arnold

## <a name="prerequisites"></a>Composants requis

Pour utiliser le service Batch Rendering, vous avez besoin des éléments suivants :

- Un [compte Azure](https://azure.microsoft.com/free/). 
- **Un compte Azure Batch.** Pour obtenir des conseils sur la création d’un compte Batch dans le portail Azure, consultez [Créer un compte Batch avec le portail Azure](batch-account-create-portal.md).
- **Un compte de stockage Azure.** Les ressources utilisées pour votre travail de rendu sont stockées dans le stockage Azure. Vous pouvez créer un compte de stockage automatiquement lorsque vous configurez votre compte Batch. Vous pouvez également utiliser un compte de stockage existant. Pour en savoir plus sur les comptes de stockage, consultez [À propos des comptes de stockage Azure](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

Pour utiliser le plug-in Batch pour Maya, vous avez besoin des éléments suivants :

- **Maya 2017**
- **Arnold for Maya**

Vous pouvez également utiliser le [portail Azure](https://portal.azure.com) pour créer des pools de machines virtuelles préconfigurés avec Maya, 3ds Max et Arnold. Vous pouvez utiliser le portail pour surveiller les travaux et diagnostiquer les tâches ayant échoué en téléchargeant les journaux des applications et en vous connectant à distance à des machines virtuelles individuelles à l’aide des protocoles RDP ou SSH.

## <a name="basic-batch-concepts"></a>Concepts Batch de base

Avant de commencer à utiliser le service Batch Rendering, il est utile de vous familiariser avec quelques concepts Batch comme les nœuds de calcul, les pools et les travaux. Pour en savoir plus sur Azure Batch en général, consultez [Exécuter des charges de travail intrinsèquement parallèles avec Batch](batch-technical-overview.md).

### <a name="pools"></a>Pools

Batch est un service de plateforme permettant d’exécuter des tâches nécessitant beaucoup de ressources système, comme un rendu, sur un **pool** de **nœuds de calcul**. Chacun des nœuds de calcul dans un pool est une machine virtuelle Azure exécutant Windows ou Linux. 

Pour plus d’informations sur les nœuds de calcul et pools Batch, consultez les sections [Pool](batch-api-basics.md#pool) et [Nœud de calcul](batch-api-basics.md#compute-node) de l’article [Développer des solutions de calcul parallèles à grande échelle avec Batch](batch-api-basics.md).

### <a name="jobs"></a>Travaux

Un travail **Batch** est une collection de tâches qui s’exécutent sur les nœuds de calcul d’un pool. Lorsque vous envoyez un travail de rendu, Batch le divise en tâches et les distribue aux nœuds de calcul dans le pool à exécuter.

Pour plus d’informations sur les travaux Batch, consultez la section [Travail](batch-api-basics.md#job) de l’article [Développer des solutions de calcul parallèles à grande échelle avec Batch](batch-api-basics.md).

## <a name="use-the-batch-plug-in-for-maya-to-submit-a-render-job"></a>Utiliser le plug-in Batch pour Maya afin d’envoyer un travail de rendu

Le plug-in Batch pour Maya vous permet d’envoyer un travail au service Batch Rendering directement depuis Maya. Les sections suivantes décrivent comment configurer le travail à partir du plug-in et comment l’envoyer. 

### <a name="load-the-batch-plug-in-in-maya"></a>Charger le plug-in Batch dans Maya

Le plug-in Batch est disponible sur [GitHub](https://github.com/Azure/azure-batch-maya/releases). Décompressez l’archive dans un répertoire de votre choix. Vous pouvez charger le plug-in directement à partir du répertoire *azure_batch_maya*.

Pour charger le plug-in dans Maya :

1. Exécutez Maya.
2. Ouvrez **Fenêtre** > **Paramètres/Préférences** > **Plug-in Manager** (Gestionnaire de plug-in).
3. Cliquez sur **Parcourir**.
4. Accédez à *azure_batch_maya/plug-in/AzureBatch.py* et sélectionnez cet élément.

### <a name="authenticate-access-to-your-batch-and-storage-accounts"></a>Authentifier l’accès à vos comptes de stockage et Batch

Pour utiliser le plug-in, vous devez vous authentifier à l’aide de vos clés de compte Azure Batch et de stockage Azure. Pour récupérer vos clés de compte :

1. Affichez le plug-in dans Maya, puis sélectionnez l’onglet **Configuration**.
2. Accédez au [portail Azure](https://portal.azure.com).
3. Sélectionnez **Comptes Batch** dans le menu de gauche. Si nécessaire, cliquez sur **Plus Services** et filtrez sur _Batch_.
4. Recherchez le compte Batch souhaité dans la liste.
5. Sélectionnez l’élément de menu **Clés** pour afficher le nom de votre compte, l’URL de celui-ci, ainsi que les clés d’accès :
    - Collez l’URL du compte Batch dans le champ **Service** du plug-in Batch.
    - Collez le nom du compte dans le champ **Compte Batch**.
    - Collez la clé de compte principale dans le champ **Batch Key** (Clé Batch).
7. Sélectionnez Comptes de stockage dans le menu de gauche. Si nécessaire, cliquez sur **Plus Services** et filtrez sur _Stockage_.
8. Recherchez le compte de stockage souhaité dans la liste.
9. Sélectionnez l’élément de menu **Clés d’accès** pour afficher le nom et les clés du compte de stockage.
    - Collez le nom du compte de stockage dans le champ **Compte de stockage** du plug-in Batch.
    - Collez la clé de compte principale dans le champ **Clé de stockage**.
10. Cliquez sur **Authentifier** pour vérifier que le plug-in peut accéder aux deux comptes.

Une fois l’authentification réussie, le plug-in définit le champ associé à l’état sur **Authentifié** : 

![Authentifier vos comptes de stockage et Batch](./media/batch-rendering-service/authentication.png)

### <a name="configure-a-pool-for-a-render-job"></a>Configurer un pool pour un travail de rendu

Une fois que vous avez authentifié vos comptes de stockage et Batch, définissez un pool pour votre travail de rendu. Le plug-in enregistre vos sélections entre les sessions. Une fois que vous avez configuré votre configuration préférée, vous n’aurez pas à la modifier, sauf si elle change.

Les sections suivantes décrivent les options disponibles, accessibles via l’onglet **Envoyer** :

#### <a name="specify-a-new-or-existing-pool"></a>Spécifier un pool nouveau ou existant

Pour spécifier un pool sur lequel exécuter le travail de rendu, sélectionnez l’onglet **Envoyer**. Cet onglet propose des options permettant de créer un pool ou d’un sélectionner un existant :

- Vous pouvez **configurer automatiquement un pool pour ce travail** (option par défaut). Lorsque vous choisissez cette option, Batch crée le pool exclusivement pour le travail actuel et supprime automatiquement le pool lorsque le travail de rendu est terminé. Cette option est recommandée lorsque vous n’avez qu’un travail de rendu à réaliser.
- Vous pouvez **réutiliser un pool persistant existant**. Si vous disposez d’un pool existant qui est inactif, vous pouvez spécifier ce pool pour l’exécution de la tâche de rendu en le sélectionnant dans la liste déroulante. La réutilisation d’un pool persistant existant vous permet de gagner du temps en évitant de configurer le pool.  
- Vous pouvez **créer un pool persistant**. En choisissant cette option, un pool dédié à l’exécution du travail est créé. Le pool n’étant pas supprimé une fois le travail terminé, vous pouvez le réutiliser pour des travaux futurs. Sélectionnez cette option si vous avez souvent besoin d’exécuter des travaux de rendu. Pour les travaux suivants, vous pouvez sélectionner l’option de **réutilisation d’un pool persistant existant** afin d’utiliser le pool persistant créé pour le premier travail.

![Spécifier le pool, l’image de système d’exploitation, la taille de machine virtuelle et la licence](./media/batch-rendering-service/submit.png)

Pour plus d’informations sur les frais liés aux machines virtuelles Azure, consultez le [Forum aux questions sur la tarification Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#faq) et le [Forum aux questions sur la tarification Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/#faq).

#### <a name="specify-the-os-image-to-provision"></a>Spécifier l’image de système d’exploitation pour la configuration

Vous pouvez spécifier le type d’image de système d’exploitation à utiliser pour configurer des nœuds de calcul dans le pool sur l’onglet **Env** (Environnement). Batch prend actuellement en charge les options d’image suivantes pour les travaux de rendu :

|Système d’exploitation  |Image  |
|---------|---------|
|Linux     |Préversion CentOS Batch |
|Windows     |Préversion Windows Batch |

#### <a name="choose-a-vm-size"></a>Choisir une taille de machine virtuelle

Vous pouvez spécifier la taille de machine virtuelle dans l’onglet **Env**. Pour plus d’informations sur les tailles de machine virtuelle disponibles, consultez [Tailles des machines virtuelles Linux dans Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) et [Tailles des machines virtuelles Windows dans Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes). 

![Spécifier la taille de système d’exploitation de machine virtuelle et la taille dans l’onglet Env](./media/batch-rendering-service/environment.png)

#### <a name="specify-licensing-options"></a>Spécifier les options de licence

Vous pouvez spécifier les licences que vous souhaitez utiliser dans l’onglet **Env**. Options disponibles :

- **Maya**, qui est activée par défaut.
- **Arnold**, qui est activé si Arnold est détecté comme étant le moteur de rendu actif dans Maya.

 Si vous souhaitez créer un rendu en utilisant votre propre licence, vous pouvez configurer le point de terminaison de votre licence en ajoutant les variables d’environnement appropriées dans le tableau. Dans ce cas, veillez à désélectionner les options de licence par défaut.

> [!IMPORTANT]
> Vous êtes facturé pour l’utilisation des licences quand les machines virtuelles s’exécutent dans le pool, même si elles ne sont pas en cours d’utilisation pour créer le rendu. Pour éviter des frais supplémentaires, accédez à l’onglet **Pools** et redimensionnez le pool sur 0 nœud jusqu’à ce que vous soyez prêt à exécuter un autre travail de rendu. 
>
>

#### <a name="manage-persistent-pools"></a>Gérer des pools persistants

Vous pouvez gérer un pool persistant dans l’onglet **Pools**. En sélectionnant un pool dans la liste, l’état actuel du pool s’affiche.

L’onglet **Pools** vous permet également de supprimer le pool et de redimensionner le nombre de machines virtuelles dans le pool. Vous pouvez redimensionner un pool sur 0 nœud pour éviter d’encourir des frais entre les charges de travail.

![Afficher, redimensionner et supprimer des pools](./media/batch-rendering-service/pools.png)

### <a name="configure-a-render-job-for-submission"></a>Configurer un travail de rendu pour l’envoi

Une fois que vous avez spécifié les paramètres pour le pool qui exécutera le travail de rendu, configurez le travail en lui-même. 

#### <a name="specify-scene-parameters"></a>Spécifier les paramètres de scène

Le plug-in Batch détecte le moteur de rendu que vous utilisez actuellement dans Maya et affiche les paramètres de rendu appropriés dans l’onglet **Envoyer**. Ces paramètres incluent l’image de départ, l’image de fin, le préfixe de sortie et la séquence d’images. Vous pouvez remplacer les paramètres de rendu de fichier de scène en spécifiant d’autres paramètres dans le plug-in. Les modifications apportées aux paramètres du plug-in ne sont pas persistantes dans les paramètres de rendu de fichier de scène. Vous pouvez donc apporter des modifications travail par travail, sans avoir à charger de nouveau le fichier de scène.

Le plug-in vous avertit si le moteur de rendu que vous avez sélectionné dans Maya n’est pas pris en charge.

Si vous chargez une nouvelle scène alors que le plug-in est ouvert, cliquez sur le bouton **Actualiser** pour vous assurer que les paramètres sont mis à jour.

#### <a name="resolve-asset-paths"></a>Résoudre les chemins d’accès de ressources

Lorsque vous chargez le plug-in, il analyse le fichier de scène pour toutes les références de fichier externe. Ces références sont affichées dans l’onglet **Ressources**. Si un chemin d’accès référencé ne peut pas être résolu, le plug-in tente de rechercher le fichier dans quelques emplacements par défaut, y compris :

- L’emplacement du fichier de scène 
- Le répertoire _sourceimages_ du projet actuel
- Le répertoire de travail actuel 

Si la ressource ne parvient pas à être localisée, elles est répertoriée avec une icône d’avertissement :

![Les ressources manquantes sont affichées avec une icône d’avertissement](./media/batch-rendering-service/missing_assets.png)

Si vous connaissez l’emplacement d’une référence de fichier non résolue, vous pouvez cliquer sur l’icône d’avertissement pour être invité à ajouter un chemin de recherche. Le plug-in utilise ensuite ce chemin de recherche pour tenter de résoudre toutes les ressources manquantes. Vous pouvez ajouter n’importe quel nombre de chemins de recherche.

Lorsqu’une référence est résolue, elle est répertoriée avec une icône verte :

![Les ressources résolues sont affichées avec une icône verte](./media/batch-rendering-service/found_assets.png)

Si votre scène requiert d’autres fichiers que le plug-in n’a pas détectés, vous pouvez ajouter des fichiers ou des répertoires. Utilisez les boutons **Ajouter des fichiers** et **Ajouter un répertoire**. Si vous chargez une nouvelle scène alors que le plug-in est ouvert, veillez à cliquer sur **Actualiser** pour mettre à jour les références de la scène.

#### <a name="upload-assets-to-an-asset-project"></a>Charger des ressources dans un projet de ressources

Lorsque vous envoyer un travail de rendu, les fichiers référencés affichés dans l’onglet **Ressources** sont automatiquement chargés vers le stockage Azure en tant que projet de ressources. Vous pouvez également charger les fichiers de ressources indépendamment d’un travail de rendu à l’aide du bouton **Charger** sur l’onglet **Ressources**. Le nom du projet de ressources est spécifié dans le champ **Projet** champ et est nommé d’après le projet Maya actif par défaut. Lorsque les fichiers de ressources sont chargés, la structure de fichier locale est conservée. 

Une fois téléchargées, les ressources peuvent être référencées par n’importe quel nombre de travaux de rendu. Toutes les ressources chargées sont disponibles pour un travail qui fait référence au projet de ressources, qu’elles soient incluses dans la scène ou non. Pour modifier le projet de ressources référencé par votre travail suivant, modifiez le nom dans le champ **Projet** de l’onglet **Ressources**. S’il existe des fichiers référencés que vous souhaitez exclure du chargement, désélectionnez-les à l’aide du bouton vert en regard de la liste.

#### <a name="submit-and-monitor-the-render-job"></a>Envoyer et surveiller le travail de rendu

Une fois que vous avez configuré le travail de rendu dans le plug-in, cliquez sur le bouton **Envoyer le travail** dans l’onglet **Envoyer** pour envoyer le travail à Batch :

![Envoyer le travail de rendu](./media/batch-rendering-service/submit_job.png)

Vous pouvez surveiller un travail en cours à partir de l’onglet **Travaux** du plug-in. Sélectionnez un travail dans la liste pour afficher l’état actuel du travail. Vous pouvez également utiliser cet onglet pour annuler et supprimer des travaux, ainsi que pour télécharger les sorties et les journaux de rendu. 

Pour télécharger des sorties, modifiez le champ **Sorties** pour définir le répertoire de destination souhaité. Cliquez sur l’icône d’engrenage pour démarrer un processus en arrière-plan qui surveille le travail et télécharge des sorties à mesure qu’il progresse : 

![Afficher l’état du travail et télécharger des sorties](./media/batch-rendering-service/jobs.png)

Vous pouvez fermer Maya sans interrompre le processus de téléchargement.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Batch, consultez [Exécuter des charges de travail intrinsèquement parallèles avec Batch](batch-technical-overview.md).
