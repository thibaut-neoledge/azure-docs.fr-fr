<properties 
   pageTitle="Création d’itinéraires et activation du transfert IP dans Azure"
   description="Création d’itinéraires et activation du transfert IP dans Azure"
   services="virtual-networks"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" /> 
<tags 
   ms.service="virtual-networks"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="telmos" />

# Création d’itinéraires et activation du transfert IP dans Azure
Vous pouvez utiliser des appliances virtuelles dans Azure pour gérer le trafic dans Azure Virtual Network. Toutefois, vous devez créer des itinéraires qui permettent aux machines virtuelles et aux services cloud de votre réseau virtuel de transmettre des paquets à votre appliance virtuelle, au lieu de la destination de votre choix. Vous devez également activer le transfert IP sur la machine virtuelle de l’appliance virtuelle, pour qu’elle puisse recevoir et transmettre des paquets qui ne lui sont pas adressés.

##Gestion des itinéraires
Vous pouvez ajouter, supprimer et modifier des itinéraires dans Azure à l'aide de PowerShell. Avant de pouvoir créer un itinéraire, vous devez créer une table d’itinéraires pour héberger l'itinéraire.

### Création d'une table d’itinéraires
Pour créer une table d’itinéraires nommée *FrontEndSubnetRouteTable*, exécutez la commande PowerShell suivante :

	New-AzureRouteTable -Name FrontEndSubnetRouteTable `
	-Location usnorth `
	-Label "Route table for frontend subnet"

### Ajout d’un routage à une table d’itinéraires
Pour ajouter un itinéraire qui définit *10.1.1.10* comme le saut suivant pour le *10.2.0.0/16* sous-réseau dans la table d’itinéraires créée précédemment, exécutez la commande PowerShell suivante :

	Set-AzureRoute -RouteTableName FrontEndSubnetRouteTable `
	-RouteName FirewallRoute -AddressPrefix 10.2.0.0/16 `
	-NextHopType VirtualAppliance `
	-NextHopIpAddress 10.1.1.10

### Association d’un itinéraire à un sous-réseau
Pour utiliser une table d’itinéraires, vous devez l’associer à un ou plusieurs sous-réseaux. Pour associer la table d’itinéraires *FrontEndSubnetRouteTable* à un sous-réseau nommé *FrontEndSubnet* dans le réseau virtuel *ProductionVnet*, exécutez la commande PowerShell suivante :

	Set-AzureSubnetRouteTable -VNetName ProductionVnet `
	-SubnetName FrontEndSubnet `
	-RouteTableName FrontEndSubnetRouteTable

### Affichage des itinéraires appliqués dans une machine virtuelle
Vous pouvez envoyer une requête à Azure pour afficher les itinéraires appliqués pour une machine virtuelle ou une instance de rôle. Les itinéraires affichés incluent les itinéraires par défaut fournis par Azure et les itinéraires publiés par une passerelle VPN. La limite d’affichage des itinéraires est de 800.

Pour afficher les itinéraires associés à la carte d’interface réseau principale sur une machine virtuelle nommée *FirewallAppliance1*, exécutez la commande PowerShell suivante :

	Get-AzureVM -Name FirewallAppliance1 `
	| Get-AzureEffectiveRouteTable

Pour afficher les itinéraires associés à la carte d’interface réseau secondaire nommée *backendnic* sur une machine virtuelle nommée *FirewallAppliance1*, exécutez la commande PowerShell suivante :

	Get-AzureVM -Name FirewallAppliance1 `
	| Get-AzureEffectiveRouteTable -NetworkInterfaceName backendnic

Pour afficher les itinéraires associés à la carte d’interface réseau principale sur une instance de rôle nommée *myRole* qui fait partie d'un service cloud nommé *myservice*, exécutez la commande PowerShell suivante :

	Get-AzureEffectiveRouteTable -ServiceName myservice `
	-RoleInstanceName myRole

## Gestion du transfert IP
Comme mentionné précédemment, vous devez activer le transfert IP sur toutes les machines virtuelles ou instances de rôle qui agissent en tant qu’appliance virtuelle.

### Activation du transfert IP
Pour activer le transfert IP dans une machine virtuelle nommée *FirewallAppliance1*, exécutez la commande PowerShell suivante :

	Get-AzureVM -Name FirewallAppliance1 `
	| Set-AzureIPForwarding -Enable

Pour activer le transfert IP dans une instance de rôle nommée *FirewallAppliance1* dans un service cloud nommé *myService*, exécutez la commande PowerShell suivante :

	Set-AzureIPForwarding -ServiceName myService `
	-RoleName FirewallAppliance1 -Enable

### Désactivation du transfert IP
Pour désactiver le transfert IP dans une machine virtuelle nommée *FirewallAppliance1*, exécutez la commande PowerShell suivante :

	Get-AzureVM -Name FirewallAppliance1 `
	| Set-AzureIPForwarding -Disable

Pour désactiver le transfert IP dans une instance de rôle nommée *FirewallAppliance1* dans un service cloud nommé *myService*, exécutez la commande PowerShell suivante :

	Set-AzureIPForwarding -ServiceName myService `
	-RoleName FirewallAppliance1 -Disable

### Affichage du statut du transfert IP
Pour afficher le statut du transfert IP sur une machine virtuelle nommée *FirewallAppliance1*, exécutez la commande PowerShell suivante :

	Get-AzureVM -Name FirewallAppliance1 `
	| Get-AzureIPForwarding

## Voir aussi

[Présentation des itinéraires définis par l’utilisateur et du transfert IP](../virtual-networks-udr-overview)

[Adresses IP publiques de niveau d'instance](../virtual-networks-instance-level-public-ip)

[Présentation de Virtual Network](https://msdn.microsoft.com/library/azure/jj156007.aspx) 

<!--HONumber=52-->
