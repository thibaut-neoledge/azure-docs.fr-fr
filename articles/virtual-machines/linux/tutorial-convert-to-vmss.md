---
title: Convertir une machine virtuelle Azure en un groupe identique | Microsoft Docs
description: "Créez et déployez un groupe de machines virtuelles identiques Linux Azure avec Azure CLI."
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/05/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 8d3376d2791b1349298db618d475ce5573083702
ms.lasthandoff: 04/07/2017

---

# <a name="convert-an-existing-azure-virtual-machine-to-a-scale-set"></a>Convertir une machine virtuelle Azure existante en un groupe identique

Ce didacticiel vous montre comment utiliser Azure CLI 2.0 pour convertir une machine virtuelle en un groupe de machines virtuelles identiques. Vous apprendrez également à automatiser la configuration des machines virtuelles dans le groupe identique. Pour plus d’informations sur l’installation d’Azure CLI 2.0, consultez [Prise en main d’Azure CLI 2.0](/cli/azure/get-started-with-azure-cli.md). Pour plus d’informations sur les jeux de mise à l’échelle, consultez [Jeux de mise à l’échelle de machine virtuelle](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

## <a name="step-1---deprovision-the-vm"></a>Étape 1 : Annulation de l’approvisionnement de la machine virtuelle

Utilisez le protocole SSH pour vous connecter à la machine virtuelle.

Annulez l’approvisionnement de la machine virtuelle à l’aide de l’agent de machine virtuelle Azure pour supprimer les fichiers et les données. Pour obtenir une présentation détaillée de l’annulation de l’approvisionnement, consultez [Capture d’une machine virtuelle Linux](capture-image.md).

```bash
sudo waagent -deprovision+user -force
exit
```

## <a name="step-2---capture-an-image-of-the-vm"></a>Étape 2 : Capture d’une image de la machine virtuelle

Pour obtenir une présentation détaillée de la capture, consultez [Capture d’une machine virtuelle Linux](capture-image.md).

Libérez la machine virtuelle à l’aide de la commande [az vm deallocate](/cli/azure/vm#deallocate) :

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Généralisez la machine virtuelle à l’aide de la commande [az vm generalize](/cli/azure/vm#generalize) :

```azurecli
az vm generalize --resource-group myResourceGroup --name myVM
```

Créez une image à partir de la ressource de machine virtuelle à l’aide de la commande [az image create](/cli/azure/image#create) :

```azurecli
az image create --resource-group myResourceGroup --name myImage --source myVM
```

## <a name="step-3---create-the-scale-set"></a>Étape 3 : Création du groupe identique

Obtenez **l’ID** de l’image.

```azurecli
az image show --resource-group myResourceGroup --name myImage --query id
```

```json
"/subscriptions/afbdaf8b-9188-4651-bce1-9115dd57c98b/resourceGroups/vmtest/providers/Microsoft.Compute/images/myImage"
```

Créez une machine virtuelle à partir de la ressource d’image à l’aide de la commande [az vmss create](/cli/azure/vmss#create) :

```azurecli
az vmss create --resource-group myResourceGroup --name myScaleSet --image /subscriptions/afbdaf8b-9188-4651-bce1-9115dd57c98b/resourceGroups/vmtest/providers/Microsoft.Compute/images/myImage --upgrade-policy-mode automatic --vm-sku Standard_DS1_v2 --data-disk-sizes-gb 10 --admin-username azureuser --generate-ssh-keys
```

Cette commande joint également un disque de données de 10 Go. N’oubliez pas que selon la taille de machine virtuelle choisie (nous avons utilisé **Standard_DS1_v2**), le nombre de disques de données autorisés est différent. Pour plus d’informations, consultez les [tailles des machines virtuelles](sizes.md).

Une fois que le groupe identique a terminé, connectez-vous à celui-ci. Obtenez la liste des adresses IP pour les instances de SSH avec la commande [az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info) :

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroup --name myScaleSet
```

```json
[
  "52.183.00.000:50000",
  "52.183.00.000:50003"
]
```

Vous pouvez désormais vous connecter à l’instance de machine virtuelle pour initialiser le disque de données

```bash
ssh -i ~/.ssh/id_rsa.pub -p 50000 azureuser@52.183.00.000
```

## <a name="step-4---initialize-the-data-disk"></a>Étape 4 : Initialisation du disque de données

Lorsque vous êtes connecté à la machine virtuelle, partitionnez le disque avec `fdisk` :

```bash
(echo n; echo p; echo 1; echo  ; echo  ; echo w) | sudo fdisk /dev/sdc
```

Écrivez un système de fichiers dans la partition avec la commande `mkfs` :

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Montez le nouveau disque afin qu’il soit accessible dans le système d’exploitation :

```bash
sudo mkdir /datadrive ; sudo mount /dev/sdc1 /datadrive
```

Le disque est désormais accessible via le point de montage du disque de données, qui peut être vérifié avec `ls /datadrive/`.

Mettez fin à la session SSH.


## <a name="step-5---configure-firewall"></a>Étape 5 : Configuration du pare-feu

Effectuez une percée à travers le pare-feu vers le serveur web hébergé par le groupe identique. Lorsque le groupe identique a été créé, un équilibreur de charge a également été créé et vous l’avez utilisé pour créer une connexion **SSH** aux machines virtuelles individuelles. Pour ouvrir un port, vous avez besoin de deux informations, que vous pouvez obtenir à l’aide d’Azure CLI.

* **Pool d’adresses IP frontales**  
`az network lb show --resource-group myResourceGroup --name myScaleSetLB --output table --query frontendIpConfigurations[0].name`

* **Pool d’adresses IP principales**  
`az network lb show --resource-group myResourceGroup --name myScaleSetLB --output table --query backendAddressPools[0].name`

Avec ces deux noms, vous pouvez ouvrir le port **80**.

```azurecli
az network lb rule create --backend-pool-name myScaleSetLBBEPool --backend-port 80 --frontend-ip-name loadBalancerFrontEnd --frontend-port 80 --name webserver --protocol tcp --resource-group myResourceGroup --lb-name myScaleSetLB
```


## <a name="step-6---automate-configuration"></a>Étape 6 : Automatisation de la configuration

Le disque de données doit être configuré sur chaque instance de machine virtuelle. Nous pouvons automatiser la configuration de la machine virtuelle avec l’extension **CustomScript**.

Commencez par créer un script *.sh* qui inclut les commandes de format du disque.

```sh
#!/bin/bash

# Setup the data disk
(echo n; echo p; echo 1; echo  ; echo  ; echo w) | fdisk /dev/sdc
fdisk /dev/sdc
mkfs -t ext4 /dev/sdc1
mkdir /datadrive
mount /dev/sdc1 /datadrive

exit 0
```

Ensuite, chargez ce fichier de script vers un emplacement où l’extension **CustomScript** peut y accéder. Une copie est disponible [ici](https://gist.githubusercontent.com/Thraka/ab1d8b78ac4b23722f3d3c1c03ac5df4).

Créez un fichier local nommé **settings.json** et placez-y le bloc JSON suivant. La propriété `flieUris` doit être définie sur l’emplacement où votre fichier de script a été chargé.

```json
{
  "fileUris": ["https://gist.githubusercontent.com/Thraka/ab1d8b78ac4b23722f3d3c1c03ac5df4/raw/3ac6e385010ac675e23ce583ce27b1a752f1b482/prep-vmss.sh"],
  "commandToExecute": "bash prep-vmss.sh" 
}
```

Déployez cette commande dans le groupe identique avec l’extension **CustomScript**, en faisant référence au fichier **settings.json** que nous venons de créer.

```azurecli
az vmss extension set --publisher Microsoft.Azure.Extensions --version 2.0 --name CustomScript --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

Cette extension s’exécute automatiquement sur toutes les instances actuelles et toutes les instances créées ultérieurement par le biais de la mise à l’échelle.

## <a name="step-7---configure-autoscale-rules"></a>Étape 7 : Configuration des règles de mise à l’échelle automatique

Actuellement, les règles de mise à l’échelle automatique ne peuvent pas être définies dans Azure CLI. Utilisez le [portail Azure](https://portal.azure.com) pour configurer la mise à l’échelle automatique.

## <a name="step-8---management-tasks"></a>Étape 8 : Tâches de gestion

Tout au long du cycle de vie du groupe identique, vous devrez peut-être exécuter une ou plusieurs tâches de gestion. En outre, vous souhaitez peut-être créer des scripts qui automatisent diverses tâches liées au cycle de vie. Azure CLI fournit un moyen rapide d’effectuer ces tâches. Voici quelques tâches courantes.

### <a name="get-connection-info"></a>Obtenir des informations de connexion

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroup --name myScaleSet
```

### <a name="set-instance-count-manual-scale"></a>Définir le nombre d’instances (mise à l’échelle manuelle)

```azurecli
az vmss scale --resource-group myResourceGroup --name myScaleSet --new-capacity 4
```

### <a name="delete-resource-group"></a>Supprimer un groupe de ressources

La suppression d’un groupe de ressources supprime également toutes les ressources qu’il contient.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur certaines des fonctionnalités du groupe de machines virtuelles identiques présentées dans ce didacticiel, consultez les informations suivantes :

- [Présentation des groupes de machines virtuelles identiques Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)
- [Vue d’ensemble d’Azure Load Balancer](../../load-balancer/load-balancer-overview.md)
- [Contrôler le flux de trafic réseau avec les groupes de sécurité réseau](../../virtual-network/virtual-networks-nsg.md)
