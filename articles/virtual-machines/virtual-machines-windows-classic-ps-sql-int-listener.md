---
title: Configurer un écouteur à équilibrage de charge interne pour des groupes de disponibilité Always On | Microsoft Docs
description: Ce didacticiel utilise des ressources créées avec le modèle de déploiement Classic, et crée un écouteur de groupe de disponibilité Always On dans Azure utilisant un équilibreur de charge interne (ILB).
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: ''
tags: azure-service-management

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 08/19/2016
ms.author: MikeRayMSFT

---
# Configurer un écouteur à équilibrage de charge interne pour des groupes de disponibilité Always On dans Azure
> [!div class="op_single_selector"]
> * [Écouteur interne](virtual-machines-windows-classic-ps-sql-int-listener.md)
> * [Écouteur externe](virtual-machines-windows-classic-ps-sql-ext-listener.md)
> 
> 

## Vue d'ensemble
Cette rubrique explique comment configurer un écouteur pour un groupe de disponibilité Always On à l’aide d’un **équilibreur de charge interne (ILB)**.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Pour configurer un écouteur ILB pour un groupe de disponibilité Always On dans un modèle Resource Manager, voir [Configurer un équilibreur de charge interne pour un groupe de disponibilité Always On dans Azure](virtual-machines-windows-portal-sql-alwayson-int-listener.md).

Votre groupe de disponibilité peut contenir des réplicas locaux uniquement, Azure uniquement, ou locaux et Azure pour les configurations hybrides. Les réplicas Azure peuvent se trouver dans une même région ou dans plusieurs régions grâce à plusieurs réseaux virtuels. Les étapes suivantes supposent que vous avez déjà [configuré un groupe de disponibilité](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md), mais pas un écouteur.

## Instructions et limitations pour les écouteurs internes
Notez les instructions suivantes concernant l'écouteur du groupe de disponibilité dans Azure à l'aide de l'équilibrage de charge interne :

* L'écouteur du groupe de disponibilité est pris en charge sur Windows Server 2008 R2, Windows Server 2012 et Windows Server 2012 R2.
* Un seul écouteur du groupe de disponibilité interne est pris en charge par service cloud, car l'écouteur est configuré sur l'équilibreur de charge interne, et il n’y a qu’un seul équilibreur de charge interne par service cloud. Toutefois, il est possible de créer plusieurs écouteurs externes. Pour plus d’informations, voir [Configurer un écouteur externe pour les groupes de disponibilité Always On dans Azure](virtual-machines-windows-classic-ps-sql-ext-listener.md).
* La création d’un écouteur interne dans un service cloud dans lequel vous avez également un écouteur externe n'est pas prise en charge avec l'adresse IP virtuelle publique du service de cloud.

## Déterminer l'accessibilité de l'écouteur
[!INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Cet article se concentre sur la création d’un écouteur qui utilise un **équilibreur de charge interne**. Si vous avez besoin d’un écouteur public/externe, voir la version de cet article qui explique comment configurer un [écouteur externe](virtual-machines-windows-classic-ps-sql-ext-listener.md)

## Créer des points de terminaison de machine virtuelle à charge équilibrée avec retour direct du serveur
Pour l'équilibrage de charge interne, vous devez commencer par créer le système d'équilibrage de charge interne. Pour ce faire, utilisez le script ci-dessous.

[!INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. Vous devez affecter une adresse IP statique pour l’**équilibrage de charge interne**. Commencez par examiner la configuration du réseau virtuel actuel en exécutant la commande suivante :
   
        (Get-AzureVNetConfig).XMLConfiguration
2. Notez le nom **Subnet** du sous-réseau qui contient les machines virtuelles qui hébergent les réplicas. Celui-ci sera utilisé dans le paramètre **$SubnetName** du script.
3. Notez ensuite le nom **VirtualNetworkSite** et la valeur **AddressPrefix** de début pour le sous-réseau qui contient les machines virtuelles qui hébergent les réplicas. Recherchez ensuite une adresse IP disponible en transférant les deux valeurs à la commande **Test-AzureStaticVNetIP** et en examinant la valeur **AvailableAddresses**. Par exemple, si le réseau virtuel est nommé *MyVNet* et a une plage d’adresses de sous-réseau démarrant avec *172.16.0.128*, la commande suivante répertorie les adresses disponibles :
   
        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
4. Choisissez l’une des adresses disponibles et utilisez-la dans le paramètre **$ILBStaticIP** du script suivant.
5. Copiez le script PowerShell ci-dessous dans un éditeur de texte et définissez des valeurs de variables adaptées à votre environnement (notez que des valeurs par défaut ont été affectées à certains paramètres). Notez que des déploiements existants qui utilisent des groupes d'affinités ne peuvent pas ajouter un équilibrage de charge interne. Pour plus d’informations sur les exigences liées à l’équilibreur de charge interne, consultez la rubrique [Équilibreur de charge interne](../load-balancer/load-balancer-internal-overview.md). Notez que, si votre groupe de disponibilité s'étend sur plusieurs régions Azure, vous devez exécuter le script une fois dans chaque centre de données pour le service cloud et les nœuds qui se trouvent dans ce centre de données.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the VNet
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value
   
        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP
   
        # Configure a load balanced endpoint for each node in $AGNodes using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }
6. Après avoir défini les variables, copiez le script de l'éditeur de texte dans votre session Azure PowerShell pour l'exécuter. Si l'invite affiche >>, appuyez sur Entrée pour vous assurer que le script s'exécute. Remarque

> [!NOTE]
> Le portail Azure Classic ne prend pas en charge l’équilibreur de charge interne actuellement. Vous ne verrez donc pas l’équilibreur de charge interne ou les points de terminaison dans le portail Azure Classic. En revanche, **Get-AzureEndpoint** retourne une adresse IP interne si l’équilibrage de charge s’y exécute. Sinon, la valeur renvoyée est null.
> 
> 

## Vérifiez que KB2854082 est installé le cas échéant
[!INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## Ouvrez les ports de pare-feu dans des nœuds de groupe de disponibilité
[!INCLUDE [pare-feu](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## Créer l'écouteur de groupe de disponibilité
[!INCLUDE [pare-feu](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. Pour l'équilibrage de charge, vous devez utiliser l'adresse IP de l'équilibrage de charge interne créé précédemment. Utilisez le script suivant pour obtenir cette adresse IP dans PowerShell.
   
        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress
2. Sur l’une des machines virtuelles, copiez le script PowerShell pour votre système d’exploitation dans un éditeur de texte, et définissez les variables sur les valeurs notées précédemment.
   
    Pour Windows Server 2012 ou version ultérieure, utilisez le script suivant :
   
        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)
   
        Import-Module FailoverClusters
   
        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   
    Pour Windows Server 2008 R2, utilisez le script suivant :
   
        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)
   
        Import-Module FailoverClusters
   
        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255
3. Une fois les variables définies, ouvrez une fenêtre Windows PowerShell avec élévation de privilèges, puis copiez le script de l'éditeur de texte et collez-le dans votre session Azure PowerShell pour l'exécuter. Si l'invite affiche >>, appuyez sur Entrée pour vous assurer que le script s'exécute.
4. Répétez cette procédure sur chaque machine virtuelle. Ce script configure la ressource Adresse IP avec l'adresse IP du service cloud et définit d'autres paramètres, tels que le port de la sonde. Lorsque la ressource d'adresse IP est mise en ligne, elle peut ensuite répondre à l'interrogation sur le port de la sonde du point de terminaison à équilibrage de charge créé précédemment dans ce didacticiel.

## Mettre l'écouteur en ligne
[!INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## Éléments de suivi
[!INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## Tester l'écouteur du groupe de disponibilité (au sein du même réseau virtuel)
[!INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## Étapes suivantes
[!INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]

<!---HONumber=AcomDC_0824_2016-->