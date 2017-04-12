---
title: "Capture d’une machine virtuelle Linux avec Azure CLI 2.0 | Microsoft Docs"
description: "Capturer et généraliser l’image d’une machine virtuelle Azure sous Linux à l’aide de disques gérés créés avec Azure CLI 2.0"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8433f326f04d0585e538695c419e451323c7869b
ms.lasthandoff: 04/03/2017


---
# <a name="how-to-generalize-and-capture-a-linux-virtual-machine"></a>Généraliser et capturer une machine virtuelle Linux
Pour réutiliser les machines virtuelles déployées et configurées dans Azure, vous capturez une image de la machine virtuelle. Ce processus implique également la généralisation de la machine virtuelle afin de supprimer les informations liées au compte personnel avant le déploiement de nouvelles machines virtuelles à partir de l’image. Cet article explique comment capturer une image de machine virtuelle avec Azure CLI 2.0 pour une machine virtuelle en utilisant Azure Managed Disks. Ces disques sont gérés par la plateforme Azure et ne nécessitent pas de préparation ou d’emplacement pour les stocker. Pour plus d’informations, voir la page [Azure Managed Disks overview](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Vue d’ensemble d’Azure Managed Disks). Cet article décrit la capture d’une machine virtuelle Linux à l’aide de l’interface Azure CLI 2.0. Vous pouvez également effectuer ces étapes à l’aide [d’Azure CLI 1.0](capture-image-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!TIP]
> Si vous souhaitez créer une copie de votre machine virtuelle Linux existante avec son état spécialisé pour la sauvegarde ou le débogage, consultez [Créer une copie d’une machine virtuelle Linux sur Azure](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Si vous souhaitez charger un disque dur virtuel Linux à partir d’une machine virtuelle locale, consultez [Chargement et création d’une machine virtuelle à partir d’une image de disque personnalisée](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  


## <a name="before-you-begin"></a>Avant de commencer
Assurez-vous de satisfaire les prérequis suivants :

* **Machine virtuelle Azure créée dans le modèle de déploiement Resource Manager** : si vous n’avez pas créé une machine virtuelle Linux, vous pouvez utiliser le [portail](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), [l’interface CLI Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou les [modèles Resource Manager](cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Configurez la machine virtuelle en fonction de vos besoins. Par exemple, [ajoutez des disques de données](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), appliquez des mises à jour et installez des applications. 

Vous devez également disposer de la dernière version d’[Azure CLI 2.0](/cli/azure/install-az-cli2) et vous connecter à un compte Azure avec la commande [az login](/cli/azure/#login).

## <a name="quick-commands"></a>Commandes rapides
Si vous avez besoin d’accomplir rapidement cette tâche, la section suivante décrit les commandes de base qui vous permettront de capturer l’image d’une machine virtuelle Linux dans Azure. Pour obtenir plus d’informations et davantage de contexte pour chaque étape, lisez la suite de ce document, à partir de [cette section](#detailed-steps). Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Exemples de noms de paramètre : `myResourceGroup`, `myVM` et `myImage`.

1. Annulez le déploiement de votre machine virtuelle source :

    ```bash
    ssh ops@myvm.westus.cloudapp.azure.com
    sudo waagent -deprovision+user -force
    exit
    ```

2. Libérez la machine virtuelle à l’aide de la commande [az vm deallocate](/cli/azure/vm#deallocate) :

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Généralisez la machine virtuelle à l’aide de la commande [az vm generalize](/cli/azure/vm#generalize) :
   
    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ```

4. Créez une image à partir de la ressource de machine virtuelle à l’aide de la commande [az image create](/cli/azure/image#create) :
   
    ```azurecli
    az image create --resource-group myResourceGroup --name myImage --source myVM
    ```

5. Créez une machine virtuelle à partir de la ressource d’image à l’aide de la commande [az vm create](/cli/azure/vm#create) :

    ```azurecli
    az vm create --resource-group myResourceGroup --name myVMDeployed --image myImage
        --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub
    ```

## <a name="detailed-steps"></a>Procédure détaillée
Les étapes suivantes vous permettent d’annuler le déploiement d’une machine virtuelle existante, de libérer et de généraliser la ressource de machine virtuelle, puis de créer une image. Vous pouvez utiliser cette image pour créer des machines virtuelles dans n’importe quel groupe de ressources de votre abonnement. Ce processus offre un avantage à [Azure Managed Disks](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) par rapport aux disques non gérés. Avec les disques non gérés, vous créez une copie blob du disque dur virtuel sous-jacent. Vous pouvez ensuite uniquement créer des machines virtuelles dans le même compte de stockage que l’objet blob de disque dur virtuel copié. Avec les disques gérés, vous créez une ressource d’image qui peut être déployée dans l’ensemble de votre abonnement.

## <a name="step-1-remove-the-azure-linux-agent"></a>Étape 1 : supprimer l’agent Linux Azure
Pour préparer la machine virtuelle à la généralisation, vous annulez le déploiement de la machine virtuelle à l’aide de l’agent de machine virtuelle Azure pour supprimer les fichiers et les données. Utilisez la commande **waagent** avec le paramètre **deprovision** sur votre machine virtuelle Linux cible. Pour plus d’informations, consultez le [Guide d’utilisateur de l’agent Linux Azure](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

1. Connectez-vous à votre machine virtuelle Linux en utilisant un client SSH.
2. Dans la fenêtre SSH, tapez la commande suivante :
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Exécutez uniquement cette commande sur une machine virtuelle que vous souhaitez capturer en tant qu’image. Cela ne garantit pas que l’image est exempte de toute information sensible ou qu’elle convient pour la redistribution.
 
3. Tapez **y** pour continuer. Vous pouvez ajouter le paramètre **-force** pour éviter cette étape de confirmation.
4. Une fois la commande terminée, tapez **exit**. Cette étape ferme le client SSH.

## <a name="step-2-capture-the-vm"></a>Étape 2 : capturer la machine virtuelle
Utilisez l’interface CLI Azure 2.0 pour généraliser et capturer la machine virtuelle. Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Les noms de paramètre sont par exemple **myResourceGroup**, **myVnet** et **myVM**.

1. Libérez la machine virtuelle dont vous avez annulé le déploiement à l’aide de la commande [az vm deallocate](/cli//azure/vm#deallocate). L’exemple suivant libère la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` :
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Généralisez la machine virtuelle à l’aide de la commande [az vm generalize](/cli//azure/vm#generalize). L’exemple suivant généralise la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` :
   
    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ```

3. Créez à présent une image de la ressource de machine virtuelle à l’aide de la commande [az image create](/cli//azure/image#create). L’exemple suivant crée une image nommée `myImage` dans le groupe de ressources nommé `myResourceGroup` à l’aide de la ressource de machine virtuelle nommée `myVM` :
   
    ```azurecli
    az image create --resource-group myResourceGroup --name myImage --source myVM
    ```
   
   > [!NOTE]
   > L’image est créée dans le même groupe de ressources que votre machine virtuelle source. Vous pouvez créer des machines virtuelles dans n’importe quel groupe de ressources de votre abonnement à partir de cette image. En matière de gestion, peut-être voudrez-vous créer un groupe de ressources spécifique pour vos ressources de machine virtuelle et vos images.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Étape 3 : créer une machine virtuelle à partir de l’image capturée
Créez une machine virtuelle à l’aide de l’image que vous avez créée en utilisant la commande [az vm create](/cli/azure/vm#create). L’exemple suivant crée une machine virtuelle nommée `myVMDeployed` à partir de l’image nommée `myImage` :

```azurecli
az vm create --resource-group myResourceGroup --name myVMDeployed --image myImage
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub
```

Avec les disques gérés, vous pouvez créer des machines virtuelles à partir d’une image dans n’importe quel groupe de ressources de votre abonnement. Cela est différent des disques non gérés, avec lesquels vous pouviez uniquement créer des machines virtuelles dans le même compte de stockage que votre disque dur virtuel source. Pour créer une machine virtuelle dans un groupe de ressources différent de celui de l’image, indiquez l’ID de ressource complet de votre image. Utilisez la commande [az image list](/cli/azure/image#list) pour afficher une liste d’images. Le résultat ressemble à l’exemple suivant :

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

L’exemple suivant utilise la commande **az vm create** pour créer une machine virtuelle dans un groupe de ressources différent de celui de l’image source en spécifiant l’ID de ressource d’image :

```azurecli
az vm create --resource-group myOtherResourceGroup --name myOtherVMDeployed 
    --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage"
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub
```


### <a name="verify-the-deployment"></a>Vérifier le déploiement
Maintenant, exécutez le SSH sur la machine virtuelle que vous avez créée pour vérifier le déploiement et lancez le déploiement et le démarrage de l’utilisation de la nouvelle machine virtuelle. Pour vous connecter via le protocole SSH, recherchez l’adresse IP ou le nom de domaine complet de votre machine virtuelle à l’aide de la commande [az vm show](/cli/azure/vm#show) :

```azurecli
az vm show --resource-group myResourceGroup --name myVM --show-details
```

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez créer plusieurs machines virtuelles à partir de votre image de machine virtuelle source. Si vous devez apporter des modifications à votre image : 

- Créez une machine virtuelle à partir de votre image.
- Apportez les mises à jour ou modifications de configuration requises.
- Suivez de nouveau ces étapes pour annuler le déploiement, libérer, généraliser et créer une image.
- Utilisez cette nouvelle image pour les déploiements futurs. Si vous le souhaitez, supprimez l’image d’origine.

Pour plus d’informations sur la gestion de vos machines virtuelles avec l’interface de ligne de commande, consultez [Azure CLI 2.0](/cli/azure/overview).

