---
title: "Déplacer une machine virtuelle AWS vers Azure | Microsoft Docs"
description: "Déplacer une instance Windows EC2 Amazon Web Services (AWS) vers les Machines virtuelles Azure en utilisant Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: cynthn
ms.openlocfilehash: 7d2b498d3f84c4fd6cccf97c6d7781f293f5b395
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-azure-using-powershell"></a>Déplacer une machine virtuelle Windows à partir de Amazon Web Services (AWS) vers Azure à l’aide de PowerShell

Si vous évaluez la capacité des machines virtuelles Azure à héberger vos charges de travail, vous pouvez exporter une instance existante de la machine virtuelle Windows fonctionnant sous Amazon Web Services (AWS) EC2, puis charger le disque dur virtuel sur Azure. Une fois le disque dur virtuel chargé, vous pouvez créer une machine virtuelle dans Azure à partir du disque dur virtuel. 

Ce sujet traite du transfert d’une machine virtuelle depuis AWS vers Azure. Si vous souhaitez transférer des machines virtuelles à partir de AWS vers Azure à l’échelle requise, voir [Migrer des machines virtuelles Windows dans Amazon Web Services (AWS) vers Azure avec Azure Site Recovery](../../site-recovery/site-recovery-migrate-aws-to-azure.md).

## <a name="prepare-the-vm"></a>Préparation de la machine virtuelle 
 
Vous pouvez télécharger des disques durs virtuels généralisés et spécialisés dans Azure. Chaque type nécessite que vous ayez préparé la machine virtuelle avant d’effectuer l’exportation à partir d’AWS. 

- **Disque dur virtuel généralisé** : toutes les informations de votre compte personnel de ce type de disque ont été supprimées avec Sysprep. Si vous prévoyez d’utiliser le disque dur virtuel en tant qu’image pour créer des machines virtuelles, vous devez : 
 
    * [Préparer une machine virtuelle Windows](prepare-for-upload-vhd-image.md).  
    * Généralisez la machine virtuelle en utilisant Sysprep.  

 
- **Disque dur virtuel spécialisé** : ce type de disque gère les comptes d’utilisateurs, les applications et les autres données d’état à partir de votre machine virtuelle d’origine. Si vous prévoyez d’utiliser le disque dur virtuel tel quel pour créer une machine virtuelle, veillez à effectuer les étapes suivantes.  
    * [Préparez un disque dur virtuel Windows à charger sur Azure](prepare-for-upload-vhd-image.md). **Ne généralisez pas** la machine virtuelle à l’aide de Sysprep. 
    * Supprimez tous les outils de virtualisation invité et les agents installés sur la machine virtuelle (c’est-à-dire les outils VMware). 
    * Vérifiez que la machine virtuelle est configurée pour extraire son adresse IP et ses paramètres DNS via DHCP. Cela permet de s’assurer que le serveur obtient une adresse IP dans le réseau virtuel lors de son démarrage.  


## <a name="export-and-download-the-vhd"></a>Exporter et télécharger le disque dur virtuel 

Exportez l’instance EC2 vers un disque dur virtuel dans un compartiment Amazon S3. Suivez les étapes décrites dans la rubrique de la documentation Amazon intitulée [Exportation d’une instance comme une machine virtuelle à l’aide de VM Import/Export](http://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) et exécutez la commande [create-instance-export-task](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) pour exporter l’instance EC2 vers un fichier du disque dur virtuel. 

Le fichier du disque dur virtuel exporté est sauvegardé dans le compartiment Amazon S3 spécifié. La syntaxe de base pour l’exportation du disque dur virtuel est indiquée ci-dessous. Remplacez le texte de l’espace réservé <brackets> par vos informations.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

Une fois le VHD exporté, suivez les instructions de la section [Comment télécharger un objet à partir d’un compartiment S3 ?](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) pour télécharger le fichier du VHD depuis le compartiment S3. 

> [!IMPORTANT]
> AWS facture des frais de transfert de données lors du téléchargement du disque dur virtuel. Pour en savoir plus, consultez la page [Tarification Amazon S3](https://aws.amazon.com/s3/pricing/).


## <a name="next-steps"></a>Étapes suivantes

À présent, vous pouvez charger le disque dur virtuel sur Azure et créer une machine virtuelle. 

- Si vous avez exécuté Sysprep sur votre source afin de la **généraliser** avant l’exportation, voir [Créer une machine virtuelle à partir d’un disque dur virtuel généralisé chargé dans Azure à l’aide de Managed Disks](upload-generalized-managed.md).
- Si vous n’avez pas exécuté Sysprep avant l’exportation, le disque dur virtuel est considéré comme **spécialisé** (voir [Créer une machine virtuelle à partir d’un disque spécialisé](create-vm-specialized.md)).

 
