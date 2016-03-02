<properties
	pageTitle="Configuration d’un écouteur à équilibrage de charge interne pour des groupes de disponibilité AlwaysOn | Microsoft Azure"
	description="Ce didacticiel utilise des ressources créées avec le modèle de déploiement classique, et crée un écouteur de groupe de disponibilité AlwaysOn dans Azure utilisant un équilibrage de charge interne (ILB)."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management"/>
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="02/03/2016"
	ms.author="jroth" />

# Configuration d'un écouteur à équilibrage de charge interne pour des groupes de disponibilité AlwaysOn dans Azure

> [AZURE.SELECTOR]
- [Internal Listener](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [External Listener](virtual-machines-sql-server-configure-public-alwayson-availability-group-listener.md)

## Vue d’ensemble

Cette rubrique explique comment configurer un écouteur pour un groupe de disponibilité AlwaysOn à l'aide d'un **équilibrage de charge interne (ILB)**.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager


Votre groupe de disponibilité peut contenir des réplicas locaux uniquement, Azure uniquement, ou locaux et Azure pour les configurations hybrides. Les réplicas Azure peuvent se trouver dans une même région ou dans plusieurs régions grâce à plusieurs réseaux virtuels. Les étapes suivantes supposent que vous avez déjà [configuré un groupe de disponibilité](virtual-machines-sql-server-alwayson-availability-groups-gui.md), mais pas un écouteur.

## Instructions et limitations pour les écouteurs internes
Notez les instructions suivantes concernant l'écouteur du groupe de disponibilité dans Azure à l'aide de l'équilibrage de charge interne :

- L'écouteur du groupe de disponibilité est pris en charge sur Windows Server 2008 R2, Windows Server 2012 et Windows Server 2012 R2.

- Un seul écouteur du groupe de disponibilité interne est pris en charge par service cloud, car l'écouteur est configuré sur l'équilibreur de charge interne, et il n’y a qu’un seul équilibreur de charge interne par service cloud. Toutefois, il est possible de créer plusieurs écouteurs externes. Pour plus d’informations, voir [Configuration d’un écouteur externe pour des groupes de disponibilité AlwaysOn dans Azure](virtual-machines-sql-server-configure-public-alwayson-availability-group-listener.md).

- La création d’un écouteur interne dans un service cloud dans lequel vous avez également un écouteur externe n'est pas prise en charge avec l'adresse IP virtuelle publique du service de cloud.

## Déterminer l'accessibilité de l'écouteur

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Cet article se concentre sur la création d’un écouteur qui utilise un **équilibreur de charge interne**. Si vous avez besoin d’un écouteur public/externe, voir la version de cet article qui explique comment configurer un [écouteur externe](virtual-machines-sql-server-configure-public-alwayson-availability-group-listener.md)

## Créer des points de terminaison de machine virtuelle à charge équilibrée avec retour direct du serveur

Pour l'équilibrage de charge interne, vous devez commencer par créer le système d'équilibrage de charge interne. Pour ce faire, utilisez le script ci-dessous.

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. Vous devez affecter une adresse IP statique pour l’**équilibrage de charge interne**. Commencez par examiner la configuration du réseau virtuel actuel en exécutant la commande suivante :

		(Get-AzureVNetConfig).XMLConfiguration

1. Notez le nom **Subnet** du sous-réseau qui contient les machines virtuelles qui hébergent les réplicas. Celui-ci sera utilisé dans le paramètre **$SubnetName** du script.

1. Notez ensuite le nom **VirtualNetworkSite** et la valeur **AddressPrefix** de début pour le sous-réseau qui contient les machines virtuelles qui hébergent les réplicas. Recherchez ensuite une adresse IP disponible en transférant les deux valeurs à la commande **Test-AzureStaticVNetIP** et en examinant la valeur **AvailableAddresses**. Par exemple, si le réseau virtuel est nommé *MyVNet* et a une plage d’adresses de sous-réseau démarrant avec *172.16.0.128*, la commande suivante répertorie les adresses disponibles :

		(Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses

1. Choisissez l’une des adresses disponibles et utilisez-la dans le paramètre **$ILBStaticIP** du script suivant.

3. Copiez le script PowerShell ci-dessous dans un éditeur de texte et définissez des valeurs de variables adaptées à votre environnement (notez que des valeurs par défaut ont été affectées à certains paramètres). Notez que des déploiements existants qui utilisent des groupes d'affinités ne peuvent pas ajouter un équilibrage de charge interne. Pour plus d’informations sur les exigences liées à l’équilibreur de charge interne, consultez la rubrique [Équilibreur de charge interne](../load-balancer/load-balancer-internal-overview.md). Notez que, si votre groupe de disponibilité s'étend sur plusieurs régions Azure, vous devez exécuter le script une fois dans chaque centre de données pour le service cloud et les nœuds qui se trouvent dans ce centre de données.

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

1. Après avoir défini les variables, copiez le script de l'éditeur de texte dans votre session Azure PowerShell pour l'exécuter. Si l'invite affiche >>, appuyez sur Entrée pour vous assurer que le script s'exécute. Remarque

>[AZURE.NOTE] Le portail Azure Classic ne prend pas en charge l’équilibreur de charge interne actuellement. Vous ne verrez donc pas l’équilibreur de charge interne ou les points de terminaison dans le portail Azure Classic. En revanche, **Get-AzureEndpoint** retourne une adresse IP interne si l’équilibrage de charge s’y exécute. Sinon, la valeur renvoyée est null.

## Vérifiez que KB2854082 est installé le cas échéant

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## Ouvrez les ports de pare-feu dans des nœuds de groupe de disponibilité

[AZURE.INCLUDE [pare-feu](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## Créer l'écouteur de groupe de disponibilité

[AZURE.INCLUDE [pare-feu](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. Pour l'équilibrage de charge, vous devez utiliser l'adresse IP de l'équilibrage de charge interne créé précédemment. Utilisez le script suivant pour obtenir cette adresse IP dans PowerShell.

		# Define variables
		$ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
		(Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

1. Copiez le script PowerShell ci-dessous dans un éditeur de texte sur l'une des machines virtuelles et définissez les variables sur les valeurs notées précédemment.

		# Define variables
		$ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
		$IPResourceName = "<IPResourceName>" # the IP Address resource name
		$ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)

		Import-Module FailoverClusters

		# If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.

		# Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
		# cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

1. Une fois les variables définies, ouvrez une fenêtre Windows PowerShell avec élévation de privilèges, puis copiez le script de l'éditeur de texte et collez-le dans votre session Azure PowerShell pour l'exécuter. Si l'invite affiche >>, appuyez sur Entrée pour vous assurer que le script s'exécute.

2. Répétez cette procédure sur chaque machine virtuelle. Ce script configure la ressource Adresse IP avec l'adresse IP du service cloud et définit d'autres paramètres, tels que le port de la sonde. Lorsque la ressource d'adresse IP est mise en ligne, elle peut ensuite répondre à l'interrogation sur le port de la sonde du point de terminaison à équilibrage de charge créé précédemment dans ce didacticiel.

## Mettre l'écouteur en ligne

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## Éléments de suivi

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## Tester l'écouteur du groupe de disponibilité (au sein du même réseau virtuel)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## Étapes suivantes

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]

<!-----HONumber=AcomDC_0204_2016-->