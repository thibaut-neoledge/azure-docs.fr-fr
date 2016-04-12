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
   ms.date="01/21/2016"
   ms.author="joaoma" />

# Création d’un équilibreur de charge accessible sur Internet dans Resource Manager à l’aide de PowerShell

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article traite du modèle de déploiement de Resource Manager. Vous pouvez également [découvrir comment créer un équilibreur de charge accessible sur Internet à l'aide du modèle de déploiement classique](load-balancer-get-started-internet-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

Les étapes ci-dessous expliquent comment créer un équilibreur de charge accessible sur Internet à l'aide d'Azure Resource Manager avec PowerShell. Avec Azure Resource Manager, les éléments pour créer un équilibrage de charge accessible sur Internet sont configurés individuellement, puis rassemblés pour créer une ressource.

Nous allons aborder la séquence de tâches individuelles qui doivent être exécutées pour créer un équilibreur de charge et expliquer en détail ce qui est effectué pour atteindre cet objectif.

## Ce qui est nécessaire pour créer un équilibrage de charge accessible sur Internet

Vous devez créer et configurer les objets suivants pour déployer un équilibreur de charge :

- Configuration d’adresses IP frontales : contient les adresses IP publiques pour le trafic réseau entrant. 

- Pool d’adresses principales : contient des interfaces réseau (NIC) pour que les machines virtuelles puissent recevoir le trafic réseau de l’équilibreur de charge.

- Règles d’équilibrage de la charge : contient des règles de mappage d’un port public situé sur l’équilibreur de charge pour le pool d’adresses principales.

- Règles NAT entrantes : contient des règles de mappage d’un port public situé sur l’équilibreur de charge vers le port d’une machine virtuelle spécifique située dans le pool d’adresses principales.

- Sondes : contient les sondes d’intégrité utilisées pour vérifier la disponibilité des instances de machines virtuelles du pool d’adresses principales.

Pour obtenir plus d’informations sur les composants de l’équilibreur de charge avec Azure Resource Manager, consultez la page [Support Azure Resource Manager pour l’équilibreur de charge](load-balancer-arm.md).


## Configurer PowerShell pour utiliser Resource Manager

Assurez-vous que vous disposez de la dernière version de production du module Azure Resource Manager (ARM) pour PowerShell.

### Étape 1

		PS C:\> Login-AzureRmAccount

Vous devez indiquer vos informations d’identification.<BR>

### Étape 2

Vérifiez les abonnements associés au compte

		PS C:\> Get-AzureRmSubscription 

### Étape 3 

Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.<BR>

		PS C:\> Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'

### Étape 4

Créez un groupe de ressources (ignorez cette étape si vous utilisez un groupe de ressources existant)


    	PS C:\> New-AzureRmResourceGroup -Name NRP-RG -location "West US"


## Créez un réseau virtuel et une adresse IP publique pour le pool d’adresses IP frontales

### Étape 1

Créez un sous-réseau et un réseau virtuel.

    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

### Étape 2

Créez une ressource d’adresse IP publique (PIP) Azure nommée *PublicIP* utilisable par un pool d'adresses IP frontales avec le nom DNS *loadbalancernrp.westus.cloudapp.azure.com*. La commande ci-dessous utilise le type d’allocation statique.

	$publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location 'West US' –AllocationMethod Static -DomainNameLabel loadbalancernrp 

>[AZURE.IMPORTANT] L'équilibreur de charge utilise l'étiquette du domaine de l'adresse IP publique en tant que préfixe du nom de domaine complet (FQDN). Cet usage diffère d’un modèle de déploiement classique qui utilise le service cloud en tant que nom de domaine complet de l’équilibreur de charge. Dans cet exemple, le nom de domaine complet est *loadbalancernrp.westus.cloudapp.azure.com*.

## Créer un pool d’adresses IP frontales et un pool d’adresses principales

### Étape 1 

Créez un pool d'adresses IP frontales nommé *LB-Frontend* qui utilise la PIP *PublicIp*.

	$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP 

### Étape 2 

Créez un pool d’adresses principales nommé *LB-backend*.

	$beaddresspool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name LB-backend

## Créer des règles d’équilibreur de charge, des règles NAT, une sonde et un équilibreur de charge

L’exemple ci-dessous crée les éléments suivants :

- une règle NAT pour transférer l'ensemble du trafic sur le port 3441 vers le port 3389 ;
- une règle NAT transférer l’ensemble du trafic sur le port 3442 vers le port 3389 ;
- une règle d’équilibreur de charge pour équilibrer tout le trafic entrant sur le port 80 vers le port 80 des adresses du pool principal ;
- une règle de sonde qui vérifie l'intégrité dans une page nommée *HealthProbe.aspx*.
- un équilibreur de charge qui utilise tous les objets ci-dessus.


### Étape 1 :

Créez les règles NAT.

	$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP1 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP2 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

### Étape 2 :

Créez une règle d’équilibreur de charge.

	$lbrule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

### Étape 3

Créer une sonde d’intégrité. Il existe deux façons de configurer une sonde :
 
Sonde HTTP
	
	$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
ou

Sonde TCP
	
	$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2

### Étape 4

Créez l’équilibreur de charge à l’aide des objets créés ci-dessus.

	$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name NRP-LB -Location 'West US' -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe

## Créer des cartes réseau

Vous devez créer des interfaces réseau (ou modifier des cartes existantes) et les associer à des règles NAT, des règles d’équilibreur de charge et des sondes.

### Étape 1 

Obtenez le réseau virtuel et le sous-réseau du réseau virtuel où les cartes réseau doivent être créées.

	$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
	$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 

### Étape 2

Créez une carte réseau nommée *lb-nic1-be*, puis associez-la à la première règle NAT et au premier (et unique) pool d'adresses principales.
	
	$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### Étape 3

Créez une carte réseau nommée *lb-nic2-be*, puis associez-la à la deuxième règle NAT et au premier (et unique) pool d’adresses principales.

	$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic2-be -Location 'West US' -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

### Étape 4

Vérifiez les cartes réseau.


	PS C:\> $backendnic1

Sortie attendue :

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



### Étape 5

Utilisez l'applet de commande `Add-AzureRmVMNetworkInterface` pour affecter les cartes réseau aux différentes machines virtuelles.

Des instructions concernant la création d'une machine virtuelle et l'affectation d'une carte réseau sont disponibles à la page [Création et préconfiguration d'une machine virtuelle Windows avec Resource Manager et Azure PowerShell](../virtual-machines/virtual-machines-windows-create-powershell.md#Example), avec l'option 5 de l'exemple.


Ou, si vous avez déjà créé une machine virtuelle, vous pouvez ajouter l'interface réseau en procédant comme suit :

#### Étape 1 

Charger la ressource d'équilibrage de charge dans une variable (si vous ne l'avez pas encore fait). La variable utilisée est appelée $lb et utilise les mêmes noms provenant de la ressource d'équilibrage de charge créée ci-dessus.

	$lb= get-azurermloadbalancer –name NRP-LB -resourcegroupname NRP-RG

#### Étape 2 

Charger la configuration du serveur principal dans une variable.

	PS C:\> $backend=Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

#### Étape 3 

Charger l'interface réseau déjà créée dans une variable. Le nom de la variable utilisé est $nic. Le nom de l'interface réseau utilisé est celui de l'exemple ci-dessus.

	$nic =get-azurermnetworkinterface –name lb-nic1-be -resourcegroupname NRP-RG

#### Étape 4

Modifier la configuration du serveur principal sur l'interface réseau.

	PS C:\> $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

#### Étape 5 

Enregistrer l'objet d'interface réseau.

	PS C:\> Set-AzureRmNetworkInterface -NetworkInterface $nic

Une fois l’interface réseau ajoutée au pool principal d'équilibreurs de charge, elle commence à recevoir le trafic réseau selon la règles d'équilibrage de charge pour cette ressource d’équilibreur de charge.

## Mettre à jour un équilibreur de charge existant


### Étape 1 :

À l’aide de l’équilibreur de charge de l’exemple ci-dessus, attribuez un objet d’équilibreur de charge à la variable $slb à l’aide de Get-AzureLoadBalancer

	$slb = get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### Étape 2 :

Dans l’exemple suivant, vous allez ajouter une nouvelle règle NAT entrante en utilisant le port 81 dans le serveur frontal et le port 8181 pour le pool principal, à un équilibreur de charge existant.

	$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol TCP


### Étape 3

Enregistrez la nouvelle configuration à l’aide de Set-AzureLoadBalancer

	$slb | Set-AzureRmLoadBalancer

## Supprimer un équilibreur de charge

Utilisez la commande `Remove-AzureLoadBalancer` pour supprimer un équilibreur de charge créé précédemment appelé « NRP-LB » dans un groupe de ressources appelé « NRP-RG ».

	Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] Vous pouvez utiliser le commutateur facultatif -Force pour éviter l’invite relative à la suppression.

## Étapes suivantes

[Prise en main de la configuration d’un équilibrage de charge interne](load-balancer-get-started-ilb-arm-ps.md)

[Configuration d'un mode de distribution d'équilibrage de charge](load-balancer-distribution-mode.md)

[Configuration des paramètres de délai d’expiration TCP inactif pour votre équilibreur de charge](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0323_2016-->