<properties 
   pageTitle="Activer ou désactiver le module complémentaire Premium ExpressRoute | Microsoft Azure"
   description="Activer ou désactiver le module complémentaire ExpressRoute Premium pour un circuit ExpressRoute. ExpressRoute Premium vous permet d'ajouter jusqu'à 10 000 itinéraires pour l'homologation publique et privée et jusqu'à 10 réseaux virtuels à votre circuit ExpressRoute. Vous pouvez également associer un réseau virtuel d’une région à un circuit ExpressRoute d’une autre région."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/02/2015"
   ms.author="cherylmc" />

# Configurer le module complémentaire ExpressRoute Premium pour votre circuit ExpressRoute

ExpressRoute Premium est un ensemble de fonctionnalités répertoriées ci-dessous.

 - Augmentation de la limite de la table d’itinéraires de 4 000 à 10 000 itinéraires pour l’homologation publique et l’homologation privée.
 - Augmentation du nombre de réseaux virtuels (Vnet) qui peuvent être connectés à un circuit ExpressRoute (la valeur par défaut est 10). 
 - Connectivité globale sur le réseau principal Microsoft. Vous pouvez désormais lier un réseau virtuel dans une région géopolitique à un circuit ExpressRoute d’une autre région. **Exemple :** vous pouvez lier un réseau virtuel créé en Europe de l’Ouest à un circuit ExpressRoute créé dans la Silicon Valley.

Consultez la page [Forum Aux Questions ExpressRoute](expressroute-faqs.md) pour plus d’informations sur le module complémentaire ExpressRoute Premium. Consultez la page [Tarification](http://azure.microsoft.com/pricing/details/expressroute/) pour connaître les coûts.

Les instructions ci-dessous vous aideront à effectuer les opérations suivantes :

- Créer un circuit ExpressRoute avec un module complémentaire Premium activé.
- Mettre à jour un circuit ExpressRoute existant pour activer le module complémentaire Premium.
- Désactiver le module complémentaire ExpressRoute Premium pour un circuit.


## Créer un circuit ExpressRoute en activant les fonctionnalités du module complémentaire Premium

###  Avant de commencer

Avant de commencer la configuration, vérifiez que les conditions préalables suivantes sont remplies :

- Un abonnement Azure
- La version la plus récente d’Azure PowerShell

###  1. Importer le module PowerShell pour ExpressRoute

Windows PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion de vos charges de travail dans Azure. Pour plus d'informations, veuillez consulter la documentation de PowerShell dans [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx).

Utilisez les cmdlets ci-dessous afin d’importer le module PowerShell pour ExpressRoute :


	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'


### 2. Configurer un nouveau circuit ExpressRoute en activant les fonctionnalités du module complémentaire Premium

Vous pouvez créer un circuit ExpressRoute avec le module complémentaire Premium activé au moment de la création. Suivez les instructions pour créer des circuits ExpressRoute avec des réseaux [NSP](expressroute-configuring-nsps.md) ou [EXP](expressroute-configuring-exps.md). Nous disposons d’un nouveau paramètre optionnel dans la cmdlet New-AzureDedicatedCircuit qui vous permet de spécifier le SKU. Le SKU peut être Standard ou Premium. La valeur par défaut est Standard. Choisir un SKU Premium activera les fonctionnalités du module complémentaire Premium sur le circuit.


		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -Sku Premium


### 3. Vérifier que le module complémentaire ExpressRoute Premium est activé
Vous pouvez vérifier si le module complémentaire ExpressRoute Premium est activé pour votre circuit. Dans l'exemple ci-dessous, les fonctionnalités du module complémentaire ExpressRoute Premium n’ont pas été activées sur le circuit ExpressRoute. Le SKU apparaîtra comme ***Premium*** si le module complémentaire est activé.

		PS C:> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled




## Activer le module complémentaire ExpressRoute Premium pour un circuit ExpressRoute existant
Vous pouvez activer les fonctionnalités du modèle complémentaire ExpressRoute Premium pour tout circuit ExpressRoute que vous avez déjà créé.


1. **Obtenir les détails concernant le circuit ExpressRoute**

	Vous pouvez obtenir les détails de votre circuit ExpressRoute à l'aide de la cmdlet PowerShell suivante :
		

    	PS C:> Get-AzureDedicatedCircuit
	
	Cette commande renverra une liste de tous les circuits que vous avez créés dans l'abonnement. Vous pouvez utiliser la commande suivante pour obtenir des détails d'un circuit ExpressRoute spécifique si vous disposez de la clé de service

		 PS C:> Get-AzureDedicatedCircuit -ServiceKey <skey>

	Remplacez la valeur <skey> par la clé de service réelle.
	
		PS C:> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled


2. **Activer le module complémentaire ExpressRoute Premium pour le circuit**


	Vous pouvez activer le module complémentaire ExpressRoute Premium pour votre circuit existant à l'aide de la cmdlet PowerShell suivante :
	
		PS C:> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Enabled

	Les fonctionnalités du module complémentaire ExpressRoute Premium seront activées sur votre circuit. Notez que nous commencerons à vous facturer la fonctionnalité du module complémentaire Premium dès que la commande aura été exécutée avec succès.


## Désactiver le module complémentaire ExpressRoute Premium pour un circuit ExpressRoute

Vous pouvez désactiver le module complémentaire ExpressRoute Premium pour un circuit ExpressRoute sur lequel le module complémentaire Premium est activé.

**Remarque**: vous devez vérifier auparavant que vous n’utilisez plus les fonctionnalités répertoriées dans la liste des fonctionnalités du module complémentaire Premium. Nous rejetterons votre demande de désactivation du module complémentaire Premium si vous avez plus de 10 réseaux virtuels connectés à votre circuit. Vous constaterez également que nous abandonnerons nos sessions BGP si vous nous adressez plus de 5 000 itinéraires après avoir désactivé le module complémentaire Premium.

1. **Obtenir les détails concernant le circuit ExpressRoute**

	Vous pouvez obtenir les détails de votre circuit ExpressRoute à l'aide de la cmdlet PowerShell suivante :
		

    	PS C:> Get-AzureDedicatedCircuit
	
	Cette commande renverra une liste de tous les circuits que vous avez créés dans l'abonnement. Vous pouvez utiliser la commande suivante pour obtenir des détails d'un circuit ExpressRoute spécifique si vous disposez de la clé de service

		 PS C:> Get-AzureDedicatedCircuit -ServiceKey <skey>

	Remplacez la valeur <skey> par la clé de service réelle.
	
		PS C:> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Enabled


3. **Désactiver le module complémentaire ExpressRoute Premium pour le circuit**


	Vous pouvez désactiver le module complémentaire ExpressRoute Premium pour votre circuit existant à l'aide de la cmdlet PowerShell suivante :
	
		PS C:> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Standard

	Les fonctionnalités du module complémentaire ExpressRoute Premium seront désactivées sur votre circuit.


 

<!---HONumber=July15_HO2-->