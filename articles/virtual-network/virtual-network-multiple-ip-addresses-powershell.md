---
title: Plusieurs adresses IP pour les machines virtuelles - PowerShell | Microsoft Docs
description: "Découvrez comment affecter plusieurs adresses IP à une machine virtuelle avec PowerShell | Gestionnaire des ressources."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c44ea62f-7e54-4e3b-81ef-0b132111f1f8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: jdial;annahar
translationtype: Human Translation
ms.sourcegitcommit: 11e490ee3b2d2f216168e827154125807a61a73f
ms.openlocfilehash: 39b45b276c50922878e9918b225f6fa399d42edf


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Affecter plusieurs adresses IP à des machines virtuelles avec PowerShell

> [!div class="op_single_selector"]
> * [Portail](virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
> * [INTERFACE DE LIGNE DE COMMANDE](virtual-network-multiple-ip-addresses-cli.md)
>

Une ou plusieurs cartes réseau sont attachées à une machine virtuelle Azure. Une ou plusieurs adresses IP publiques ou privées, statiques ou dynamiques, peuvent être affectées à chaque carte réseau. L’affectation de plusieurs adresses IP à une machine virtuelle permet :

* D’héberger plusieurs sites web ou services avec des adresses IP différentes et des certificats SSL sur un serveur unique.
* de jouer le rôle d’une appliance virtuelle réseau, telle qu’un pare-feu ou un équilibreur de charge.
* La possibilité d’ajouter l’une des adresses IP privées pour toutes les cartes réseau à un pool principal Azure Load Balancer. Auparavant, seule l’adresse IP principale de la carte réseau principale pouvait être ajoutée à un pool principal. Pour plus d’informations sur la façon d’équilibrer la charge entre plusieurs configurations IP, consultez l’article [Équilibrage de la charge entre plusieurs configurations IP](../load-balancer/load-balancer-multiple-ip.md).

Une ou plusieurs configurations IP sont associées à chaque carte réseau attachée à une machine virtuelle. Une adresse IP privée statique ou dynamique est affectée à chaque configuration. Une ressource d’adresse IP publique peut également être associée à chaque configuration. Une adresse IP statique ou dynamique est affectée à une ressource d’adresse IP publique. Pour plus d’informations sur les adresses IP dans Azure, consultez l’article [Adresses IP dans Azure](virtual-network-ip-addresses-overview-arm.md).

Cet article explique comment utiliser PowerShell pour affecter plusieurs adresses IP à une machine virtuelle créée à l’aide du modèle de déploiement Azure Resource Manager. Il n’est pas possible d’affecter plusieurs adresses IP à des ressources créées à l’aide du modèle de déploiement classique. Pour en savoir plus sur les modèles de déploiement Azure, voir [Comprendre les modèles de déploiement](../resource-manager-deployment-model.md).

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="scenario"></a>Scénario
Une machine virtuelle avec une seule carte réseau est créée et connectée à un réseau virtuel. La machine virtuelle nécessite trois adresses IP *privées* différentes et deux adresses IP *publiques*. Les adresses IP sont affectées aux configurations IP suivantes :

* **IPConfig-1 :** attribue une adresse IP privée *dynamique* (par défaut) et une adresse IP publique *statique*.
* **IPConfig-2 :** attribue une adresse IP privée *statique* et une adresse IP publique *statique*.
* **IPConfig-3 :** attribue une adresse IP privée *dynamique* et aucune adresse IP publique.
  
    ![Plusieurs adresses IP](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

Les configurations IP sont associées à la carte réseau lors de sa création, et la carte réseau est attachée à la machine virtuelle lors de la création de celle-ci. Les types d’adresses IP utilisés pour le scénario sont indiqués à titre d’illustration. Vous pouvez affecter les types d’adresses IP et d’attribution que vous souhaitez.

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Créer une machine virtuelle avec plusieurs adresses IP

Les étapes qui suivent expliquent comment créer un exemple de machine virtuelle avec plusieurs adresses IP, comme décrit dans le scénario. Modifiez les noms des variables et les types d’adresses IP en fonction des besoins de votre implémentation.

1. Ouvrez une invite de commandes PowerShell et effectuez les étapes restantes de cette section dans une même session PowerShell. Si vous n’avez pas installé, ni configuré PowerShell, effectuez les étapes de l’article [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs) .
2. Inscrivez-vous pour la version préliminaire en envoyant à [Plusieurs adresses IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) un message électronique contenant votre ID d’abonnement et l’utilisation prévue. N’essayez pas d’effectuer les étapes restantes :
    - tant que vous n’avez pas reçu de message vous informant que vous avez été accepté dans la version préliminaire ;
    - sans suivre les instructions fournies dans le message que vous recevez.
3. Accomplissez les étapes 1 à 4 décrites dans [Créer une machine virtuelle Windows](../virtual-machines/virtual-machines-windows-ps-create.md). N’accomplissez pas l’étape 5 (création de ressource IP publique et d’interface réseau). Si vous modifiez les noms des variables utilisées dans cet article, modifiez également les noms des variables dans les étapes restantes. Pour créer une machine virtuelle Linux, sélectionnez un système d’exploitation Linux au lieu de Windows.
4. Créez une variable pour stocker l’objet de sous-réseau créé à l’étape 4 (Créer une machine virtuelle) de l’article Création d’une machine virtuelle Windows en tapant la commande suivante :

    ```powershell
    $SubnetName = $mySubnet.Name
    $Subnet = $myVnet.Subnets | Where-Object { $_.Name -eq $SubnetName }
    ```
5. Définissez les configurations IP à affecter à l’interface réseau. Vous pouvez ajouter, supprimer ou modifier les configurations selon les besoins. Les configurations décrites dans le scénario sont les suivantes :

    **IPConfig-1**

    Entrez les commandes suivantes pour créer :
    - une ressource d’adresse IP publique avec une adresse IP publique statique ;
    - une configuration IP avec la ressource d’adresse IP publique et une adresse IP privée dynamique.

    ```powershell
    $myPublicIp1     = New-AzureRmPublicIpAddress -Name "myPublicIp1" -ResourceGroupName $myResourceGroup -Location $location -AllocationMethod Static
    $IpConfigName1  = "IPConfig-1"
    $IpConfig1      = New-AzureRmNetworkInterfaceIpConfig -Name $IpConfigName1 -Subnet $Subnet -PublicIpAddress $myPublicIp1 -Primary
    ```

    Remarquez le commutateur `-Primary` dans la commande précédente. Lorsque vous affectez plusieurs configurations IP à une interface réseau, une configuration doit être affectée en tant que configuration *principale*.

    > [!NOTE]
    > Les adresses IP publiques ont un coût nominal. Pour en savoir plus, lisez la page [Tarification des adresses IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Il existe une limite au nombre d’adresses IP publiques qui peuvent être utilisées dans un abonnement. Pour plus d’informations sur les limites, voir [Limites d’Azure](../azure-subscription-service-limits.md#networking-limits).
    >

    **IPConfig-2**

    Modifiez la valeur de la variable **$IPAddress** qui suit une adresse valide disponible sur le sous-réseau que vous avez créé. Pour vérifier si l’adresse 10.0.0.5 est disponible sur le sous-réseau, entrez la commande `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.5 -VirtualNetwork $myVnet`. Si l’adresse est disponible, la sortie retourne *True*. S’il n’est pas disponible, la sortie retourne *False* et la liste des adresses disponibles. Entrez les commandes suivantes pour créer une nouvelle ressource d’adresse IP publique et une nouvelle configuration IP avec une adresse IP publique et une adresse IP privée statiques :
    
    ```powershell
    $IpConfigName2 = "IPConfig-2"
    $IPAddress     = 10.0.0.5
    $myPublicIp2   = New-AzureRmPublicIpAddress -Name "myPublicIp2" -ResourceGroupName $myResourceGroup `
    -Location $location -AllocationMethod Static
    $IpConfig2     = New-AzureRmNetworkInterfaceIpConfig -Name $IpConfigName2 `
    -Subnet $Subnet -PrivateIpAddress $IPAddress -PublicIpAddress $myPublicIp2
    ```

    **IPConfig-3**

    Entrez les commandes suivantes pour créer une configuration IP avec une adresse IP privée dynamique et aucune adresse IP publique :

    ```powershell
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName3 -Subnet $Subnet
    ```
6. Créez la carte réseau en utilisant les configurations IP définies à l’étape précédente en entrant la commande suivante :

    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name myNIC -ResourceGroupName $myResourceGroup `
    -Location $location -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
    ```
    > [!NOTE]
    > Bien que cet article affecte toutes les configurations IP à une seule carte réseau, vous pouvez également affecter plusieurs configurations IP à tout carte réseau dans une machine virtuelle. Pour découvrir comment créer une machine virtuelle avec plusieurs cartes réseau, voir [Créer une machine virtuelle avec plusieurs cartes d’interface réseau](virtual-network-deploy-multinic-arm-ps.md).

7. Accomplissez l’étape 6 décrite dans [Créer une machine virtuelle](../virtual-machines/virtual-machines-windows-ps-create.md). 

    > [!WARNING]
    > L’étape 6 décrite dans Créer une machine virtuelle échoue dans les cas suivants :
    > - Vous avez modifié la variable nommée $myNIC à l’étape 6 de cet article.
    > - Vous n’avez pas accompli les étapes précédentes décrites ici et dans Créer un machine virtuelle.
    >
8. Entrez la commande suivante pour afficher les ressources d’adresses IP privées et d’adresse IP publique affectées à la carte réseau :

    ```powershell
    $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```
9. Ajoutez les adresses IP privées pour le système d’exploitation de la machine virtuelle en suivant les étapes pour votre système d’exploitation dans la section [Ajouter des adresses IP à un système d’exploitation de machine virtuelle](#os-config) de cet article. N’ajoutez pas les adresses IP publiques au système d’exploitation.

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>Ajouter des adresses IP à une machine virtuelle

Vous pouvez ajouter des adresses IP privées et publiques à une carte réseau en suivant les étapes décrites ci-après. Les exemples fournis dans les sections suivantes supposent que vous disposez déjà d’une machine virtuelle avec les trois configurations IP décrites dans le [scénario](#Scenario) de cet article, mais ce n’est pas une condition obligatoire.

1. Ouvrez une invite de commandes PowerShell et effectuez les étapes restantes de cette section dans une même session PowerShell. Si vous n’avez pas installé, ni configuré PowerShell, effectuez les étapes de l’article [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs) .
2. Inscrivez-vous pour la version préliminaire en envoyant à [Plusieurs adresses IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) un message électronique contenant votre ID d’abonnement et l’utilisation prévue. N’essayez pas d’effectuer les étapes restantes :
    - tant que vous n’avez pas reçu de message vous informant que vous avez été accepté dans la version préliminaire ;
    - sans suivre les instructions fournies dans le message que vous recevez.
3. Remplacez les « valeurs » des $Variables suivantes par le nom de l’interface réseau à laquelle vous voulez ajouter une adresse IP, et par le groupe de ressources et l’emplacement dans lesquels réside l’interface réseau :

    ```powershell
    $NICname         = "myNIC"
    $myResourceGroup = "myResourceGroup"
    $location        = "westcentralus"
    ```

    Si vous ne connaissez pas le nom de l’interface réseau que vous souhaitez modifier, entrez les commandes suivantes, puis modifiez les valeurs des variables précédentes :

    ```powershell
    Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location
    ```
4. Créez une variable et affectez-la à l’interface réseau à l’aide de la commande suivante :

    ```powershell
    $myNIC = Get-AzureRmNetworkInterface -Name $NICname -ResourceGroupName $myResourceGroup
    ```
5. Dans les commandes suivantes, remplacez *myVNet* et *mySubnet* par les noms du réseau virtuel et du sous-réseau auxquels la carte réseau est connectée. Entrez les commandes pour récupérer les objets réseau virtuel et sous-réseau auxquels la carte réseau est connectée :

    ```powershell
    $myVnet = Get-AzureRMVirtualnetwork -Name myVNet -ResourceGroupName $myResourceGroup
    $Subnet = $myVnet.Subnets | Where-Object { $_.Name -eq "mySubnet" }
    ```
    Si vous ne connaissez pas le nom du réseau virtuel ou du sous-réseau auxquels la carte réseau est connectée, entrez la commande suivante :
    ```powershell
    $mynic.IpConfigurations
    ```
    Dans la sortie, recherchez un texte similaire au texte suivant :

        Subnet   : {
                     "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"

    Dans cette sortie, *myVnet* est le réseau virtuel, et *mySubnet* le sous-réseau, auxquels la carte réseau est connectée.

6. Suivez les étapes de l’une des sections suivantes, selon vos besoins :

    **Ajouter une adresse IP privée**

    Pour ajouter une adresse IP privée à une carte d’interface réseau, vous devez créer une configuration IP. La commande suivante crée une configuration avec l’adresse IP statique 10.0.0.7. Si vous souhaitez ajouter une adresse IP privée dynamique, supprimez `-PrivateIpAddress 10.0.0.7` avant d’entrer la commande. Lorsque vous spécifiez une adresse IP statique, il doit s’agir d’une adresse non utilisée pour le sous-réseau. Nous vous recommandons de commencer par entrer la commande `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` afin de tester l’adresse pour vous assurer de sa disponibilité. Si l’adresse IP est disponible, la sortie retourne *True*. S’il n’est pas disponible, la sortie retourne *False* et la liste des adresses disponibles.

    ```powershell
    Add-AzureRmNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
     $myNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
    ```

    Créez autant de configurations que nécessaire, à l’aide de noms de configuration unique et d’adresses IP privées (pour les configurations avec des adresses IP statiques).

    Ajoutez l’adresse IP privée au système d’exploitation de la machine virtuelle en suivant les étapes pour votre système d’exploitation dans la section [Ajouter des adresses IP à un système d’exploitation de machine virtuelle](#os-config) de cet article.

    **Ajouter une adresse IP publique**

    Une adresse IP publique est ajoutée en associant une ressource d’adresse IP publique à une nouvelle configuration IP ou une configuration IP existante. Suivez les étapes de l’une des sections ci-après, selon votre besoin.

    > [!NOTE]
    > Les adresses IP publiques ont un coût nominal. Pour en savoir plus, lisez la page [Tarification des adresses IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Il existe une limite au nombre d’adresses IP publiques qui peuvent être utilisées dans un abonnement. Pour plus d’informations sur les limites, voir [Limites d’Azure](../azure-subscription-service-limits.md#networking-limits).
    >

    **Associer la ressource d’adresse IP publique à une nouvelle configuration IP**
    
    Lorsque vous ajoutez une adresse IP publique dans une nouvelle configuration IP, vous devez également ajouter une adresse IP privée, car toutes les configurations IP doivent avoir une adresse IP privée. Vous pouvez ajouter une ressource d’adresse IP publique existante ou en créer une. Pour une ressource d’adresse IP publique, entrez la commande suivante :
    
    ```powershell
    $myPublicIp3   = New-AzureRmPublicIpAddress -Name "myPublicIp3" -ResourceGroupName $myResourceGroup `
    -Location $location -AllocationMethod Static
    ```

    Pour créer une configuration IP avec une adresse IP privée dynamique et la ressource d’adresse IP publique *myPublicIP3* associée, entrez la commande suivante :

    ```powershell
    Add-AzureRmNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
     $myNIC -Subnet $Subnet -PublicIpAddress $myPublicIp3
    ```

    **Associer la ressource d’adresse IP publique à une configuration IP existante**

    Une ressource d’adresse IP publique ne peut être associée qu’à une configuration IP n’ayant pas encore de ressource associée. Pour déterminer si une configuration IP a une adresse IP publique associée, entrez la commande suivante :

    ```powershell
    $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```

    Recherchez dans la sortie une ligne similaire à celle ci-dessous :

        Name       PrivateIpAddress PublicIpAddress                                           Primary
        ----       ---------------- ---------------                                           -------
        IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
        IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
        IpConfig-3 10.0.0.6                                                                     False

    Étant donné que la colonne **PublicIpAddress** pour *IpConfig-3* est vide, aucune ressource d’adresse IP publique n’y est actuellement associée. Vous pouvez ajouter une ressource d’adresse IP publique existante à IpConfig-3, ou entrer la commande suivante pour en créer une :

    ```powershell
    $myPublicIp3   = New-AzureRmPublicIpAddress -Name "myPublicIp3" -ResourceGroupName $myResourceGroup `
    -Location $location -AllocationMethod Static
    ```

    Pour associer la ressource d’adresse IP publique à la configuration IP existante nommée *IPConfig-3*, entrez la commande suivante :
    
    ```powershell
    Set-AzureRmNetworkInterfaceIpConfig -Name IpConfig-3 -NetworkInterface $mynic -Subnet $Subnet -PublicIpAddress $myPublicIp3
    ```

7. Définissez la carte réseau avec la nouvelle configuration IP en entrant la commande suivante :

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $myNIC
    ```

8. Affichez les ressources d’adresses IP privées et d’adresse IP publique affectées à la carte réseau en entrant la commande suivante :

    ```powershell   
    $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```
9. Ajoutez l’adresse IP privée au système d’exploitation de la machine virtuelle en suivant les étapes pour votre système d’exploitation dans la section [Ajouter des adresses IP à un système d’exploitation de machine virtuelle](#os-config) de cet article. N’ajoutez pas l’adresse IP publique au système d’exploitation.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]


<!--HONumber=Dec16_HO1-->


