<properties 
   pageTitle="Adresses IP pour les machines virtuelles - PowerShell | Microsoft Azure"
   description="Apprenez à affecter des adresses IP à une machine virtuelle à l’aide d’Azure PowerShell."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="jdial" />

# Affecter plusieurs adresses IP aux machines virtuelles

Une ou plusieurs interfaces réseau peuvent être attachées à une machine virtuelle Azure. Une ou plusieurs adresses IP publiques ou privées peuvent être affectées à chaque interface réseau. Si vous n’êtes pas familiarisé avec les adresses IP dans Azure, lisez l’article [Adresses IP dans Azure](virtual-network-ip-addresses-overview-arm.md) pour en savoir plus. Cet article explique comment utiliser Azure PowerShell pour affecter plusieurs adresses IP à une interface réseau dans le modèle de déploiement Azure Resource Manager.

L’affectation de plusieurs adresses IP à une interface réseau permet à la machine virtuelle :

- d’héberger plusieurs sites web ou services avec des adresses IP différentes et des certificats SSL sur un serveur unique ;
- de jouer le rôle d’une appliance virtuelle réseau, telle qu’un pare-feu ou un équilibreur de charge.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

Pour obtenir la version préliminaire, envoyez un e-mail à [Plusieurs adresses IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) en indiquant votre ID d’abonnement et l’utilisation prévue.

## <a name = "create"></a>Créer une machine virtuelle avec plusieurs adresses IP

1. Ouvrez une invite de commandes PowerShell et effectuez les étapes restantes de cette section dans une même session PowerShell. Si vous n’avez pas installé, ni configuré PowerShell, effectuez les étapes de l’article [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

2. Remplacez les « valeurs » des $Variables suivantes par le nom de l’interface réseau, le [groupe de ressources](../resource-group-overview.md#resource-groups) auquel vous souhaitez l’affecter et l’[emplacement](https://azure.microsoft.com/regions) Azure de destination.

		$NicName     = "VM1-NIC1"
		$NicRgName   = "RG1"
		$NicLocation = "westus"

	Si vous ne connaissez pas le nom d’un emplacement ou d’un groupe de ressources Azure, tapez les commandes suivantes :

		Get-AzureRmLocation 	 | Format-Table Location
		Get-AzureRmResourceGroup | Format-Table ResourceGroupName	
 
3. <a name="subnet"></a>L’interface réseau doit être connectée à un sous-réseau au sein d’un réseau virtuel Azure existant dans le même emplacement et avec le même [abonnement](../azure-glossary-cloud-terminology.md#subscription) que l’interface réseau. Si vous n’êtes pas familiarisé avec les réseaux virtuels, consultez l’article [Présentation du réseau virtuel](virtual-networks-overview.md) pour en savoir plus sur ce sujet ou consultez l’article [Créer un réseau virtuel](virtual-networks-create-vnet-arm-ps.md) pour apprendre à créer un réseau virtuel. Remplacez les « valeurs » des $Variables suivantes par le nom du réseau virtuel et du sous-réseau auxquels connecter l’interface réseau et par le nom du groupe de ressources dans lequel le réseau virtuel réside.

		$VNetName   = "VNet1"
		$SubnetName = "Subnet1"
		$VNetRgName = "Network"

	Si vous ne connaissez pas le nom d’un réseau virtuel existant, entrez la commande suivante et remplacez *VNet1* dans la variable précédente par le nom d’un réseau virtuel :
		
		Get-AzureRmVirtualNetwork | Format-Table Name
		
	Si la liste renvoyée est vide, vous devez créer un réseau virtuel. Pour en savoir plus, consultez l’article [Créer un réseau virtuel](virtual-networks-create-vnet-arm-ps.md).

	Tapez les commandes suivantes pour obtenir le nom des sous-réseaux du réseau virtuel et remplacez *Subnet1* ci-dessus par le nom du sous-réseau :
		
		$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $VNetRgName
		$VNet.Subnets | Format-Table Name, AddressPrefix

4. Entrez la commande suivante pour récupérer le sous-réseau et l’affecter à une variable.

		$Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }

5. <a name="ipconfigs"></a>Définissez les configurations IP à affecter à l’interface réseau. Chaque configuration peut avoir une adresse IP statique ou dynamique privée et une ressource d’adresse IP publique associée avec une adresse statique ou dynamique.

	Les exemples de configuration suivants sont créés et affectés à une interface réseau à laquelle trois adresses IP privées et une adresse IP publique sont affectées.

	- **IPConfig-1** : une adresse IP privée dynamique (par défaut) et une adresse IP publique émanant de la ressource d’adresses IP publiques nommée *PIP1*.
	- **IPConfig-2** : une adresse IP privée statique et aucune adresse IP publique.
	- **IPConfig-3** : une adresse IP privée dynamique et aucune adresse IP publique.

	Ajoutez ou supprimez un certain nombre de configurations parmi les suivantes, selon le nombre d’adresses IP à associer à l’interface réseau et les paramètres que vous souhaitez configurer.

	**IPConfig-1**

	Remplacez la valeur *PIP1* par le nom d’une ressource d’adresse IP publique qui existe dans l’emplacement où vous créez l’interface réseau et qui n’est pas associée à une autre interface réseau. Remplacez *RG1* par le nom du groupe de ressources dans lequel la ressource d’adresse IP publique existe. Remplacez *IPConfig-1* par le nom que vous voulez attribuer à la première configuration IP. Entrez les commandes suivantes :

		$PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName "RG1"

		$IpConfigName1 = "IPConfig-1"
		$IPConfig1     = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName1 -Subnet $Subnet -PublicIpAddress $PIP1 -Primary 

	Notez la présence du commutateur *-Primary*. Lorsque vous affectez plusieurs configurations IP à une interface réseau, une configuration doit être affectée en tant que configuration *principale*. Si vous ne connaissez pas le nom d’une ressource d’adresse IP publique existante, entrez les commandes suivantes :

		Get-AzureRMPublicIPAddress |Format-Table Name, Location, IPAddress, IpConfiguration

	Si la colonne **IPConfiguration** du résultat est vide, la ressource d’adresse IP publique peut être utilisée, car elle n’est pas associée à une interface réseau existante. Si la liste est vide ou ne contient aucune ressource d’adresse IP publique disponible, vous pouvez en créer une à l’aide de la commande **New-AzureRmPublicIPAddress**.

	>[AZURE.NOTE] Les adresses IP publiques ont un coût nominal. Pour en savoir plus, lisez la page [Tarification des adresses IP](https://azure.microsoft.com/pricing/details/ip-addresses).

	**IPConfig-2**

	Remplacez *IPConfig-2* par le nom que vous souhaitez donner à la seconde configuration IP et remplacez *10.0.0.5* par une adresse IP valide inutilisée pour le sous-réseau que vous affectez à l’interface réseau. Entrez les commandes suivantes :

		$IPConfigName2 = "IPConfig-2"
		$IPAddress = 10.0.0.5

		$IPConfig2 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName2 -Subnet $Subnet -PrivateIpAddress

	Si vous ne connaissez pas la plage d’adresses IP affectée au sous-réseau, entrez la commande suivante :

		$VNet.Subnets | Format-Table Name, AddressPrefix
		
	**IPConfig-3**

	Remplacez *IPConfig-3* par le nom que vous voulez attribuer à la troisième configuration IP, puis entrez les commandes suivantes :

		$IPConfigName3 = "IPConfig-3"
		$IPConfig3 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName3 -Subnet $Subnet
		
	>[AZURE.NOTE] Vous pouvez affecter jusqu’à 250 adresses IP privées à une interface réseau. Il existe une limite au nombre d’adresses IP publiques qui peuvent être utilisées dans un abonnement. Pour plus d’informations, consultez l’article [Limites d’Azure](../azure-subscription-service-limits.md#networking-limits---azure-resource-manager).

6. Créez l’interface réseau en utilisant les configurations IP définies à l’étape précédente.

		$nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRgName -Location $NicLocation -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3

7. Attachez l’interface réseau lors de la création d’une machine virtuelle en suivant les étapes de l’article [Créer une machine virtuelle](../virtual-machines/virtual-machines-windows-ps-create.md). Bien que l’article porte sur la création d’une machine virtuelle exécutant Windows Server, les étapes sont identiques pour une machine virtuelle sous Linux hormis que le système d’exploitation est différent. Effectuez les étapes 1 à 3 de l’article. Ignorez les étapes 4 et 5, puis exécutez l’étape 6 de l’article Créer une machine virtuelle.

	>[AZURE.WARNING] L’étape 6 de l’article Créer une machine virtuelle échoue si vous avez remplacé la variable $nic par autre chose ou si vous n’avez pas effectué les étapes précédentes de cet article.

8. Pour afficher les adresses IP privées qu’Azure DHCP a affectées à l’interface réseau et la ressource d’adresse IP publique affectée à celle-ci, entrez la commande suivante :

		$nic.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary

9. <a name="os"></a>À la configuration TCP/IP du système d’exploitation, ajoutez manuellement toutes les adresses IP privées secondaires (adresses IP auxquelles est associé le mot clé *False* dans la colonne **Principale** de la sortie affichée à l’étape précédente). L’adresse IP privée affectée à *IPConfig-1* à l’étape 5 est automatiquement affectée au système d’exploitation par le biais d’Azure DHCP, car il s’agit de la configuration *principale*.

	**Windows**

	1. Tapez ipconfig /all à l’invite de commandes. Seule l’adresse IP privée *principale* est visible (via DHCP).
	2. Ouvrez les propriétés de la carte réseau.
	3. Ouvrez les propriétés de **Internet Protocol Version 4**.
		- Cliquez sur **Utiliser l’adresse IP suivante** et entrez les valeurs suivantes :
			- **Adresse IP** : entrez l’adresse IP privée *principale*.
			- **Masque de sous-réseau** : définissez cette option en fonction de votre sous-réseau. Par exemple, si le sous-réseau est un sous-réseau /24, le masque de sous-réseau est 255.255.255.0.
			- **Passerelle par défaut** : première adresse IP du sous-réseau. Si votre sous-réseau est 10.0.0.0/24m, l’adresse IP de la passerelle est 10.0.0.1.
		- Cliquez sur **Utiliser l’adresse de serveur DNS suivante** et saisissez les valeurs ci-dessous :
			- **Serveur DNS préféré :** saisissez 168.63.129.16 si vous n’utilisez pas votre propre serveur DNS. Dans le cas contraire, entrez l’adresse IP de votre serveur DNS.
		- Cliquez sur le bouton **Avancé** et ajoutez des adresses IP supplémentaires. Ajoutez chacune des adresses IP privées secondaires de l’étape 8 à l’interface réseau avec le même sous-réseau que celui de l’adresse IP principale.
		- Cliquez sur **OK** pour fermer les paramètres TCP/IP, puis sur **OK** à nouveau pour fermer les paramètres de la carte réseau.
	4. Tapez *ipconfig /all* à l’invite de commandes. Toutes les adresses IP que vous avez ajoutées sont affichées et le protocole DHCP est désactivé.

	**Linux (Ubuntu)**
	
	1. Ouvrez une fenêtre de terminal.
	2. Assurez-vous d’être l’utilisateur root. Si ce n’est pas le cas, vous pouvez utiliser la commande suivante :
	
			sudo -i 
	3. Mettez à jour le fichier de configuration de l’interface réseau (en supposant que « eth0 » est utilisé).
		- Conservez l’élément de ligne existant pour dhcp. Cela configure l’adresse IP principale qui était utilisée antérieurement.
		- Ajoutez une configuration pour une adresse IP statique supplémentaire à l’aide des commandes suivantes :

				cd /etc/network/interfaces.d/
				ls

		Un fichier .cfg doit s’afficher.
	4. Ouvrez le fichier : vi *nom\_de\_fichier*.

		Les lignes suivantes doivent figurer à la fin du fichier :

			auto eth0
			iface eth0 inet dhcp
	5. Ajoutez les lignes suivantes après les lignes qui existent dans ce fichier :

			iface eth0 inet static
			address <your private IP address here>
	6. Enregistrez le fichier à l’aide de la commande suivante :

			:wq
	7.  Réinitialisez l’interface réseau à l’aide de la commande suivante :

			sudo ifdown eth0 && sudo ifup eth0

		>[AZURE.IMPORTANT]Exécutez les scripts ifup et ifdown sur la même ligne si vous utilisez une connexion à distance.
	8. Vérifiez que l’adresse IP est ajoutée à l’interface réseau à l’aide de la commande suivante :

			ip addr list eth0

		Vous devez voir l’adresse IP que vous avez ajoutée à la liste.

	**Linux (Redhat, CentOS, etc.)**
	
	1. Ouvrez une fenêtre de terminal.
	2. Assurez-vous d’être l’utilisateur root. Si ce n’est pas le cas, vous pouvez utiliser la commande suivante :

			sudo -i
	3. Entrez votre mot de passe et suivez les instructions qui s’affichent. Une fois que vous êtes l’utilisateur root, accédez au dossier Scripts réseau avec la commande suivante :

			cd /etc/sysconfig/network-scripts
	4. Répertoriez les fichiers ifcfg connexes à l’aide de la commande suivante :

			ls ifcfg-*

		Vous devez voir *ifcfg-eth0* dans la liste de fichiers.
	5. Copiez le fichier *ifcfg-eth0* et nommez-le *ifcfg-eth0:0* à l’aide de la commande suivante :

			cp ifcfg-eth0 ifcfg-eth0:0
	6. Modifiez le fichier *ifcfg-eth0:0* à l’aide de la commande suivante :

			vi ifcfg-eth1
	7. Donnez à l’appareil le nom approprié figurant dans le fichier. *eth0:0* dans ce cas, avec la commande suivante :

			DEVICE=eth0:0
	8. Modifiez la ligne *IPADDR = YourPrivateIPAddress* pour y indiquer l’adresse IP.
	9. Enregistrez le fichier à l’aide de la commande suivante :

			(:wq)
	10. Redémarrez les services réseau et vérifiez que les modifications ont été appliquées en exécutant les commandes suivantes :

			/etc/init.d/network restart
			Ipconfig

		Vous devez voir l’adresse IP que vous avez ajoutée à la liste, dans le cas présent *eth0:0*.

## <a name="add"></a>Ajouter des adresses IP à une machine virtuelle existante

Pour ajouter des adresses IP supplémentaires à une interface réseau, procédez comme suit :

1. Ouvrez une invite de commandes PowerShell et effectuez les étapes restantes de cette section dans une même session PowerShell. Si vous n’avez pas installé, ni configuré PowerShell, effectuez les étapes de l’article [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

2. Remplacez les « valeurs » des $Variables suivantes par le nom de l’interface réseau à laquelle ajouter les adresses IP et le groupe de ressources et l’emplacement dans lesquels l’interface réseau réside :

		$NicName     = "RG1-VM1-NI1"
		$NicRgName   = "RG1"
		$NicLocation = "westus"

	Si vous ne connaissez pas le nom de l’interface réseau que vous souhaitez modifier, entrez les commandes suivantes, puis modifiez les valeurs des variables précédentes :

		Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location

3. Créez une variable et affectez-lui l’interface réseau à l’aide de la commande suivante :

		$nic = Get-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRgName

4. Récupérez l’ID de sous-réseau auquel l’interface réseau est connectée en suivant les instructions de l’[étape 3](#subnet) de la section Créer une machine virtuelle avec plusieurs adresses IP dans cet article.

5. Créez les configurations IP que vous souhaitez ajouter au réseau en suivant les instructions de l’[étape 5](#ipconfigs) de la section Créer une machine virtuelle avec plusieurs adresses IP dans cet article.

6. Remplacez *$IPConfigName4* par le nom de la configuration IP que vous avez créée à l’étape précédente. Pour ajouter la configuration, entrez la commande suivante :

		Add-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName4 -NetworkInterface $nic -Subnet $Subnet1 

7. Pour configurer l’interface réseau avec la configuration IP, entrez la commande suivante :

		Set-AzureRmNetworkInterface -NetworkInterface $nic

8. Ajoutez les adresses IP que vous avez ajoutées à l’interface réseau pour le système d’exploitation de la machine virtuelle en suivant les instructions de l’[étape 9](#os) de la section Créer une machine virtuelle avec plusieurs adresses IP dans cet article.

<!---HONumber=AcomDC_0928_2016-->