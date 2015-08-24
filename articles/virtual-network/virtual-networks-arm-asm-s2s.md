<properties 
   pageTitle="Connexion de réseaux virtuels classiques à des réseaux virtuels ARM dans Azure : guide de solutions"
   description="Apprenez à créer une connexion VPN entre les réseaux virtuels classiques et les nouveaux réseaux virtuels"
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
   ms.date="07/14/2015"
   ms.author="telmos" />

# Connexion de réseaux virtuels classiques aux nouveaux réseaux virtuels

Azure propose actuellement deux modes de gestion : Azure Service Manager (appelé classique) et Azure Resource Manager (ARM). Si vous utilisez Azure depuis un certain temps, vous avez probablement des machines virtuelles Azure et des rôles d'instance en cours d'exécution sur un réseau virtuel classique. Il est possible que vos nouvelles machines virtuelles et instances de rôle s'exécutent sur un réseau virtuel créé dans ARM.

Dans ce cas, vous devez vous assurer que la nouvelle infrastructure est en mesure de communiquer avec vos ressources classiques. Pour ce faire, vous pouvez configurer une connexion VPN entre les deux réseaux virtuels. La figure ci-dessous illustre un exemple d'environnement à deux réseaux virtuels (classique et ARM), liés par une connexion tunnel sécurisée.

![](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure01.png)

>[AZURE.NOTE]Ce document vous guide dans une solution de bout en bout, à des fins de test. Si vous avez déjà votre configuration de réseaux virtuels et connaissez les passerelles VPN et la connexion de site à site dans Azure, consultez [Configure a S2S VPN between an ARM VNet and a classic VNet](../virtual-networks-arm-asm-s2s-howto.md).

Pour tester ce scénario, vous allez :

1. [créer un environnement de réseau virtuel classique](#Create-a-classic-VNet-environment) ;
2. [créer un nouvel environnement de réseau virtuel](#Create-a-new-VNet-environment) ;
3. [connecter les deux réseaux virtuels](#Connect-the-two-VNets).

Vous exécuterez les étapes ci-dessus à l'aide des outils de gestion d’Azure classiques dans un premier temps, y compris le portail classique, les fichiers de configuration réseau et les applets de commande PowerShell d’Azure Service Manager. Vous passerez ensuite aux nouveaux outils de gestion, y compris le nouveau portail Azure, les modèles ARM et les applets de commande PowerShell ARM.

>[AZURE.IMPORTANT]Pour que les réseaux virtuels soient connectés, leur bloc CIDR ne doit pas entrer en conflit. Assurez-vous que chaque réseau virtuel a un bloc CIDR unique !

## Création d’un environnement de réseau virtuel classique

Vous pouvez utiliser un réseau classique existant pour vous connecter à un nouveau réseau virtuel ARM. Dans cet exemple, vous allez apprendre à créer un réseau virtuel classique, avec deux sous-réseaux, une passerelle et une machine virtuelle, à des fins de test.

### Étape 1 : création d’un réseau virtuel classique

Pour créer un réseau virtuel qui est mappé à la figure 1 ci-dessus, suivez les instructions ci-dessous.

1. À partir d'une console PowerShell, ajoutez votre compte Azure en exécutant la commande ci-dessous.

		Add-AzureAccount

2. Suivez les instructions dans la boîte de dialogue de connexion pour vous connecter avec votre compte Azure.

3. Assurez-vous que vous utilisez les applets de commande PowerShell de gestion des services Azure en exécutant la commande ci-dessous.

		Switch-AzureMode AzureServiceManagement

4. Téléchargez votre fichier de configuration réseau Azure en exécutant la commande ci-dessous.

		Get-AzureVNetConfig -ExportToFile c:\Azure\classicvnets.netcfg

		XMLConfiguration                                OperationDescription                            OperationId                                     OperationStatus                                
		----------------                                --------------------                            -----------                                     ---------------                                
		<?xml version="1.0" encoding="utf-8"?>...       Get-AzureVNetConfig                             04ab3224-7e1c-cc1a-8b75-96c6c300ddb8            Succeeded       

5. Ouvrez le fichier que vous venez de télécharger et ajoutez un site réseau local nommé *vnet02* qui utilise *10.2.0.0/16* comme préfixe d'adresse et une adresse IP publique valide comme adresse de passerelle VPN. Vous pouvez le faire en ajoutant un élément **LocalNetworkSite** à l’élément **LocalNetworkSites** dans votre fichier de configuration. L'extrait de fichier ci-dessous montre un exemple d’élément **LocalNetworksSites**.

	    <LocalNetworkSites>
	      <LocalNetworkSite name="vnet02">
	        <AddressSpace>
	          <AddressPrefix>10.2.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <VPNGatewayAddress>2.0.0.2</VPNGatewayAddress>
	      </LocalNetworkSite>
	    </LocalNetworkSites>		

6. Dans l’élément **VirtualNetworkSites**, ajoutez un nouveau réseau virtuel avec le préfixe d'adresse *10.1.0.0/16* et deux sous-réseaux, conformément au scénario ci-dessus. L'extrait de fichier ci-dessous montre un exemple d’élément **VirtualNetworkSites**.
	
	    <VirtualNetworkSites>
	      <VirtualNetworkSite name="vnet01" Location="East US">
	        <AddressSpace>
	          <AddressPrefix>10.1.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <Subnets>
	          <Subnet name="Subnet1">
	            <AddressPrefix>10.1.0.0/24</AddressPrefix>
	          </Subnet>
	          <Subnet name="GatewaySubnet">
	            <AddressPrefix>10.1.200.0/29</AddressPrefix>
	          </Subnet>
	        </Subnets>
	      </VirtualNetworkSite>
	    </VirtualNetworkSites>

7. Enregistrez le fichier, puis téléchargez-le vers Azure en exécutant la commande ci-dessous. Assurez-vous que vous modifiez le chemin d'accès selon les besoins de votre environnement.

		Set-AzureVNetConfig -ConfigurationPath c:\Azure\classicvnets.netcfg

		OperationDescription                                            OperationId                                                     OperationStatus                                                
		--------------------                                            -----------                                                     ---------------                                                
		Set-AzureVNetConfig                                             e0ee6e66-9167-cfa7-a746-7cab93c22013                            Succeeded 

### Étape 2 : création d’une machine virtuelle dans le réseau virtuel classique

Pour créer une machine virtuelle dans le réseau virtuel classique à l'aide des applets de commande d’Azure Service Manager, suivez les instructions ci-dessous.

1. Récupérez une image de machine virtuelle depuis Azure. La commande PowerShell suivante récupère la dernière image Windows Server 2012 R2 disponible.

		$WinImage = (Get-AzureVMImage `
		    | ?{$_.ImageFamily -eq "Windows Server 2012 R2 Datacenter"} `
		    | sort PublishedDate -Descending)[0]		

2. Créez un compte de stockage pour stocker le disque dur virtuel de la machine virtuelle en exécutant la commande ci-dessous.

		$storage1 = New-AzureStorageAccount `
			-StorageAccountName v1v2teststorage1 `
		    -Location "East US"	

3.  Créez la machine virtuelle en exécutant les commandes ci-dessous. Veillez à remplacer les valeurs de nom et de mot de passe utilisateur.

		$vm1 = New-AzureVMConfig -Name "VM01" -InstanceSize "ExtraLarge" `
		    -Image $WinImage.ImageName –AvailabilitySetName "MyAVSet1" `
		    -MediaLocation "https://v1v2teststorage1.blob.core.windows.net/vhd/vm01.vhd"
		Add-AzureProvisioningConfig –VM $vm1 -Windows `
		    -AdminUserName "user" -Password "P@ssw0rd" 

4.  Connectez la machine virtuelle à*Subnet1* en exécutant les commandes ci-dessous.

		Set-AzureSubnet -SubnetNames "Subnet1" -VM $vm1
		Set-AzureStaticVNetIP  -IPAddress "10.1.0.101" -VM $vm1

5. Créez un service cloud pour héberger la machine virtuelle en exécutant la commande ci-dessous.

		New-AzureService -ServiceName "v1v2svc01" -Location "East US"
 		New-AzureVM -ServiceName "v1v2svc01" –VNetName "vnet01" –VMs $vm1

### Étape 3 : création d’une passerelle VPN pour le réseau virtuel classique 

Pour créer la passerelle VPN pour vnet01 à l’aide du portail Azure classique, suivez les instructions ci-dessous.

1. Lancez le portail Azure classique à l’adresse https://manage.windowsazure.com. Si nécessaire, spécifiez vos informations d'identification.
2. Faites défiler la liste de **TOUS LES ÉLÉMENTS** et cliquez sur **RÉSEAUX**.
3. Dans la liste des réseaux virtuels, cliquez sur **vnet01**, puis cliquez sur **CONFIGURER**.
4. Sous **Connectivité de site à site**, cochez la case **Se connecter au réseau local**.
5. Dans la liste **RÉSEAU LOCAL**, sélectionnez **vnet02**, puis cliquez sur **ENREGISTRER**, puis sur**OUI**.
6. Cliquez sur **TABLEAU DE BORD** et vous remarquerez le message indiquant qu’une passerelle n'a pas encore été créée, comme illustré dans la figure 2 ci-dessous.

	![Tableau de bord de réseau virtuel](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure02.png)

7. Cliquez sur **CRÉER UNE PASSERELLE** comme illustré à la figure 3 ci-dessous pour créer une passerelle VPN pour vnet01.

	![Tableau de bord de réseau virtuel](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure03.png)

8. Dans la liste des types de passerelle, cliquez sur **DYNAMIQUE**, puis sur **OUI**. Vous voyez les modifications de l'image du tableau de bord, montrant la passerelle en jaune, pendant sa création.

	![Tableau de bord de réseau virtuel](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure04.png)

	>[AZURE.NOTE]Cette opération peut prendre plusieurs minutes.

9. Notez l'adresse IP publique de la passerelle, comme illustré ci-dessous, après sa création. Vous aurez besoin de cette adresse ultérieurement pour créer un réseau local pour le réseau virtuel ARM.

	![Tableau de bord de réseau virtuel](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure05.png)

## Création d’un nouvel environnement de réseau virtuel

Maintenant que le réseau virtuel classique est en cours d'exécution, avec une machine virtuelle et une passerelle, il est temps de créer le réseau virtuel ARM.

### Étape 1 : création d’un réseau virtuel dans ARM

Pour créer le réseau virtuel ARM, avec deux sous-réseaux et un réseau local pour le réseau virtuel classique à l'aide d'un modèle ARM, suivez les instructions ci-dessous.

1. Téléchargez les fichiers azuredeploy.json et azuredeploy-parameters.json depuis [git hub](https://github.com/Azure/azure-quickstart-templates/tree/master/arm-asm-s2s).
2. Ouvrez le fichier azuredeploy.json dans Visual Studio et notez que le modèle crée quatre ressources : 

	- **Passerelle locale** : cette ressource représente la passerelle créée pour le réseau virtuel auquel vous souhaitez vous connecter. Dans ce scénario, c’est la passerelle pour vnet01.
	- **Réseau virtuel** : cette ressource représente un réseau virtuel ARM devant être créé. Dans ce scénario, cela correspond à vnet02.
	- **Adresse IP publique de la passerelle** : cette ressource représente l'adresse IP publique de la passerelle devant être créée pour le réseau virtuel ARM. 
	- **Passerelle** : cette ressource représente la passerelle devant être créée pour le réseau virtuel ARM (vnet02).

3. Notez les paramètres utilisés dans ce fichier. La plupart d'entre eux ont une valeur par défaut. Vous pouvez modifier ces valeurs selon vos besoins.

4. Ouvrez le fichier azuredeploy-parameters.json dans Visual Studio. Ce fichier contient des valeurs à utiliser pour les paramètres du fichier de modèle. Modifiez les valeurs suivantes, si nécessaire.

	- **subscriptionId** : modifiez cette valeur et collez votre ID d'abonnement. Si vous ne connaissez pas votre ID d'abonnement, exécutez l’applet de commande PowerShell **Get-AzureSubscription** pour récupérer votre ID.
	- **location** : spécifiez l'emplacement Azure où le réseau virtuel sera créé. Dans ce scénario, ce sera **Centre des États-Unis**.
	- **virtualNetworkName** : le nom du réseau virtuel ARM devant être créé. Dans ce scénario, cela correspond à **vnet02**.
	- **localGatewayName** : le réseau local auquel vous souhaitez vous connecter, à partir de votre nouveau réseau virtuel ARM. Dans ce scénario, cela correspond à **vnet01**.
	- **localGatewayIpAddress** : l'adresse IP publique de la passerelle créée pour le réseau auquel vous souhaitez vous connecter. Dans ce scénario, cela correspond à l'adresse IP que vous avez notée à l'étape 9 ci-dessus, lors de la création de la passerelle VPN pour**vnet01**.
	- **localGatewayAddressPrefix** : le bloc CIDR pour votre réseau local auquel se connecte votre réseau virtuel. Dans ce scénario, le réseau virtuel auquel vous vous connectez est **vnet01** et son bloc CIDR est **10.1.0.0/16**.
	- **gatewayPublicIPName** : le nom de l'objet IP devant être créé pour l'adresse IP publique de la passerelle qui sera créée pour le réseau virtuel ARM.
	- **gatewayName** : le nom de l'objet passerelle qui sera créé pour le réseau virtuel ARM.
	- **addressPrefix** : le bloc CIDR pour le réseau virtuel ARM. Dans ce scénario, cela correspond à **10.2.0.0/16**.
	- **subnet1Prefix** : le bloc CIDR pour un sous-réseau dans le réseau virtuel ARM. Dans ce scénario, cela correspond à **10.2.0.0/24**.
	- **gatewaySubnetPrefix** : le bloc CIDR pour le sous-réseau de passerelle dans le réseau virtuel ARM. Dans ce scénario, cela correspond à **10.2.200.0/29**.
	- **connectionName** : le nom de l'objet de connexion devant être créé.
	- **sharedKey** : la clé IPSec partagée pour la connexion. Dans ce scénario, cela correspond à **abc123**.

5. Pour créer le réseau virtuel ARM et ses objets connexes, exécutez la commande PowerShell suivante dans un nouveau groupe de ressources nommé **RG1**. Assurez-vous que vous modifiez le chemin d'accès pour le fichier de modèle et le fichier de paramètres.

		Switch-AzureMode AzureResourceManager
		New-AzureResourceGroup -Name RG1 -Location "Central US" `
		    -TemplateFile C:\Azure\azuredeploy.json `
		    -TemplateParameterFile C:\Azure\azuredeploy-parameters.json		

	>[AZURE.NOTE]Cette opération peut prendre plusieurs minutes.

7. À partir de votre navigateur, accédez à https://portal.azure.com/ et entrez vos informations d'identification, si nécessaire.
8. Cliquez sur la vignette du groupe de ressources **RG1** dans le portail Azure, comme illustré ci-dessous.

	![Tableau de bord de réseau virtuel](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure06.png)

9. Notez les ressources ajoutées au groupe à l'aide du modèle ARM.

### Étape 2 : création d’une machine virtuelle dans ARM

Pour créer une machine virtuelle dans le nouveau réseau virtuel, suivez les instructions ci-dessous dans le portail Azure.

1. Dans le portail, cliquez sur le bouton **NOUVEAU**, puis cliquez sur **Compute**, puis sur**Windows Server 2012 R2 Datacenter**.
2. En bas du volet droit, dans **Sélectionner une pile de calcul**, sélectionnez **Utiliser la pile du gestionnaire de ressources** pour créer la machine virtuelle dans ARM, comme indiqué ci-dessous, puis cliquez sur **Créer**.

	![Tableau de bord de réseau virtuel](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure07.png)

3. Dans le panneau **Informations de base**, entrez le nom de la machine virtuelle, un nom d'utilisateur, un mot de passe, un abonnement, un groupe de ressources et un emplacement de machine virtuelle, puis cliquez sur**OK**. La figure ci-dessous illustre les paramètres pour ce scénario.

	![Tableau de bord de réseau virtuel](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure08.png)

4. Dans le panneau **Choisir une taille**, sélectionnez une taille, puis cliquez sur **Sélectionner**. Pour ce scénario, sélectionnez **D2**.
5. Dans le panneau **Paramètres**, cliquez sur **Réseau virtuel**, puis sur **vnet02**.
6. Cliquez sur **Sous-réseau**, puis sur **Subnet1**, puis sur**OK**. Le panneau **Résumé** doit ressembler à la figure ci-dessous.

	![Tableau de bord de réseau virtuel](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure09.png)

7. Cliquez sur **OK**, puis sur **Créer** pour créer la machine virtuelle. Vous verrez une nouvelle vignette dans le portail, illustrant le processus d’approvisionnement de la machine virtuelle, comme indiqué ci-dessous.

	![Tableau de bord de réseau virtuel](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure10.png)

	>[AZURE.NOTE]Cette opération peut prendre plusieurs minutes. Vous pouvez passer à la partie suivante de ce document.

## Connexion des deux réseaux virtuels

Maintenant que vous disposez de deux réseaux virtuels avec des machines virtuelles, il est temps de connecter les réseaux virtuels via les passerelles précédemment établies et de tester la connexion.

### Étape 1 : configuration de la passerelle pour le réseau virtuel classique

Vous devez configurer le réseau virtuel classique de manière à utiliser l'adresse IP de la passerelle créée pour le réseau virtuel ARM (vnet02), puis établir une connexion à partir de chaque réseau virtuel. Pour ce faire, procédez comme suit :

1. Pour récupérer l'adresse IP de la passerelle dans le réseau virtuel ARM, exécutez la commande suivante et notez la sortie. Notez l'adresse, vous en aurez besoin par la suite pour modifier les paramètres de réseau local pour le réseau virtuel classique.

		Get-AzurePublicIpAddress | ?{$_.Name -eq "ArmAsmS2sGatewayIp"}

		Name                     : ArmAsmS2sGatewayIp
		ResourceGroupName        : RG1
		Location                 : centralus
		Id                       : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Microsoft.Network/publicIPAddresses/ArmAsmS2sGatewayIp
		Etag                     : W/"1ee6c1bd-8be1-488e-a571-77b05b49e33a"
		ProvisioningState        : Succeeded
		Tags                     : 
		PublicIpAllocationMethod : Dynamic
		IpAddress                : 23.99.213.28
		IdleTimeoutInMinutes     : 4
		IpConfiguration          : {
		                             "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworkGateways/ArmAsmGateway/ipConfigurations/vn
		                           etGatewayConfig"
		                           }
		DnsSettings              : null

2. Assurez-vous que vous utilisez l’API de gestion des services Azure pour vos commandes PowerShell en exécutant la commande ci-dessous.

		Switch-AzureMode AzureServiceManagement

3. Téléchargez votre fichier de configuration réseau Azure en exécutant la commande ci-dessous.

		Get-AzureVNetConfig -ExportToFile c:\Azure\classicvnets.netcfg

4. Ouvrez le fichier que vous venez de télécharger et modifiez l’élément **LocalNetworkSite** pour **vnet02** afin d’ajouter l'adresse IP de la passerelle pour le nouveau réseau virtuel obtenu au cours de l'étape 1 ci-dessus. L'élément doit ressembler à l'exemple ci-dessous.

	      <LocalNetworkSite name="vnet03">
	        <AddressSpace>
	          <AddressPrefix>10.3.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <VPNGatewayAddress>23.99.213.28</VPNGatewayAddress>
	      </LocalNetworkSite>

5. Enregistrez le fichier, puis exécutez la commande suivante pour configurer le réseau virtuel classique. Veillez à modifier le chemin d'accès pour pointer vers le fichier que vous avez enregistré à l'étape 4 ci-dessus.

		Set-AzureVNetConfig -ConfigurationPath c:\Azure\classicvnets.netcfg

6. Définissez la clé IPSec partagée pour la passerelle de réseau virtuel classique en exécutant la commande ci-dessous. Le résultat doit ressembler à ce qui suit. Si vous utilisez vos propres réseaux virtuels préexistants, veillez à modifier les noms des réseaux virtuels.

		Set-AzureVNetGatewayKey -VNetName vnet01 -LocalNetworkSiteName vnet02 -SharedKey abc123 

		Error          : 
		HttpStatusCode : OK
		Id             : b2154475-bf00-480e-ad1e-aed893014979
		Status         : Successful
		RequestId      : 08257a09d723cb8982c47b85edb0e08a
		StatusCode     : OK

### Étape 2 : configuration de la passerelle pour le réseau virtuel ARM

Maintenant que la passerelle de réseau virtuel classique est configurée, il est temps d’établir la connexion. Pour ce faire, procédez comme suit :

1. À partir d'une console PowerShell, exécutez la commande suivante pour passer au mode ARM. 

		Switch-AzureMode AzureResourceManager

2. Créez la connexion entre les passerelles, en exécutant les commandes suivantes.

		$vnet01gateway = Get-AzureLocalNetworkGateway -Name vnet01 -ResourceGroupName RG1
		$vnet02gateway = Get-AzureVirtualNetworkGateway -Name ArmAsmGateway -ResourceGroupName RG1
		
		New-AzureVirtualNetworkGatewayConnection -Name arm-asm-s2s-connection `
			-ResourceGroupName RG1 -Location "Central US" -VirtualNetworkGateway1 $vnet02gateway `
			-LocalNetworkGateway2 $vnet01gateway -ConnectionType IPsec `
			-RoutingWeight 10 -SharedKey 'abc123'

3. Ouvrez le portail Azure à l’adresse https://manage.windowsazure.com et, si nécessaire, entrez vos informations d'identification.
4. Sous **TOUS LES ÉLÉMENTS**, faites défiler vers le bas et cliquez sur **RÉSEAUX**, puis sur **vnet01**, puis sur **TABLEAU DE BORD**. Notez que la connexion entre **vnet01** et **vnet02** est maintenant établie, comme indiqué ci-dessous.

	![Tableau de bord de réseau virtuel](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure11.png)

5. Même si vous pouvez gérer le réseau virtuel classique et sa connexion à partir du portail classique, il est recommandé d'utiliser le nouveau portail Azure. Pour ouvrir le nouveau portail, accédez à https://portal.azure.com.
6. Dans le nouveau portail, cliquez sur **PARCOURIR TOUT**, puis sur **Réseaux virtuels (classiques)**, puis sur **vnet01**. Notez le panneau **Connexions VPN** illustré ci-dessous.

	![Tableau de bord de réseau virtuel](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure12.png)

### Étape 3 : test de la connectivité

Maintenant que les deux réseaux virtuels sont connectés, il est temps de tester la connectivité en effectuant un test ping, d’une machine virtuelle à l'autre. Vous devez modifier les paramètres de pare-feu dans l’une des machines virtuelles afin d’autoriser le trafic ICMP, puis effectuer un test ping de cette machine virtuelle à l’autre. Pour ce faire, procédez comme suit :

1. Dans le portail Azure, cliquez sur **PARCOURIR TOUT**, puis sur **Machines virtuelles**, puis sur **VM02**.
2. Dans le panneau **VM02**, cliquez sur **Connecter**. Si nécessaire, cliquez sur **Ouvrir** sur la bannière de sécurité de votre navigateur pour ouvrir le fichier RDP.
3. Dans la boîte de dialogue **Connexion Bureau à distance**, cliquez sur **Connecter**.
4. Dans la boîte de dialogue **Sécurité Windows**, entrez votre nom d'utilisateur de machine virtuelle et votre mot de passe. 
5. Dans la boîte de dialogue **Connexion Bureau à distance**, cliquez sur **Oui**.
6. Une fois connecté à la machine virtuelle, dans **Server Manager**, cliquez sur **Outils**, puis sur **Pare-feu Windows avec fonctions avancées de sécurité**.
7. Dans la fenêtre **Pare-feu Windows avec fonctions avancées de sécurité**, cliquez sur **Règles de trafic entrant**.
8. Dans la liste **Règles de trafic entrant**, cliquez avec le bouton droit sur **Partage de fichiers et d’imprimantes (Demande d’écho - Trafic entrant ICMPv4)**puis cliquez sur **Activer la règle**.
9. Dans la barre des tâches, cliquez sur le bouton **Windows PowerShell**, puis exécutez la commande suivante.

		ipconfig

		Connection-specific DNS Suffix  . : 4oxp4ce0c5rulb1iey4cdqhasg.gx.internal.cloudapp.net
		Link-local IPv6 Address . . . . . : fe80::8cea:a98a:5c48:4c58%12
		IPv4 Address. . . . . . . . . . . : 10.2.0.101
		Subnet Mask . . . . . . . . . . . : 255.255.255.0
		Default Gateway . . . . . . . . . : 10.2.0.101

10. Notez l'adresse IP de la machine virtuelle. Dans ce scénario, cela correspond à **10.2.0.101**. Vous effectuerez un test ping de cette adresse à partir de l’autre machine virtuelle pour tester la connectivité entre les réseaux virtuels.
11. Dans le volet gauche du portail Azure, cliquez sur **Machines virtuelles (classiques)**, puis sur **VM01**, puis sur **Connecter**. Si nécessaire, cliquez sur **Ouvrir** sur la bannière de sécurité de votre navigateur pour ouvrir le fichier RDP.
12. Dans la boîte de dialogue **Connexion Bureau à distance**, cliquez sur **Connecter**.
13. Dans la boîte de dialogue **Sécurité Windows**, entrez votre nom d'utilisateur de machine virtuelle et votre mot de passe. 
14. Dans la boîte de dialogue **Connexion Bureau à distance**, cliquez sur **Oui**.
15. Dans la barre des tâches de la machine virtuelle distante, cliquez sur le bouton **Windows PowerShell**, puis exécutez la commande suivante.

		ping 10.2.0.101

		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126
		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126
		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126
		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126

## Étapes suivantes

- En savoir plus sur [le fournisseur de ressources réseau (NRP) pour ARM](../resource-groups-networking.md).
- Afficher les instructions générales sur la [création d’une connexion VPN S2S entre un réseau virtuel classique et un réseau virtuel ARM](../virtual-networks-arm-asm-s2s-howto.md).

<!---HONumber=August15_HO7-->