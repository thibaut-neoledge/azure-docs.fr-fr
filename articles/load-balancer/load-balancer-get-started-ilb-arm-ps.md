<properties 
   pageTitle="Créer un équilibreur de charge interne dans Resource Manager à l’aide de PowerShell | Microsoft Azure"
   description="Découvrez comment créer un équilibreur de charge interne à l’aide de PowerShell dans Resource Manager"
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
   ms.date="01/21/2015"
   ms.author="joaoma" />

# Prise en main de la création d’un équilibreur de charge interne à l’aide de PowerShell

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]<BR>[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


Les étapes ci-dessous expliquent comment créer un équilibrage de charge interne à l’aide d’Azure Resource Manager avec PowerShell. Avec Azure Resource Manager, les éléments pour créer un équilibrage de charge interne sont configurés individuellement, puis rassemblés pour créer une ressource.

Dans cet article, nous allons aborder la séquence de tâches individuelles qui doivent être exécutées pour créer un équilibreur de charge interne et expliquer en détail ce qui est effectué pour atteindre cet objectif.


## Ce qui est nécessaire pour créer un équilibrage de charge interne


Les éléments suivants doivent être configurés avant la création d’un équilibrage de charge interne :

- Configuration d’adresses IP frontales : configure l'adresse IP privée pour le trafic réseau entrant 

- Pool d'adresses principales : configure les interfaces réseau qui recevront le trafic d'équilibrage de charge provenant du pool d'adresses IP frontales

- Règles d’équilibrage de charge : configure le port local et source pour l’équilibrage de charge.

- Sondes : configure la sonde d'état d'intégrité pour les instances d’un ordinateur virtuel.

- Règles NAT entrantes : configure les règles de port pour accéder directement à l'une des instances d’un ordinateur virtuel.

Pour plus d’informations sur le chargement des composants de l’équilibreur de charge avec Azure Resource Manager, consultez la page [Support Azure Resource Manager pour l’équilibreur de charge](load-balancer-arm.md).

Les étapes suivantes montrent comment configurer un équilibreur de charge entre 2 machines virtuelles.


## Étape par étape à l’aide de PowerShell


### Configurer PowerShell pour utiliser Resource Manager

Assurez-vous de disposer de la dernière version de production du module Azure pour PowerShell et d’une installation correcte de PowerShell pour accéder à votre abonnement Azure.

### Étape 1 :

		PS C:\> Login-AzureRmAccount



### Étape 2 :

Vérifiez les abonnements associés au compte

		PS C:\> get-AzureRmSubscription 

Vous devez indiquer vos informations d’identification.<BR>

### Étape 3 

Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.<BR>


		PS C:\> Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### Création d’un groupe de ressources pour l’équilibrage de charge

### Étape 4

Créez un groupe de ressources (ignorez cette étape si vous utilisez un groupe de ressources existant)

    	PS C:\> New-AzureRmResourceGroup -Name NRP-RG -location "West US"

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Ce dernier est utilisé comme emplacement par défaut des ressources de ce groupe. Assurez-vous que toutes les commandes pour créer un équilibrage de charge utiliseront le même groupe de ressources.

Dans l'exemple ci-dessus, nous avons créé un groupe de ressources appelé « NRP-RG » et l’emplacement « West US ».

## Créer un réseau virtuel et une adresse IP privée pour le pool d’adresses IP frontal


### Étape 1 :

Crée un sous-réseau pour le réseau virtuel et définit une affectation à la variable $backendSubnet

	$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24

Créez un réseau virtuel :

	$vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

Crée le réseau virtuel et ajoute le sous-réseau lb-subnet-be au réseau virtuel NRPVNet et définit une affectation à la variable $vnet.



## Création du pool d’adresses IP frontales et du pool d’adresses principales

Configuration d’un pool d’adresses IP frontales pour le trafic entrant du réseau d’équilibrage de charge et d’un pool d’adresses principales pour recevoir le trafic à charge équilibrée.

### Étape 1 

Créez un pool d’adresses IP frontales à l’aide de l’adresse IP privée 10.0.2.5 pour le sous-réseau 10.0.2.0/24 qui sera le point de terminaison du trafic réseau entrant.

	$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id

### Étape 2 

Configurez un pool d’adresses principales utilisé pour recevoir le trafic entrant à partir du pool d’adresses IP frontales :

	$beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"


## Création des règles d’équilibrage de charge, des règles NAT, de la sonde et de l’équilibrage de charge

Après avoir créé le pool d’adresses d’IP frontales et le pool d’adresses principales, vous devrez créer les règles qui appartiendront à la ressource d’équilibrage de charge :

### Étape 1

	$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

	$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

 	$lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80


L’exemple ci-dessus crée les éléments suivants :

- une règle NAT selon laquelle tout le trafic entrant au port 3441 passera au port 3389 ;
- une deuxième règle NAT selon laquelle tout le trafic entrant au port 3442 passera au port 3389 ;
- une règle d’équilibrage de charge qui équilibrera la charge pour tout le trafic entrant sur le port public 80 vers le port local 80 dans le pool d’adresses principales ;
- une règle d’analyse qui vérifiera l’état d’intégrité pour le chemin d’accès « HealthProbe.aspx ».



### Étape 2

Créez l’équilibrage de charge en ajoutant tous les objets (règles NAT, règles d’équilibrage de charge, configurations de sonde) :

	$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe 


## Création d’interfaces réseau

Après avoir créé l’équilibrage de charge interne, vous devez définir les interfaces réseau qui recevront le trafic entrant du réseau à charge équilibrée, les règles NAT et la sonde. Dans ce cas, l’interface réseau est configurée individuellement et peut affectée à un ordinateur virtuel par la suite.


### Étape 1 


Obtenez le réseau virtuel des ressources et le sous-réseau pour créer des interfaces réseau :

	$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

	$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 


Dans cette étape, nous créons une interface réseau qui appartiendra au pool principal de l’équilibrage de charge et associons la première règle NAT pour RDP à cette interface réseau :
	
	$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### Étape 2

Créez une deuxième interface réseau appelée LB-Nic2-BE :

Dans cette étape, nous créons une deuxième interface réseau, définissons une affectation au même pool principal de l’équilibrage de charge et associons la deuxième règle NAT créée pour RDP :

 	$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]


On obtient alors le résultat suivant :


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



### Étape 3 

Utilisez la commande Add-AzureRmVMNetworkInterface pour affecter la carte réseau à une machine virtuelle.

Pour la procédure détaillée à suivre afin de créer une machine virtuelle et définir une affectation à une carte réseau, consultez la documentation [Création et configuration d’une machine virtuelle Windows avec Resource Manager et Azure PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md#Example), option 4 ou 5.

Ou, si vous avez déjà créé une machine virtuelle, vous pouvez ajouter l'interface réseau en procédant comme suit :

#### Étape 1 

Charger la ressource d'équilibrage de charge dans une variable (si vous ne l'avez pas encore fait). La variable utilisée est appelée $lb et utilise les mêmes noms provenant de la ressource d'équilibrage de charge créée ci-dessus.

	$lb= Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG

#### Étape 2 

Charger la configuration du serveur principal dans une variable.

	$backend= Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

#### Étape 3 

Charger l'interface réseau déjà créée dans une variable. Le nom de la variable utilisé est $nic. Le nom de l'interface réseau utilisé est celui de l'exemple ci-dessus.

	$nic=Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG

#### Étape 4

Modifier la configuration du serveur principal sur l'interface réseau.

	PS C:\> $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

#### Étape 5 

Enregistrer l'objet d'interface réseau.

	PS C:\> Set-AzureRmNetworkInterface -NetworkInterface $nic

Une fois l’interface réseau ajoutée au pool principal d'équilibreurs de charge, elle commence à recevoir le trafic réseau selon la règles d'équilibrage de charge pour cette ressource d’équilibreur de charge.

## Mettre à jour un équilibreur de charge existant


### Étape 1 :

À l’aide de l’équilibreur de charge de l’exemple ci-dessus, attribuez un objet d’équilibreur de charge à la variable $slb à l’aide de Get-AzureRmLoadBalancer.

	$slb=get-azureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### Étape 2

Dans l’exemple suivant, vous allez ajouter une nouvelle règle NAT entrante en utilisant le port 81 dans le serveur frontal et le port 8181 pour le pool principal, à un équilibreur de charge existant.

	$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### Étape 3

Enregistrez la nouvelle configuration à l’aide de Set-AzureLoadBalancer

	$slb | Set-AzureRmLoadBalancer

## Supprimer un équilibreur de charge

Utilisez la commande Remove-AzureRmLoadBalancer pour supprimer un équilibreur de charge créé précédemment appelé « NRP-LB » dans un groupe de ressources appelé « NRP-RG ».

	Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] Vous pouvez utiliser le commutateur facultatif -Force pour éviter l’invite relative à la suppression.



## Étapes suivantes

[Configuration d’un mode de distribution d’équilibrage de charge](load-balancer-distribution-mode.md)

[Configuration des paramètres de délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=AcomDC_0128_2016-->