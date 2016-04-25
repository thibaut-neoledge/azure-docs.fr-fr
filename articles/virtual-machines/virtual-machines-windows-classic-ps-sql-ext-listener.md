<properties
	pageTitle="Configuration d'un écouteur externe pour des groupes de disponibilité AlwaysOn | Microsoft Azure"
	description="Ce didacticiel vous guide tout au long des étapes de création d'un écouteur de groupe de disponibilité AlwaysOn dans Azure qui est accessible en externe à l'aide de l'adresse IP virtuelle publique du service cloud associé."
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="MikeRayMSFT"
	manager="jhubbard"
	editor=""
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="04/05/2016"
	ms.author="mikeray" />

# Configuration d'un écouteur externe pour des groupes de disponibilité AlwaysOn dans Azure

> [AZURE.SELECTOR]
- [Écouteur interne](virtual-machines-windows-classic-ps-sql-int-listener.md)
- [Écouteur externe](virtual-machines-windows-classic-ps-sql-ext-listener.md)

Cette rubrique explique comment configurer un écouteur pour un groupe de disponibilité AlwaysOn accessible en externe sur Internet. Il est possible d'associer l'**adresse IP virtuelle publique** du service cloud à l'écouteur.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager


Votre groupe de disponibilité peut contenir des réplicas locaux uniquement, Azure uniquement, ou locaux et Azure pour les configurations hybrides. Les réplicas Azure peuvent se trouver dans une même région ou dans plusieurs régions grâce à plusieurs réseaux virtuels. Les étapes suivantes supposent que vous avez déjà [configuré un groupe de disponibilité](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md), mais pas un écouteur.

## Instructions et limitations pour les écouteurs externes

Notez que les instructions suivantes s'appliquent pour l'écouteur du groupe de disponibilité dans Azure lorsque vous effectuez un déploiement à l'aide de l'adresse IP virtuelle publique du service cloud :

- L'écouteur du groupe de disponibilité est pris en charge sur Windows Server 2008 R2, Windows Server 2012 et Windows Server 2012 R2.

- L'application cliente doit se trouver dans un service cloud différent de celui qui contient vos machines virtuelles de groupe de disponibilité. Azure ne prend pas en charge le retour direct du serveur avec un client et un serveur se trouvant dans le même service cloud.

- Par défaut, les étapes décrites dans cet article montrent comment configurer un écouteur pour utiliser l'adresse IP virtuelle (VIP) du service cloud. Toutefois, il est possible de réserver et de créer plusieurs adresses IP virtuelles pour votre service cloud. Cela vous permet d'utiliser les étapes dans cet article pour créer plusieurs écouteurs associés à une adresse IP virtuelle différente. Pour plus d'informations sur la création de plusieurs adresses IP virtuelles, consultez la section [Plusieurs adresses IP virtuelles par service cloud](../load-balancer/load-balancer-multivip.md).

- Si vous créez un écouteur pour un environnement hybride, le réseau local doit disposer de la connectivité à l'Internet public en plus du VPN de site à site avec le réseau virtuel Azure. Dans le sous-réseau Azure, l'écouteur du groupe de disponibilité est accessible uniquement par l'adresse IP publique du service cloud respectif.

- La création d’un écouteur externe dans un service cloud dans lequel vous avez également un écouteur interne n'est pas prise en charge avec l'équilibrage de charge interne.

## Déterminer l'accessibilité de l'écouteur

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Cet article se concentre sur la création d’un écouteur qui utilise un **équilibrage de charge externe**. Si vous souhaitez un écouteur qui est privé sur votre réseau virtuel, consultez la version de cet article qui explique comment configurer un [écouteur avec équilibrage de charge interne](virtual-machines-windows-classic-ps-sql-int-listener.md)

## Créer des points de terminaison de machine virtuelle à charge équilibrée avec retour direct du serveur

L'équilibrage de charge externe utilise l'adresse IP virtuelle publique du service cloud qui héberge vos machines virtuelles. Par conséquent, vous n'avez besoin créer ou configurer l'équilibrage de charge dans ce cas.

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. Copiez le script PowerShell ci-dessous dans un éditeur de texte et définissez des valeurs de variables adaptées à votre environnement (des valeurs par défaut ont été affectées à certains paramètres). Notez que, si votre groupe de disponibilité s'étend sur plusieurs régions Azure, vous devez exécuter le script une fois dans chaque centre de données pour le service cloud et les nœuds qui se trouvent dans ce centre de données.

		# Define variables
		$ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
		$AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas

		# Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
		ForEach ($node in $AGNodes)
		{
		    Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
		}

1. Après avoir défini les variables, copiez le script de l'éditeur de texte dans votre session Azure PowerShell pour l'exécuter. Si l'invite affiche >>, appuyez sur Entrée pour vous assurer que le script s'exécute.

## Vérifiez que KB2854082 est installé le cas échéant

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## Ouvrez les ports de pare-feu dans des nœuds de groupe de disponibilité

[AZURE.INCLUDE [pare-feu](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## Créer l'écouteur de groupe de disponibilité

[AZURE.INCLUDE [pare-feu](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. Pour l'équilibrage de charge externe, vous devrez obtenir l'adresse IP virtuelle publique du service cloud qui contient vos réplicas. Connectez-vous au portail Azure Classic. Accédez au service cloud qui contient les machines virtuelles de votre groupe de disponibilité. Ouvrez le **Tableau de bord**.

3. Notez l'adresse affichée sous **Adresse IP virtuelle (VIP) publique**. Si votre solution couvre des réseaux virtuels, répétez cette étape pour chaque service cloud contenant une machine virtuelle qui héberge un réplica.

4. Copiez le script PowerShell ci-dessous dans un éditeur de texte sur l'une des machines virtuelles et définissez les variables sur les valeurs notées précédemment.

		# Define variables
		$ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
		$IPResourceName = "<IPResourceName>" # the IP Address resource name
		$CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service

		Import-Module FailoverClusters

		# If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.

		# Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
		# cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255


1. Une fois les variables définies, ouvrez une fenêtre Windows PowerShell avec élévation de privilèges, puis copiez le script de l'éditeur de texte et collez-le dans votre session Azure PowerShell pour l'exécuter. Si l'invite affiche >>, appuyez sur Entrée pour vous assurer que le script s'exécute.

1. Répétez cette procédure sur chaque machine virtuelle. Ce script configure la ressource Adresse IP avec l'adresse IP du service cloud et définit d'autres paramètres, tels que le port de la sonde. Lorsque la ressource d'adresse IP est mise en ligne, elle peut ensuite répondre à l'interrogation sur le port de la sonde du point de terminaison à équilibrage de charge créé précédemment dans ce didacticiel.

## Mettre l'écouteur en ligne

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## Éléments de suivi

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## Tester l'écouteur du groupe de disponibilité (au sein du même réseau virtuel)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## Tester l'écouteur du groupe de disponibilité (sur Internet)

Pour accéder à l'écouteur depuis l'extérieur du réseau virtuel, vous devez utiliser l'équilibrage de charge externe/public (décrit dans cette rubrique) au lieu de l'équilibrage de charge interne, qui est accessible uniquement dans le même réseau virtuel. Dans la chaîne de connexion, vous spécifiez le nom du service cloud. Par exemple, si vous avez un service cloud nommé *mycloudservice*, l’instruction sqlcmd se présente comme suit :

	sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Contrairement à l'exemple précédent, l'authentification SQL est nécessaire, car l'appelant ne peut pas utiliser l'authentification Windows sur internet. Pour plus d’informations, consultez le billet de blog [AlwaysOn Availability Group in Azure VM: Client Connectivity Scenarios](http://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). Lorsque vous utilisez l'authentification SQL, veillez à créer la même connexion sur les deux réplicas. Pour plus d’informations sur la résolution des problèmes de connexions avec des groupes de disponibilité, consultez la rubrique [Comment mapper des connexions ou utiliser des utilisateurs de base de données SQL à relation contenant-contenu pour se connecter à d’autres réplicas et mapper aux bases de données de disponibilité](http://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Si les réplicas AlwaysOn se situent dans des sous-réseaux différents, les clients doivent spécifier **MultisubnetFailover=True** dans la chaîne de connexion. Cela entraîne des tentatives parallèles de connexion aux réplicas dans les différents sous-réseaux. Notez que ce scénario inclut un déploiement de groupe de disponibilité AlwaysOn sur plusieurs régions.

## Étapes suivantes

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]

<!---HONumber=AcomDC_0413_2016-->