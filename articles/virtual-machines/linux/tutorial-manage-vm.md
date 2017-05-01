---
title: "Gérer des machines virtuelles Linux avec Azure CLI | Microsoft Docs"
description: "Didacticiel - Gérer des machines virtuelles Linux avec Azure CLI"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/28/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: c0f22806034eef1fc5ff37d547d066f554ec0a85
ms.lasthandoff: 04/07/2017

---

# <a name="manage-linux-virtual-machines-with-the-azure-cli"></a>Gérer des machines virtuelles Linux avec Azure CLI

Dans ce didacticiel, vous créez une machine virtuelle et effectuez des tâches de gestion courantes telles que l’ajout d’un disque, l’automatisation de l’installation de logiciels et ma création d’un instantané de machine virtuelle. 

Pour suivre ce didacticiel, assurez-vous que vous avez installé la dernière version [d’Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="step-1--log-in-to-azure"></a>Étape 1 : Connexion à Azure

Tout d’abord, ouvrez un terminal et connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/#login).

```azurecli
az login
```

## <a name="step-2--create-resource-group"></a>Étape 2 : Création d’un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](https://docs.microsoft.com/cli/azure/group#create). 

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Un groupe de ressources doit être créé avant les machines virtuelles. Dans cet exemple, un groupe de ressources nommé `myResourceGroup` est créé dans la région `westeurope`. 

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="step-3---prepare-configuration"></a>Étape 3 : Préparer la configuration

Lors du déploiement d’une machine virtuelle, **cloud-init** peut être utilisé pour automatiser les configurations, comme l’installation de packages, la création de fichiers et l’exécution de scripts. Dans ce didacticiel, les configurations de deux éléments sont automatisées :

- Installation d’un serveur Web NGINX
- Configuration d’un deuxième disque sur la machine virtuelle

Étant donné que la configuration **cloud-init** se produit au moment du déploiement de la machine virtuelle, une configuration **cloud-init** doit être définie avant de créer la machine virtuelle.

Créez un fichier nommé `cloud-init.txt` et copiez le contenu suivant dedans. Cette configuration installe le package NGINX et exécute des commandes pour formater et monter le deuxième disque.

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
runcmd:
  - (echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
  - sudo mkfs -t ext4 /dev/sdc1
  - sudo mkdir /datadrive
  - sudo mount /dev/sdc1 /datadrive
```

## <a name="step-4---create-virtual-machine"></a>Étape 4 : Créer une machine virtuelle

Créez une machine virtuelle avec la commande [az vm create](https://docs.microsoft.com/cli/azure/vm#create). 

Lorsque vous créez une machine virtuelle, plusieurs options sont disponibles, comme l’image de système d’exploitation, les informations d’identification d’administration ou le dimensionnement des disques. Dans cet exemple, une machine virtuelle exécutant Ubuntu est créée avec un nom de `myVM`. Un disque de 50 Go est créé et lié à la machine virtuelle à l’aide de l’argument `--data-disk-sizes-gb`. L’argument `--custom-data` accepte la configuration cloud-init et la copie sur la machine virtuelle. Enfin, les clés SSH sont également créées si elles n’existent pas.

```azurecli
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --generate-ssh-keys \
  --data-disk-sizes-gb 50 \
  --custom-data cloud-init.txt
```

Une fois que la machine virtuelle a été créée, l’interface Azure CLI fournit les informations suivantes. Prenez note de l’adresse IP publique, cette adresse étant utilisée pour accéder à la machine virtuelle. 

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

Alors que la machine virtuelle a été déployée, la configuration **cloud-init** peut prendre quelques minutes. 

## <a name="step-5--configure-firewall"></a>Étape 5 : Configuration du pare-feu

Un [groupe de sécurité réseau](../../virtual-network/virtual-networks-nsg.md) (NSG) Azure contrôle le trafic entrant et sortant pour une ou plusieurs machines virtuelles. Les règles de groupe de sécurité réseau autorisent ou refusent le trafic réseau sur un port ou une plage de ports spécifique. Ces règles peuvent également inclure un préfixe d’adresse source afin que seul le trafic provenant d’une source prédéfinie puisse communiquer avec une machine virtuelle.

Dans la section précédente, le serveur web NGINX a été installé. Sans règle de groupe de sécurité de réseau pour autoriser le trafic entrant sur le port 80, le serveur web n’est pas accessible sur Internet. Cette étape vous guide lors de la création de la règle de groupe de sécurité réseau pour autoriser les connexions entrantes sur le port 80.

### <a name="create-nsg-rule"></a>Création de la règle de groupe de sécurité réseau

Pour créer une règle de groupe de sécurité réseau entrante, utilisez la commande [az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port). L’exemple suivant ouvre le port `80` pour la machine virtuelle.

```azurecli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM 
```

Maintenant, accédez à l’adresse IP publique de la machine virtuelle. Avec la règle de groupe de sécurité réseau en place, le site web NGINX par défaut s’affiche.

![Site par défaut NGINX](./media/tutorial-manage-vm/nginx.png)  

## <a name="step-6--snapshot-virtual-machine"></a>Étape 6 – Capture d’un instantané de la machine virtuelle

La capture instantanée du disque crée une copie en lecture seule à un moment donné du disque. Dans cette étape, un instantané du disque de système d’exploitation des machines virtuelles est capturé. Avec un instantané de disque de système d’exploitation, la machine virtuelle peut être rapidement restaurée à un état spécifique, ou l’instantané peut être utilisé pour créer une nouvelle machine virtuelle avec un état identique.

### <a name="create-snapshot"></a>Création d’un instantané

Avant de créer un instantané, l’ID ou le nom du disque est nécessaire. Utilisez la commande [az vm show](https://docs.microsoft.com/cli/azure/vm#show) pour obtenir l’ID du disque. Dans cet exemple, l’ID du disque est stocké dans une variable et est utilisé dans une étape ultérieure.

```azurecli
osdiskid=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

Maintenant que vous avez l’ID du disque, la commande suivante crée l’instantané.

```azurcli
az snapshot create -g myResourceGroup --source "$osdiskid" --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Création d’un disque à partir d’une capture instantanée

Cet instantané peut ensuite être converti en disque qui peut être utilisé pour recréer la machine virtuelle.

```azurecli
az disk create --resource-group myResourceGroup --name mySnapshotDisk --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Restauration de la machine virtuelle à partir de l’instantané

Pour illustrer la récupération de machine virtuelle, supprimez la machine virtuelle existante. 

```azurecli
az vm delete --resource-group myResourceGroup --name myVM
```

Lorsque vous recréez la machine virtuelle, l’interface réseau existant est réutilisée. Cela garantit que les configurations de sécurité réseau sont conservées.

Obtenez le nom d’interface réseau avec la commande [az network nic list](https://docs.microsoft.com/cli/azure/network/nic#list). Cet exemple place le nom dans une variable nommée `nic`, qui est utilisée dans l’étape suivante.

```azurecli
nic=$(az network nic list --resource-group myResourceGroup --query "[].[name]" -o tsv)
```

Créez une nouvelle machine virtuelle à l’aide du disque d’instantané.

```azurecli
az vm create --resource-group myResourceGroup --name myVM --attach-os-disk mySnapshotDisk --os-type linux --nics $nic
```

Prenez note de la nouvelle adresse IP publique, et accédez à cette adresse dans un navigateur Internet. Vous verrez que NGINX est en cours d’exécution sur la machine virtuelle restaurée. 

### <a name="reconfigure-data-disk"></a>Reconfiguration du disque de données

Le disque de données peut désormais être rattaché à la machine virtuelle. 

Trouvez d’abord le nom des disques de données à l’aide de la commande [az disk list](https://docs.microsoft.com/cli/azure/disk#list). Cet exemple place le nom du disque dans une variable nommée `datadisk`, qui est utilisée dans l’étape suivante.

```azurecli
datadisk=$(az disk list -g myResourceGroup --query "[?contains(name,'myVM')].[name]" -o tsv)
```

Utilisez la commande [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk#attach) pour attacher le disque.

```azurecli
az vm disk attach –g myResourceGroup –-vm-name myVM –-disk $datadisk
```

Le disque doit également être monté sur le système d’exploitation. Pour monter le disque, connectez-vous à la machine virtuelle et exécutez `sudo mount /dev/sdc1 /datadrive`, ou votre opération de montage de disque par défaut. 

## <a name="step-7--management-tasks"></a>Étape 7 : Tâches de gestion

Pendant le cycle de vie d’une machine virtuelle, vous souhaiterez exécuter des tâches de gestion telles que le démarrage, l’arrêt ou la suppression d’une machine virtuelle. En outre, vous souhaiterez peut-être créer des scripts pour automatiser les tâches répétitives ou complexes. À l’aide de l’interface Azure CLI, de nombreuses tâches courantes de gestion peuvent être exécutées à partir de la ligne de commande ou dans des scripts. 

### <a name="get-ip-address"></a>Obtenir l’adresse IP

Cette commande renvoie les adresses IP publiques et privées d’une machine virtuelle.  

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myVM
```

### <a name="resize-virtual-machine"></a>Redimensionner la machine virtuelle

Pour redimensionner une machine virtuelle Azure, vous devez connaître le nom des tailles disponibles dans la région Azure choisie. Vous pouvez trouver ces tailles avec la commande [az vm list-sizes](https://docs.microsoft.com/cli/azure/vm#list-sizes).

```azurecli
az vm list-sizes --location westeurope --output table
```

La machine virtuelle peut être redimensionnée à l’aide de la commande [az vm resize](https://docs.microsoft.com/cli/azure/vm#resize). 

```azurecli
az vm resize -g myResourceGroup -n myVM --size Standard_F4s
```

### <a name="stop-virtual-machine"></a>Arrêt d’une machine virtuelle

```azurecli
az vm stop --resource-group myResourceGroup --name myVM
```

### <a name="start-virtual-machine"></a>Démarrage d’une machine virtuelle

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

### <a name="delete-resource-group"></a>Supprimer un groupe de ressources

La suppression d’un groupe de ressources supprime également toutes les ressources qu’il contient.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes
Ce didacticiel crée une machine virtuelle en utilisant des ressources Azure individuelles. Le didacticiel suivant s’appuie sur ces concepts pour créer une application à haute disponibilité avec équilibrage de charge et résistante aux événements de maintenance. Continuez avec le didacticiel suivant, [Création d’une application à haute disponibilité avec équilibrage de charge sur des machines virtuelles Linux dans Azure](tutorial-load-balance-nodejs.md).

Exemples : [Exemples de scripts de ligne de commande](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
