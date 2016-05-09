<properties 
   pageTitle="Lier un réseau virtuel à un circuit ExpressRoute à l’aide du modèle de déploiement classique et de PowerShell | Microsoft Azure"
   description="Ce document vous présente comment lier des réseaux virtuels à des circuits ExpressRoute à l'aide du modèle de déploiement classique et de PowerShell."
   services="expressroute"
   documentationCenter="na"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/14/2016"
   ms.author="ganesr" />

# Lier un réseau virtuel à un circuit ExpressRoute

> [AZURE.SELECTOR]
- [Portail Azure - Resource Manager](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell - Resource Manager](expressroute-howto-linkvnet-arm.md)
- [PowerShell - Classique](expressroute-howto-linkvnet-classic.md)



Cet article vous aidera à lier des réseaux virtuels à des circuits ExpressRoute à l'aide du modèle de déploiement classique et de PowerShell. Les réseaux virtuels peuvent appartenir au même abonnement, ou faire partie d’un autre abonnement.

**À propos des modèles de déploiement Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Conditions préalables à la configuration

1. Vous devez utiliser la dernière version des modules Azure PowerShell. Vous pouvez télécharger et installer le dernier module PowerShell à partir de la section PowerShell de la [page de téléchargements Azure](https://azure.microsoft.com/downloads/). Suivez les instructions de la page [Installation et configuration d'Azure PowerShell](../powershell-install-configure.md) pour savoir comment configurer votre ordinateur afin d'utiliser les modules Azure PowerShell. 
2. Veillez à consulter les pages relatives aux [conditions préalables](expressroute-prerequisites.md), à la [configuration requise pour le routage](expressroute-routing.md) et aux [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
3. Vous devez disposer d’un circuit ExpressRoute actif. 
	- Suivez les instructions permettant de [créer un circuit ExpressRoute](expressroute-howto-circuit-classic.md) et faites-le activer par votre fournisseur de service de connectivité. 
	- Vérifiez que l’homologation privée Azure est configurée pour votre circuit. Pour obtenir des instructions de routage, consultez l'article sur la [configuration du routage](expressroute-howto-routing-classic.md). 
	- L’homologation privée Azure doit être configurée et l’homologation BGP entre votre réseau et Microsoft doit être opérationnelle pour que vous puissiez activer la connectivité de bout en bout.

Vous pouvez lier jusqu’à 10 réseaux virtuels à un circuit ExpressRoute. Tous les circuits ExpressRoute doivent être situés dans la même région géopolitique. Si vous avez activé le module complémentaire Premium d’ExpressRoute, vous pouvez lier un plus grand nombre de réseaux virtuels à votre circuit ExpressRoute. Pour plus d'informations sur le module complémentaire Premium, consultez le [FAQ](expressroute-faqs.md).

## Connecter un réseau virtuel du même abonnement à un circuit

Vous pouvez lier un réseau virtuel à un circuit ExpressRoute à l’aide de l’applet de commande suivante. Assurez-vous que la passerelle de réseau virtuel est créée et prête pour la liaison avant d’exécuter l’applet de commande.

	New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
	Provisioned

## Connecter un réseau virtuel d’un autre abonnement au circuit

Vous pouvez partager un circuit ExpressRoute entre plusieurs abonnements. La figure ci-après montre un schéma simple sur la façon de partager des circuits ExpressRoute entre plusieurs abonnements.

Chacun des petits clouds dans le cloud principal est utilisé pour représenter les abonnements appartenant à différents services au sein d’une organisation. Chaque service au sein de l’organisation peut utiliser son propre abonnement pour déployer ses services, mais peut partager un même circuit ExpressRoute pour se connecter à votre réseau local. Un seul service (dans cet exemple, le service informatique) peut posséder le circuit ExpressRoute. D’autres abonnements au sein de l’organisation peuvent utiliser le circuit ExpressRoute.

>[AZURE.NOTE] Les frais de connectivité et de bande passante pour le circuit dédié s’appliquent au propriétaire du circuit ExpressRoute. Tous les réseaux virtuels partagent la même bande passante.

![Connectivité entre abonnements](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### Administration

Le *propriétaire du circuit* est l'administrateur/le co-administrateur de l'abonnement dans lequel le circuit ExpressRoute est créé. Le propriétaire du circuit peut autoriser les administrateurs/co-administrateurs d'autres abonnements (appelés *utilisateurs du circuit* dans le diagramme de flux de travail) à utiliser ce circuit dédié de l'organisation. Une fois autorisés, les utilisateurs du circuit peuvent lier le réseau virtuel dans leur abonnement au circuit ExpressRoute.

Le propriétaire du circuit a le pouvoir de modifier et de révoquer les autorisations à tout moment. La révocation d’une autorisation entraîne la suppression de tous les liens de l’abonnement dont l’accès a été révoqué.

### Opérations du propriétaire du circuit 

#### Création d’une autorisation
	
Le propriétaire du circuit autorise les administrateurs d’autres abonnements à utiliser le circuit spécifié. Dans l’exemple ci-dessous, l’administrateur du circuit (service informatique de Contoso) permet à l’administrateur d’un autre abonnement (Dev-Test), en spécifiant son ID Microsoft, de lier jusqu’à deux réseaux virtuels au circuit. L'applet de commande n'envoie pas de courrier électronique à l’ID Microsoft spécifié. Le propriétaire du circuit doit notifier explicitement au propriétaire de l’autre abonnement que l’autorisation a pris fin.

	New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
		
	Description         : Dev-Test Links 
	Limit               : 2 
	LinkAuthorizationId : ********************************** 
	MicrosoftIds        : devtest@contoso.com 
	Used                : 0

#### Vérification des autorisations

Le propriétaire du circuit peut vérifier toutes les autorisations émises sur un circuit donné en exécutant l’applet de commande suivante.

	Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
	
	Description         : EngineeringTeam 
	Limit               : 3 
	LinkAuthorizationId : #################################### 
	MicrosoftIds        : engadmin@contoso.com 
	Used                : 1 
	
	Description         : MarketingTeam 
	Limit               : 1 
	LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ 
	MicrosoftIds        : marketingadmin@contoso.com 
	Used                : 0 
	
	Description         : Dev-Test Links 
	Limit               : 2 
	LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
	MicrosoftIds        : salesadmin@contoso.com 
	Used                : 2 
	

#### Mise à jour des autorisations

Le propriétaire du circuit peut modifier les autorisations à l’aide de l’applet de commande suivante.

	Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
		
	Description         : Dev-Test Links 
	Limit               : 5 
	LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
	MicrosoftIds        : devtest@contoso.com 
	Used                : 0


#### Suppression des autorisations

Le propriétaire du circuit peut révoquer/supprimer les autorisations accordées à l’utilisateur en exécutant l’applet de commande suivante.

	Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### Opérations de l’utilisateur du circuit

#### Vérification des autorisations

L’utilisateur du circuit peut vérifier les autorisations à l’aide de l’applet de commande suivante.

	Get-AzureAuthorizedDedicatedCircuit
		
	Bandwidth                        : 200
	CircuitName                      : ContosoIT
	Location                         : Washington DC
	MaximumAllowedLinks              : 2
	ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
	ServiceProviderName              : equinix
	ServiceProviderProvisioningState : Provisioned
	Status                           : Enabled
	UsedLinks                        : 0

#### Échange des autorisations de lien

L’utilisateur du circuit peut exécuter l’applet de commande suivante pour échanger une autorisation de lien.

	New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1' 
		
	State VnetName 
	----- -------- 
	Provisioned SalesVNET1

## Étapes suivantes

Pour plus d'informations sur ExpressRoute, consultez le [FAQ sur ExpressRoute](expressroute-faqs.md).

<!---HONumber=AcomDC_0427_2016-->