<properties 
   pageTitle="Configuration du service ExpressRoute via un fournisseur Exchange"
   description="Ce didacticiel vous guide dans la configuration du service ExpressRoute via des fournisseurs Exchange."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" /> <tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="04/29/2015"
   ms.author="cherylmc"/>

#  Configurer une connexion ExpressRoute via un fournisseur Exchange

Pour configurer votre connexion ExpressRoute via un fournisseur Exchange, vous devrez respecter plusieurs étapes dans l'ordre approprié. Ces instructions vous aideront à effectuer les opérations suivantes :

- Création et gestion des circuits ExpressRoute
- Configuration de l’homologation BGP pour les circuits ExpressRoute
- Liaison d’un réseau virtuel au circuit ExpressRoute

##  Conditions préalables à la configuration


Avant de commencer la configuration, vérifiez que les conditions préalables suivantes sont remplies :

- Abonnement Azure 
- Version la plus récente de Microsoft Azure PowerShell 
- Conditions requises pour Virtual Network : 
	- Un jeu de préfixes d'adresses IP à utiliser sur des réseaux virtuels dans Azure
	- Un jeu de préfixes IP locaux \(pouvant contenir des adresses IP publiques\)
	- La passerelle de réseau virtuel doit être créée avec un sous-réseau /28.
	- Un jeu supplémentaire de préfixes IP \(/ 28\) situé en dehors du réseau virtuel. Il sera utilisé pour la configuration de l'homologation BGP.
	- Un numéro AS pour votre réseau. Pour plus d'informations sur les numéros AS, consultez [Numéros de système autonome \(AS\)](http://www.iana.org/assignments/as-numbers/as-numbers.xhtml).
	- Un hachage MD5 si vous avez besoin d'une session BGP authentifiée
	- Des ID pour le réseau local virtuel \(vLAN\) sur lequel le trafic sera envoyé. Vous aurez besoin de 2 ID vLAN par circuit : un pour les réseaux virtuels et l'autre pour les services hébergés sur des adresses IP publiques.
	- Des [Numéros de système autonome \(AS\)](http://www.iana.org/assignments/as-numbers/as-numbers.xhtml) pour votre réseau.
	- Deux interconnexions 1 Gbit/s / 10 Gbits/s pour le réseau Ethernet Exchange du fournisseur Exchange.
	- Une paire de routeurs capables de prendre en charge le protocole BGP pour le routage

##  Flux de travail du déploiement


![](./media/expressroute-configuring-exps/expressroute-exp-connectivity-workflow.png)

##  Configuration des paramètres à l'aide de PowerShell

Windows PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion de vos charges de travail dans Azure. Pour plus d'informations, veuillez consulter la documentation de PowerShell dans [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx).

1. **Importez le module PowerShell pour ExpressRoute.**

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1' 
	
2. **Récupérez la liste des fournisseurs, des emplacements et des bandes passantes pris en charge.**

	Avant de créer un circuit, vous aurez besoin d’une liste des fournisseurs de services, des emplacements pris en charge et des options de bande passante pour chaque emplacement. L'applet de commande PowerShell suivante renvoie ces informations. Vous les utiliserez dans les étapes ultérieures.

    	PS C:\> Get-AzureDedicatedCircuitServiceProvider
		**The information returned will look similar to the example below:**
		
		
		Name                 DedicatedCircuitLocations      DedicatedCircuitBandwidths                                                                                                                                                                                   
		----                 -------------------------      --------------------------                                                                                                                                                                                   
		AT&T                 Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		British Telecom      London,Amsterdam               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Equinix              Amsterdam,Atlanta,Chicago,Dall 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		                     as,New York,Seattle,Silicon                                                                                                                                                                                                                 
		                     Valley,Washington                                                                                                                                                                                                                           
		                     DC,London,Hong                                                                                                                                                                                                                              
		                     Kong,Singapore,Sydney,Tokyo                                                                                                                                                                                                                 
		IIJ                  Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Level 3              London,Silicon                 200Mbps:200, 500Mbps:500, 1Gbps:1000                                                                                                                                                                         
		Communications -     Valley,Washington DC                                                                                                                                                                                                                        
		Exchange                                                                                                                                                                                                                                                         
		Level 3              London,Silicon                 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Communications -     Valley,Washington DC                                                                                                                                                                                                                        
		IPVPN                                                                                                                                                                                                                                                            
		Orange               Amsterdam,London               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		SingTel Domestic     Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		SingTel              Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		International                                                                                                                                                                                                                                                    
		TeleCity Group       Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Telstra Corporation  Sydney                         10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Verizon              Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
 
3. **Création d’un circuit dédié**
 
	L'exemple ci-dessous montre comment créer un circuit ExpressRoute de 200 Mbps via Equinix Silicon Valley. Si vous utilisez un autre fournisseur et différents paramètres, remplacez ces informations lors de la création de votre requête.

	Voici un exemple de demande pour une nouvelle clé de service :
      
		#Creating a new circuit
		$Bandwidth = 200
		$CircuitName = "EquinixSVTest"
		$ServiceProvider = "Equinix"
		$Location = "Silicon Valley"
		 
		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location
		 
		#Getting service key
		Get-AzureDedicatedCircuit
 
	La réponse ressemblera à l'exemple ci-dessous :
            
		Bandwidth                        : 200
		CircuitName                      : EquinixSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled

	Vous pouvez récupérer ces informations à tout moment à l'aide de l'applet de commande Get-AzureCircuit. L'appel sans paramètre répertorie tous les circuits. Votre clé de Service apparaît dans le champ ServiceKey.

		PS C:\> Get-AzureDedicatedCircuit
				 
		Bandwidth                        : 200
		CircuitName                      : EquinixSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled


4. **Envoyez la clé de Service à votre fournisseur Exchange.**

	Votre fournisseur Exchange utilisera la clé de service pour activer son extrémité de la connexion. Vous devez fournir le fournisseur de connectivité avec des informations supplémentaires pour permettre la connexion.

5. **Vérifiez régulièrement le statut et l'état de la clé du circuit.**

	Cela vous permettra de savoir quand votre fournisseur aura activé votre circuit. Une fois le circuit activé, *ServiceProviderProvisioningState* s'affiche avec l’état *Provisioned* comme indiqué dans l'exemple ci-dessous.

		PS C:\> Get-AzureDedicatedCircuit
				 
		Bandwidth                        : 200
		CircuitName                      : EquinixSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Status                           : Enabled
 
6. **Configurez le routage pour le réseau virtuel.**

	Nous utilisons des sessions BGP pour échanger des itinéraires et mais aussi pour vérifier que nous avons une haute disponibilité. L'exemple ci-dessous permet de créer une session BGP pour votre circuit. Utilisez vos propres valeurs lors de la création de votre session.

		#Setting up a bgp session
		$ServiceKey = "<your key>"
		$PriSN = "<subnet/30 you use IP #1 and Azure uses IP #2>"
		$SecSN = "<subnet/30 use IP #1 and Azure uses IP #2>"
		$ASN = <your ASN>
		$VLAN = <your vlan ID>
		 
		#Create a new bgp peering session
		New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN VlanId $VLAN –AccessType Private
		#Get BGP parameters and Azure ASN
		Get-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Private
		#Update BGP peering config
		Set-AzureBGPPeering  -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Private
		#Removing BGP peering config
		Remove-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Private

	Vous pouvez obtenir des informations de routage pour un circuit à l'aide de Get-AzureBGPPeering en fournissant la clé de service. Vous pouvez également mettre à jour les paramètres BGP à l'aide de Set-AzureBGPPeering. La session BGP ne sera pas créée lors de l’exécution de cette commande. Le circuit doit être lié à un réseau virtuel \(au minimum\) pour que la création de la session BGP fonctionne.

	La réponse ci-dessous vous fournira les informations dont vous aurez besoin pour les étapes suivantes. Utilisez le pair ASN pour la configuration BGP sur les VRF de votre routeur.
                    
		PS C:\> New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Private
				
		AzureAsn            : 12076
		PeerAsn             : 65001
		PrimaryAzurePort    : EQIX-SJC-06GMR-CIS-1-PRI-A
		PrimaryPeerSubnet   : 10.0.1.0/30
		SecondaryAzurePort  : EQIX-SJC-06GMR-CIS-2-SEC-A
		SecondaryPeerSubnet : 10.0.2.0/30
		State               : Enabled
		VlanId              : 100

7. **Configurez le routage pour les services hébergés sur des adresses IP publiques.**
	
	Nous utilisons des sessions BGP pour échanger des itinéraires et mais aussi pour vérifier que nous avons une haute disponibilité. L'exemple ci-dessous permet de créer une session BGP pour votre circuit. Utilisez vos propres valeurs lors de la création de votre session.

		#Setting up a bgp session
		$ServiceKey = "<your key>"
		$PriSN = "<subnet/30 subnet you use IP #1 and Azure uses IP #2>"
		$SecSN = "< subnet/30 subnet you use IP #1 and Azure uses IP #2>"
		$ASN = <your ASN> 
		$VLAN = <your vlan ID>
		 
		#Create a new bgp peering session
		New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Public
		#Get BGP parameters and Azure ASN
		Get-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Public
		#Update BGP peering config
		Set-AzureBGPPeering  -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Public
		#Removing BGP peering config
		Remove-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Public
 
	Vous pouvez obtenir des informations de routage pour un circuit à l'aide de Get-AzureBGPPeering en fournissant la clé de service. Vous pouvez également mettre à jour les paramètres BGP à l'aide de Set-AzureBGPPeering. La session BGP ne sera pas créée lors de l’exécution de cette commande. Le circuit doit être lié à un réseau virtuel \(au minimum\) pour que la création de la session BGP fonctionne.

	La réponse ci-dessous vous fournira les informations dont vous aurez besoin pour les étapes suivantes. Utilisez le pair ASN pour la configuration BGP sur les VRF de votre routeur.

		PS C:\> New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Private
		 
		AzureAsn            : 12076
		PeerAsn             : 65001
		PrimaryAzurePort    : EQIX-SJC-06GMR-CIS-1-PRI-A
		PrimaryPeerSubnet   : 10.0.1.8/30
		SecondaryAzurePort  : EQIX-SJC-06GMR-CIS-2-SEC-A
		SecondaryPeerSubnet : 10.0.2.8/30
		State               : Enabled
		VlanId              : 101
 
8. **Configurez votre réseau virtuel et votre passerelle.**

	Consultez [Configuration d’un réseau virtuel et d’une passerelle pour ExpressRoute](https://msdn.microsoft.com/library/azure/dn643737.aspx). Notez que le sous-réseau de la passerelle doit être/28 pour pouvoir fonctionner avec une connexion ExpressRoute.

9. **Liez votre réseau à un circuit.** Suivez les instructions suivantes uniquement après avoir vérifié que votre circuit est passé à l'état et au statut suivants :
	- ServiceProviderProvisioningState: Provisioned
	- Status: Enabled
	 
			PS C:\> $Vnet = "MyTestVNet"
			New-AzureDedicatedCircuitLink -ServiceKey $ServiceKey -VNetName $Vnet
<!--HONumber=54-->