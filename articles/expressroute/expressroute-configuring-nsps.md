<properties 
   pageTitle="Configuration d’ExpressRoute avec des fournisseurs de services réseau (NSP)"
   description="Ce didacticiel vous guide dans la configuration d’ExpressRoute via des NSP"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="04/29/2015"
   ms.author="cherylmc"/>

#  Configuration d’une connexion ExpressRoute via un fournisseur de services réseau

Pour configurer votre connexion ExpressRoute via un fournisseur de services Exchange, vous devrez effectuer plusieurs étapes dans l'ordre approprié. Ces instructions vous aideront à effectuer les opérations suivantes :

- Création et gestion des circuits ExpressRoute
- Liaison d’un réseau virtuel au circuit ExpressRoute

##  Conditions préalables à la configuration


Avant de commencer la configuration, vérifiez que les conditions préalables suivantes sont remplies :

- Abonnement Azure
- Version la plus récente de Windows PowerShell
- Conditions requises pour Virtual Network :
	- Un jeu de préfixes d'adresses IP à utiliser sur des réseaux virtuels dans Azure.
	- Un jeu de préfixes IP locaux (pouvant contenir des adresses IP publiques).
	- La passerelle de réseau virtuel doit être créée avec un sous-réseau /28.
	- Un jeu supplémentaire de préfixes IP (/ 28) situé en dehors du réseau virtuel. Il sera utilisé pour la configuration des itinéraires. Vous devrez le transmettre au fournisseur de services réseau.
	- Un numéro AS pour votre réseau. Vous devrez transmettre cette information au fournisseur de services réseau. Vous pouvez utiliser des numéros AS privés. Si vous choisissez de procéder ainsi, ces numéros doivent être > 65 000. Pour plus d'informations sur les numéros AS, consultez la rubrique Numéros de système autonome (AS). 

- Pour le fournisseur de services réseau :
	- Vous devez disposer d'un réseau VPN pris en charge par ExpressRoute. Vérifiez auprès de votre fournisseur de services réseau si votre service VPN répond à la configuration requise.

##  Flux de travail du déploiement

![Workflow du fournisseur de services réseau](./media/expressroute-configuring-nsps/expressroute-nsp-connectivity-workflow.png)

##  Configuration des paramètres à l'aide de PowerShell
Windows PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion de vos charges de travail dans Azure. Pour plus d'informations, veuillez consulter la documentation de PowerShell dans [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx)



1. **Importez le module PowerShell pour ExpressRoute.**

		Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
		Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1' 

2. **Récupérez la liste des fournisseurs, des emplacements et des bandes passantes pris en charge.**

	Avant de créer un circuit, vous aurez besoin d’une liste des fournisseurs de services, des emplacements pris en charge et des options de bande passante pour chaque emplacement. L'applet de commande PowerShell suivante renvoie ces informations. Vous les utiliserez dans les étapes ultérieures.

		PS C:> Get-AzureDedicatedCircuitServiceProvider

	Les informations renvoyées ressembleront à l'exemple ci-dessous :

		PS C:> Get-AzureDedicatedCircuitServiceProvider
	
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
		

3. **Demandez une clé de service et communiquez-la au fournisseur Exchange.**

	Vous utiliserez une cmdlet PowerShell pour effectuer cette demande. Pour cet exemple, nous utiliserons AT&T Netbond comme fournisseur de services et spécifierons un circuit ExpressRoute de 50 Mbit/s dans la Silicon Valley. Si vous utilisez un autre fournisseur et différents paramètres, remplacez ces informations lors de la création de votre requête.

	Voici un exemple de demande pour une nouvelle clé de service :

		##Creating a new circuit
		$Bandwidth = 50
		$CircuitName = "NetBondSVTest"
		$ServiceProvider = "at&t"
		$Location = "Silicon Valley"
		
		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location
		
		#Getting service key
		Get-AzureDedicatedCircuit

	La réponse ressemblera à l'exemple ci-dessous :

		Bandwidth                        : 50
		CircuitName                      : NetBondSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : at&t
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled

	Vous pouvez récupérer ces informations à tout moment à l'aide de l'applet de commande Get-AzureCircuit. L'appel sans paramètre répertorie tous les circuits. Votre clé de Service apparaît dans le champ ServiceKey.

		PS C:> Get-AzureDedicatedCircuit
		
		Bandwidth                        : 500
		CircuitName                      : NetBondSVTest
		Location                         : Silicon Valley
		ServiceKey                       : 00-0000-0000-0000-0000000000
		ServiceProviderName              : at&t
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled

	Une fois cette étape terminée, vous pourrez accéder au stockage Azure et à d'autres services.



4. **Configurez votre réseau virtuel et votre passerelle.**

	Consultez [Configuration d’un réseau virtuel et d’une passerelle pour ExpressRoute](https://msdn.microsoft.com/library/azure/dn643737.aspx). Notez que le sous-réseau de la passerelle doit être/28 pour pouvoir fonctionner avec une connexion ExpressRoute.

5. **Liez votre réseau à un circuit.**

	Effectuez les étapes suivantes uniquement après avoir vérifié que votre
 
	- ServiceProviderState: Provisioned
	- Status: Enabled

	Vérifiez que vous disposez d'au moins un réseau virtuel Azure avec une passerelle créée. La passerelle doit être en cours d'exécution.

		PS C:> $Vnet = "MyTestVNet"
		New-AzureDedicatedCircuitLink -ServiceKey $ServiceKey -VNetName $Vnet
		
		Provisioned 

<!---HONumber=62-->