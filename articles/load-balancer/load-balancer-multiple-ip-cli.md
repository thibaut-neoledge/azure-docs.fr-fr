---
title: "Équilibrage de charge sur plusieurs configurations IP à l’aide de l’interface Azure CLI | Microsoft Docs"
description: "Apprendre à affecter plusieurs adresses IP à une machine virtuelle avec Azure CLI | Resource Manager."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 394315f81cf694cc2bb3a28b45694361b11e0670
ms.openlocfilehash: f52a86b01e45a32315b017c2605f7caebb68b006
ms.lasthandoff: 02/14/2017


---
# <a name="load-balancing-on-multiple-ip-configurations"></a>Équilibrage de charge sur plusieurs configurations IP

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-multiple-ip.md)
> * [INTERFACE DE LIGNE DE COMMANDE](load-balancer-multiple-ip-cli.md)
>

Cet article décrit comment utiliser Azure Load Balancer avec plusieurs adresses IP par interface de réseau virtuel (carte réseau). La prise en charge de plusieurs adresses IP sur une carte réseau est une fonctionnalité actuellement disponible en version préliminaire. Pour plus d’informations, consultez la section [Limitations](#limitations) de cet article. Le scénario suivant illustre le fonctionnement de cette fonctionnalité avec l’équilibrage de charge Azure.

Avec ce scénario, nous disposons de deux machines virtuelles exécutant Windows, chacune avec une seule carte réseau virtuelle. Chaque carte réseau possède plusieurs configurations IP. Chaque machine virtuelle héberge les deux sites web pour contoso.com et fabrikam.com. Chaque site web est lié à l’une des configurations IP sur la carte réseau. Nous utilisons un équilibrage de charge pour exposer deux adresses IP frontales, une par site web, afin de distribuer le trafic à la configuration IP correspondante pour le site web. Ce scénario utilise le même numéro de port sur les deux serveurs frontaux, ainsi que les deux adresses IP de pool principal.

![Image du scénario LB](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="limitations"></a>Limitations

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

Inscrivez-vous pour la version d’évaluation en exécutant les commandes suivantes dans PowerShell après votre connexion, puis sélectionnez l’abonnement approprié :

```
Register-AzureRmProviderFeature -FeatureName AllowMultipleIpConfigurationsPerNic -ProviderNamespace Microsoft.Network

Register-AzureRmProviderFeature -FeatureName AllowLoadBalancingonSecondaryIpconfigs -ProviderNamespace Microsoft.Network

Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

N’essayez pas d’effectuer les étapes restantes avant d’obtenir le résultat suivant lorsque vous exécutez la commande ```Get-AzureRmProviderFeature``` :
        
```powershell
FeatureName                            ProviderName      RegistrationState
-----------                            ------------      -----------------      
AllowLoadBalancingOnSecondaryIpConfigs Microsoft.Network Registered       
AllowMultipleIpConfigurationsPerNic    Microsoft.Network Registered       
```
        
>[!NOTE] 
>Cela peut prendre quelques minutes.

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Étapes pour équilibrer la charge sur plusieurs configurations IP

Exécutez la procédure ci-dessous afin de respecter le scénario décrit dans cet article :

1. [Installez et configurez l’interface Azure CLI](../xplat-cli-install.md) en suivant les étapes de l’article lié, puis connectez-vous au compte Azure.
2. [Créez un groupe de ressources](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-groups-and-choose-deployment-locations) appelé *contosofabrikam*, tel que décrit ci-dessus.

    ```azurecli
    azure group create contosofabrikam westcentralus
    ```

3. [Créez un groupe à haute disponibilité](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-availability-set) pour les deux machines virtuelles. Pour ce scénario, utilisez la commande suivante :

    ```azurecli
    azure availset create --resource-group contosofabrikam --location westcentralus --name myAvailabilitySet
    ```

4. [Créez un réseau virtuel](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet) appelé *myVNet* et un sous-réseau appelé *mySubnet* :

    ```azurecli
    azure network vnet create --resource-group contosofabrikam --name myVnet --address-prefixes 10.0.0.0/16  --location westcentralus

    azure network vnet subnet create --resource-group contosofabrikam --vnet-name myVnet --name mySubnet --address-prefix 10.0.0.0/24
    ```

5. [Créez l’équilibrage de charge](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-load-balancer-and-ip-pools) appelé *mylb* :

    ```azurecli
    azure network lb create --resource-group contosofabrikam --location westcentralus --name mylb
    ```

6. Créez deux adresses IP publiques dynamiques pour les configurations IP frontales de votre équilibrage de charge :

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp1 --domain-name-label contoso --allocation-method Dynamic

    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp2 --domain-name-label fabrikam --allocation-method Dynamic
    ```

7. Créez deux configurations IP frontales, *contosofe* et *fabrikamfe*, respectivement :

    ```azurecli
    azure network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp1 --name contosofe
    azure network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp2 --name fabrkamfe
    ```

8. Créez deux pools d’adresses frontales, *contosopool* et *fabrikampool*, une [sonde](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-load-balancer-health-probe) - *HTTP* et vos règles d’équilibrage de charge, *HTTPc* et *HTTPf* :

    ```azurecli
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name contosopool
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name fabrikampool

    azure network lb probe create --resource-group contosofabrikam --lb-name mylb --name HTTP --protocol "http" --interval 15 --count 2 --path index.html

    azure network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPc --protocol tcp --probe-name http--frontend-port 5000 --backend-port 5000 --frontend-ip-name contosofe --backend-address-pool-name contosopool
    azure network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPf --protocol tcp --probe-name http --frontend-port 5000 --backend-port 5000 --frontend-ip-name fabrkamfe --backend-address-pool-name fabrikampool
    ```

9. Exécutez la commande suivante, puis vérifiez la sortie afin de [confirmer que votre équilibrage de charge](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#verify-the-load-balancer) a été correctement créé :

    ```azurecli
    azure network lb show --resource-group contosofabrikam --name mylb
    ```

10. [Créez une adresse IP publique](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-public-ip-address), *myPublicIp* et [un compte de stockage](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-storage-account), *mystorageaccont1* pour votre première machine virtuelle VM1, tel que représenté ci-dessous :

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP --domain-name-label mypublicdns345 --allocation-method Dynamic

    azure storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount1
    ```

11. [Créez une interface réseau](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-nic-to-use-with-the-linux-vm), *VM1-NIC*, pour VM1, ajoutez une seconde configuration IP, *VM1-ipconfig2*, puis[créez la machine virtuelle](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-the-linux-vms), tel que représenté ci-dessous :

    ```azurecli
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic --ip-config-name VM1-ipconfig1 --public-ip-name myPublicIP --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    azure network nic ip-config create --resource-group contosofabrikam --nic-name myNic --name VM1-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    azure vm create --resource-group contosofabrikam --name VM1 --location westcentralus --os-type linux --nic-name myNic --vnet-name VNet1 --vnet-subnet-name Subnet1 --availset-name myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount1 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

12. Répétez les étapes 10-11 pour votre seconde machine virtuelle :

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns785 --allocation-method Dynamic
    azure storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount3426
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 --ip-config-name VM2-ipconfig1 --public-ip-name myPublicIP2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    azure network nic ip-config create --resource-group contosofabrikam --nic-name myNic2 --name VM2-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    azure vm create --resource-group contosofabrikam --name VM2 --location westcentralus --os-type linux --nic-name myNic2 --vnet-name VNet1 --vnet-subnet-name Subnet1 --availset-name myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount2 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

13. Enfin, vous devez configurer les enregistrements de ressource DNS pour qu’ils pointent sur l’adresse IP frontale respective de l’équilibrage de charge. Vous pouvez héberger vos domaines dans Azure DNS. Pour plus d’informations sur l’utilisation d’Azure DNS avec un équilibrage de charge, voir [Utiliser Azure DNS avec d’autres services Azure](../dns/dns-for-azure-services.md).

