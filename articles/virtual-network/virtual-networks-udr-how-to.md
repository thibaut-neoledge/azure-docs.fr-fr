<properties 
   pageTitle="Création d’itinéraires et activation du transfert IP dans Azure"
   description="Apprenez à gérer les itinéraires définis par l’utilisateur et le transfert IP"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="telmos" />

# Création d’itinéraires et activation du transfert IP dans Azure
Vous pouvez utiliser des appliances virtuelles dans Azure pour gérer le trafic dans Azure Virtual Network. Toutefois, vous devez créer des itinéraires qui permettent aux machines virtuelles et aux services cloud de votre réseau virtuel de transmettre des paquets à votre appliance virtuelle, au lieu de la destination de votre choix. Vous devez également activer le transfert IP sur la machine virtuelle de l’appliance virtuelle, pour qu’elle puisse recevoir et transmettre des paquets qui ne lui sont pas adressés.

## Gestion des itinéraires
Vous pouvez ajouter, supprimer et modifier des itinéraires dans Azure à l'aide de PowerShell. Avant de pouvoir créer un itinéraire, vous devez créer une table d’itinéraires pour héberger l'itinéraire.

### Création d'une table d’itinéraires
Pour créer une table d’itinéraires nommée *FrontEndSubnetRouteTable*, exécutez la commande PowerShell suivante :

	```powershell
	New-AzureRouteTable -Name FrontEndSubnetRouteTable `
		-Location uscentral `
		-Label "Route table for front end subnet"
	```

La sortie de la commande ci-dessus doit se présenter comme suit :

	Name                      Location   Label                          
	----                      --------   -----                          
	FrontEndSubnetRouteTable  West US    Route table for front end subnet

### Ajout d’un routage à une table d’itinéraires
Pour ajouter un itinéraire qui définit *10.1.1.10* comme le saut suivant pour le *10.2.0.0/16* sous-réseau dans la table d’itinéraires créée précédemment, exécutez la commande PowerShell suivante :

	```powershell
	Get-AzureRouteTable FrontEndSubnetRouteTable `
		|Set-AzureRoute -RouteName FirewallRoute -AddressPrefix 10.2.0.0/16 `
		-NextHopType VirtualAppliance `
		-NextHopIpAddress 10.1.1.10
	```

La sortie de la commande ci-dessus doit se présenter comme suit :

	Name     : FrontEndSubnetRouteTable
	Location : Central US
	Label    : Route table for frontend subnet
	Routes   : 
	           Name                 Address Prefix    Next hop type        Next hop IP address
	           ----                 --------------    -------------        -------------------
	           firewallroute        10.2.0.0/16       VirtualAppliance     10.1.1.10    

### Association d’un itinéraire à un sous-réseau
Pour utiliser une table d’itinéraires, vous devez l’associer à un ou plusieurs sous-réseaux. Pour associer la table d’itinéraires *FrontEndSubnetRouteTable* à un sous-réseau nommé *FrontEndSubnet* dans le réseau virtuel *ProductionVnet*, exécutez la commande PowerShell suivante :

	```powershell
	Set-AzureSubnetRouteTable -VirtualNetworkName ProductionVnet `
		-SubnetName FrontEndSubnet `
		-RouteTableName FrontEndSubnetRouteTable
	```

### Affichage des itinéraires appliqués dans une machine virtuelle
Vous pouvez envoyer une requête à Azure pour afficher les itinéraires appliqués pour une machine virtuelle ou une instance de rôle. Les itinéraires affichés incluent les itinéraires par défaut fournis par Azure et les itinéraires publiés par une passerelle VPN. La limite d’affichage des itinéraires est de 800.

Pour afficher les itinéraires associés à la carte d’interface réseau principale sur une machine virtuelle nommée *FWAppliance1*, exécutez la commande PowerShell suivante :

	```powershell
	Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
		| Get-AzureEffectiveRouteTable
	```

La sortie de la commande ci-dessus doit se présenter comme suit :

	Effective routes : 
	                   Name            Address Prefix    Next hop type    Next hop IP address Status   Source     
	                   ----            --------------    -------------    ------------------- ------   ------     
	                                   {10.0.0.0/8}      VNETLocal                            Active   Default    
	                                   {0.0.0.0/0}       Internet                             Active   Default    
	                                   {25.0.0.0/8}      Null                                 Active   Default    
	                                   {100.64.0.0/10}   Null                                 Active   Default    
	                                   {172.16.0.0/12}   Null                                 Active   Default    
	                                   {192.168.0.0/16}  Null                                 Active   Default    
	                   firewallroute   {10.2.0.0/16}     Null             10.1.1.10           Active   User      

Pour afficher les itinéraires associés à la carte d’interface réseau secondaire nommée *backendnic* sur une machine virtuelle nommée *FWAppliance1*, exécutez la commande PowerShell suivante :

	```powershell
	Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
		| Get-AzureEffectiveRouteTable -NetworkInterfaceName backendnic
	```

Pour afficher les itinéraires associés à la carte d’interface réseau principale sur une instance de rôle nommée *myRole* qui fait partie d'un service cloud nommé *ProductionVMs*, exécutez la commande PowerShell suivante :

	```powershell
	Get-AzureEffectiveRouteTable -ServiceName ProductionVMs `
		-RoleInstanceName myRole
	```

## Gestion du transfert IP
Comme mentionné précédemment, vous devez activer le transfert IP sur toutes les machines virtuelles ou instances de rôle qui agissent en tant qu’appliance virtuelle.

### Activation du transfert IP
Pour activer le transfert IP dans une machine virtuelle nommée *FWAppliance1*, exécutez la commande PowerShell suivante :

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
	| Set-AzureIPForwarding -Enable
```

Pour activer le transfert IP dans une instance de rôle nommée *FWAppliance* dans un service cloud nommé *DMZService*, exécutez la commande PowerShell suivante :

```powershell
Set-AzureIPForwarding -ServiceName DMZService `
	-RoleName FWAppliance -Enable
```

### Désactivation du transfert IP
Pour désactiver le transfert IP dans une machine virtuelle nommée *FWAppliance1*, exécutez la commande PowerShell suivante :

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName DMZService `
	| Set-AzureIPForwarding -Disable
```

Pour désactiver le transfert IP dans une instance de rôle nommée *FWAppliance* dans un service cloud nommé *DMZService*, exécutez la commande PowerShell suivante :

```powershell
Set-AzureIPForwarding -ServiceName DMZService `
	-RoleName FWAppliance -Disable
```

### Affichage du statut du transfert IP
Pour afficher le statut du transfert IP sur une machine virtuelle nommée *FWAppliance1*, exécutez la commande PowerShell suivante :

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
	| Get-AzureIPForwarding
``` 

<!---HONumber=AcomDC_1210_2015-->