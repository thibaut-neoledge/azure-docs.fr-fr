---
title: "Copier un disque géré Azure pour la sauvegarde | Microsoft Docs"
description: "Apprenez à créer une copie de disque géré Azure à utiliser pour la sauvegarde ou la résolution des problèmes de disque."
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 2/6/2017
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 6fda4b6e77104b6022b86010b53b46ae5df1b82e
ms.openlocfilehash: 8bc6ac01f1592d371abbb2df3ff6ecf4ee2a993c
ms.lasthandoff: 02/27/2017


---
# <a name="create-a-copy-of-a-vhd-stored-as-an-azure-managed-disk-by-using-managed-snapshots"></a>Créer une copie d’un disque dur virtuel stocké en tant que disque Azure géré à l’aide de captures instantanées gérées
Prenez une capture instantanée d’un disque géré pour la sauvegarde ou créez un disque géré à partir de la capture instantanée et attachez-le à une machine virtuelle de test pour résoudre les problèmes. Une capture instantanée gérée est une copie complète d’un disque géré de machine virtuelle à un moment donné. Elle crée une copie en lecture seule de votre disque dur virtuel et, par défaut, le stocke sous la forme d’un disque géré Standard. 

Pour plus d’informations sur la tarification, consultez la page [Tarification Azure Storage](https://azure.microsoft.com/pricing/details/managed-disks/). <!--Add link to topic or blog post that explains managed disks. -->

Utilisez le Portail Azure ou Azure CLI 2.0 pour prendre une capture instantanée du disque géré.

## <a name="use-azure-cli-20-to-take-a-snapshot"></a>Utiliser Azure CLI 2.0 pour prendre une capture instantanée

> [!NOTE] 
> L’exemple suivant nécessite l’installation d’Azure CLI 2.0 et la connexion à votre compte Azure.

Les étapes suivantes montrent comment obtenir et prendre une capture instantanée d’un disque de système d’exploitation géré en utilisant la commande `az snapshot create` avec le paramètre `--source-disk`. L’exemple suivant suppose qu’il existe une machine virtuelle nommée `myVM` créée avec un disque de système d’exploitation géré dans le groupe de ressources `myResourceGroup`.

```azure-cli
# take the disk id with which to create a snapshot
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create -g myResourceGroup --source "$osDiskId" --name osDisk-backup
```

Le résultat suivant doit s’afficher :

```json
{
  "accountType": "Standard_LRS",
  "creationData": {
    "createOption": "Copy",
    "imageReference": null,
    "sourceResourceId": null,
    "sourceUri": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/disks/osdisk_6NexYgkFQU",
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/osDisk-backup",
  "location": "westus",
  "name": "osDisk-backup",
  "osType": "Linux",
  "ownerId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "timeCreated": "2017-02-06T21:27:10.172980+00:00",
  "type": "Microsoft.Compute/snapshots"
}
```

## <a name="use-azure-portal-to-take-a-snapshot"></a>Utiliser le portail Azure pour prendre une capture instantanée 

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans l’angle supérieur gauche, cliquez sur **Nouveau** et recherchez **Capture instantanée**.
3. Dans le panneau Capture instantanée, cliquez sur **Créer**.
4. Entrez un **nom** pour la capture instantanée.
5. Sélectionnez un [groupe de ressources](../../azure-resource-manager/resource-group-overview.md#resource-groups) existant ou tapez le nom d’un nouveau groupe. 
6. Sélectionnez un emplacement de centre de données Azure.  
7. Dans **Disque source**, sélectionnez le disque géré dont vous souhaitez obtenir une capture instantanée.
8. Sélectionnez le **type de compte** à utiliser pour stocker la capture instantanée. Nous vous recommandons d’utiliser le type **Standard_LRS**, sauf si vous avez besoin de la stocker sur un disque hautes performances.
9. Cliquez sur **Create**.

Si vous envisagez d’utiliser la capture instantanée pour créer un disque géré et l’attacher à une machine virtuelle qui doit être hautement performante, utilisez le paramètre `--sku Premium_LRS` avec la commande `az snapshot create`. Cela permet de créer la capture instantanée et de la stocker en tant que disque géré Premium. Les disques gérés Premium sont plus performants, car il s’agit de disques SSD, mais ils coûtent plus cher que des disques Standard (HDD).



