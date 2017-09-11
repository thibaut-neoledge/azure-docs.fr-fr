---
title: "Approvisionner des pools Azure Batch à partir d’images personnalisées | Microsoft Docs"
description: "Vous pouvez créer un pool Batch à partir d’une image personnalisée pour approvisionner les nœuds qui contiennent les logiciels et les données dont vous avez besoin pour votre application. Les images personnalisées sont un moyen efficace de configurer les nœuds de calcul pour exécuter vos charges de travail Batch."
services: batch
author: tamram
manager: timlt
ms.service: batch
ms.topic: article
ms.date: 08/07/2017
ms.author: tamram
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 3d655766b4f2a5efb0c8c29ffa81a89f84b3e17c
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---

# <a name="use-a-custom-image-to-create-a-pool-of-virtual-machines"></a>Utiliser une image personnalisée pour créer un pool de machines virtuelles

Quand vous créez un pool de machines virtuelles dans Azure Batch, vous spécifiez une image de machine virtuelle qui fournit le système d’exploitation à chaque nœud de calcul du pool. Vous pouvez créer un pool de machines virtuelles à partir d’une image de la Place de marché ou en fournissant une image de disque dur virtuel (VHD) personnalisée que vous avez préparée. Quand vous fournissez une image personnalisée, vous pouvez contrôler la façon dont le système d’exploitation est configuré au moment où chaque nœud de calcul est approvisionné. Votre image personnalisée peut également inclure des applications et des données de référence qui sont disponibles sur le nœud de calcul dès qu’il est approvisionné.

L’utilisation d’une image personnalisée peut réduire le temps de préparation des nœuds de calcul du pool appelés à exécuter la charge de travail Batch. Même si vous pouvez utiliser une image de la Place de marché et installer les logiciels sur chaque nœud de calcul une fois le nœud approvisionné, cette approche peut être moins efficace qu’utiliser une image personnalisée. 

Ce qui peut justifier l’utilisation d’une image personnalisée configurée pour votre scénario, ce sont entre autres les nécessités suivantes :

- **Configurer le système d’exploitation (SE)** : le système d’exploitation peut être configurée de façon spéciale sur l’image personnalisée. 
- **Installer des applications volumineuses** : il est plus judicieux d’installer des applications sur une image personnalisée que de les installer sur chaque nœud de calcul après leur approvisionnement.
- **Copier des quantités importantes de données** : si les données sont copiées sur l’image personnalisée, elles ne doivent l’être qu’une seule fois, et non sur chaque nœud de calcul, ce qui fait gagner du temps et de la bande passante.
- **Redémarrer la machine virtuelle pendant le processus d’installation** : le redémarrage de la machine virtuelle est un processus qui peut s’avérer fastidieux, surtout si vous avez plusieurs nœuds de calcul.

## <a name="prerequisites"></a>Composants requis

- **Un compte Batch créé avec le mode d’allocation de pool Abonnement utilisateur.** Pour pouvoir utiliser des images personnalisées pour vos pools de machines virtuelles, créez votre compte Batch avec le [mode d’allocation de pool](batch-api-basics.md#pool-allocation-mode) Abonnement utilisateur. Grâce à ce mode, les pools Batch sont alloués au sein de l’abonnement où se trouve le compte. Consultez la section [Compte](batch-api-basics.md#account) dans [Développer des solutions de calcul parallèles à grande échelle avec Batch](batch-api-basics.md) pour en savoir plus sur la définition du mode d’allocation de pool quand vous créez un compte Batch.

- **Un compte de stockage Azure.** Pour créer un pool de machines virtuelles à partir d’une image personnalisée, vous avez besoin d’un compte Stockage Azure standard à usage général qui relève du même abonnement et de la même région. Si vous créez une image personnalisée à partir d’une machine virtuelle Azure, vous devez copier l’image dans le compte de stockage où réside le disque SE de la machine virtuelle, et vous n’avez pas besoin de créer un compte de stockage distinct. 
    
## <a name="prepare-a-custom-image"></a>Préparer une image personnalisée

Pour préparer une image personnalisée en vue de l’utiliser avec Batch, vous devez généraliser une installation existante de Windows ou Linux. La généralisation d’une installation de système d’exploitation supprime les informations spécifiques à la machine. Le résultat est une image qui peut être installée sur d’autres ordinateurs ou machines virtuelles.  

> [!IMPORTANT]
> Batch ne prend actuellement pas en charge l’utilisation d’images gérées Azure pour approvisionner un pool. L’image personnalisée dont vous vous servez pour approvisionner un pool doit être stockée dans le Stockage Azure. 
>
> Au moment de préparer votre image personnalisée, gardez à l’esprit les points suivants :
> - Vérifiez que l’image de base du système d’exploitation utilisée pour approvisionner les pools Batch ne comporte pas d’extensions Azure préinstallées, telle que l’extension Script personnalisé. Si l’image contient une extension préinstallée, Azure peut rencontrer des problèmes de déploiement de la machine virtuelle.
> - Assurez-vous que l’image de base du système d’exploitation utilise le lecteur temporaire par défaut, puisque l’agent de nœud Batch attend actuellement ce lecteur.
>
>

Vous pouvez utiliser une image Windows ou Linux préparée comme image personnalisée. Par exemple, si vous voulez utiliser une image locale, chargez-la sur un compte Stockage Azure qui relève du même abonnement et de la même région que votre compte Batch en utilisant [AzCopy](../storage/storage-use-azcopy.md) ou un autre outil de chargement.

Vous pouvez aussi préparer une image personnalisée à partir d’une machine virtuelle Azure nouvelle ou existante. Si vous créez une machine virtuelle, vous pouvez utiliser une image de la Place de marché Azure comme image de base et personnaliser cette dernière. Pour personnaliser l’image de base, créez une machine virtuelle Azure et ajoutez-y des applications ou des données. Généralisez ensuite la machine virtuelle pour qu’elle serve d’image personnalisée et enregistrez-la dans le Stockage Azure. 

Pour préparer une image personnalisée à partir d’une machine virtuelle Azure, procédez comme suit :

1. Créez une machine virtuelle Azure **non gérée** à partir d’une image de la Place de marché Azure. La Place de marché Azure propose des images pour [Windows](../virtual-machines/windows/quick-create-portal.md) et [Linux](../virtual-machines/linux/quick-create-portal.md).
    
    À l’étape 3 du processus de création de machine virtuelle, veillez à sélectionnez **Non** pour l’option **Stockage : Utiliser Managed Disks**. De même, notez le nom du compte de stockage pour le disque SE de la machine virtuelle, car c’est aussi dans ce compte de stockage que Azure enregistrera votre image personnalisée :

    ![Créer une machine virtuelle non gérée et noter le nom du compte de stockage](media/batch-custom-images/vm-create-storage.png)
 
2. Terminez le processus de création de votre machine virtuelle et attendez qu’il soit alloué par Azure. Voici une image qui montre une machine virtuelle à l’état d’exécution dans le portail Azure :

    ![Créer une machine virtuelle à partir d’une image de la Place de marché](media/batch-custom-images/vm-status-running.png)

3. Une fois que la machine virtuelle s’exécute, connectez-la via le protocole RDP (pour Windows) ou SSH (pour Linux). Le cas échéant, installez les logiciels nécessaires ou copiez les données souhaitées, puis généralisez la machine virtuelle. Suivez les étapes décrites dans [Généraliser la machine virtuelle](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sa-copy-generalized.md#generalize-the-vm). 
   
4. Suivez la procédure de [connexion à Azure PowerShell](../virtual-machines/windows/sa-copy-generalized.md#log-in-to-azure-powershell). Pour installer Azure PowerShell, consultez [Vue d’ensemble d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.2.0). 

5. Ensuite, suivez les étapes pour [libérer la machine virtuelle et définir l’état sur Généralisé](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sa-copy-generalized#deallocate-the-vm-and-set-the-state-to-generalized). 

    Dans le portail Azure, notez que la machine virtuelle est libérée :

    ![Vérifier que la machine virtuelle est libérée](media/batch-custom-images/vm-status-deallocated.png)

6.  Créez et enregistrez l’image de machine virtuelle dans le Stockage Azure à l’aide de l’applet de commande PowerShell [Save-AzureRmVMImage](https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvmimage). Suivez les instructions décrites dans [Créer l’image](../virtual-machines/windows/sa-copy-generalized.md#create-the-image).
    
    L’image de machine virtuelle est enregistrée dans le compte de Stockage Azure créé au moment où vous avez créé la machine virtuelle comme indiqué dans l’étape 1 de cette procédure. L’applet de commande Save-AzureRmVMImage enregistre l’image dans le conteneur **système** de ce compte de stockage. Le paramètre `-DestinationContainername` nomme un répertoire virtuel dans le conteneur **système**. Le paramètre `-VHDNamePrefix` spécifie un préfixe pour le nom de l’objet blob. Ce préfixe est ajouté devant le nom de l’objet blob avec un trait d’union. 

    Par exemple, supposons que vous appelez Save-AzureRmVMImage avec les paramètres suivants :  

        Save-AzureRmVMImage -ResourceGroupName sample-resource-group -Name vm-custom-image -DestinationContainerName batchimages -VHDNamePrefix custom -Path C:\Temp\Images\vm-custom-image.json

    L’image obtenue est enregistrée à l’emplacement et sous le nom d’objet blob indiqués ici :

    ![Emplacement du VHD enregistré dans le conteneur système](media/batch-custom-images/vhd-in-vm-storage-account.png)

    > [!NOTE]
    > Une machine virtuelle non gérée Azure crée plusieurs comptes de stockage à des différentes fins. Si vous n’avez pas noté le nom du conteneur de stockage du disque SE au moment de créer la machine virtuelle, recherchez le compte de stockage associé qui contient le conteneur **système**. Dans le conteneur **système**, recherchez une image personnalisée en utilisant les valeurs que vous avez spécifiées pour la commande **Save-AzureRmVMImage**.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Créer un pool à partir d’une image personnalisée dans le portail

Une fois que vous avez enregistré votre image personnalisée et que vous connaissez son emplacement, vous pouvez créer un pool Batch à partir de cette image. Suivez ces étapes pour créer un pool à partir du portail Azure :

1. Accédez à votre compte  Batch dans le portail Azure. Ce compte doit relever du même abonnement et de la même région que le compte de stockage qui contient l’image personnalisée. 
2. Dans la fenêtre **Paramètres** située à gauche, sélectionnez l’élément de menu **Pools**.
3. Dans la fenêtre **Pools**, sélectionnez la commande **Ajouter**.
4. Dans la fenêtre **Ajouter un pool**, sélectionnez **Image personnalisée (Linux/Windows)** dans la liste déroulante **Type d’image**. Le portail affiche le sélecteur **Image personnalisée**. Accédez au compte de stockage où réside votre image personnalisée, puis sélectionnez le VHD souhaité dans le conteneur. 
5. Sélectionnez le **Serveur de publication/Offre/Référence (SKU)** qui convient pour votre VHD personnalisé.
6. Spécifiez les autres paramètres obligatoires, notamment **Taille de nœud**, **Nœuds dédiés cibles** et **Nœuds basse priorité** et éventuellement les paramètres facultatifs.

    Par exemple, pour une image personnalisée Microsoft Windows Server Datacenter 2016, la fenêtre **Ajouter un pool** s’affiche comme suit :

    ![Ajouter un pool à partir d’une image Windows personnalisée](media/batch-custom-images/add-pool-custom-image.png)
  
Pour vérifier si un pool existant est basé sur une image personnalisée, consultez la propriété **Système d’exploitation** dans la section récapitulative des ressources de la fenêtre **Pool**. Si le pool a été créé à partir d’une image personnalisée, sa valeur est **Image de machine virtuelle personnalisée**.

Tous les VHD associés à un pool sont affichés dans la fenêtre **Propriétés** de ce dernier.
 
## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une présentation détaillée de Batch, consultez [Développer des solutions de calcul parallèles à grande échelle avec Batch](batch-api-basics.md).
- Pour plus d’informations sur la création d’un compte Batch, consultez [Créer un compte Batch avec le portail Azure](batch-account-create-portal.md).
