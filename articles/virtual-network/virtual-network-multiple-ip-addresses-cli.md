---
title: "Machine virtuelle avec adresses IP à l’aide d’Azure CLI 2.0 | Microsoft Docs"
description: "Découvrez comment attribuer plusieurs adresses IP à une machine virtuelle à l’aide d’Azure CLI 2.0 | Resource Manager"
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: 80ea942cc8119050474ee5c8332925e5452d264e
ms.lasthandoff: 03/22/2017


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli-20"></a>Affecter plusieurs adresses IP à des machines virtuelles à l’aide d’Azure CLI 2.0

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Cet article explique comment créer une machine virtuelle dans le modèle de déploiement Azure Resource Manager à l’aide de l’interface Azure CLI 2.0. Il n’est pas possible d’affecter plusieurs adresses IP à des ressources créées à l’aide du modèle de déploiement classique. Pour en savoir plus sur les modèles de déploiement Azure, voir [Comprendre les modèles de déploiement](../resource-manager-deployment-model.md).

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Créer une machine virtuelle avec plusieurs adresses IP

Vous pouvez effectuer cette tâche à l’aide d’Azure CLI 2.0 (cet article) ou d’[Azure CLI 1.0](virtual-network-multiple-ip-addresses-cli-nodejs.md). Modifiez les valeurs, le cas échéant, en fonction de votre environnement. Les étapes qui suivent expliquent comment créer un exemple de machine virtuelle avec plusieurs adresses IP, comme décrit dans le scénario. Modifiez les valeurs des variables entre symboles "" et les types d’adresses IP en fonction des besoins de votre implémentation. 

1. Installez [Azure CLI 2.0](/cli/azure/install-az-cli2) si vous ne l’avez pas encore fait.
2. Créez une paire de clés SSH publique et privée pour les machines virtuelles Linux en effectuant les étapes décrites dans l’article [Créer une paire de clés publique et privée SSH pour les machines virtuelles Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. À partir d’un interpréteur de commandes, connectez-vous avec la commande `az login`, puis sélectionnez l’abonnement que vous utilisez.
4. Créez la machine virtuelle en exécutant le script suivant sur un ordinateur Linux ou Mac. Le script crée un groupe de ressources, un réseau virtuel (VNet), une carte réseau avec trois configurations IP et une machine virtuelle avec les deux cartes réseau qui lui sont associées. La carte réseau, l’adresse IP publique, le réseau virtuel et les ressources de la machine virtuelle doivent se trouver au même emplacement et correspondre au même abonnement. Bien que les ressources n’aient pas toutes à se trouver dans le même groupe de ressources, c’est le cas dans le script suivant.

```bash
#!/bin/sh

RgName="myResourceGroup"
Location="westcentralus"
az group create --name $RgName --location $Location

# Create a public IP address resource with a static IP address using the `--allocation-method Static` option. If you
# do not specify this option, the address is allocated dynamically. The address is assigned to the resource from a pool
# of IP adresses unique to each Azure region. Download and view the file from
# https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists the ranges for each region.

PipName="myPublicIP"

# This name must be unique within an Azure location.
DnsName="myDNSName"

az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--dns-name $DnsName\
--allocation-method Static

# Create a virtual network with one subnet

VnetName="myVnet"
VnetPrefix="10.0.0.0/16"
VnetSubnetName="mySubnet"
VnetSubnetPrefix="10.0.0.0/24"

az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $VnetSubnetName \
--subnet-prefix $VnetSubnetPrefix

# Create a network interface connected to the subnet and associate the public IP address to it. Azure will create the
# first IP configuration with a dynamic private IP address and will associate the public IP address resource to it.

NicName="MyNic1"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet1Name \
--vnet-name $VnetName \
--public-ip-address $PipName

# Create a second public IP address, a second IP configuration, and associate it to the NIC. This configuration has a
# static public IP address and a static private IP address.

az network public-ip create \
--resource-group $RgName \
--location $Location \
--name myPublicIP2 \
--dns-name mypublicdns2 \
--allocation-method Static

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--name IPConfig-2 \
--private-ip-address 10.0.0.5 \
--public-ip-name myPublicIP2

# Create a third IP configuration, and associate it to the NIC. This configuration has a dynamic private IP address and
# no public IP address.

azure network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--name IPConfig-3

# Note: Though this article assigns all IP configurations to a single NIC, you can also assign multiple IP configurations
# to any NIC in a VM. To learn how to create a VM with multiple NICs, read the Create a VM with multiple NICs 
# article: https://docs.microsoft.com/azure/virtual-network/virtual-network-deploy-multinic-arm-cli.

# Create a VM and attach the NIC.

VmName="myVm"

# Replace the value for the following **VmSize** variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article. The script fails if the VM size
# is not supported in the location you select. Run the `azure vm sizes --location westcentralus` command to get a full list
# of VMs in US West Central, for example.

VmSize="Standard_DS1"

# Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
# `az vm image list` command.

OsImage="credativ:Debian:8:latest"

Username="adminuser"

# Replace the following value with the path to your public key file. If you're creating a Windows VM, remove the following
# line and you'll be prompted for the password you want to configure for the VM.

SshKeyValue="~/.ssh/id_rsa.pub"

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $NicName \
--admin-username $Username \
--ssh-key-value $SshKeyValue
```

Outre la création d’une machine virtuelle avec une carte réseau dotée de 3 configurations IP, le script crée :

- Un disque unique géré par compte Premium par défaut, mais vous pouvez choisir un autre type de disque. Consultez l’article [Créer une machine virtuelle Linux à l’aide d’Azure CLI 2.0](../virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pour plus d’informations.
- Un réseau virtuel avec un sous-réseau et deux adresses IP publiques. Vous pouvez également utiliser des ressources *existantes* en matière de réseau virtuel, sous-réseau, carte réseau ou adresse IP publique. Pour savoir comment utiliser des ressources réseau existantes au lieu de créer des ressources supplémentaires, utilisez `az vm create -h`.

Les adresses IP publiques ont un coût nominal. Pour en savoir plus, lisez la page [Tarification des adresses IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Il existe une limite au nombre d’adresses IP publiques qui peuvent être utilisées dans un abonnement. Pour plus d’informations sur les limites, voir [Limites d’Azure](../azure-subscription-service-limits.md#networking-limits).

Une fois la machine virtuelle créée, saisissez la commande `az network nic show --name MyNic1 --resource-group myResourceGroup` afin d’afficher la configuration de carte réseau. Saisissez l’élément `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` afin d’afficher une liste des configurations IP associées à la carte réseau.

Ajoutez les adresses IP privées pour le système d’exploitation de la machine virtuelle en suivant les étapes pour votre système d’exploitation dans la section [Ajouter des adresses IP à un système d’exploitation de machine virtuelle](#os-config) de cet article.

## <a name="add"></a>Ajouter des adresses IP à une machine virtuelle

Vous pouvez ajouter des adresses IP privées et publiques à une carte réseau en suivant les étapes décrites ci-après. Les exemples reposent sur le [scénario](#Scenario) décrit dans cet article.

1. Ouvrez un interpréteur de commandes Azure et effectuez les étapes restantes de cette section en une seule session. Si l’interface Azure CLI n’est pas encore installée et configurée, suivez les étapes décrites dans l’article consacré à l’[installation d’Azure CLI 2.0](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) et connectez-vous à votre compte Azure avec la commande `az-login`.

2. Suivez les étapes de l’une des sections suivantes, selon vos besoins :

    **Ajouter une adresse IP privée**
    
    Pour ajouter une adresse IP privée à une carte d’interface réseau, vous devez créer une configuration IP à l’aide de la commande ci-dessous. Si vous souhaitez ajouter une adresse IP privée dynamique, supprimez `-PrivateIpAddress 10.0.0.7` avant la saisie de la commande. Lorsque vous spécifiez une adresse IP statique, il doit s’agir d’une adresse non utilisée pour le sous-réseau.

    ```bash
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    Créez autant de configurations que nécessaire, à l’aide de noms de configuration unique et d’adresses IP privées (pour les configurations avec des adresses IP statiques).

    **Ajouter une adresse IP publique**
    
    Pour ajouter une adresse IP publique, associez-la à une configuration IP nouvelle ou existante. Suivez les étapes de l’une des sections ci-après, selon votre besoin.

    Les adresses IP publiques ont un coût nominal. Pour en savoir plus, lisez la page [Tarification des adresses IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Il existe une limite au nombre d’adresses IP publiques qui peuvent être utilisées dans un abonnement. Pour plus d’informations sur les limites, voir [Limites d’Azure](../azure-subscription-service-limits.md#networking-limits).

    - **Associer la ressource à une nouvelle configuration IP**
    
        Lorsque vous ajoutez une adresse IP publique dans une nouvelle configuration IP, vous devez également ajouter une adresse IP privée, car toutes les configurations IP doivent avoir une adresse IP privée. Vous pouvez ajouter une ressource d’adresse IP publique existante ou en créer une. Pour en créer une nouvelle, saisissez la commande suivante :
    
        ```bash
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

         Pour créer une nouvelle configuration IP avec une adresse IP privée dynamique et la ressource d’adresse IP publique *myPublicIP3*, saisissez la commande suivante :

        ```bash
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --public-ip-address myPublicIP3
        ```

    - **Associer la ressource à une configuration IP existante**
       Une ressource d’adresse IP publique peut uniquement être associée à une configuration d’adresses IP n’ayant pas encore de ressource associée. Pour déterminer si une configuration IP a une adresse IP publique associée, entrez la commande suivante :

        ```bash
        az network nic ip-config list \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
        ```

        Sortie retournée :
    
            Name        PublicIpAddressId
            
            ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
            IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
            IPConfig-3

        Étant donné que la colonne **PublicIpAddress** pour *IpConfig-3* est vide dans la sortie, aucune ressource d’adresse IP publique n’y est actuellement associée. Vous pouvez ajouter une ressource d’adresse IP publique existante sur IpConfig-3, ou entrer la commande suivante pour en créer une :

        ```bash
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        Entrez la commande suivante pour associer la ressource d’adresse IP publique à la configuration IP existante nommée *IPConfig-3* :
    
        ```bash
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. Affichez les ressources d’adresses IP privées et l’adresse IP publique affectées à la carte d’interface réseau en saisissant la commande suivante :

    ```bash
    az network nic ip-config list \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    Sortie retournée : <br>
    
        Name        PrivateIpAddress    PrivateIpAllocationMethod    PublicIpAddressId
        
        ipconfig1   10.0.0.4            Dynamic                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
        IPConfig-2  10.0.0.5            Static                       /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
        IPConfig-3  10.0.0.6            Dynamic                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    

4. Ajoutez les adresses IP privées que vous avez ajoutées à l’interface réseau pour le système d’exploitation de la machine virtuelle en suivant les instructions fournies dans la section [Ajouter des adresses IP à un système d’exploitation de machine virtuelle](#os-config) de cet article. N’ajoutez pas les adresses IP publiques au système d’exploitation.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]

