---
title: "Machine virtuelle avec plusieurs adresses IP à l’aide d’Azure CLI 1.0 | Microsoft Docs"
description: "Découvrez comment attribuer plusieurs adresses IP à une machine virtuelle à l’aide d’Azure CLI 1.0 | Resource Manager."
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
ms.openlocfilehash: 62154c38b785b72181f6ee73383f84bd23caeaec
ms.lasthandoff: 03/22/2017


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-azure-cli-10"></a>Attribuer plusieurs adresses IP aux machines virtuelles à l’aide d’Azure CLI 1.0

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Cet article explique comment créer une machine virtuelle dans le modèle de déploiement Azure Resource Manager à l’aide d’Azure CLI 1.0. Il n’est pas possible d’affecter plusieurs adresses IP à des ressources créées à l’aide du modèle de déploiement classique. Pour en savoir plus sur les modèles de déploiement Azure, voir [Comprendre les modèles de déploiement](../resource-manager-deployment-model.md).

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Créer une machine virtuelle avec plusieurs adresses IP

Vous pouvez effectuer cette tâche à l’aide d’Azure CLI 1.0 (cet article) ou d’[Azure CLI 2.0](virtual-network-multiple-ip-addresses-cli.md). Les étapes qui suivent expliquent comment créer un exemple de machine virtuelle avec plusieurs adresses IP, comme décrit dans le scénario. Modifiez les noms des variables et les types d’adresses IP en fonction des besoins de votre implémentation.

1. Installez et configurez Azure CLI 1.0 en suivant les étapes décrites dans l’article [Installer et configurer l’interface de ligne de commande Azure](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et connectez-vous à votre compte Azure par le biais de la commande `azure-login`.

2. [Créez un groupe de ressources](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-groups-and-choose-deployment-locations), ainsi qu’un [réseau virtuel et un sous-réseau](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet). Définissez les valeurs des champs ` --address-prefixes` et `--address-prefix` sur les valeurs suivantes pour qu’elles correspondent au scénario présenté dans cet article :

        --address-prefixes 10.0.0.0/16
        --address-prefix 10.0.0.0/24

    >[!NOTE] 
    >L’article référencé ci-dessus utilise Europe de l’Ouest comme emplacement pour créer des ressources, mais cet article utilise Ouest des États-Unis. Modifiez l’emplacement en conséquence.

3. [Créez un compte de stockage](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-storage-account) pour votre machine virtuelle.

4. Créez la carte réseau et les configurations IP à affecter à la carte réseau. Vous pouvez ajouter, supprimer ou modifier les configurations selon les besoins. Les configurations décrites dans le scénario sont les suivantes :

    **IPConfig-1**

    Entrez les commandes suivantes pour créer :

    - une ressource d’adresse IP publique avec une adresse IP publique statique ;
    - Une configuration IP avec la ressource d’adresse IP publique et une adresse IP privée dynamique

    ```bash
    azure network public-ip create \
    --resource-group myResourceGroup \
    --location westcentralus \
    --name myPublicIP \
    --domain-name-label mypublicdns \
    --allocation-method Static
    ```

    > [!NOTE]
    > Les adresses IP publiques ont un coût nominal. Pour en savoir plus, lisez la page [Tarification des adresses IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Il existe une limite au nombre d’adresses IP publiques qui peuvent être utilisées dans un abonnement. Pour plus d’informations sur les limites, voir [Limites d’Azure](../azure-subscription-service-limits.md#networking-limits).

    ```bash
    azure network nic create \
    --resource-group myResourceGroup \
    --location westcentralus \
    --subnet-vnet-name myVnet \
    --subnet-name mySubnet \
    --name myNic1 \
    --public-ip-name myPublicIP
    ```

    **IPConfig-2**

     Entrez les commandes suivantes pour créer une nouvelle ressource d’adresse IP publique et une nouvelle configuration IP avec une adresse IP publique statique et une adresse IP privée statique :
    
    ```bash
    azure network public-ip create \
    --resource-group myResourceGroup \
    --location westcentralus \
    --name myPublicIP2 \
    --domain-name-label mypublicdns2 \
    --allocation-method Static

    azure network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name IPConfig-2 \
    --private-ip-address 10.0.0.5 \
    --public-ip-name myPublicIP2
    ```

    **IPConfig-3**

    Entrez les commandes suivantes pour créer une configuration IP avec une adresse IP privée dynamique et aucune adresse IP publique :

    ```bash
    azure network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name IPConfig-3
    ```

    >[!NOTE] 
    >Bien que cet article affecte toutes les configurations IP à une seule carte réseau, vous pouvez également affecter plusieurs configurations IP à une carte réseau dans une machine virtuelle. Pour apprendre à créer une machine virtuelle avec plusieurs cartes d’interface réseau, lisez l’article Créer une machine virtuelle avec plusieurs cartes d’interface réseau.

5. Article [Créer une machine virtuelle Linux](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-the-linux-vms). Veillez à supprimer la propriété ```  --availset-name myAvailabilitySet \ ``` dans la mesure où elle n’est pas requise pour ce scénario. Utilisez l’emplacement approprié en fonction de votre scénario. 

    >[!WARNING] 
    > L’étape 6 dans l’article Créer une machine virtuelle échoue si la taille de la machine virtuelle n’est pas prise en charge dans l’emplacement sélectionné. Exécutez la commande suivante pour obtenir une liste complète des machines virtuelles dans la région Centre-Ouest des États-Unis, par exemple : `azure vm sizes --location westcentralus` Ce nom d’emplacement peut être modifié en fonction de votre scénario.

    Pour modifier la taille de la machine virtuelle sur Standard DS2 v2, par exemple, ajoutez simplement la propriété suivante `--vm-size Standard_DS3_v2` à la commande `azure vm create` à l’étape 6.

6. Entrez la commande suivante pour afficher la carte d’interface réseau et les configurations IP associées :

    ```bash
    azure network nic show \
    --resource-group myResourceGroup \
    --name myNic1
    ```
7. Ajoutez les adresses IP privées pour le système d’exploitation de la machine virtuelle en suivant les étapes pour votre système d’exploitation dans la section [Ajouter des adresses IP à un système d’exploitation de machine virtuelle](#os-config) de cet article.

## <a name="add"></a>Ajouter des adresses IP à une machine virtuelle

Vous pouvez ajouter des adresses IP privées et publiques à une carte réseau en suivant les étapes décrites ci-après. Les exemples reposent sur le [scénario](#Scenario) décrit dans cet article.

1. Ouvrez l’interface de ligne de commande Azure et effectuez les étapes restantes de cette section en une seule session CLI. Si l’interface de ligne de commande Azure n’est pas encore installée et configurée, suivez les étapes décrites dans l’article [Installer et configurer l’interface de ligne de commande Azure](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et connectez-vous à votre compte Azure.

2. Suivez les étapes de l’une des sections suivantes, selon vos besoins :

    - **Ajouter une adresse IP privée**
    
        Pour ajouter une adresse IP privée à une carte d’interface réseau, vous devez créer une configuration IP à l’aide de la commande ci-dessous.  Si vous souhaitez ajouter une adresse IP privée dynamique, supprimez `-PrivateIpAddress 10.0.0.7` avant la saisie de la commande. Lorsque vous spécifiez une adresse IP statique, il doit s’agir d’une adresse non utilisée pour le sous-réseau.

        ```bash
        azure network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --private-ip-address 10.0.0.7 \
        --name IPConfig-4
        ```
        Créez autant de configurations que nécessaire, à l’aide de noms de configuration unique et d’adresses IP privées (pour les configurations avec des adresses IP statiques).

    - **Ajouter une adresse IP publique**
    
        Pour ajouter une adresse IP publique, associez-la à une configuration IP nouvelle ou existante. Suivez les étapes de l’une des sections ci-après, selon votre besoin.

        > [!NOTE]
        > Les adresses IP publiques ont un coût nominal. Pour en savoir plus, lisez la page [Tarification des adresses IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Il existe une limite au nombre d’adresses IP publiques qui peuvent être utilisées dans un abonnement. Pour plus d’informations sur les limites, voir [Limites d’Azure](../azure-subscription-service-limits.md#networking-limits).
        >

        - **Associer la ressource à une nouvelle configuration IP**
    
            Lorsque vous ajoutez une adresse IP publique dans une nouvelle configuration IP, vous devez également ajouter une adresse IP privée, car toutes les configurations IP doivent avoir une adresse IP privée. Vous pouvez ajouter une ressource d’adresse IP publique existante ou en créer une. Pour en créer une nouvelle, saisissez la commande suivante :
    
            ```bash
              azure network public-ip create \
            --resource-group myResourceGroup \
            --location westcentralus \
            --name myPublicIP3 \
            --domain-name-label mypublicdns3
            ```

             Pour créer une nouvelle configuration IP avec une adresse IP privée dynamique et la ressource d’adresse IP publique *myPublicIP3*, saisissez la commande suivante :

            ```bash
            azure network nic ip-config create \
            --resource-group myResourceGroup \
            --nic-name myNic \
            --name IPConfig-4 \
            --public-ip-name myPublicIP3
            ```

        - **Association de la ressource à une configuration IP existante**

            Une ressource d’adresse IP publique ne peut être associée qu’à une configuration IP n’ayant pas encore de ressource associée. Pour déterminer si une configuration IP a une adresse IP publique associée, entrez la commande suivante :

            ```bash
            azure network nic ip-config list \
            --resource-group myResourceGroup \
            --nic-name myNic1
            ```

            Recherchez une ligne similaire à celle qui suit pour IPConfig-3 dans la sortie retournée : <br>
            
                Name               Provisioning state  Primary  Private IP allocation  Private IP version  Private IP address  Subnet    Public IP
            
                default-ip-config  Succeeded           true     Dynamic                IPv4                10.0.0.4            mySubnet  myPublicIP
                IPConfig-2         Succeeded           false    Static                 IPv4                10.0.0.5            mySubnet  myPublicIP2
                IPConfig-3         Succeeded           false    Dynamic                IPv4                10.0.0.6            mySubnet

            Étant donné que l’**adresse IP publique** pour *IpConfig-3* est vide, aucune ressource d’adresse IP publique n’y est actuellement associée. Vous pouvez ajouter une ressource d’adresse IP publique existante sur IpConfig-3, ou entrer la commande suivante pour en créer une :

            ```bash
            azure network public-ip create \
            --resource-group  myResourceGroup \
            --location westcentralus \
            --name myPublicIP3 \
            --domain-name-label mypublicdns3 \
            --allocation-method Static
            ```
    
            Entrez la commande suivante pour associer la ressource d’adresse IP publique à la configuration IP existante nommée *IPConfig-3* :
    
            ```bash
            azure network nic ip-config set \
            --resource-group myResourceGroup \
            --nic-name myNic1 \
            --name IPConfig-3 \
            --public-ip-name myPublicIP3
            ```

3. Affichez les ressources d’adresses IP privées et d’adresse IP publique affectées à la carte réseau en entrant la commande suivante :

    ```bash
    azure network nic ip-config list \
    --resource-group myResourceGroup \
    --nic-name myNic1
    ```

    Le résultat retourné ressemble à ce qui suit : <br>

        Name               Provisioning state  Primary  Private IP allocation  Private IP version  Private IP address  Subnet    Public IP
        
        default-ip-config  Succeeded           true     Dynamic                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                 IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Dynamic                IPv4                10.0.0.6            mySubnet  myPublicIP3

4. Ajoutez les adresses IP privées que vous avez ajoutées à l’interface réseau pour le système d’exploitation de la machine virtuelle en suivant les instructions fournies dans la section [Ajouter des adresses IP à un système d’exploitation de machine virtuelle](#os-config) de cet article. N’ajoutez pas les adresses IP publiques au système d’exploitation.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]

