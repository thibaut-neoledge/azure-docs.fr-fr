---
title: "Créer une copie de votre machine virtuelle Linux à l’aide d’Azure CLI 1.0 | Microsoft Docs"
description: "Découvrez comment créer une copie de votre machine virtuelle Linux Azure à l’aide d’Azure CLI 1.0 dans le modèle de déploiement Resource Manager"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 62ae54f3596c9383cbf3b401fcfdb42ecfdee63c
ms.lasthandoff: 04/03/2017


---
# <a name="create-a-copy-of-a-linux-virtual-machine-running-on-azure-with-the-azure-cli-10"></a>Créer une copie d’une machine virtuelle Linux exécutée sur Azure à l’aide d’Azure CLI 1.0
Cet article vous explique comment créer une copie de votre machine virtuelle Azure exécutant Linux dans le modèle de déploiement Resource Manager. Copiez tout d’abord les disques du système d’exploitation et les disques de données dans un nouveau conteneur, configurez les ressources réseau puis créez la nouvelle machine virtuelle.

Vous pouvez également [charger et créer une machine virtuelle à partir d’une image de disque personnalisée](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche
Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

- Azure CLI 1.0 : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager (cet article)
- [Azure CLI 2.0](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager

## <a name="before-you-begin"></a>Avant de commencer
Assurez-vous de disposer des composants requis suivants avant de commencer la procédure :

* Vous avez téléchargé et installé l [’interface de ligne de commande Azure](../../cli-install-nodejs.md) sur votre ordinateur. 
* Vous devez également recueillir quelques informations concernant votre machine virtuelle Linux Azure existante :

| Informations sur la machine virtuelle source | Comment les obtenir |
| --- | --- |
| Nom de la machine virtuelle |`azure vm list` |
| Nom du groupe ressources |`azure vm list` |
| Emplacement |`azure vm list` |
| Nom du compte de stockage |`azure storage account list -g <resourceGroup>` |
| Nom du conteneur |`azure storage container list -a <sourcestorageaccountname>` |
| Nom du fichier VHD de la machine virtuelle source |`azure storage blob list --container <containerName>` |

* Vous devrez faire certains choix concernant votre nouvelle machine virtuelle :    <br> -Nom du conteneur    <br> -Nom de la machine virtuelle    <br> -Taille de la machine virtuelle    <br> -Nom du réseau virtuel    <br> -Nom du sous-réseau    <br> -Nom IP    <br> -nom de la carte réseau

## <a name="login-and-set-your-subscription"></a>Se connecter et définir votre abonnement
1. Connectez-vous à l’interface de ligne de commande.

    ```azurecli
    azure login
    ```
2. Assurez-vous que vous êtes en mode Gestionnaire de ressources.

    ```azurecli
    azure config mode arm
    ```
3. Définissez l'abonnement approprié. Vous pouvez utiliser ’azure account list’ pour voir tous vos abonnements.

    ```azurecli
    azure account set mySubscriptionID
    ```

## <a name="stop-the-vm"></a>Arrêtez la machine virtuelle.
Arrêtez et libérez la machine virtuelle source. Vous pouvez utiliser ’azure vm list’ pour obtenir une liste de toutes les machines virtuelles de votre abonnement ainsi que les noms de leurs groupes de ressources.

```azurecli
azure vm stop myResourceGroup myVM
azure vm deallocate myResourceGroup MyVM
```


## <a name="copy-the-vhd"></a>Copier le disque dur virtuel
Vous pouvez copier le disque dur virtuel du stockage source vers le stockage de destination à l’aide de la commande `azure storage blob copy start`. Dans cet exemple, nous allons copier le disque dur virtuel vers le même compte de stockage, mais dans un autre conteneur.

Pour copier le disque dur virtuel vers un autre conteneur du même compte de stockage, tapez :

```azurecli
azure storage blob copy start \
        https://mystorageaccountname.blob.core.windows.net:8080/mycontainername/myVHD.vhd \
        myNewContainerName
```

## <a name="set-up-the-virtual-network-for-your-new-vm"></a>Configurer le réseau virtuel pour votre nouvelle machine virtuelle
Configurez un réseau virtuel et une carte réseau pour votre nouvelle machine virtuelle. 

```azurecli
azure network vnet create myResourceGroup myVnet -l myLocation

azure network vnet subnet create -a <address.prefix.in.CIDR/format> myResourceGroup myVnet mySubnet

azure network public-ip create myResourceGroup myPublicIP -l myLocation

azure network nic create myResourceGroup myNic -k mySubnet -m myVnet -p myPublicIP -l myLocation
```


## <a name="create-the-new-vm"></a>Créer la machine virtuelle
Vous pouvez maintenant créer une machine virtuelle à partir du disque virtuel chargé [en utilisant un modèle Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) ou via la CLI en spécifiant l’URI pointant vers votre disque copié, comme suit :

```azurecli
azure vm create -n myVM -l myLocation -g myResourceGroup -f myNic \
        -z Standard_DS1_v2 -y Linux \
        https://mystorageaccountname.blob.core.windows.net:8080/mycontainername/myVHD.vhd 
```



## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur l’utilisation de l’interface de ligne de commande Azure pour gérer votre nouvelle machine virtuelle, voir [Commandes de l’interface de ligne de commande Azure en mode Azure Resource Manager](../azure-cli-arm-commands.md).


