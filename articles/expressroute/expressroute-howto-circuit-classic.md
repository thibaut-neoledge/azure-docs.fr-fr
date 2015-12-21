<properties
   pageTitle="Étapes de configuration d’un circuit ExpressRoute à l’aide de PowerShell | Microsoft Azure"
   description="Cet article vous guide tout au long des étapes de création et d’approvisionnement d'un circuit ExpressRoute. Cet article vous montre également comment vérifier l'état, mettre à jour ou supprimer et annuler l’approvisionnement de votre circuit."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/08/2015"
   ms.author="cherylmc"/>

# Créer et modifier un circuit ExpressRoute à l’aide de PowerShell

> [AZURE.SELECTOR]
[PowerShell - Classic](expressroute-howto-circuit-classic.md)
[PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)

Cet article vous guide tout au long des étapes de création d’un circuit ExpressRoute à l’aide des applets de commande PowerShell et du modèle de déploiement classique. Les étapes ci-dessous vous montreront également comment vérifier l'état, mettre à jour ou supprimer et annuler l’approvisionnement d'un circuit ExpressRoute.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]


## Conditions préalables à la configuration

- Vous devez utiliser la dernière version des modules Azure PowerShell. Vous pouvez télécharger et installer le module PowerShell le plus récent à partir de la section PowerShell de la [page de téléchargements Azure](http://azure.microsoft.com/downloads). Suivez les instructions de la page [Installation et configuration d'Azure PowerShell](../powershell-install-configure.md) pour savoir comment configurer votre ordinateur afin d'utiliser les modules Azure PowerShell. 
- Veillez à consulter les pages relatives aux [conditions préalables](expressroute-prerequisites.md) et aux [workflows](expressroute-workflows.md) avant de commencer la configuration.

## Pour créer et approvisionner un circuit ExpressRoute

1. **Importez le module PowerShell pour ExpressRoute.**

 	Vous devez importer les modules Azure et ExpressRoute dans la session PowerShell pour utiliser les applets de commande ExpressRoute. Exécutez les commandes suivantes pour importer les modules Azure et ExpressRoute dans la session PowerShell.

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Récupérez la liste des fournisseurs, des emplacements et des bandes passantes pris en charge.**

	Avant de créer un circuit ExpressRoute, vous aurez besoin d’une liste des fournisseurs de services, des emplacements pris en charge et des options de bande passante. L’applet de commande PowerShell *Get-AzureDedicatedCircuitServiceProvider* retourne ces informations, que vous utiliserez dans les étapes ultérieures.

		PS C:\> Get-AzureDedicatedCircuitServiceProvider

		Name                 DedicatedCircuitLocations      DedicatedCircuitBandwidths                                                                                                                                                                                   
		----                 -------------------------      --------------------------                                                                                                                                                                                   
		Aryaka Networks      Silicon Valley,Washington      200Mbps:200, 500Mbps:500, 1Gbps:1000                                                                                                                                                                         
		                     DC,Singapore                                                                                                                                                                                                                                
		AT&T                 Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		AT&T Netbond         Washington DC,Silicon          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     Valley,Dallas                                                                                                                                                                                                                               
		British Telecom      London,Amsterdam,Washington    10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     DC,Tokyo,Silicon Valley                                                                                                                                                                                                                     
		Colt Ethernet        Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Colt IPVPN           Amsterdam,London               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Comcast              Washington DC,Silicon Valley   200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Equinix              Amsterdam,Atlanta,Chicago,Dall 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		                     as,New York,Seattle,Silicon                                                                                                                                                                                                                 
		                     Valley,Washington                                                                                                                                                                                                                           
		                     DC,London,Hong Kong,Singapore,                                                                                                                                                                                                              
		                     Sydney,Tokyo,Sao Paulo,Los                                                                                                                                                                                                                  
		                     Angeles,Melbourne                                                                                                                                                                                                                           
		IIJ                  Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		InterCloud           Washington                     200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		                     DC,London,Singapore,Amsterdam                                                                                                                                                                                                               
		Internet Solutions   London,Amsterdam               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		– Cloud Connect                                                                                                                                                                                                                                                  
		Interxion            Amsterdam                      200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Level 3              London,Chicago,Dallas,Seattle, 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Communications -     Silicon Valley,Washington DC                                                                                                                                                                                                                
		Exchange                                                                                                                                                                                                                                                         
		Level 3              London,Chicago,Dallas,Seattle, 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Communications -     Silicon Valley,Washington DC                                                                                                                                                                                                                
		IPVPN                                                                                                                                                                                                                                                            
		Megaport             Melbourne,Sydney               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		NEXTDC               Melbourne                      200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		NTT Communications   Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Orange               Amsterdam,London,Silicon       10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     Valley,Washington DC,Hong Kong                                                                                                                                                                                                              
		PCCW Global Limited  Hong Kong                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		SingTel Domestic     Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		SingTel              Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		International                                                                                                                                                                                                                                                    
		Tata Communications  Hong Kong,Singapore,London,Ams 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     terdam,Chennai,Mumbai                                                                                                                                                                                                                       
		TeleCity Group       Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Telstra Corporation  Sydney,Melbourne               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Verizon              London,Hong Kong,Silicon       10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     Valley,Washington DC                                                                                                                                                                                                                        
		Vodafone             London                         10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Zayo Group           Washington DC                  200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
    	

3. **Créez un circuit ExpressRoute.**

	L'exemple ci-dessous montre comment créer un circuit ExpressRoute de 200 Mbps via Equinix dans Silicon Valley. Si vous utilisez un autre fournisseur et différents paramètres, remplacez ces informations lors de la création de votre requête.

	Voici un exemple de demande pour une nouvelle clé de service :

		#Creating a new circuit
		$Bandwidth = 200
		$CircuitName = "MyTestCircuit"
		$ServiceProvider = "Equinix"
		$Location = "Silicon Valley"

		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData 

	Ou, si vous souhaitez créer un circuit ExpressRoute avec le module complémentaire premium, utilisez l'exemple suivant. Consultez la page [Forum Aux Questions ExpressRoute](expressroute-faqs.md) pour plus d’informations sur le module complémentaire premium.

		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData
	
	
	La réponse contiendra la clé de service. Vous pouvez obtenir une description détaillée de tous les paramètres en exécutant la commande suivante :

		get-help new-azurededicatedcircuit -detailed 

4. **Répertoriez tous les circuits ExpressRoute.**

	Vous pouvez exécuter la commande *Get-AzureDedicatedCircuit* pour obtenir une liste de tous les circuits ExpressRoute que vous avez créés.

		#Getting service key
		Get-AzureDedicatedCircuit

	La réponse ressemblera à l'exemple ci-dessous :

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Sku                              : Standard
		Status                           : Enabled

	Vous pouvez récupérer ces informations à tout moment à l’aide de l’applet de commande *Get-AzureDedicatedCircuit*. L'appel sans paramètre répertorie tous les circuits. Votre clé de service apparaît dans le champ *ServiceKey*.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Sku                              : Standard
		Status                           : Enabled

	Vous pouvez obtenir une description détaillée de tous les paramètres en exécutant la commande suivante :

		get-help get-azurededicatedcircuit -detailed 

5. **Envoyez la clé de service à votre fournisseur de connectivité pour l’approvisionnement.**

	Lorsque vous créez un circuit ExpressRoute, ce circuit affichera l’état suivant :
	
		ServiceProviderProvisioningState : NotProvisioned
		
		Status                           : Enabled

	*ServiceProviderProvisioningState* fournit des informations sur l’état actuel de l’approvisionnement du côté du fournisseur de services et Status indique l’état du côté de Microsoft. Un circuit ExpressRoute doit être dans l’état suivant pour pouvoir être utilisé.

		ServiceProviderProvisioningState : Provisioned
		
		Status                           : Enabled

	Le circuit passera à l'état suivant lorsque le fournisseur de connectivité est sur le point de l’activer pour vous.

		ServiceProviderProvisioningState : Provisioned
		
		Status                           : Enabled



5. **Vérifiez régulièrement le statut et l'état de la clé du circuit.**

	Cela vous permet de savoir quand votre fournisseur a activé votre circuit. Une fois le circuit configuré, *ServiceProviderProvisioningState* s’affiche avec l’état *Provisioned* comme indiqué dans l’exemple ci-dessous.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

6. **Créez votre configuration de routage.**
	
	Reportez-vous à la page [Configuration du circuit ExpressRoute (créer et modifier des homologations de circuit)](expressroute-howto-routing-classic.md) pour obtenir des instructions pas à pas.

7. **Liaison d’un réseau virtuel à un circuit ExpressRoute.**

	Liez ensuite un VNet à votre circuit ExpressRoute. Reportez-vous à la rubrique [Liaison de réseaux virtuels à des circuits ExpressRoute](expressroute-howto-linkvnet-classic.md) pour obtenir des instructions étape par étape. Si vous avez besoin de créer un réseau virtuel pour ExpressRoute, consultez la rubrique [Création d’un réseau virtuel pour ExpressRoute](expressroute-howto-createvnet-classic.md) afin d’obtenir des instructions.

##  Pour obtenir l'état d'un circuit ExpressRoute

Vous pouvez récupérer ces informations à tout moment à l’aide de l’applet de commande *Get-AzureCircuit*. L'appel sans paramètre répertorie tous les circuits.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

		Bandwidth                        : 1000
		CircuitName                      : MyAsiaCircuit
		Location                         : Singapore
		ServiceKey                       : #################################
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

Vous pouvez obtenir des informations sur un circuit ExpressRoute spécifique en passant, en tant que paramètre, la clé de service à l’appel.

		PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled


Vous pouvez obtenir une description détaillée de tous les paramètres en exécutant la commande suivante :

		get-help get-azurededicatedcircuit -detailed 

##  Pour modifier un circuit ExpressRoute

Vous pouvez modifier certaines propriétés d'un circuit ExpressRoute sans affecter la connectivité.

Vous pouvez effectuer les opérations suivantes :

- Activer / désactiver le module complémentaire ExpressRoute premium pour votre circuit ExpressRoute sans aucune interruption de service.
- Augmenter la bande passante de votre circuit ExpressRoute sans aucune interruption de service.

Reportez-vous à la page [Forum Aux Questions ExpressRoute](expressroute-faqs.md) pour plus d'informations sur les limites et les limitations.

### Activation du module complémentaire ExpressRoute premium

Vous pouvez activer le module complémentaire ExpressRoute premium pour votre circuit existant à l'aide de l’applet de commande PowerShell suivante :
	
		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Enabled

Les fonctionnalités du module complémentaire ExpressRoute premium seront activées sur votre circuit. Notez que nous commencerons à vous facturer la fonctionnalité du module complémentaire premium dès que la commande aura été exécutée avec succès.

### Désactivation du module complémentaire ExpressRoute premium

Vous pouvez désactiver le module complémentaire ExpressRoute premium pour votre circuit existant à l'aide de l’applet de commande PowerShell suivante :
	
		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

Le composant additionnel premium est maintenant désactivé pour votre circuit.

>[AZURE.IMPORTANT]Cette opération peut échouer si vous utilisez des ressources supérieures à ce qui autorisé pour le circuit standard.
>
>- Vous devez vous assurer que le nombre de réseaux virtuels liés au circuit est inférieur à 10 avant de rétrograder du niveau premium à standard. Si vous ne le faites, votre demande de mise à jour échouera et le tarif premium vous sera facturé.
- Vous devez dissocier tous les réseaux virtuels dans d'autres régions géopolitiques. Si vous ne le faites, votre demande de mise à jour échouera et le tarif premium vous sera facturé.
- Votre table de routage doit être inférieure à 4 000 routages pour l'homologation privée. Si la taille de la table de routage est supérieure à 4 000 routages, la session BGP s’arrêtera et ne sera pas réactivée tant que le nombre de préfixes publiés ne sera pas inférieur à 4 000.


### Mise à jour de la bande passante d’un circuit ExpressRoute

Consultez le [Forum Aux Questions ExpressRoute](expressroute-faqs.md) pour connaître les options de bande passante prises en charge par votre fournisseur. Vous pouvez choisir n'importe quelle taille supérieure à la taille de votre circuit existant. Une fois que vous sélectionnez la taille dont vous avez besoin, vous pouvez utiliser la commande suivante pour redimensionner votre circuit.

		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

Votre circuit sera redimensionné du côté de Microsoft. Vous devez contacter votre fournisseur de connectivité pour mettre à jour les configurations de son côté afin de refléter cette modification. Notez que nous allons commencer à vous facturer la bande bande passante mise à jour à partir de cet instant.

>[AZURE.IMPORTANT]Vous ne pouvez pas réduire la bande passante d’un circuit ExpressRoute sans interrompre le service. La rétrogradation de la bande passante vous oblige à annuler le déploiement du circuit ExpressRoute, puis à réapprovisionner un nouveau circuit ExpressRoute.

##  Pour supprimer et annuler l’approvisionnement d’un circuit ExpressRoute

Vous pouvez supprimer votre circuit ExpressRoute en exécutant la commande suivante :

		PS C:\> Remove-AzureDedicatedCircuit -ServiceKey "*********************************"	

Notez que vous devez dissocier tous les réseaux virtuels du circuit ExpressRoute pour que cette opération réussisse. Si cette opération échoue, vérifiez si certains de vos réseaux virtuels sont liés au circuit.

Si l’état d’approvisionnement du fournisseur de services du circuit ExpressRoute est activé, l’état passe de l’état activé à l’état *disabling* (désactivation). Vous devez contacter votre fournisseur de services pour annuler l’approvisionnement du circuit de son côté. Nous continuerons à réserver des ressources et à vous facturer jusqu'à ce que le fournisseur de services termine l'annulation de l’approvisionnement et nous envoie une notification.

Si le fournisseur de services a annulé l’approvisionnement du circuit (l’état d’approvisionnement du fournisseur de services affiche la valeur *not provisioned* - non approvisionné) avant que vous n’exécutiez l’applet de commande ci-dessus, nous annulerons l’approvisionnement du circuit et cesserons la facturation.

## Étapes suivantes

- [Configuration du routage](expressroute-howto-routing-classic.md)

<!---HONumber=AcomDC_1210_2015-->