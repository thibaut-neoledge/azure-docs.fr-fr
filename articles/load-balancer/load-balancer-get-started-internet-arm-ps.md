<properties 
   pageTitle="Créer un équilibreur de charge accessible sur Internet dans Resource Manager à l’aide de PowerShell| Microsoft Azure"
   description="Découvrir comment créer un équilibreur de charge accessible sur Internet dans Resource Manager à l’aide de PowerShell"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/21/2015"
   ms.author="joaoma" />

# Prise en main de la création d’un équilibreur de charge accessible sur Internet dans Resource Manager à l’aide de PowerShell

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article traite du modèle de déploiement de Resource Manager. Si vous recherchez un modèle de déploiement classique Azure, accédez à la page [Prise en main de la création d’un équilibreur de charge accessible sur Internet à l’aide d’un déploiement classique](load-balancer-get-started-internet-classic-portal.md)

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

Les étapes ci-dessous expliquent comment créer un équilibreur de charge accessible sur Internet à l’aide d’Azure Resource Manager avec PowerShell. Avec Azure Resource Manager, les éléments pour créer un équilibrage de charge accessible sur Internet sont configurés individuellement, puis rassemblés pour créer une ressource.

Dans cette page, nous allons aborder la séquence de tâches individuelles qui doivent être exécutées pour créer un équilibrage de charge et expliquer en détail ce qui est effectué pour atteindre l’objectif : créer un équilibrage de charge.

## Ce qui est nécessaire pour créer un équilibrage de charge accessible sur Internet

Vous devez créer et configurer les objets suivants pour déployer un équilibreur de charge :

- Configuration d’adresses IP frontales : contient les adresses IP publiques pour le trafic réseau entrant. 

- Pool d’adresses principales : contient des interfaces réseau (NIC) pour recevoir le trafic de l’équilibreur de charge.

- Règles d’équilibrage de la charge : contient des règles de mappage d’un port public situé sur l’équilibreur de charge sur les ports situés sur les cartes réseau du pool d’adresses principales.

- Règles NAT entrantes : contient des règles de mappage d’un port public situé sur l’équilibreur de charge sur un port d’une carte réseau individuelle située dans le pool d’adresses principales.

- Sondes : contient les sondes d’intégrité utilisées pour vérifier la disponibilité des machines virtuelles liées aux cartes réseau du pool d’adresses principales.

Pour plus d’informations sur les composants de l’équilibreur de charge avec Azure Resource Manager, consultez la page [Support Azure Resource Manager pour l’équilibrage de charge](load-balancer-arm.md).


## Configurer PowerShell pour utiliser Resource Manager
Assurez-vous de disposer de la dernière version de production du module Azure pour PowerShell et d’une installation correcte de PowerShell pour accéder à votre abonnement Azure.

### Étape 1 :

1. Si vous n’avez jamais utilisé Azure PowerShell, consultez la page [Installation et configuration d’Azure PowerShell](powershell-install-configure.md) et suivez les instructions jusqu’à la fin pour vous connecter à Azure et sélectionner votre abonnement.
2. À partir d’une invite de commandes Azure PowerShell, exécutez l’applet de commande **Switch-AzureMode** pour passer en mode Resource Manager, comme illustré ci-dessous.

		Switch-AzureMode AzureResourceManager
	
	Sortie attendue :

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.


>[AZURE.WARNING]L’applet de commande Switch-AzureMode sera bientôt obsolète. Lorsque ce sera le cas, toutes les applets de commande Resource Manager seront renommées.



### Étape 2

Connectez-vous à votre compte Azure.


    PS C:\> Add-AzureAccount

Vous devez indiquer vos informations d’identification.


### Étape 3

Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.

    PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

Pour afficher la liste des abonnements disponibles, utilisez l'applet de commande « Get-AzureSubscription ».

## Créer un groupe de ressources

Créez un groupe de ressources nommé *NRP-RG* à l’emplacement Azure *Ouest des États-Unis*.

    PS C:\> New-AzureResourceGroup -Name NRP-RG -location "West US"

## Créer un réseau virtuel et une adresse IP publique pour le pool d’adresses IP frontales

### Étape 1 :

Créez un sous-réseau et un réseau virtuel.

	$backendSubnet = New-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
	New-AzurevirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

### Étape 2 :

Créez une adresse IP publique (PIP) nommée *PublicIP* pour qu’elle soit utilisée par un pool d’adresses IP frontales avec le nom DNS *loadbalancernrp.westus.cloudapp.azure.com*. La commande ci-dessous utilise le type d’allocation statique.

	$publicIP = New-AzurePublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location "West US" –AllocationMethod Static -DomainNameLabel loadbalancernrp 

>[AZURE.IMPORTANT]L’équilibreur de charge utilise l’étiquette du domaine de l’adresse IP publique en tant que nom de domaine complet (FQDN). Cet usage diffère d’un modèle de déploiement classique qui utilise le service cloud en tant que nom de domaine complet de l’équilibreur de charge. Dans cet exemple, le nom de domaine complet est *loadbalancernrp.westus.cloudapp.azure.com*.

## Créer un pool d’adresses IP frontales et un pool d’adresses principales

### Étape 1 : 

Créez un pool d’adresses IP frontales nommé *LB-Frontend* qui utilise l’adresse IP publique *PublicIp*.

	$frontendIP = New-AzureLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP 

### Étape 2 

Créez un pool d’adresses principales nommé *LB-backend*.

	$beaddresspool= New-AzureLoadBalancerBackendAddressPoolConfig -Name "LB-backend"

## Créer des règles d’équilibreur de charge, des règles NAT, une sonde et un équilibreur de charge

L’exemple ci-dessous crée les éléments suivants :

- une règle NAT pour transférer l’ensemble du trafic du port 3441 vers le port 3389<sup>1</sup> ;
- une règle NAT transférer l’ensemble du trafic sur le port 3442 vers le port 3389 ;
- une règle d’équilibreur de charge pour équilibrer tout le trafic entrant sur le port 80 vers le port 80 des adresses du pool principal ;
- une règle de sonde qui vérifie l’état d’intégrité dans une page nommée *HealthProbe.aspx*.
- un équilibreur de charge qui utilise tous les objets ci-dessus.


<sup>1</sup> Les règles NAT sont associées à une instance de machine virtuelle spécifique derrière l'équilibreur de charge. Le trafic réseau entrant vers le port 3341 sera envoyé à une machine virtuelle spécifique sur le port 3389 associé à une règle NAT dans l'exemple ci-dessous. Vous devez choisir un protocole pour la règle NAT : UDP ou TCP. Il est impossible d'attribuer les deux protocoles au même port.

### Étape 1

Créez les règles NAT.

	$inboundNATRule1= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

### Étape 2 :

Créez une règle d’équilibreur de charge.

	$lbrule = New-AzureLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

### Étape 3

Créer une sonde d’intégrité.

	$healthProbe = New-AzureLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

### Étape 4

Créez l’équilibreur de charge à l’aide des objets créés ci-dessus.

	$NRPLB = New-AzureLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe 

## Créer des cartes réseau

Vous devez créer des cartes réseau (ou modifier des cartes existantes) et les associer à des règles NAT, des règles d’équilibreur de charge et des sondes.

### Étape 1 : 

Obtenez le réseau virtuel et le sous-réseau sur lesquels les cartes réseau doivent être créées.

	$vnet = Get-AzureVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
	$backendSubnet = Get-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 

### Étape 2 :

Créez une carte réseau nommée *lb-nic1-be*, puis associez-la à la première règle NAT et au premier (et unique) pool d’adresses principales.
	
	$backendnic1= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### Étape 3

Créez une carte réseau nommée *lb-nic2-be*, puis associez-la à la deuxième règle NAT et au premier (et unique) pool d’adresses principales.

	$backendnic2= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

### Étape 4

Vérifiez les cartes réseau.


	PS C:\> $backendnic1

Sortie attendue :

	Name                 : lb-nic1-be
	ResourceGroupName    : NRP-RG
	Location             : westus
	Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
	Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
	ProvisioningState    : Succeeded
	Tags                 :
	VirtualMachine       : null
	IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/"d448256a-e1df-413a-9103-a137e07276d1"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
	DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
	AppliedDnsSettings   :
	NetworkSecurityGroup : null
	Primary              : False



### Étape 5

Utilisez l’applet de commande `Add-AzureVMNetworkInterface` pour affecter les cartes réseau aux différentes machines virtuelles.

Des instructions sur la manière de créer une machine virtuelle et d’affecter une carte réseau sont disponibles à la page [Création et préconfiguration d’une machine virtuelle Windows avec Resource Manager et Azure PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md#Example), et utilisent l’option 5 de l’exemple.

## Mettre à jour un équilibreur de charge existant


### Étape 1 :

À l’aide de l’équilibreur de charge de l’exemple ci-dessus, attribuez un objet d’équilibreur de charge à la variable $slb à l’aide de Get-AzureLoadBalancer

	$slb=get-azureLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### Étape 2 :

Dans l’exemple suivant, vous allez ajouter une nouvelle règle NAT entrante en utilisant le port 81 dans le serveur frontal et le port 8181 pour le pool principal, à un équilibreur de charge existant.

	$slb | Add-AzureLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### Étape 3

Enregistrez la nouvelle configuration à l’aide de Set-AzureLoadBalancer

	$slb | Set-AzureLoadBalancer

## Supprimer un équilibreur de charge

Utilisez la commande Remove-AzureLoadBalancer pour supprimer un équilibreur de charge créé précédemment appelé « NRP-LB » dans un groupe de ressources appelé « NRP-RG ».

	Remove-AzureLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE]Vous pouvez utiliser le commutateur facultatif -Force pour éviter l’invite relative à la suppression.

## Étapes suivantes

[Prise en main de la configuration d’un équilibrage de charge interne](load-balancer-internal-getstarted.md)

[Configuration d'un mode de distribution d'équilibrage de charge](load-balancer-distribution-mode.md)

[Configuration des paramètres de délai d’expiration TCP inactif pour votre équilibreur de charge](load-balancer-tcp-idle-timeout.md)

<!---HONumber=Nov15_HO3-->