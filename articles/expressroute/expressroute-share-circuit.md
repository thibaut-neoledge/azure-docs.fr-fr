<properties 
   pageTitle="Partage d’un circuit ExpressRoute entre plusieurs abonnements | Microsoft Azure"
   description="Cet article vous guide pour partager votre circuit ExpressRoute entre plusieurs abonnements Azure."
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
   ms.date="07/20/2015"
   ms.author="cherylmc" />

# Partage d’un circuit ExpressRoute entre plusieurs abonnements

Vous pouvez partager un seul circuit ExpressRoute entre plusieurs abonnements. La **Figure 1** montre un schéma simple sur la façon de partager des circuits ExpressRoute entre plusieurs abonnements.

Chacun des petits clouds dans le cloud principal est utilisé pour représenter les abonnements appartenant à différents services au sein d’une organisation. Chaque service au sein de l’organisation peut utiliser son propre abonnement pour déployer ses services, mais peut partager un même circuit ExpressRoute dédié pour se connecter au réseau d’entreprise. Un seul service (dans cet exemple, le service informatique) peut posséder le circuit ExpressRoute dédié. Les frais de connectivité et de bande passante pour le circuit dédié s’appliquent au propriétaire du circuit dédié. D’autres abonnements au sein de l’organisation peuvent utiliser le circuit ExpressRoute.

**Figure 1**

![Partage d’abonnement](./media/expressroute-share-circuit/IC766124.png)

## Administration

Le *propriétaire du circuit* est l’administrateur/le co-administrateur de l’abonnement dans lequel le circuit dédié ExpressRoute est créé. Le propriétaire du circuit peut autoriser les administrateurs/co-administrateurs d’autres abonnements (appelés *utilisateurs du circuit* dans le diagramme de flux de travail) à utiliser ce circuit dédié de l’organisation. Une fois autorisés, les utilisateurs du circuit peuvent lier le réseau virtuel dans leur abonnement au circuit dédié.

Le propriétaire du circuit a le pouvoir de modifier et de révoquer les autorisations à tout moment. La révocation d’une autorisation entraîne la suppression de tous les liens de l’abonnement dont l’accès a été révoqué.

## Workflow

1. Le propriétaire du circuit autorise les administrateurs d’autres abonnements à utiliser le circuit spécifié. Dans l’exemple ci-dessous, l’administrateur du circuit (service informatique de Contoso) permet à l’administrateur d’un autre abonnement (Ventes Contoso), en spécifiant son ID Microsoft, de lier jusqu’à deux réseaux virtuels au circuit. L'applet de commande n'envoie pas de courrier électronique à l’ID Microsoft spécifié. Le propriétaire du circuit doit notifier explicitement au propriétaire de l’autre abonnement que l’autorisation a pris fin.

		PS C:\> New-AzureDedicatedCircuitLinkAuthorization -ServiceKey '6ed7e310-1a02-4261-915f-6ccfedc416f1' -Description 'SalesTeam' -Limit 2 -MicrosoftIds 'salesadmin@contoso.com'
		
		Description         : SalesTeam 
		Limit               : 2 
		LinkAuthorizationId : e2bc2645-6fd4-44a4-94f5-f2e43e6953ed 
		MicrosoftIds        : salesadmin@contoso.com 
		Used                : 0

1. Dès la notification par le propriétaire du circuit, l’administrateur de l’abonnement autorisé peut exécuter l’applet de commande suivante pour récupérer la clé de service du circuit. Dans cet exemple, l’administrateur du service des ventes de Contoso doit d’abord se connecter à l’aide de l’ID Microsoft spécifié, salesadmin@contoso.com.

		PS C:\> Get-AzureAuthorizedDedicatedCircuit
		
		Bandwidth                        : 100
		CircuitName                      : ContosoIT
		Location                         : Washington DC
		MaximumAllowedLinks              : 2
		ServiceKey                       : 6ed7e310-1a02-4261-915f-6ccfedc416f1
		ServiceProviderName              : ###########
		ServiceProviderProvisioningState : Provisioned
		Status                           : Enabled
		UsedLinks                        : 0

1. L’administrateur de l’abonnement autorisé exécute l'applet de commande suivante pour terminer l'opération de liaison.

		PS C:\> New-AzureDedicatedCircuitLink –servicekey 6ed7e310-1a02-4261-915f-6ccfedc416f1 –VnetName 'SalesVNET1' 
		
			State VnetName 
			----- -------- 
			Provisioned SalesVNET1

Et c’est terminé ! Le réseau virtuel du service des ventes de Contoso sur Azure est maintenant lié à un circuit créé/possédé par le service informatique de Contoso.

## Gestion des autorisations

Le propriétaire du circuit peut partager un circuit avec au plus dix abonnements Azure. Le propriétaire du circuit peut voir qui a été autorisé à accéder au circuit. Le propriétaire peut révoquer l’autorisation à tout moment. Quand le propriétaire du circuit révoque une autorisation, identifiée par LinkAuthorizationId, tous les liens autorisés par cette autorisation sont immédiatement supprimés. Les réseaux virtuels liés perdent alors la connectivité au réseau local via le circuit ExpressRoute.

	PS C:\> Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: 6ed7e310-1a02-4261-915f-6ccfedc416f1 
	
	Description         : EngineeringTeam 
	Limit               : 3 
	LinkAuthorizationId : cc958457-c8c1-4f16-af09-e7f099da64bf 
	MicrosoftIds        : engadmin@contoso.com 
	Used                : 1 
	
	Description         : MarketingTeam 
	Limit               : 1 
	LinkAuthorizationId : d972726f-c7b9-4658-8598-ad3208ac9348 
	MicrosoftIds        : marketingadmin@contoso.com 
	Used                : 0 
	
	Description         : SalesTeam 
	Limit               : 2 
	LinkAuthorizationId : e2bc2645-6fd4-44a4-94f5-f2e43e6953ed 
	MicrosoftIds        : salesadmin@contoso.com 
	Used                : 2 
	
	PS C:\> Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey '6ed7e310-1a02-4261-915f-6ccfedc416f1' -AuthorizationId 'e2bc2645-6fd4-44a4-94f5-f2e43e6953ed'


Le diagramme ci-dessous illustre le flux de travail d’autorisation :

![Flux de travail de partage d’abonnement](./media/expressroute-share-circuit/IC759525.png)

## Étapes suivantes

Pour plus d’informations sur ExpressRoute, consultez la rubrique [Présentation d’ExpressRoute](expressroute-introduction.md).

<!---HONumber=July15_HO4-->