---
title: Accélération réseau pour une machine virtuelle - PowerShell | Microsoft Docs
description: Découvrez comment configurer l’accélération réseau pour une machine virtuelle à l’aide de PowerShell.
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: jdial

---
# <a name="accelerated-networking-for-a-virtual-machine"></a>Accélération réseau pour une machine virtuelle
> [!div class="op_single_selector"]
> * [Portail Azure](virtual-network-accelerated-networking-portal.md)
> * [PowerShell](virtual-network-accelerated-networking-powershell.md)
> 
> 

L’accélération réseau active la virtualisation SR-IOV (Single Root I/O Virtualization) sur une machine virtuelle, ce qui améliore considérablement les performances réseau. Ce chemin d’accès hautes performances contourne l’hôte du chemin de données en réduisant la latence, l’instabilité et l’utilisation du processeur pour servir les charges de travail réseau les plus exigeantes sur les types de machine virtuelle pris en charge. Cet article explique comment utiliser Azure PowerShell pour configurer l’accélération réseau dans le modèle de déploiement Azure Resource Manager. Vous pouvez également créer une machine virtuelle avec accélération réseau à l’aide du portail Azure. Pour en savoir plus, cliquez sur la zone du portail Azure en haut de cet article.

L’illustration suivante montre la communication entre deux machines virtuelles avec ou sans accélération réseau :

![Opérateurs de comparaison](./media/virtual-network-accelerated-networking-powershell/image1.png)

Sans accélération réseau, tout le trafic réseau vers et depuis la machine virtuelle doit traverser l’hôte et le commutateur virtuel. Le commutateur virtuel fournit au trafic réseau toutes les stratégies, telles que les groupes de sécurité réseau, les listes de contrôle d’accès, l’isolation et d’autres services de réseau virtualisé. Pour plus d’informations, consultez l’article [Virtualisation réseau Hyper-V et commutateur virtuel](https://technet.microsoft.com/library/jj945275.aspx) .

Dans le cas de l’accélération réseau, le trafic réseau parvient à la carte réseau avant d’être transmis à la machine virtuelle. Toutes les stratégies réseau du commutateur virtuel s’appliquent sans que l’accélération réseau soit déchargée et appliquée dans le matériel. L’application de la stratégie au niveau du matériel permet à la carte réseau de transférer le trafic directement à la machine virtuelle, en ignorant l’hôte et le commutateur virtuel, tout en conservant toutes les stratégies qu’il a appliquées dans l’hôte.

Les avantages de l’accélération réseau s’appliquent uniquement à la machine virtuelle activée. Pour de meilleurs résultats, il convient d’activer cette fonctionnalité sur au moins deux machines virtuelles connectées au même réseau virtuel.  Lors de la communication entre les réseaux virtuels ou lors de la connexion locale, cette fonctionnalité a peu d’incidence sur la latence globale.

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="benefits"></a>Avantages
* **Latence plus faible/Nombre supérieur de paquets par seconde (pps) :** la suppression du commutateur virtuel du chemin de données évite que les paquets liés au traitement des stratégies séjournent sur l’hôte, ce qui augmente le nombre de paquets pouvant être traités dans la machine virtuelle.
* **Instabilité réduite :** le traitement du commutateur virtuel dépend de la quantité de stratégie qui doit être appliquée et de la charge de travail du processeur qui effectue le traitement. Le déchargement des stratégies sur le matériel supprime cette variabilité en fournissant des paquets directement à la machine virtuelle, en supprimant l’hôte dans la communication de la machine virtuelle et toutes les interruptions de logiciel et les changements de contexte.
* **Utilisation réduite du processeur :** ignorer le commutateur virtuel dans l’hôte réduit l’utilisation du processeur pour le traitement du trafic réseau.

## <a name="limitations"></a>Limitations
Les limitations suivantes existent lors de l’utilisation de cette fonctionnalité :

* **Création de l’interface réseau :** l’accélération réseau ne peut être activée que pour une nouvelle interface réseau.  Elle ne peut pas être activée sur une interface réseau existante.
* **Création de machines virtuelles :** une interface réseau avec accélération réseau peut être rattachée uniquement à une machine virtuelle lorsque cette dernière est créée. L’interface réseau ne peut pas être attachée à une machine virtuelle existante.
* **Régions :** option disponible uniquement dans les régions Azure Ouest-Centre des États-Unis et Europe de l’Ouest. Le nombre de régions augmentera dans le futur.
* **Systèmes d’exploitation pris en charge :** Microsoft Windows Server 2012 R2 et Windows Server 2016 Technical Preview 5. Linux et Windows Server 2012 seront pris en charge prochainement.
* **Taille de machine virtuelle :** les tailles Standard_D15_v2 et Standard_DS15_v2 sont les seules tailles d’instance de machine virtuelle prises en charge. Pour plus d’informations, consultez [Tailles des machines virtuelles Windows](../virtual-machines/virtual-machines-windows-sizes.md) . Le nombre de tailles d’instance de machine virtuelle augmentera dans le futur.

Les modifications apportées à ces limites seront annoncées sur la page des [mises à jour concernant l’accélération réseau Azure](https://azure.microsoft.com/updates/accelerated-networking-in-preview).

## <a name="create-a-windows-vm-with-accelerated-networking"></a>Créer une machine virtuelle Windows avec accélération réseau
1. Ouvrez une invite de commandes PowerShell et effectuez les étapes restantes de cette section dans une même session PowerShell. Si vous n’avez pas installé, ni configuré PowerShell, effectuez les étapes de l’article [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) .
2. Pour obtenir la version préliminaire, envoyez un e-mail à [Abonnements à l’accélération réseau](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) en indiquant votre ID d’abonnement et l’utilisation prévue. N’effectuez pas les étapes restantes tant que vous n’avez pas reçu d’e-mail vous informant que vous avez été accepté dans la version préliminaire.
3. Inscrivez votre capacité avec l’abonnement en entrant les commandes suivantes :
   
        Register-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
4. Remplacez *westcentralus* par le nom d’un autre emplacement pris en charge par cette fonctionnalité dans la section [Limitations](#limitations) de cet article (le cas échéant). Entrez la commande suivante pour définir une variable pour l’emplacement :
   
        $locName = "westcentralus"
5. Remplacez *RG1* par un nom de groupe de ressources qui contiendra la nouvelle interface réseau, puis entrez les commandes suivantes pour la créer :
   
        $rgName = "RG1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
6. Remplacez *VM1-NIC1* par le nom que vous souhaitez donner à l’interface réseau, puis entrez la commande suivante :
   
        $NICName = "VM1-NIC1"
7. L’interface réseau doit être connectée à un sous-réseau au sein d’un réseau virtuel Azure existant dans le même emplacement et avec le même [abonnement](../azure-glossary-cloud-terminology.md#subscription) que l’interface réseau. Si vous n’êtes pas familiarisé avec les réseaux virtuels, lisez l’article [Présentation du réseau virtuel](virtual-networks-overview.md) . Pour créer un réseau virtuel, suivez les étapes de l’article [Créer un réseau virtuel](virtual-networks-create-vnet-arm-ps.md) . Remplacez les « valeurs » des $Variables suivantes par le nom du réseau virtuel et du sous-réseau auxquels vous souhaitez connecter l’interface réseau.
   
        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"
   
    Si vous ne connaissez pas le nom d’un réseau virtuel existant à l’emplacement que vous avez choisi à l’étape 3, entrez les commandes suivantes :
   
        $VirtualNetworks = Get-AzureRmVirtualNetwork
        $VirtualNetworks | Where-Object {$_.Location -eq $locName} | Format-Table Name, Location
   
    Si la liste est vide, vous devez créer un réseau virtuel à l’emplacement spécifié. Pour créer un réseau virtuel, suivez les étapes de l’article [Créer un réseau virtuel](virtual-networks-create-vnet-arm-ps.md) .
   
    Pour obtenir le nom des sous-réseaux du réseau virtuel, tapez les commandes suivantes et remplacez *Subnet1* ci-dessus par le nom du sous-réseau :
   
        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $VNet.Subnets | Format-Table Name, AddressPrefix
8. Entrez les commandes suivantes pour récupérer le réseau virtuel et le sous-réseau et affectez-les à des variables.
   
        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }
9. Identifiez une ressource d’adresse IP publique existante qui peut être associée à l’interface réseau afin de vous y connecter via Internet. Si vous ne souhaitez pas accéder à la machine virtuelle avec l’interface réseau sur Internet, vous pouvez ignorer cette étape. Sans adresse IP publique, vous devez vous connecter à la machine virtuelle à partir d’une autre machine virtuelle connectée au même réseau virtuel. 
   
    Remplacez *PIP1* par le nom d’une ressource d’adresse IP publique qui existe dans l’emplacement où vous créez l’interface réseau et qui n’est pas associée à une autre interface réseau. Si nécessaire, remplacez $rgName par le nom du groupe de ressources existant dans la ressource d’adresse IP publique, puis entrez la commande suivante :
   
        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $rgName
   
    Entrez les commandes suivantes si vous ne connaissez pas le nom d’une ressource d’adresse IP publique existante :
   
        $PublicIPAddresses = Get-AzureRmPublicIPAddress
        $PublicIPAddresses | Where-Object { $_.Location -eq $locName } |Format-Table Name, Location, IPAddress, IpConfiguration
   
    Si la colonne **IPConfiguration** est vide dans le résultat, la ressource d’adresse IP publique n’est pas associée à une interface réseau existante et peut donc être utilisée. Si la liste est vide ou ne contient aucune ressource d’adresse IP publique disponible, vous pouvez en créer une à l’aide de la commande New-AzureRmPublicIPAddress.
   
   > [!NOTE]
   > Les adresses IP publiques ont un coût nominal. Pour en savoir plus, lisez la page [Tarification des adresses IP](https://azure.microsoft.com/pricing/details/ip-addresses) .
   > 
   > 
10. Si vous avez choisi de ne pas ajouter une ressource d’adresse IP publique à l’interface, supprimez *-PublicIPAddress $PIP1* à la fin de la commande suivante. Créez l’interface réseau avec accélération réseau en entrant la commande suivante :
    
        $nic = New-AzureRmNetworkInterface -Location $locName -Name $NICName -ResourceGroupName $rgName -Subnet $Subnet -EnableAcceleratedNetworking -PublicIpAddress $PIP1 
11. Affectez l’interface réseau à une machine virtuelle lors de la création de la machine virtuelle en suivant les étapes 3 et 6 de l’article [Créer une machine virtuelle](../virtual-machines/virtual-machines-windows-ps-create.md) . À l’étape 6-2, remplacez *Standard_A1* par l’une des tailles de machine virtuelle indiquées dans la section [Limitations](#limitations) de cet article.
    
    > [!NOTE]
    > Si vous avez remplacé le *nom* des variables $locName, $rgName ou $nic dans cet article, l’étape 6 de l’article Créer une machine virtuelle échouera. Cependant, vous pouvez remplacer les *valeurs* des variables.
    > 
    > 
12. Une fois que la machine virtuelle est créée, téléchargez le [pilote Accélération réseau](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84), connectez-vous à la machine virtuelle, puis exécutez le programme d’installation du pilote à l’intérieur de la machine virtuelle.
13. Cliquez avec le bouton droit sur l’icône **Gestionnaire de périphériques**. Vérifiez que la **carte Ethernet Mellanox ConnectX-3 Virtual Function** apparaît sous l’option **Réseau** développée, comme dans l’image suivante :
    
    ![Gestionnaire de périphériques](./media/virtual-network-accelerated-networking-powershell/image2.png)

<!--HONumber=Oct16_HO2-->


