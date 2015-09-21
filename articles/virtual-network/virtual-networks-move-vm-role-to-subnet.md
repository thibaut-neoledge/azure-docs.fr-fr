<properties 
   pageTitle="Déplacement d’une machine virtuelle ou d’une instance de rôle vers un autre sous-réseau"
   description="En savoir plus sur le déplacement des machines virtuelles et des instances de rôle vers un autre sous-réseau"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/04/2015"
   ms.author="telmos" />

# Déplacement d’une machine virtuelle ou d’une instance de rôle vers un autre sous-réseau

Vous pouvez utiliser PowerShell pour déplacer vos machines virtuelles d’un sous-réseau à l’autre dans le même réseau virtuel. Les instances de rôle peuvent être déplacées en modifiant le fichier CSCFG, au lieu d’utiliser PowerShell.

Pourquoi déplacer des ordinateurs virtuels vers un autre sous-réseau ? La migration entre sous-réseaux est utile quand le sous-réseau plus ancien est trop petit et ne peut pas être étendu en raison des machines virtuelles existantes exécutées dans ce sous-réseau. Dans ce cas, vous pouvez créer un sous-réseau plus grand et migrer les machines virtuelles vers le nouveau sous-réseau, puis, une fois la migration terminée, vous pouvez supprimer l’ancien sous-réseau vide.

## Comment déplacer une machine virtuelle vers un autre sous-réseau

Pour déplacer une machine virtuelle, exécutez l’applet de commande PowerShell Set-AzureSubnet, en prenant exemple sur le modèle ci-dessous. Dans l’exemple ci-dessous, nous déplaçons TestVM de son sous-réseau actuel vers Subnet-2. Assurez-vous de modifier l’exemple pour refléter votre environnement. Notez que chaque fois que vous exécutez l’applet de commande Update-AzureVM dans une procédure, elle redémarre votre machine virtuelle dans le cadre du processus de mise à jour.

	Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
	| Set-AzureSubnet –SubnetNames Subnet-2 `
	| Update-AzureVM

Si vous avez spécifié une adresse DIP statique pour votre machine virtuelle, vous devrez effacer ce paramètre avant de déplacer la machine virtuelle vers un nouveau sous-réseau. Dans ce cas, utilisez ce qui suit :

	Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
	| Remove-AzureStaticVNetIP `
	| Update-AzureVM
	Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
	| Set-AzureSubnet -SubnetNames Subnet-2 `
	| Update-AzureVM

## Pour déplacer une instance de rôle vers un autre sous-réseau

Pour déplacer une instance de rôle, modifiez le fichier CSCFG. Dans l’exemple ci-dessous, nous déplaçons « Role0 » dans le réseau virtuel *VNETName* depuis son sous-réseau actuel vers *Subnet-2*. Étant donné que l’instance de rôle a déjà été déployée, vous devez juste modifier le nom du sous-réseau = Subnet-2. Assurez-vous de modifier l’exemple pour refléter votre environnement.

	<NetworkConfiguration>
	    <VirtualNetworkSite name="VNETName" />
	    <AddressAssignments>
	       <InstanceAddress roleName="Role0">
	            <Subnets><Subnet name="Subnet-2" /></Subnets>
	       </InstanceAddress>
	    </AddressAssignments>
	</NetworkConfiguration> 

<!---HONumber=Sept15_HO2-->