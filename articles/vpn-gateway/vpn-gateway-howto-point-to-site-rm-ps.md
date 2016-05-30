<properties 
   pageTitle="Configurer une connexion de passerelle VPN point à site à un réseau virtuel Azure | Microsoft Azure"
   description="Connectez-vous de façon sécurisée à votre réseau virtuel Azure en créant une connexion VPN point à site. Cette configuration est utile si vous avez besoin d'une connexion intersite à partir d'un emplacement distant sans utiliser de périphérique VPN. Elle peut également être utilisée avec des configurations réseau hybrides. Cet article contient des instructions PowerShell pour les réseaux virtuels qui ont été créés à l’aide du modèle de déploiement de Resource Manager."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/30/2016"
   ms.author="cherylmc" />

# Configurer une connexion point à site à un réseau virtuel à l’aide de PowerShell

> [AZURE.SELECTOR]
- [PowerShell - Resource Manager](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Portail - Azure Classic](vpn-gateway-point-to-site-create.md)

Une configuration de point à site vous permet de créer individuellement une connexion sécurisée à votre réseau virtuel à partir d’un ordinateur client. Le démarrage de la connexion à partir de l’ordinateur client permet d’établir une connexion VPN. Une connexion point à site est une excellente solution lorsque vous souhaitez vous connecter à votre réseau virtuel à partir d’un site distant, par exemple depuis votre domicile ou une conférence, ou lorsque seuls quelques clients doivent se connecter à un réseau virtuel.

Les connexions de ce type ne nécessitent pas de périphérique VPN ou d’adresse IP publique pour fonctionner. Pour plus d’informations sur les connexions de point à site, consultez la page [FAQ sur la passerelle VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) et [À propos des connexions entre locaux](vpn-gateway-cross-premises-options.md).

Cet article s’applique aux connexions de passerelle VPN de point à site à un réseau virtuel créées en utilisant le **modèle de déploiement Resource Manager** (Gestion des services).

**À propos des modèles de déploiement Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

**Modèles de déploiement et outils pour les connexions de point à site**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

![Diagramme point à site](./media/vpn-gateway-point-to-site-create/point2site.png "point à site")


## À propos de cette configuration

Dans ce scénario, vous allez créer un réseau virtuel avec une connexion point à site. Une connexion de ce type est composée des éléments suivants : réseau virtuel avec une passerelle, un fichier .cer de certificat racine chargé, un certificat client et la configuration VPN côté client.

Nous allons utiliser les valeurs suivantes pour cette configuration :

- Nom : **TestVNet**, utilisant les espaces d’adressage **192.168.0.0/16** et **10.254.0.0/16**. Notez que vous pouvez utiliser plusieurs espaces d’adressage pour un réseau virtuel.
- Nom du sous-réseau : **FrontEnd**, utilisant **192.168.1.0/24**
- Nom du sous-réseau : **BackEnd**, utilisant **10.254.1.0/24**
- Nom du sous-réseau : **GatewaySubnet**, utilisant **192.168.200.0/24**. Le nom du sous-réseau *GatewaySubnet* est obligatoire pour que la passerelle fonctionne. 
- Pool d’adresses des clients VPN : **172.16.201.0/24**. Les clients VPN qui se connectent au réseau virtuel à l’aide de cette connexion point à site reçoivent une adresse IP de ce pool.
- Abonnement : Vérifiez que vous disposez de l'abonnement approprié si vous en possédez plusieurs.
- Groupe de ressources : **TestRG**
- Emplacement : **États-Unis de l’Est**
- Serveur DNS : **Adresse IP** du serveur DNS que vous souhaitez utiliser pour la résolution de noms.
- Nom de passerelle : **GW**
- Nom d’adresse IP publique : **GWIP**
- Type de VPN : **RouteBased**


## Avant tout chose

- Assurez-vous de disposer d’un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
	
- Vous aurez besoin d’installer les applets de commande PowerShell Azure Resource Manager (version 1.0.2 ou ultérieure). Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

## Configurer une connexion point à site pour Azure

1. Dans la console PowerShell, connectez-vous à votre compte Azure. Cette applet de commande vous invite à saisir vos informations d’identification de connexion pour votre compte Azure. Une fois que vous êtes connecté, l'applet de commande télécharge vos paramètres de compte pour qu'ils soient reconnus par Azure PowerShell.

		Login-AzureRmAccount 

2. Obtenez la liste de vos abonnements Azure.

		Get-AzureRmSubscription

3. Spécifiez l’abonnement à utiliser.

		Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. Dans cette configuration, les variables PowerShell suivantes sont déclarées avec les valeurs de votre choix. Les valeurs déclarées seront utilisées dans les exemples de script. Déclarez les valeurs que vous souhaitez utiliser. Utilisez l’exemple ci-dessous en utilisant vos propres valeurs si nécessaire.

		$VNetName  = "TestVNet"
		$FESubName = "FrontEnd"
		$BESubName = "Backend"
		$GWSubName = "GatewaySubnet"
		$VNetPrefix1 = "192.168.0.0/16"
		$VNetPrefix2 = "10.254.0.0/16"
		$FESubPrefix = "192.168.1.0/24"
		$BESubPrefix = "10.254.1.0/24"
		$GWSubPrefix = "192.168.200.0/26"
		$VPNClientAddressPool = "172.16.201.0/24"
		$RG = "TestRG"
		$Location = "East US"
		$DNS = "8.8.8.8"
		$GWName = "GW"
		$GWIPName = "GWIP"
		$GWIPconfName = "gwipconf"
    	$P2SRootCertName = "ARMP2SRootCert.cer"

5. Créez un groupe de ressources.

		New-AzureRmResourceGroup -Name $RG -Location $Location

6. Créez les configurations de sous-réseau du réseau virtuel en les nommant *FrontEnd*, *BackEnd* et *GatewaySubnet*. Notez que ces préfixes doivent faire partie de l’espace d’adressage du réseau virtuel déclaré ci-dessus.

		$fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
		$besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
		$gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix

7. Création du réseau virtuel. Notez que le serveur DNS spécifié doit pouvoir résoudre les noms des ressources auxquelles vous vous connectez. Pour cet exemple, nous avons utilisé une adresse IP publique, mais vous pouvez y insérer vos propres valeurs.

		New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS

8. Spécifiez les variables pour le réseau virtuel que vous venez de créer.

		$vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

9. Demandez une adresse IP publique attribuée dynamiquement. Cette adresse IP est nécessaire au bon fonctionnement de la passerelle. Vous connecterez ultérieurement la passerelle à la configuration IP de passerelle.
		
		$pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
		$ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
		
10. Chargez un fichier .cer de certificat racine vers Azure. Vous pouvez utiliser un certificat racine de votre environnement de certificat d’entreprise ou un certificat racine auto-signé. Vous pouvez télécharger jusqu’à 20 certificats racine. Pour obtenir des instructions sur la création d’un certificat racine auto-signé à l’aide de *makecert*, consultez [Utilisation des certificats racine auto-signés pour les configurations point à site](vpn-gateway-certificates-point-to-site.md). Notez que le fichier .cer ne doit pas contenir la clé privée du certificat racine. Pour obtenir la clé publique comme indiqué dans l’exemple ci-dessous, exportez le fichier CER au format X.509 codé en base 64, puis ouvrez-le dans le bloc-notes. Là, copiez tout entre -----BEGIN CERTIFICATE----- et -----END CERTIFICATE-----
	
	Ci-après figure un exemple de ce à quoi il ressemble. La difficulté du chargement des données de certificat public tient au fait que vous devez copier et coller la chaîne entière sans espaces. Dans le cas contraire, le chargement ne fonctionne pas. Vous devez utiliser votre propre fichier .cer de certificat pour cette étape. N’essayez pas de copier et coller l’exemple ci-dessous.

		$MyP2SRootCertPubKeyBase64 = "MIIDUzCCAj+gAwIBAgIQRggGmrpGj4pCblTanQRNUjAJBgUrDgMCHQUAMDQxEjAQBgNVBAoTCU1pY3Jvc29mdDEeMBwGA1UEAxMVQnJrIExpdGUgVGVzdCBSb290IENBMB4XDTEzMDExOTAwMjQxOFoXDTIxMDExOTAwMjQxN1owNDESMBAGA1UEChMJTWljcm9zb2Z0MR4wHAYDVQQDExVCcmsgTGl0ZSBUZXN0IFJvb3QgQ0EwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQC7SmE+iPULK0Rs7mQBO/6a6B6/G9BaMxHgDGzAmSG0Qsyt5e08aqgFnPdkMl3zRJw3lPKGha/JCvHRNrO8UpeAfc4IXWaqxx2iBipHjwmHPHh7+VB8lU0EJcUe7WBAI2n/sgfCwc+xKtuyRVlOhT6qw/nAi8e5don/iHPU6q7GCcnqoqtceQ/pJ8m66cvAnxwJlBFOTninhb2VjtvOfMQ07zPP+ZuYDPxvX5v3nd6yDa98yW4dZPuiGO2s6zJAfOPT2BrtyvLekItnSgAw3U5C0bOb+8XVKaDZQXbGEtOw6NZvD4L2yLd47nGkN2QXloiPLGyetrj3Z2pZYcrZBo8hAgMBAAGjaTBnMGUGA1UdAQReMFyAEOncRAPNcvJDoe4WP/gH2U+hNjA0MRIwEAYDVQQKEwlNaWNyb3NvZnQxHjAcBgNVBAMTFUJyayBMaXRlIFRlc3QgUm9vdCBDQYIQRggGmrpGj4pCblTanQRNUjAJBgUrDgMCHQUAA4IBAQCGyHhMdygS0g2tEUtRT4KFM+qqUY5HBpbIXNAav1a1dmXpHQCziuuxxzu3iq4XwnWUF1OabdDE2cpxNDOWxSsIxfEBf9ifaoz/O1ToJ0K757q2Rm2NWqQ7bNN8ArhvkNWa95S9gk9ZHZLUcjqanf0F8taJCYgzcbUSp+VBe9DcN89sJpYvfiBiAsMVqGPc/fHJgTScK+8QYrTRMubtFmXHbzBSO/KTAP5rBTxse88EGjK5F8wcedvge2Ksk6XjL3sZ19+Oj8KTQ72wihN900p1WQldHrrnbixSpmHBXbHr9U0NQigrJp5NphfuU5j81C8ixvfUdwyLmTv7rNA7GTAD"
		$p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $MyP2SRootCertPubKeyBase64

11. Créez la passerelle de réseau virtuel pour votre réseau virtuel. Le paramètre GatewayType doit être défini sur la valeur Vpn, tandis que le paramètre VpnType doit être défini sur la valeur RouteBased.

		New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Standard -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## Configuration de client

Chaque client qui se connecte à Azure à l’aide d’une connexion point à site doit remplir deux conditions : le client VPN doit être configuré pour se connecter, et il doit disposer d’un certificat client installé. Des packages de configuration de client VPN sont disponibles pour les clients Windows. Pour plus d’informations, consultez le [FAQ sur la passerelle VPN](vpn-gateway-vpn-faq.md#point-to-site-connections).

1. Téléchargez le package de configuration du client VPN. Dans cette étape, utilisez l’exemple suivant pour télécharger le package de configuration du client.

		Get-AzureRmVpnClientPackage -ResourceGroupName $RG -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

	L’applet de commande PowerShell renvoie un lien URL. Effectuez un copier-coller de ce lien renvoyé dans un navigateur web pour télécharger le package sur votre ordinateur. Ci-après figure un exemple d’URL renvoyée.

    	"https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
	
2. Générez et installez les certificats clients (*.pfx) créés à partir du certificat racine sur les ordinateurs clients. Vous pouvez choisir la méthode d’installation qui vous est familière. Si vous utilisez un certificat racine auto-signé et que vous ne savez pas comment effectuer cette opération, vous pouvez consulter [Utilisation des certificats racine auto-signés pour les configurations point à site](vpn-gateway-certificates-point-to-site.md).

3. Pour vous connecter à votre réseau virtuel, sur l’ordinateur client, accédez aux connexions VPN et recherchez celle que vous venez de créer. Elle porte le même nom que votre réseau virtuel. Cliquez sur **Connecter**. Un message contextuel faisant référence à l’utilisation du certificat peut s’afficher. Le cas échéant, cliquez sur **Continuer** pour utiliser des privilèges élevés.

4. Dans la page de statut **Connexion**, cliquez sur **Connecter** pour démarrer la connexion. Si un écran **Sélectionner un certificat** apparaît, vérifiez que le certificat client affiché est celui que vous souhaitez utiliser pour la connexion. Dans le cas contraire, utilisez la flèche déroulante pour sélectionner le certificat approprié, puis cliquez sur **OK**.

5. À présent, votre connexion doit être établie. Vous pouvez vérifier votre connexion en suivant la procédure ci-dessous.

## Vérifier votre connexion

1. Pour vérifier que votre connexion VPN est active, ouvrez une invite de commandes avec élévation de privilèges, puis exécutez *ipconfig/all*.

2. Affichez les résultats. Notez que l’adresse IP que vous avez reçue est l’une des adresses du pool d’adresses de client VPN point à site que vous avez spécifiées dans votre configuration. Les résultats doivent être semblables à ce qui suit :
    
		PPP adapter VNetEast:
			Connection-specific DNS Suffix .:
			Description.....................: VNetEast
			Physical Address................:
			DHCP Enabled....................: No
			Autoconfiguration Enabled.......: Yes
			IPv4 Address....................: 172.16.201.3(Preferred)
			Subnet Mask.....................: 255.255.255.255
			Default Gateway.................:
			NetBIOS over Tcpip..............: Enabled

## Pour ajouter ou supprimer un certificat racine

Les certificats sont utilisés pour authentifier les clients VPN pour les VPN point à site. La procédure suivante vous guidera dans l'ajout et la suppression des certificats racine.

### Ajout d'un certificat racine

Vous pouvez ajouter jusqu'à 20 certificats racine dans Azure. Suivez les étapes ci-dessous pour ajouter un certificat racine.

1. Créez et préparez le nouveau certificat racine pour le téléchargement.

		$P2SRootCertName2 = "ARMP2SRootCert2.cer"
		$MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

2. Téléchargez le nouveau certificat racine. Notez que vous ne pouvez ajouter qu'un seul certificat racine à la fois.

		Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2

3. Vous pouvez vérifier que le nouveau certificat a été correctement ajouté à l'aide de l'applet de commande suivante.

		Get-AzureRmVpnClientRootCertificate -ResourceGroupName $RG -VirtualNetworkGatewayName $GWName

### Suppression d'un certificat racine

Vous pouvez supprimer un certificat racine à partir d'Azure. Lorsque vous supprimez un certificat racine, les certificats clients qui ont été générés à partir du certificat racine ne pourront plus se connecter à Azure via une connexion point à site avant d'avoir installé un certificat client qui est généré à partir d'un certificat racine valide dans Azure.

1. Supprimez un certificat racine.

		Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

 
2. Utilisez l'applet de commande suivante pour vérifier que le certificat a été supprimé avec succès.

		Get-AzureRmVpnClientRootCertificate -ResourceGroupName $RG -VirtualNetworkGatewayName $GWName

## Gestion de la liste des certificats clients révoqués

Vous pouvez révoquer des certificats clients. La liste de révocation de certificat vous permet de refuser sélectivement la connexion point à site en fonction des certificats clients individuels. Tandis que la suppression d'un certificat racine à partir d'Azure révoque l'accès pour tous les certificats clients généré/signés par le certificat racine révoqué, la révocation d'un certificat client vous permet de supprimer l'accès d'un certificat particulier. La pratique courante consiste à utiliser le certificat racine pour gérer l'accès au niveaux de l'équipe ou de l'organisation, tout en utilisant des certificats clients révoqués pour le contrôle d'accès précis des utilisateurs individuels.

### Révocation d'un certificat client

1. Obtenez l'empreinte numérique du certificat du client à révoquer.

		$RevokedClientCert1 = "ClientCert1"
		$RevokedThumbprint1 = "‎ef2af033d0686820f5a3c74804d167b88b69982f"

2. Ajoutez l'empreinte numérique à la liste d'empreintes numériques révoquées.

		Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

3. Vérifiez que l'empreinte numérique a été ajoutée à la liste de révocation de certificats. Vous devez ajouter une empreinte numérique à la fois.

		Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### Réactivation d'un certificat client

Vous pouvez réactiver un certificat client en supprimant l'empreinte numérique de la liste des certificats clients révoqués.

1.  Supprimez l'empreinte numérique de la liste d'empreintes numériques de certificats clients révoqués.

		Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

2. Vérifiez si l'empreinte numérique est supprimée de la liste de révocation.

		Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## Étapes suivantes

Vous pouvez ajouter une machine virtuelle à votre réseau virtuel. Consultez [Création d’une machine virtuelle](../virtual-machines/virtual-machines-windows-hero-tutorial.md) pour connaître les différentes étapes.

<!---HONumber=AcomDC_0518_2016-->