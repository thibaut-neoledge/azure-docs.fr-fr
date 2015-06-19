<properties 
   pageTitle="Créer une machine virtuelle avec plusieurs cartes d’interface réseau"
   description="Création de machines virtuelles avec plusieurs cartes d’interface réseau"
   services="virtual-network, virtual-machines"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/30/2015"
   ms.author="telmos" />

# Créer une machine virtuelle avec plusieurs cartes d’interface réseau

La fonctionnalité « Multi-NIC » vous permet de créer et de gérer plusieurs cartes d’interface réseau (NIC) virtuelles sur vos machines virtuelles (VM) Azure. Il s’agit d’une exigence pour de nombreuses appliances virtuelles réseau, telles que les solutions de distribution d’applications et d’optimisation de réseau étendu (WAN). La fonctionnalité Multi-NIC contribue également à renforcer la gestion du trafic réseau en permettant notamment d’isoler le trafic entre une NIC frontale et une ou plusieurs NIC principales, ou de séparer le trafic du plan de données de celui du plan de gestion.

![Fonctionnalité Multi-NIC pour machine virtuelle](./media/virtual-networks-multiple-nics/IC757773.png)

La figure ci-dessus illustre une machine virtuelle équipée de trois NIC connectées à différents sous-réseaux.

## Exigences et contraintes

Actuellement, les exigences et contraintes liées à la fonctionnalité Multi-NIC sont les suivantes :

- Les machines virtuelles à plusieurs NIC doivent être créées dans des réseaux virtuels Azure. Les machines virtuelles ne faisant pas partie d’un réseau virtuel ne sont pas prises en charge. 
- Au sein d’un même service cloud, seuls les paramétrages suivants sont autorisés : 
	- toutes les machines virtuelles de ce service cloud doivent prendre en charge la fonctionnalité Multi-NIC, ou 
	- chacune des machines virtuelles de ce service cloud ne doit comporter qu’une seule NIC. 

>[AZURE.IMPORTANT]Si vous essayez d’ajouter une machine virtuelle multi-NIC à un déploiement (service cloud) contenant déjà une machine virtuelle à une seule NIC, vous recevrez le message d’erreur suivant : « Les machines virtuelles avec des interfaces réseau secondaires et les machines virtuelles sans interface réseau secondaire ne sont pas prises en charge dans le même déploiement ; de même, une machine virtuelle n’ayant aucune interface réseau secondaire ne peut pas être mise à jour pour obtenir des interfaces réseau secondaires et inversement. »
 
- Une adresse IP virtuelle accessible via Internet n’est prise en charge que sur la NIC « par défaut ». Il n’existe qu’une seule adresse IP virtuelle pour l’adresse IP de la NIC par défaut. 
- Pour l’instant, les adresses IP publiques de niveau d’instance ne sont pas prises en charge pour les machines virtuelles à plusieurs NIC. 
- L’ordre des NIC à l’intérieur de la machine virtuelle est aléatoire et peut changer lors des mises à jour de l’infrastructure Azure. Toutefois, les adresses IP et les adresses MAC Ethernet correspondantes restent identiques. Par exemple, supposons qu’**Eth1** comporte l’adresse IP 10.1.0.100 et l’adresse MAC 00-0D-3A-B0-39-0D. Après une mise à jour et un redémarrage de l’infrastructure Azure, il se peut qu’Eth1 devienne Eth2, mais l’association d’adresses IP et MAC ne change pas. Lorsqu’un redémarrage est effectué par le client, l’ordre des NIC reste identique. 
- L’adresse de chaque NIC équipant chacune des machines virtuelles doit figurer dans un sous-réseau, et les différentes NIC d’une même machine virtuelle peuvent recevoir des adresses situées dans le même sous-réseau. 
- La taille de machine virtuelle détermine le nombre de NIC que vous pouvez créer pour une machine virtuelle. Le tableau ci-dessous indique les nombres de NIC autorisés en fonction des tailles de machine virtuelle : 

|Taille de machine virtuelle (références SKU Standard)|NIC (nombre maximal autorisé par machine virtuelle)|
|---|---|
|Toutes les tailles de base|1|
|A0\très petite|1|
|A1\petite|1|
|A2\moyenne|1|
|A3\grande|2|
|A4\très grande|4|
|A5|1|
|A6|2|
|A7|4|
|A8|2|
|A9|4|
|A10|2|
|A11|4|
|D1|1|
|D2|2|
|D3|4|
|D4|8|
|D11|2|
|D12|4|
|D13|8|
|D14|16|
|DS1|1|
|DS2|2|
|DS3|4|
|DS4|8|
|DS11|2|
|DS12|4|
|DS13|8|
|DS14|16|
|G1|1|
|G2|2|
|G3|4|
|G4|8|
|G5|16|
|Tous les autres tailles|1|

## Groupes de sécurité réseau
Les NIC d’une machine virtuelle peuvent être associées à un groupe de sécurité réseau (NSG), y compris les NIC d’une machine virtuelle sur laquelle la fonctionnalité Multi-NIC est activée. Si une NIC reçoit une adresse d’un sous-réseau associé à un NSG, les règles qui régissent le NSG du sous-réseau s’appliquent également à cette NIC. Outre l’association de sous-réseaux à des NSG, vous pouvez également associer une NIC à un NSG.

Si un sous-réseau est associé à un NSG,et qu’une NIC de ce sous-réseau est liée individuellement à un NSG, les règles du NSG associé sont appliquées dans l’« **ordre du flux de trafic** » en fonction de la direction du trafic entrant ou sortant de la NIC :

- Le **trafic entrant** dont la destination est la NIC en question passe d’abord par le sous-réseau, en déclenchant les règles du NSG du sous-réseau, puis transite par la NIC et déclenche les règles du NSG de la NIC. - Le **trafic sortant** dont la source est la NIC en question commence par sortir de la NIC, en déclenchant les règles du NSG du sous-réseau, puis transite par le sous-réseau, et déclenche alors les règles du NSG du sous-réseau. 

La figure ci-dessus représente le mode d’application des règles du NSG en fonction du flux de trafic (de la machine virtuelle vers le sous-réseau, ou du sous-réseau vers la machine virtuelle).

## Configuration d’une machine virtuelle à plusieurs NIC

Les instructions ci-dessous expliquent comment créer une machine virtuelle Multi-NIC contenant 3 NIC : une NIC par défaut et deux NIC supplémentaires. Cette procédure de configuration crée une machine virtuelle qui sera configurée en fonction du fragment de fichier de configuration de service ci-dessous :

	<VirtualNetworkSite name="MultiNIC-VNet" Location="North Europe">
	<AddressSpace>
	  <AddressPrefix>10.1.0.0/16</AddressPrefix>
	    </AddressSpace>
	    <Subnets>
	      <Subnet name="Frontend">
	        <AddressPrefix>10.1.0.0/24</AddressPrefix>
	      </Subnet>
	      <Subnet name="Midtier">
	        <AddressPrefix>10.1.1.0/24</AddressPrefix>
	      </Subnet>
	      <Subnet name="Backend">
	        <AddressPrefix>10.1.2.0/23</AddressPrefix>
	      </Subnet>
	      <Subnet name="GatewaySubnet">
	        <AddressPrefix>10.1.200.0/28</AddressPrefix>
	      </Subnet>
	    </Subnets>
	… Skip over the remainder section …
	</VirtualNetworkSite>


La configuration requise pour l’exécution des commandes PowerShell de cet exemple est la suivante :

- Un abonnement Azure.
- Un réseau virtuel configuré. Pour plus d’informations sur les réseaux virtuels, voir l’article [Présentation du réseau virtuel](https://msdn.microsoft.com/library/azure/jj156007.aspx).
- La dernière version d’Azure PowerShell téléchargée et installée. Consultez [Installation et configuration d’Azure PowerShell](../install-configure-powershell).

1. Sélectionnez une image de machine virtuelle dans la galerie d’images de machine virtuelle Azure. Notez que les images changent fréquemment et sont disponibles par région. L’image indiquée dans l’exemple ci-dessous étant susceptible de changer ou de ne pas être disponible dans votre région, veillez à spécifier l’image dont vous avez besoin. 

	    $image = Get-AzureVMImage `
	    	-ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201410.01-en.us-127GB.vhd"

1. Créez une configuration de machine virtuelle.

		$vm = New-AzureVMConfig -Name "MultiNicVM" -InstanceSize "ExtraLarge" `
			-Image $image.ImageName –AvailabilitySetName "MyAVSet"

1. Créez la connexion d’administrateur par défaut.

		Add-AzureProvisioningConfig –VM $vm -Windows -AdminUserName "<YourAdminUID>" `
			-Password "<YourAdminPassword>"

1. Ajoutez des NIC à la configuration de machine virtuelle.

		Add-AzureNetworkInterfaceConfig -Name "Ethernet1" `
			-SubnetName "Midtier" -StaticVNetIPAddress "10.1.1.111" -VM $vm 
		Add-AzureNetworkInterfaceConfig -Name "Ethernet2" `
			-SubnetName "Backend" -StaticVNetIPAddress "10.1.2.222" -VM $vm

1. Spécifiez le sous-réseau et l’adresse IP de la NIC par défaut.

		Set-AzureSubnet -SubnetNames "Frontend" -VM $vm Set-AzureStaticVNetIP  `
			-IPAddress "10.1.0.100" -VM $vm

1. Créez la machine virtuelle dans votre réseau virtuel.

		New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm

>[AZURE.NOTE]Le réseau virtuel que vous spécifiez ici doit déjà exister (comme indiqué dans la configuration requise). L’exemple ci-dessous spécifie un réseau virtuel nommé « MultiNIC-VNet ».

## Voir aussi

[Présentation du réseau virtuel](https://msdn.microsoft.com/library/azure/jj156007.aspx).

[Tâches de configuration du réseau virtuel](https://msdn.microsoft.com/library/azure/jj156206.aspx)

[Billet de blog : Plusieurs NIC de machine virtuelle et appliances de réseau virtuel dans Azure](../multiple-vm-nics-and-network-virtual-appliances-in-azure) (en anglais)

<!---HONumber=58--> 