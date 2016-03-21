<properties 
   pageTitle="Liaison de réseaux virtuels à des circuits ExpressRoute | Microsoft Azure"
   description="Ce document fournit une vue d’ensemble de la façon de lier des réseaux virtuels à des circuits ExpressRoute."
   services="expressroute"
   documentationCenter="na"
   authors="ganesr"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/16/2016"
   ms.author="ganesr" />

# Liaison de réseaux virtuels à des circuits ExpressRoute

> [AZURE.SELECTOR]
- [PowerShell - Classique](expressroute-howto-linkvnet-classic.md)
- [PowerShell - Resource Manager](expressroute-howto-linkvnet-arm.md)
- [Modèle - Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection)

Cet article vous donne une vue d'ensemble de la façon de lier des réseaux virtuels à des circuits ExpressRoute. Les réseaux virtuels peuvent appartenir au même abonnement, ou faire partie d’un autre abonnement. Cet article s’applique aux réseaux virtuels déployés à l’aide du modèle de déploiement Resource Manager. Si vous voulez lier un réseau virtuel qui a été déployé à l’aide du modèle de déploiement classique, consultez [Lier un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-classic.md).


**À propos des modèles de déploiement Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Conditions préalables à la configuration

- Vous devez utiliser la dernière version des modules Azure PowerShell version 1.0 ou ultérieure. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md). 
- Veillez à consulter les pages relatives aux [conditions préalables](expressroute-prerequisites.md), à la [configuration requise pour le routage](expressroute-routing.md) et aux [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
- Vous devez disposer d’un circuit ExpressRoute actif. 
	- Suivez les instructions permettant de [créer un circuit ExpressRoute](expressroute-howto-circuit-arm.md) et faites-le activer par votre fournisseur de service de connectivité. 
	- Vérifiez que l’homologation privée Azure est configurée pour votre circuit. Pour obtenir des instructions de routage, consultez l'article sur la [configuration du routage](expressroute-howto-routing-arm.md). 
	- L’homologation privée Azure doit être configurée et l’homologation BGP entre votre réseau et Microsoft doit être opérationnelle pour que vous puissiez activer la connectivité de bout en bout.
	- Vous devez disposer d'un réseau virtuel et d’une passerelle de réseau virtuel créés et totalement approvisionnés. Suivez les instructions pour créer une [passerelle VPN](../articles/vpn-gateway-create-site-to-site-rm-powershell.md), mais assurez-vous d’utiliser `-GatewayType ExpressRoute`.

Vous pouvez lier jusqu’à 10 réseaux virtuels à un circuit ExpressRoute. Tous les circuits ExpressRoute doivent être situés dans la même région géopolitique. Si vous avez activé le module complémentaire Premium d’ExpressRoute, vous pouvez lier un plus grand nombre de réseaux virtuels à votre circuit ExpressRoute. Pour plus d'informations sur le module complémentaire Premium, consultez le [FAQ](expressroute-faqs.md).

## Connexion d’un réseau virtuel dans le même abonnement Azure à un circuit ExpressRoute

Vous pouvez connecter une passerelle de réseau virtuel à un circuit ExpressRoute à l’aide de l’applet de commande suivante. Assurez-vous que la passerelle de réseau virtuel est créée et prête pour la liaison avant d’exécuter l’applet de commande.

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
	$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute

## Connexion d’un réseau virtuel dans un autre abonnement Azure à un circuit ExpressRoute

Vous pouvez partager un circuit ExpressRoute entre plusieurs abonnements. La figure ci-après montre un schéma simple sur la façon de partager des circuits ExpressRoute entre plusieurs abonnements. Chacun des petits clouds dans le cloud principal est utilisé pour représenter les abonnements appartenant à différents services au sein d’une organisation. Chaque service au sein de l’organisation peut utiliser son propre abonnement pour déployer ses services, mais peut partager un même circuit ExpressRoute pour se connecter à votre réseau local. Un seul service (dans cet exemple, le service informatique) peut posséder le circuit ExpressRoute. D’autres abonnements au sein de l’organisation peuvent utiliser le circuit ExpressRoute.

>[AZURE.NOTE] Les frais de connectivité et de bande passante pour le circuit dédié s’appliquent au propriétaire du circuit ExpressRoute. Tous les réseaux virtuels partagent la même bande passante.

![Connectivité entre abonnements](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### Administration

Le *propriétaire du circuit* est l’utilisateur avec pouvoir autorisé de la ressource de circuit ExpressRoute. Le propriétaire du circuit peut créer des autorisations utilisables par *les utilisateurs du circuit*. *Les utilisateurs du circuit* sont les propriétaires des passerelles de réseau virtuel (qui ne figurent pas dans le même abonnement que le circuit ExpressRoute). *Les utilisateurs du circuit* peuvent utiliser des autorisations (une seule autorisation par réseau virtuel).

Le *propriétaire du circuit* a le pouvoir de modifier et de révoquer les autorisations à tout moment. La révocation d’une autorisation entraîne la suppression de toutes les connexions de l’abonnement dont l’accès a été révoqué.

### Opérations du propriétaire du circuit 

#### Création d’une autorisation
	
Le propriétaire du circuit crée une autorisation. Cela entraîne la création d'une clé d'autorisation qui peut être utilisée par un utilisateur du circuit pour se connecter à ses passerelles de réseau virtuel vers un circuit ExpressRoute. Une autorisation n’est valide que pour une seule connexion.

L'extrait de l'applet de commande ci-dessous montre comment créer une autorisation.

		Add-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization1"
		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
		$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"

		$auth1 = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization1"
		

La réponse contient la clé d'autorisation et l'état

		Name                   : MyAuthorization1
		Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
		Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
		AuthorizationKey       : ####################################
		AuthorizationUseStatus : Available
		ProvisioningState      : Succeeded

		

#### Vérification des autorisations

Le propriétaire du circuit peut vérifier toutes les autorisations émises sur un circuit donné en exécutant l’applet de commande suivante.

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit
	

#### Ajout d'autorisations

Le propriétaire du circuit peut ajouter des autorisations à l’aide de l’applet de commande suivante.

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	Add-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization2"
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
	
	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit

	
#### Suppression des autorisations

Le propriétaire du circuit peut révoquer/supprimer les autorisations accordées à l’utilisateur en exécutant l’applet de commande suivante.

	Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -Circuit $circuit
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit	

### Opérations de l’utilisateur du circuit

L'utilisateur du circuit a besoin de l'ID de l'homologue et une clé d'autorisation du propriétaire du circuit. La clé du circuit est similaire à celle indiquée ci-dessous :


La clé d'autorisation est un GUID.

#### Utilisation des autorisations de connexion

L’utilisateur du circuit peut exécuter l’applet de commande suivante pour échanger une autorisation de lien.

	$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"	
	$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"

#### Libération des autorisations de connexion

Vous pouvez libérer une autorisation en supprimant la connexion entre le circuit ExpressRoute et le réseau virtuel.

## Étapes suivantes

Pour plus d'informations sur ExpressRoute, consultez le [FAQ sur ExpressRoute](expressroute-faqs.md).

<!---HONumber=AcomDC_0309_2016-->