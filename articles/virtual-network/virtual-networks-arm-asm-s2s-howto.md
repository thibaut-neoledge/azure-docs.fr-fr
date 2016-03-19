<properties 
   pageTitle="Connexion de réseaux virtuels classiques à des réseaux virtuels ARM dans Azure"
   description="Apprenez à créer une connexion VPN entre les réseaux virtuels classiques et les nouveaux réseaux virtuels"
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
   ms.date="12/11/2015"
   ms.author="telmos" />

# Connexion de réseaux virtuels classiques aux nouveaux réseaux virtuels

Azure propose actuellement deux modes de gestion : Azure Service Manager (appelé classique) et Azure Resource Manager (ARM). Si vous utilisez Azure depuis un certain temps, vous avez probablement des machines virtuelles Azure et des rôles d'instance en cours d'exécution sur un réseau virtuel classique. Il est possible que vos nouvelles machines virtuelles et instances de rôle s'exécutent sur un réseau virtuel créé dans ARM.

Dans ce cas, vous devez vous assurer que la nouvelle infrastructure est en mesure de communiquer avec vos ressources classiques. Pour ce faire, vous pouvez configurer une connexion VPN entre les deux réseaux virtuels.

Dans cet article, vous allez apprendre à créer une connexion VPN de site à site (S2S) entre un réseau virtuel classique et un réseau virtuel ARM.

>[AZURE.NOTE]Cet article part du principe que vous avez déjà des réseaux virtuels classiques et ARM et que vous savez configurer une connexion VPN S2S pour les réseaux virtuels classiques. Pour une solution de bout en bout détaillée sur la connectivité VPN S2S entre réseaux virtuels classiques et ARM, consultez [Guide de solutions : connexion d’un réseau virtuel classique à un réseau virtuel ARM à l’aide d’un VPN S2S](../virtual-networks-arm-asm-s2s.md).

Vous pouvez consulter une vue d'ensemble des tâches à effectuer pour créer une connexion VPN S2S entre un réseau virtuel classique et un réseau virtuel ARM en utilisant des passerelles Azure ci-dessous.

1 : [création d’une passerelle VPN pour le réseau virtuel classique](#Step-1:-Create-a-VPN-gateway-for-the-classic-VNet)

2 : [création d’une passerelle VPN pour le réseau virtuel ARM](#Step-2:-Create-a-VPN-gateway-for-the-ARM-VNet)

3 : [création d’une connexion entre les passerelles](#Step-3:-Create-a-connection-between-the-gateways)

## Étape 1 : création d’une passerelle VPN pour le réseau virtuel classique

Pour créer la passerelle VPN pour le réseau virtuel classique, suivez les instructions ci-dessous.

1. Ouvrez le portail classique depuis https://manage.windowsazure.com et entrez vos informations d'identification si nécessaire.
2. Dans le coin gauche inférieur de l'écran, cliquez sur le bouton **NOUVEAU**, puis sur **SERVICES RÉSEAU**, puis sur**RÉSEAUX VIRTUELS**, puis sur**AJOUTER UN RÉSEAU LOCAL**.
3. Dans la fenêtre **Spécifier les détails de votre réseau local**, tapez un nom pour le réseau virtuel ARM auquel vous souhaitez vous connecter, puis dans le coin inférieur droit de la fenêtre, cliquez sur la flèche.
3. Dans la zone de texte **IP DE DÉPART** de l'espace d'adressage, saisissez le préfixe de réseau pour le réseau virtuel ARM auquel vous souhaitez vous connecter. 
4. Dans la liste déroulante **CIDR (NOMBRE D'ADRESSES)**, sélectionnez le nombre de bits utilisés pour la partie réseau du bloc CIDR utilisé par le réseau virtuel ARM auquel vous souhaitez vous connecter.
5. Dans **ADRESSE IP DU PÉRIPHÉRIQUE VPN (FACULTATIF)**, saisissez une adresse IP publique valide. Nous modifierons cette adresse IP ultérieurement. Cliquez ensuite sur la coche en bas à droite de l'écran. La figure ci-dessous illustre des exemples de paramètres pour ce scénario.

	![Paramètres de réseau local](..\virtual-network\media\virtual-networks-arm-asm-s2s-howto\figurex1.png)

5. Sur la page **réseaux**, cliquez sur **RÉSEAUX VIRTUELS**, puis sur votre réseau virtuel classique, puis sur **CONFIGURER**.
6. Sous **Connectivité de site à site**, cochez la case **Se connecter au réseau local**.
7. Sélectionnez le réseau local que vous avez créé à l'étape 4 dans la liste déroulante de réseaux disponibles **RÉSEAU LOCAL**, puis cliquez sur **ENREGISTRER**.
8. Une fois vos paramètres enregistrés, cliquez sur **TABLEAU DE BORD**, puis sur **CRÉER UNE PASSERELLE** au bas de la page, puis sur **ROUTAGE DYNAMIQUE**, puis sur **OUI**.
9. Attendez que la passerelle soit créée et copiez son adresse IP publique. Vous en aurez besoin pour configurer la passerelle dans le réseau virtuel ARM.

## Étape 2 : création d’une passerelle VPN pour le réseau virtuel ARM

Pour créer une passerelle VPN pour le réseau virtuel ARM, suivez les instructions ci-dessous.

1. À partir d'une console PowerShell, exécutez la commande suivante pour passer au mode ARM.

		Switch-AzureMode AzureResourceManager

2. Créez un réseau local en exécutant la commande ci-dessous. Le réseau local doit utiliser le bloc CIDR du réseau virtuel classique auquel vous voulez vous connecter et l'adresse IP publique de la passerelle créée à l'étape 1 ci-dessus.

		New-AzureLocalNetworkGateway -Name VNetClassicNetwork `
			-Location "East US" -AddressPrefix "10.0.0.0/20" `
			-GatewayIpAddress "168.62.190.190" -ResourceGroupName RG1

3. Créez une adresse IP publique pour la passerelle en exécutant la commande ci-dessous.

		$ipaddress = New-AzurePublicIpAddress -Name gatewaypubIP`
			-ResourceGroupName RG1 -Location "East US" `
			-AllocationMethod Dynamic

4. Récupérez le sous-réseau utilisé pour la passerelle en exécutant la commande ci-dessous.

		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name GatewaySubnet `
			-VirtualNetwork (Get-AzureVirtualNetwork -Name VNetARM -ResourceGroupName RG1) 

	>[AZURE.IMPORTANT]Le sous-réseau de passerelle doit déjà exister et doit être nommé GatewaySubnet.

5. Créez un objet de configuration IP pour la passerelle en exécutant la commande ci-dessous. Vous voyez l'ID d'un sous-réseau de passerelle. Ce sous-réseau doit exister dans le réseau virtuel.

		$ipconfig = New-AzureVirtualNetworkGatewayIpConfig `
			-Name ipconfig -PrivateIpAddress 10.1.2.4 `
			-SubnetId $subnet.id -PublicIpAddressId $ipaddress.id

	>[AZURE.IMPORTANT]La propriété ID du sous-réseau et des objets d'adresse IP doit être transmis aux paramètres *SubnetId* et *PublicIpAddressId*. Vous ne pouvez pas utiliser une chaîne simple.
	
5. Créez la passerelle de réseau virtuel ARM en exécutant la commande ci-dessous.

		New-AzureVirtualNetworkGateway -Name v1v2Gateway -ResourceGroupName RG1 `
			-Location "East US" -GatewayType Vpn -IpConfigurations $ipconfig `
			-EnableBgp $false -VpnType RouteBased

6. Une fois la passerelle VPN créée, récupérez son adresse IP publique en exécutant la commande ci-dessous. Copiez l'adresse IP, vous en aurez besoin pour configurer le réseau local pour le réseau virtuel classique.

		Get-AzurePublicIpAddress -Name gatewaypubIP -ResourceGroupName RG1

## Étape 3 : création d’une connexion entre les passerelles

1. Ouvrez le portail classique depuis https://manage.windowsazure.com et entrez vos informations d'identification si nécessaire.
2. Dans le portail classique, faites défiler et cliquez sur **RÉSEAUX**, puis sur **RÉSEAUX LOCAUX**, puis sur le réseau virtuel ARM auquel vous souhaitez vous connecter, puis sur le bouton **MODIFIER**.
3. Dans**ADRESSE IP DU PÉRIPHÉRIQUE VPN (FACULTATIF)**, saisissez l’adresse IP de la passerelle de réseau virtuel ARM obtenue à l'étape 2, puis cliquez sur la flèche vers la droite dans le coin inférieur droit, puis sur la coche.
4. À partir d'une console PowerShell, exécutez la commande suivante pour passer au mode Azure Service Manager.

		Switch-AzureMode AzureServiceManager

5. Configurez une clé partagée en exécutant la commande ci-dessous. Veillez à remplacer les noms des réseaux virtuels par les noms de vos réseaux virtuels.

		Set-AzureVNetGatewayKey -VNetName VNetClassic `
			-LocalNetworkSiteName VNetARM -SharedKey abc123

6. À partir d'une console PowerShell, exécutez la commande suivante pour passer au mode Azure Resource Manager.

		Switch-AzureMode AzureResourceManager

7. Créez la connexion VPN en exécutant les commandes ci-dessous.

		$vnet01gateway = Get-AzureLocalNetworkGateway -Name VNetClassic -ResourceGroupName RG1
		$vnet02gateway = Get-AzureVirtualNetworkGateway -Name v1v2Gateway -ResourceGroupName RG1
		
		New-AzureVirtualNetworkGatewayConnection -Name arm-asm-s2s-connection `
			-ResourceGroupName RG1 -Location "East US" -VirtualNetworkGateway1 $vnet02gateway `
			-LocalNetworkGateway2 $vnet01gateway -ConnectionType IPsec `
			-RoutingWeight 10 -SharedKey 'abc123'

## Étapes suivantes

- En savoir plus sur [le fournisseur de ressources réseau (NRP) pour ARM](../resource-groups-networking.md).
- Créer une [solution de bout en bout pour connecter un réseau virtuel classique à un réseau virtuel ARM à l’aide d’un VPN S2S](../virtual-networks-arm-asm-s2s.md).

<!---HONumber=AcomDC_1217_2015-->