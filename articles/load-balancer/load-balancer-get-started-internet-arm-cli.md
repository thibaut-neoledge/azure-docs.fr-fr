<properties 
   pageTitle="Créer un équilibreur de charge accessible sur Internet dans le gestionnaire de ressources en utilisant l’interface de ligne de commande | Microsoft Azure"
   description="Découvrez comment créer un équilibreur de charge accessible sur Internet dans Resource Manager à l’aide de l’interface de ligne de commande Azure"
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
   ms.date="02/24/2016"
   ms.author="joaoma" />

# Prise en main de la création d’un équilibreur de charge accessible sur Internet à l’aide de l’interface de ligne de commande

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article traite du modèle de déploiement de Resource Manager. Vous pouvez également [découvrir comment créer un équilibreur de charge accessible sur Internet à l'aide du déploiement classique](load-balancer-get-started-internet-classic-portal.md).


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

Nous allons aborder la séquence de tâches individuelles qui doivent être exécutées pour créer un équilibreur de charge et expliquer en détail ce qui est effectué pour atteindre cet objectif.


## Ce qui est nécessaire pour créer un équilibrage de charge accessible sur Internet

Vous devez créer et configurer les objets suivants pour déployer un équilibreur de charge.

- Configuration d’adresses IP frontales : contient les adresses IP publiques pour le trafic réseau entrant. 

- Pool d’adresses principales : contient des interfaces réseau (NIC) pour que les machines virtuelles puissent recevoir le trafic réseau de l’équilibreur de charge.

- Règles d’équilibrage de la charge : contient des règles de mappage d’un port public situé sur l’équilibreur de charge pour le pool d’adresses principales.

- Règles NAT entrantes : contient des règles de mappage d’un port public situé sur l’équilibreur de charge vers le port d’une machine virtuelle spécifique située dans le pool d’adresses principales.

- Sondes : contient les sondes d’intégrité utilisées pour vérifier la disponibilité des instances de machines virtuelles du pool d’adresses principales.

Pour obtenir plus d’informations sur les composants de l’équilibreur de charge avec Azure Resource Manager, consultez la page [Support d’Azure Resource Manager pour l’équilibrage de charge](load-balancer-arm.md).

## Configurer l’interface de ligne de commande pour utiliser le gestionnaire de ressources

1. Si vous n’avez jamais utilisé l’interface de ligne de commande Azure, consultez [Installation et configuration de l’interface de ligne de commande Azure](../../articles/xplat-cli-install.md) et suivez les instructions jusqu’à l’étape où vous sélectionnez votre compte et votre abonnement Azure.

2. Exécutez la commande **azure config mode** pour passer en mode Resource Manager, comme illustré ci-dessous.

		azure config mode arm

	Sortie attendue :

		info:    New mode is arm

## Créez un réseau virtuel et une adresse IP publique pour le pool d’adresses IP frontales

### Étape 1 :

Créez un réseau virtuel (VNet) nommé *NRPVnet* dans l’emplacement Est des États-Unis à l’aide d’un groupe de ressources nommé *NRPRG*.

	azure network vnet create NRPRG NRPVnet eastUS -a 10.0.0.0/16

Créez un sous-réseau nommé *NRPVnetSubnet* avec un bloc CIDR 10.0.0.0/24 dans *NRPVnet*.

	azure network vnet subnet create NRPRG NRPVnet NRPVnetSubnet -a 10.0.0.0/24

### Étape 2

Créez une adresse IP publique nommée *NRPPublicIP* pour qu’elle soit utilisée par un pool d’adresses IP frontales avec le nom DNS *loadbalancernrp.westus.cloudapp.azure.com*. La commande ci-dessous utilise le type d’allocation statique et un délai d’inactivité de 4 minutes.

	azure network public-ip create -g NRPRG -n NRPPublicIP -l eastus -d loadbalancernrp -a static -i 4


>[AZURE.IMPORTANT] L’équilibreur de charge utilise l’étiquette du domaine de l’adresse IP publique en tant que nom de domaine complet (FQDN). Cet usage diffère d’un déploiement classique qui utilise le service cloud en tant que nom de domaine complet de l’équilibreur de charge. Dans cet exemple, le nom de domaine complet est *loadbalancernrp.eastus.cloudapp.azure.com*.

## Créer un équilibrage de charge

Dans l’exemple suivant, la commande suivante crée un équilibreur de charge nommé *NRPlb* dans le groupe de ressources *NRPRG*, dans la zone Azure *Est des États-Unis*.

	azure network lb create NRPRG NRPlb eastus

## Créez un pool d’adresses IP frontales et un pool d’adresses principales

L’exemple ci-dessous crée le pool d’IP frontal qui reçoit le trafic réseau entrant pour l’équilibreur de charge et le pool d’IP principal où le pool frontal envoie le trafic de réseau équilibré.

### Étape 1 : 

Créez un pool d’IP frontal associant l’IP publique créée lors de l’étape précédente et l’équilibreur de charge.

	azure network lb frontend-ip create nrpRG NRPlb NRPfrontendpool -i nrppublicip

### Étape 2 : 

Configurez un pool d’adresses principal utilisé pour recevoir le trafic entrant à partir du pool d’adresses IP frontal :

	azure network lb address-pool create NRPRG NRPlb NRPbackendpool

## Créer des règles LB, des règles NAT et de sonde

L’exemple ci-dessous crée les éléments suivants :

- une règle NAT pour transférer l’ensemble du trafic sur le port 21 vers le port 22<sup>1</sup> ;
- une règle NAT transférer l’ensemble du trafic sur le port 23 vers le port 22 ;
- une règle d’équilibreur de charge pour équilibrer tout le trafic entrant sur le port 80 vers le port 80 des adresses du pool principal ;
- une règle de sonde qui vérifie l’état d’intégrité dans une page nommée *HealthProbe.aspx*.

<sup>1</sup> Les règles NAT sont associées à une instance de machine virtuelle spécifique derrière l’équilibreur de charge. Le trafic réseau entrant vers le port 21 est envoyé à une machine virtuelle spécifique sur le port 22 associé à une règle NAT dans l’exemple ci-dessous. Vous devez choisir un protocole pour la règle NAT : UDP ou TCP. Il est impossible d'attribuer les deux protocoles au même port.

### Étape 1

Créer les règles NAT.

	azure network lb inbound-nat-rule create -g nrprg -l nrplb -n ssh1 -p tcp -f 21 -b 22
	azure network lb inbound-nat-rule create -g nrprg -l nrplb -n ssh2 -p tcp -f 23 -b 22

Paramètres :

- **-g** - nom de groupe de ressources
- **-l** - nom d’un équilibreur de charge 
- **- n** - nom de la ressource selon qu’il s’agit d’une règle NAT, sonde ou règle lb.
- **-p** - protocole (TCP ou UDP)  
- **-f** - port frontal à utiliser (la commande probe utilise -f pour définir le chemin d’accès de sondage)
- **-b** - port de serveur principal à utiliser

### Étape 2 :

Créez une règle d’équilibreur de charge.

	azure network lb rule create nrprg nrplb lbrule -p tcp -f 80 -b 80 -t NRPfrontendpool -o NRPbackendpool
### Étape 3

Créer une sonde d’intégrité.

	azure network lb probe create -g nrprg -l nrplb -n healthprobe -p "http" -o 80 -f healthprobe.aspx -i 15 -c 4

	
	

**-g** - groupe de ressources **-l** - nom du jeu d’équilibrage de charge **-n** - nom de la sonde d’intégrité **-p** - protocole utilisé par la sonde d’intégrité **-i** - intervalle de sondage, en secondes **-c** - nombre de contrôles

### Étape 4

Enregistrer vos paramètres.

	azure network lb show nrprg nrplb

Sortie attendue :

	info:    Executing command network lb show
	+ Looking up the load balancer "nrplb"
	+ Looking up the public ip "NRPPublicIP"	
	data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb
	data:    Name                            : nrplb
	data:    Type                            : Microsoft.Network/loadBalancers
	data:    Location                        : eastus
	data:    Provisioning State              : Succeeded
	data:    Frontend IP configurations:
	data:      Name                          : NRPfrontendpool
	data:      Provisioning state            : Succeeded
	data:      Public IP address id          : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/publicIPAddresses/NRPPublicIP
	data:      Public IP allocation method   : Static
	data:      Public IP address             : 40.114.13.145
	data:
	data:    Backend address pools:
	data:      Name                          : NRPbackendpool
	data:      Provisioning state            : Succeeded
	data:
	data:    Load balancing rules:
	data:      Name                          : HTTP
	data:      Provisioning state            : Succeeded
	data:      Protocol                      : Tcp
	data:      Frontend port                 : 80
	data:      Backend port                  : 80
	data:      Enable floating IP            : false
	data:      Idle timeout in minutes       : 4
	data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
	data:      Backend address pool          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
	data:
	data:    Inbound NAT rules:
	data:      Name                          : ssh1
	data:      Provisioning state            : Succeeded
	data:      Protocol                      : Tcp
	data:      Frontend port                 : 21
	data:      Backend port                  : 22
	data:      Enable floating IP            : false
	data:      Idle timeout in minutes       : 4
	data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
	data:
	data:      Name                          : ssh2
	data:      Provisioning state            : Succeeded
	data:      Protocol                      : Tcp
	data:      Frontend port                 : 23
	data:      Backend port                  : 22
	data:      Enable floating IP            : false
	data:      Idle timeout in minutes       : 4
	data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
	data:
	data:    Probes:
	data:      Name                          : healthprobe
	data:      Provisioning state            : Succeeded
	data:      Protocol                      : Http
	data:      Port                          : 80
	data:      Interval in seconds           : 15
	data:      Number of probes              : 4
	data:
	info:    network lb show command OK

## Créer des cartes réseau

Vous devez créer des cartes réseau (ou modifier des cartes existantes) et les associer à des règles NAT, des règles d’équilibreur de charge et des sondes.

### Étape 1 : 

Créez une carte réseau nommée *lb-nic1-be*, puis associez-la à la règle NAT *rdp1* et au premier (et unique) pool d’adresses principales *NRPbackendpool*.
	
	azure network nic create -g nrprg -n lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus

Paramètres :

- **-g** - nom de groupe de ressource
- **-n** - nom de la ressource de carte réseau
- **--subnet-name** - nom du sous-réseau 
- **--subnet-vnet-name** - nom du réseau virtuel
- **-d** - ID de ressource de pool principal - commence par /subscription/ {subscriptionID/resourcegroups/<resourcegroup-name>/providers/Microsoft.Network/loadbalancers/<load-balancer-name>/backendaddresspools/<name-of-the-backend-pool> 
- **-e** - ID de la règle NAT qui sera associée à la ressource de la carte d’interface réseau - commence par /subscriptions/####################################/resourceGroups/<resourcegroup-name>/providers/Microsoft.Network/loadBalancers/<load-balancer-name>/inboundNatRules/<nat-rule-name>


Sortie attendue :

	info:    Executing command network nic create
	+ Looking up the network interface "lb-nic1-be"
	+ Looking up the subnet "nrpvnetsubnet"
	+ Creating network interface "lb-nic1-be"
	+ Looking up the network interface "lb-nic1-be"
	data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
	data:    Name                            : lb-nic1-be
	data:    Type                            : Microsoft.Network/networkInterfaces
	data:    Location                        : eastus
	data:    Provisioning state              : Succeeded
	data:    Enable IP forwarding            : false
	data:    IP configurations:
	data:      Name                          : NIC-config
	data:      Provisioning state            : Succeeded
	data:      Private IP address            : 10.0.0.4
	data:      Private IP Allocation Method  : Dynamic
	data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
	data:      Load balancer backend address pools
	data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
	data:      Load balancer inbound NAT rules:
	data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
	data:
	info:    network nic create command OK

### Étape 2 :

Créez une carte réseau nommée *lb-nic2-be*, puis associez-la à la règle NAT *rdp2* et au premier (et unique) pool d’adresses principales *NRPbackendpool*.

 	azure network nic create -g nrprg -n lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus

### Étape 3 

Créez une machine virtuelle nommée *web1*, puis associez-la à la carte réseau nommée *lb-nic1-be*. Un compte de stockage appelé *web1nrp* a été créé avant d’exécuter la commande ci-dessous.

	azure vm create --resource-group nrprg --name web1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

>[AZURE.IMPORTANT] Les machines virtuelles d’un équilibreur de charge doivent se trouver dans le même groupe à haute disponibilité. Utilisez `azure availset create` pour créer un groupe à haute disponibilité.

La sortie se présente comme suit :

	info:    Executing command vm create
	+ Looking up the VM "web1"
	Enter username: azureuser
	Enter password for azureuser: *********
	Confirm password: *********
	info:    Using the VM Size "Standard_A1"
	info:    The [OS, Data] Disk or image configuration requires storage account
	+ Looking up the storage account web1nrp
	+ Looking up the availability set "nrp-avset"
	info:    Found an Availability set "nrp-avset"
	+ Looking up the NIC "lb-nic1-be"
	info:    Found an existing NIC "lb-nic1-be"
	info:    Found an IP configuration with virtual network subnet id "/subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet" in the NIC "lb-nic1-be"
	info:    This is a NIC without publicIP configured
	+ Creating VM "web1"
	info:    vm create command OK

>[AZURE.NOTE] Le message vous informant qu’**il s’agit d’une carte réseau sans publicIP configuré** est un comportement attendu, car la carte réseau créée pour l’équilibreur de charge se connecte à Internet à l’aide de l’adresse IP publique de l’équilibreur de charge.

Si la carte réseau *lb-nic1-be* est associée à la règle NAT *rdp1*, vous pouvez vous connecter à *web1* en utilisant le protocole RDP via le port 3441 de l’équilibreur de charge.

### Étape 4

Créez une machine virtuelle nommée *web2*, puis associez-la à la carte réseau nommée *lb-nic2-be*. Un compte de stockage appelé *web1nrp* a été créé avant d’exécuter la commande ci-dessous.

	azure vm create --resource-group nrprg --name web2 --location eastus --vnet-	name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## Mettre à jour un équilibreur de charge existant

Vous pouvez ajouter des règles faisant référence à l'équilibreur de charge existant. Dans l’exemple ci-dessous, une nouvelle règle d’équilibrage de charge est ajoutée à un équilibrage de charge existant **NRPlb**

	azure network lb rule create -g nrprg -l nrplb -n lbrule2 -p tcp -f 8080 -b 8051 -t frontendnrppool -o NRPbackendpool

Paramètres :

**-g** - nom du groupe de ressources<br> **-l** - nom de l’équilibreur de charge<BR> **-n** - nom de règle de l’équilibreur de charge<BR> **-p** - protocole<BR> **-f** - port frontal<BR> **-b** - port de serveur principal <BR> **-t** - nom du port frontal<BR> **-b** - nom du pool de serveur principal <BR>

## Suppression d’un équilibreur de charge 


Pour supprimer un équilibreur de charge, utilisez la commande suivante

	azure network lb delete -g nrprg -n nrplb 

Où **nrprg** correspond au groupe de ressources et **nrplb** au nom de l’équilibreur de charge.

## Étapes suivantes

[Prise en main de la configuration d’un équilibrage de charge interne](load-balancer-get-started-ilb-arm-cli.md)

[Configuration d'un mode de distribution d'équilibrage de charge](load-balancer-distribution-mode.md)

[Configuration des paramètres de délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0302_2016-->