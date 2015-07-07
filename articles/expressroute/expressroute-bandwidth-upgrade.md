<properties 
   pageTitle="Mettre à niveau dynamiquement la bande passante ExpressRoute | Microsoft Azure"
   description="Comment augmenter dynamiquement la bande passante d'un circuit ExpressRoute sans interruption de service."
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
   ms.date="06/03/2015"
   ms.author="cherylmc" />

# Mettre à niveau de la bande passante du circuit ExpressRoute de façon dynamique et sans interruption de service

Vous pouvez augmenter la taille d'un circuit ExpressRoute sans interruption de service. Ces instructions vous aideront à mettre à jour la bande passante d'un circuit ExpressRoute à l'aide de PowerShell. Reportez-vous à la page [Forum Aux Questions ExpressRoute](expressroute-faqs.md) pour plus d'informations sur les limites et les limitations.

##  Conditions préalables à la configuration

Avant de commencer la configuration, vérifiez que les conditions préalables suivantes sont remplies :

- Un abonnement Azure
- La version la plus récente d’Azure PowerShell
- Un circuit ExpressRoute actif configuré et opérationnel


##  Configuration des paramètres à l'aide de PowerShell

Windows PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion de vos charges de travail dans Azure. Pour plus d'informations, veuillez consulter la documentation de PowerShell dans [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx).

1. **Importez le module PowerShell pour ExpressRoute.**

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Obtenir les détails concernant le circuit ExpressRoute**

	Vous pouvez obtenir les détails de votre circuit ExpressRoute à l'aide de la cmdlet PowerShell suivante :
		

    	PS C:> Get-AzureDedicatedCircuit
	
	Cette commande renverra une liste de tous les circuits que vous avez créés dans l'abonnement. Vous pouvez utiliser la commande suivante pour obtenir des détails d'un circuit ExpressRoute spécifique si vous disposez de la clé de service :

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


3. **Augmenter la bande passante du circuit ExpressRoute du côté de Microsoft**
	
	Consultez le [Forum Aux Questions ExpressRoute](expressroute-faqs.md) pour connaître les options de bande passante prises en charge par votre fournisseur. Vous pouvez choisir n'importe quelle taille supérieure à la taille de votre circuit existant. Une fois que vous sélectionné la taille dont vous avez besoin, vous pouvez utiliser la commande suivante pour redimensionner votre circuit.

		PS C:> Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

	Votre circuit sera redimensionné du côté de Microsoft. Notez que nous allons commencer à vous facturer la bande bande passante mise à jour à partir de cet instant.

4. **Augmenter la bande passante du circuit ExpressRoute du côté du fournisseur de services**

	Contactez votre fournisseur de connectivité (NSP/EXP) et fournissez-lui les informations concernant la bande passante mise à jour. Suivez le processus de mise à jour de la commande indiqué par votre fournisseur de services pour effectuer cette tâche.

 

<!---HONumber=62-->