---
title: "Créer une machine virtuelle avec plusieurs cartes réseau - Azure CLI 2.0 | Microsoft Docs"
description: "Découvrez comment créer une machine virtuelle avec plusieurs cartes réseau en utilisant Azure CLI 2.0."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8e906a4b-8583-4a97-9416-ee34cfa09a98
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: 624f179c481448eb6c556b8cd9d90ef06d807c60
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-vm-with-multiple-nics-using-the-azure-cli-20"></a>Créer une machine virtuelle avec plusieurs cartes réseau en utilisant Azure CLI 2.0

[!INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[!INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

> [!NOTE]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../resource-manager-deployment-model.md).  Cet article traite de l’utilisation du modèle de déploiement Resource Manager que Microsoft recommande pour la plupart des nouveaux déploiements à la place du [modèle de déploiement classique](virtual-network-deploy-multinic-classic-cli.md).
>

## <a name="create"></a>Créer la machine virtuelle

Vous pouvez effectuer cette tâche à l’aide d’Azure CLI 2.0 (cet article) ou d’[Azure CLI 1.0](virtual-network-deploy-multinic-cli-nodejs.md). Les valeurs des variables comprises entre "" dans les étapes suivantes créent des ressources avec ces paramètres à partir du scénario. Modifiez les valeurs, le cas échéant, en fonction de votre environnement.

1. Installez [Azure CLI 2.0](/cli/azure/install-az-cli2) si vous ne l’avez pas encore fait.
2. Créez une paire de clés SSH publique et privée pour les machines virtuelles Linux en effectuant les étapes décrites dans l’article [Créer une paire de clés publique et privée SSH pour les machines virtuelles Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Dans un interpréteur de commandes, connectez-vous par le biais de la commande `az login`.
4. Créez la machine virtuelle en exécutant le script suivant sur un ordinateur Linux ou Mac. Ce script crée un groupe de ressources, un réseau virtuel comprenant deux sous-réseaux, deux cartes réseau, et une machine virtuelle avec deux cartes réseau associées. L’une des cartes réseau est connectée à un sous-réseau, et une adresse IP publique et privée statique lui est attribuée. L’autre carte réseau est associée à l’autre sous-réseau. Une adresse IP privée statique lui est attribuée, mais aucune adresse IP publique. La carte réseau, l’adresse IP publique, le réseau virtuel et les ressources de la machine virtuelle doivent se trouver au même emplacement et correspondre au même abonnement. Bien que les ressources n’aient pas toutes à se trouver dans le même groupe de ressources, c’est le cas dans le script suivant.

    ```azurecli
    #!/bin/sh

    RgName="Multi-NIC-VM"
    Location="westus"
    az group create --name $RgName --location $Location

    # Create a public IP address resource with a static IP address using the `--allocation-method Static` option.
    # If you do not specify this option, the address is allocated dynamically. The address is assigned to the
    # resource from a pool of IP adresses unique to each Azure region. 
    # Download and view the file from https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists
    # the ranges for each region.

    PipName="PIP-WEB"

    az network public-ip create \
    --name $PipName \
    --resource-group $RgName \
    --location $Location \
    --allocation-method Static

    # Create a virtual network with one subnet

    VnetName="VNet1"
    VnetPrefix="10.0.0.0/16"
    VnetSubnet1Name="Front-End"
    VnetSubnet1Prefix="10.0.0.0/24"

    az network vnet create \
    --name $VnetName \
    --resource-group $RgName \
    --location $Location \
    --address-prefix $VnetPrefix \
    --subnet-name $VnetSubnet1Name \
    --subnet-prefix $VnetSubnet1Prefix

    # Create a second subnet within the VNet

    VnetSubnet2Name="Back-end"
    VnetSubnet2Prefix="10.0.1.0/24"

    az network vnet subnet create \
    --vnet-name $VnetName \
    --resource-group $RgName \
    --name $VnetSubnet2Name \
    --address-prefix $VnetSubnet2Prefix

    # Create a network interface connected to one of the subnets. The NIC is assigned a single dynamic private and
    # public IP address by default, but you can instead, assign static addresses, or no public IP address at all.
    # You can also assign multiple private or public IP addresses to each NIC. To learn more about IP addressing
    # options for NICs, enter the `az network nic create -h` command.

    Nic1Name="NIC-FE"
    PrivateIpAddress1="10.0.0.5"

    az network nic create \
    --name $Nic1Name \
    --resource-group $RgName \
    --location $Location \
    --subnet $VnetSubnet1Name \
    --vnet-name $VnetName \
    --private-ip-address $PrivateIpAddress1 \
    --public-ip-address $PipName

    # Create a second network interface and connect it to the other subnet. Though multiple NICs attached to the same
    # VM can be connected to different subnets, the subnets must all be within the same VNet. Add additional NICs as necessary.

    Nic2Name="NIC-BE"
    PrivateIpAddress2="10.0.1.5"

    az network nic create \
    --name $Nic2Name \
    --resource-group $RgName \
    --location $Location \
    --subnet $VnetSubnet2Name \
    --vnet-name $VnetName \
    --private-ip-address $PrivateIpAddress2 \

    # Create a VM and attach the two NICs.

    VmName="WEB"

    # Replace the value for the following **VmSize** variable with a value from the
    # https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article. Not all VM sizes support
    # more than one NIC, so be sure to select a VM size that supports the number of NICs you want to attach to the VM.
    # You must create the VM with at least two NICs if you want to add more after VM creation. If you create a VM with
    # only one NIC, you can't add additional NICs to the VM after VM creation, regardless of how many NICs the VM supports.
    # The VM size specified in the following variable supports two NICs.

    VmSize="Standard_DS2"

    # Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
    # `az vm image list` command.

    OsImage="credativ:Debian:8:latest"

    Username="adminuser"

    # Replace the following value with the path to your public key file.

    SshKeyValue="~/.ssh/id_rsa.pub"

    # Before executing the following command, add variable names of additional NICs you may have added to the script that
    # you want to attach to the VM. If creating a Windows VM, remove the **ssh-key-value** line and you'll be prompted for
    # the password you want to configure for the VM.

    az vm create \
    --name $VmName \
    --resource-group $RgName \
    --image $OsImage \
    --location $Location \
    --size $VmSize \
    --nics $Nic1Name $Nic2Name \
    --admin-username $Username \
    --ssh-key-value $SshKeyValue
    ```

    Outre la création d’une machine virtuelle avec deux cartes réseau, le script crée :
    - Un disque unique géré par compte Premium par défaut, mais vous pouvez choisir un autre type de disque. Consultez l’article [Créer une machine virtuelle Linux à l’aide d’Azure CLI 2.0](../virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pour plus d’informations.
    - Un réseau virtuel avec deux sous-réseaux et une seule adresse IP publique. Vous pouvez également utiliser des ressources *existantes* en matière de réseau virtuel, sous-réseau, carte réseau ou adresse IP publique. Pour savoir comment utiliser des ressources réseau existantes au lieu de créer des ressources supplémentaires, utilisez `az vm create -h`.

## <a name = "validate"></a>Valider la création d’une machine virtuelle et de cartes réseau

1. Entrez la commande `az resource list --resouce-group Multi-NIC-VM --output table` pour afficher la liste des ressources créées par le script. La sortie retournée doit afficher six ressources : deux cartes réseau, un disque, une adresse IP publique, un réseau virtuel et une machine virtuelle.
2. Entrez la commande `az network public-ip show --name PIP-WEB --resource-group Multi-NIC-VM --output table`. Dans la sortie retournée, la valeur **IpAddress** et la valeur **PublicIpAllocationMethod** sont *Statique*.
3. Avant d’exécuter la commande suivante, supprimez les <>, remplacez *Username* par le nom que vous avez utilisé pour la variable **Username** du script, et remplacez *ipAddress* par la valeur **ipAddress** de l’étape précédente. Entrez la commande suivante pour vous connecter à la machine virtuelle : `ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>`. 
4. Une fois la connexion à la machine virtuelle établie, exécutez la commande `sudo ifconfig` pour afficher les interfaces *eth0* et *eth1*. Les adresses IP privées statiques spécifiées dans le script par les serveurs Azure DHCP sont attribuées à chaque carte réseau. Les adresses IP et MAC attribuées aux cartes réseau ne changent pas tant que la machine virtuelle n’est pas supprimée. Nous vous recommandons de ne pas modifier l’adressage IP dans un système d’exploitation, car cela peut désactiver la connexion à l’ordinateur. Les adresses IP publiques n’apparaissent pas dans le système d’exploitation, car il s’agit d’adresses réseau converties en et à partir d’adresses IP privées par l’infrastructure Azure.

## <a name= "clean-up"></a>Supprimer la machine virtuelle et les ressources associées

Si vous avez créé un groupe de ressources uniquement dans le cadre de la procédure décrite dans cet article, vous pouvez supprimer toutes les ressources en supprimant le groupe de ressources par le biais de la commande `az group delete --name Multi-NIC-VM`.

>[!WARNING]
>Vérifiez qu’il n’existe aucune autre ressource du groupe de ressources que celles créées par le script dans cet article avant de supprimer le groupe de ressources. Exécutez la commande `az resource list --resource-group Multi-NIC-VM` pour afficher les ressources du groupe de ressources.

Il est recommandé de supprimer les ressources si vous n’utilisez pas la machine virtuelle en production. La machine virtuelle, l’adresse IP publique et le disque occasionnent des frais tant que ces ressources sont configurées.

## <a name="next-steps"></a>Étapes suivantes

Tout le trafic réseau peut circuler vers et en provenance de la machine virtuelle créée dans le cadre de cet article. Vous pouvez définir des règles entrantes et sortantes au sein d’un groupe de sécurité réseau afin de limiter le trafic susceptible de circuler vers et en provenance de chaque interface réseau, chaque sous-réseau ou les deux. Pour en savoir plus sur les groupes de sécurité réseau, consultez l’article [Vue d’ensemble d’un groupe de sécurité réseau](virtual-networks-nsg.md).
