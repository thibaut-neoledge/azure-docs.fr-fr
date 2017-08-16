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
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 1248aeeaa159789b008eb003c2cd7babe0432919
ms.contentlocale: fr-fr
ms.lasthandoff: 08/09/2017

---

# <a name="use-a-custom-image-to-create-a-pool"></a>Utiliser une image personnalisée pour créer un pool

Quand vous créez un pool Azure Batch, vous spécifiez une image de machine virtuelle qui fournit la configuration du système d’exploitation pour chaque nœud de calcul dans le pool. Vous pouvez créer un pool à l’aide d’une image de la Place de marché, ou en fournissant une image personnalisée que vous avez préparée. Quand vous fournissez une image personnalisée, vous pouvez contrôler la façon dont le système d’exploitation est configuré au moment où chaque nœud de calcul est approvisionné. Votre image personnalisée peut également inclure des applications et des données de référence qui sont disponibles sur le nœud de calcul dès qu’il est approvisionné.

L’utilisation d’une image personnalisée peut réduire le temps de préparation des nœuds de calcul du pool appelés à exécuter la charge de travail Batch. Même si vous pouvez utiliser une image de la Place de marché et installer les logiciels sur chaque nœud de calcul une fois le nœud approvisionné, cette approche peut être moins efficace qu’utiliser une image personnalisée. Si vous devez installer des applications volumineuses, copier des quantités importantes de données ou redémarrer la machine virtuelle pendant le processus d’installation, envisagez d’utiliser une image personnalisée adaptée à vos besoins.  

## <a name="prerequisites"></a>Prérequis

- **Un compte Batch créé avec le mode d’allocation de pool Abonnement utilisateur.** Pour pouvoir utiliser des images personnalisées pour vos pools de machines virtuelles, créez votre compte Batch avec le [mode d’allocation de pool](batch-api-basics.md#pool-allocation-mode) Abonnement utilisateur. Grâce à ce mode, les pools Batch sont alloués au sein de l’abonnement où se trouve le compte. Consultez la section [Compte](batch-api-basics.md#account) dans [Développer des solutions de calcul parallèles à grande échelle avec Batch](batch-api-basics.md) pour en savoir plus sur la définition du mode d’allocation de pool quand vous créez un compte Batch.

- **Un compte de stockage Azure.** Pour créer un pool de configuration de machines virtuelles à l’aide d’une image personnalisée, vous devez disposer au minimum d’un compte de stockage Microsoft Azure standard dans lequel stocker vos images VHD personnalisées. Les images personnalisées sont stockées en tant que blobs. Pour faire référence à vos images personnalisées lorsque vous créez un pool, spécifiez les URI des blobs VHD d’images personnalisées pour la propriété [osDisk](https://docs.microsoft.com/rest/api/batchservice/add-a-pool-to-an-account#bk_osdisk) de la propriété [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/add-a-pool-to-an-account#bk_vmconf).

    Assurez-vous que vos comptes de stockage répondent aux critères suivants :   
    
    - Les comptes de stockage contenant des blobs VHD d’images personnalisées doivent se trouver dans le même abonnement que le compte Batch (abonnement utilisateur).
    - Les comptes de stockage spécifiés doivent se trouver dans la même région que le compte Batch.
    - Actuellement, seuls les comptes de stockage standards sont pris en charge. Nous prendrons prochainement en charge le stockage Azure Premium.
    - Vous pouvez spécifier un compte de stockage avec plusieurs blobs VHD personnalisés, ou plusieurs comptes de stockage incluant un seul blob. Nous vous recommandons d’utiliser plusieurs comptes de stockage, afin d’optimiser les performances.
    - Un seul blob VHD d’images personnalisées peut gérer jusqu’à 40 instances de machines virtuelles Linux, ou 20 instances de machines virtuelles Windows. Vous pouvez créer des copies des blobs VHD pour pouvoir générer des pools incluant plusieurs machines virtuelles. Par exemple, un pool incluant 200 machines virtuelles Windows requiert la spécification de 10 blobs VHD uniques pour la propriété **osDisk**.
    
## <a name="prepare-a-custom-image"></a>Préparer une image personnalisée

Pour préparer une image personnalisée en vue de l’utiliser avec Batch, vous devez généraliser une installation existante de Windows ou Linux. La généralisation d’une installation de système d’exploitation supprime les informations spécifiques à la machine. Le résultat est une image qui peut être installée sur d’autres ordinateurs ou machines virtuelles.  

Vous pouvez utiliser une image de la Place de marché en tant qu’image de base pour votre image personnalisée. Pour personnaliser l’image de base, créez une machine virtuelle Azure et ajoutez-y des applications ou des données. Ensuite, généralisez la machine virtuelle pour qu’elle serve d’image personnalisée.   

Pour plus d’informations sur la préparation d’images Linux personnalisées à partir de machines virtuelles Azure, consultez [Guide pratique pour créer une image d’une machine virtuelle ou d’un VHD](../virtual-machines/linux/capture-image.md). 

Pour plus d’informations sur la préparation d’images Windows personnalisées à partir de machines virtuelles Azure, consultez [Créer une image personnalisée d’une machine virtuelle Azure à l’aide de PowerShell](../virtual-machines/windows/tutorial-custom-images.md) et [Vue d’ensemble de Sysprep (préparation du système)](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). 

> [!IMPORTANT]
> Lors de la préparation de votre image personnalisée, gardez à l’esprit les points suivants :
> - Vérifiez que l’image de base du système d’exploitation utilisée pour approvisionner les pools Batch ne comporte pas d’extensions Azure préinstallées, telle que l’extension Script personnalisé. Si l’image contient une extension préinstallée, Azure peut rencontrer des problèmes de déploiement de la machine virtuelle.
> - Assurez-vous que l’image de base du système d’exploitation utilise le lecteur temporaire par défaut, puisque l’agent de nœud Batch attend actuellement ce lecteur.
>
>
    
## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Créer un pool à partir d’une image personnalisée dans le portail

Pour créer un pool à partir d’une image personnalisée, à l’aide du portail Azure, procédez comme suit :

1. Accédez à votre compte  Batch dans le portail Azure.
2. Dans le panneau **Paramètres**, sélectionnez l’élément de menu **Pools**.
3. Dans le panneau **Pools**, sélectionnez la commande **Ajouter**. Le panneau **Ajouter un pool** s’affiche.
4. Sélectionnez l’option **Image personnalisée (Linux/Windows)** dans la liste déroulante **Type d’image**. Le portail affiche le sélecteur **Image personnalisée**. Choisissez un ou plusieurs VHD dans le même conteneur et cliquez sur le bouton **Sélectionner**. 
   La prise en charge de la sélection de VHD à partir de plusieurs comptes de stockage et conteneurs différents sera disponible dans une prochaine version.
5. Sélectionnez les valeurs **d’éditeur/d’offre/de référence SKU** adéquates de vos VHD personnalisés, choisissez le mode de **mise en cache**, puis indiquez l’ensemble des autres paramètres du pool.
6. Pour vérifier si un pool est basé sur une image personnalisée, consultez la propriété relative au **système d’exploitation** dans la section Récapitulatif des ressources du panneau **Pool**. La valeur de cette propriété doit être **Image de machine virtuelle personnalisée**.
7. Tous les VHD associés à un pool sont affichés sur le panneau **Propriétés** de ce dernier.
 
## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une présentation détaillée de Batch, consultez [Développer des solutions de calcul parallèles à grande échelle avec Batch](batch-api-basics.md).
- Pour plus d’informations sur la création d’un compte Batch, consultez [Créer un compte Batch avec le portail Azure](batch-account-create-portal.md).
