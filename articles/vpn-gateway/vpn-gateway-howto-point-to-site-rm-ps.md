<properties 
   pageTitle="Configurer une connexion de passerelle VPN point à site à un réseau virtuel Azure | Microsoft Azure"
   description="Connectez-vous de façon sécurisée à votre réseau virtuel Azure en créant une connexion VPN point à site. Cette configuration est utile si vous avez besoin d’une connexion intersite à partir d’un emplacement distant sans utiliser de périphérique VPN. Elle peut également être utilisée avec des configurations réseau hybrides. Cet article contient des instructions PowerShell pour les réseaux virtuels qui ont été créés à l’aide du modèle de déploiement de Resource Manager."
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
   ms.date="01/11/2016"
   ms.author="cherylmc" />

# Configurer une connexion point à site à un réseau virtuel à l’aide de PowerShell

> [AZURE.SELECTOR]
- [PowerShell - Resource Manager](vpn-gateway-howto-point-to-site-rm-ps.md)
- [PowerShell - Classic](vpn-gateway-point-to-site-create.md)

Une configuration de point à site vous permet de créer individuellement une connexion sécurisée à votre réseau virtuel à partir d’un ordinateur client. Le démarrage de la connexion à partir de l’ordinateur client permet d’établir une connexion VPN. Une connexion point à site est une excellente solution lorsque vous souhaitez vous connecter à votre réseau virtuel à partir d’un site distant, par exemple depuis votre domicile ou une conférence, ou lorsque seuls quelques clients doivent se connecter à un réseau virtuel. Les connexions de ce type ne nécessitent pas de périphérique VPN ou d’adresse IP publique pour fonctionner. Pour plus d’informations sur les connexions de point à site, consultez la page [FAQ sur la passerelle VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) et [À propos des connexions intersites](vpn-gateway-cross-premises-options.md).

Cet article s’applique aux réseaux virtuels et aux passerelles VPN créés à l’aide du modèle de déploiement d’**Azure Resource Manager**. Si vous souhaitez configurer une connexion point à site pour un réseau virtuel créé à l’aide de Service Management (également connu sous le nom de modèle de déploiement classique), consultez [cet article](vpn-gateway-point-to-site-create.md).

[AZURE.INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## À propos de cette configuration

Dans ce scénario, vous allez créer un réseau virtuel avec une connexion point à site. Une connexion de ce type est composée des éléments suivants : réseau virtuel avec une passerelle, un fichier .cer de certificat racine chargé, un certificat client et la configuration VPN côté client.

Nous allons utiliser les valeurs suivantes pour cette configuration :

- Nom = **TestVNet** , utilisant les espaces d’adressage 192.168.0.0/16 et 10.254.0.0/16. Notez que vous pouvez utiliser plusieurs espaces d’adressage pour un réseau virtuel.
- Nom du sous-réseau = **FrontEnd**, utilisant 192.168.1.0/24
- Nom du sous-réseau = **BackEnd**, utilisant 10.254.1.0/24
- Nom du sous-réseau = **GatewaySubnet**, utilisant 192.168.200.0/24. Le nom du sous-réseau *GatewaySubnet* est obligatoire pour que la passerelle fonctionne. 
- Pool d’adresses des clients VPN : 172.16.201.0/24. Les clients VPN qui se connectent au réseau virtuel à l’aide de cette connexion point à site reçoivent une adresse IP de ce pool.
- Abonnement = Vérifiez que vous disposez de l’abonnement approprié si vous en possédez plusieurs.
- Groupe de ressources = **TestRG**
- Emplacement = **East US**
- Serveur DNS = Adresse IP du serveur DNS que vous souhaitez utiliser pour la résolution de noms.
- Nom de passerelle = **GW**
- Nom d’adresse IP publique = **GWIP**
- Type de VPN = **RouteBased**


## Avant tout chose

Vérifiez que vous disposez d’un abonnement Azure et que vous avez installé les applets de commande Azure PowerShell nécessaires pour cette configuration (1.0.2 ou ultérieure). Si vous ne possédez pas déjà un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou vous inscrire à une [évaluation gratuite](http://azure.microsoft.com/pricing/free-trial/).
	
**À propos de l’installation des modules d’applet de commande PowerShell**

	[AZURE.INCLUDE [vpn-gateway-ps-rm-howto](../../includes/vpn-gateway-ps-rm-howto-include.md)]

## Configurer une connexion point à site pour Azure

1. Dans la console PowerShell, connectez-vous à votre compte Azure. Cette applet de commande vous invite à saisir vos informations d’identification de connexion pour votre compte Azure. Une fois que vous êtes connecté, l’applet de commande télécharge vos paramètres de compte pour qu’ils soient reconnus par Azure PowerShell.

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

		$vnet   = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

9. Demandez une adresse IP publique attribuée dynamiquement. Cette adresse IP est nécessaire au bon fonctionnement de la passerelle. Vous connecterez ultérieurement la passerelle à la configuration IP de passerelle.
		
		$pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
		$ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
		
10. Chargez un fichier .cer de certificat racine vers Azure. Vous pouvez utiliser un certificat racine de votre environnement de certificat d’entreprise ou un certificat racine auto-signé. Vous pouvez télécharger jusqu’à 20 certificats racine. Pour connaître les instructions à suivre pour créer un certificat racine auto-signé à l’aide de *makecert*, reportez-vous à la section située à la fin de cet article. Notez que le fichier .cer ne doit pas contenir la clé privée du certificat racine.
	
	Ci-après figure un exemple de ce à quoi il ressemble. La difficulté du chargement des données de certificat public tient au fait que vous devez copier et coller la chaîne entière sans espaces. Dans le cas contraire, le chargement ne fonctionne pas. Vous devez utiliser votre propre fichier .cer de certificat pour cette étape. N’essayez pas de copier et coller l’exemple ci-dessous.

		$MyP2SRootCertPubKeyBase64 = "MIIDUzCCAj+gAwIBAgIQRggGmrpGj4pCblTanQRNUjAJBgUrDgMCHQUAMDQxEjAQBgNVBAoTCU1pY3Jvc29mdDEeMBwGA1UEAxMVQnJrIExpdGUgVGVzdCBSb290IENBMB4XDTEzMDExOTAwMjQxOFoXDTIxMDExOTAwMjQxN1owNDESMBAGA1UEChMJTWljcm9zb2Z0MR4wHAYDVQQDExVCcmsgTGl0ZSBUZXN0IFJvb3QgQ0EwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQC7SmE+iPULK0Rs7mQBO/6a6B6/G9BaMxHgDGzAmSG0Qsyt5e08aqgFnPdkMl3zRJw3lPKGha/JCvHRNrO8UpeAfc4IXWaqxx2iBipHjwmHPHh7+VB8lU0EJcUe7WBAI2n/sgfCwc+xKtuyRVlOhT6qw/nAi8e5don/iHPU6q7GCcnqoqtceQ/pJ8m66cvAnxwJlBFOTninhb2VjtvOfMQ07zPP+ZuYDPxvX5v3nd6yDa98yW4dZPuiGO2s6zJAfOPT2BrtyvLekItnSgAw3U5C0bOb+8XVKaDZQXbGEtOw6NZvD4L2yLd47nGkN2QXloiPLGyetrj3Z2pZYcrZBo8hAgMBAAGjaTBnMGUGA1UdAQReMFyAEOncRAPNcvJDoe4WP/gH2U+hNjA0MRIwEAYDVQQKEwlNaWNyb3NvZnQxHjAcBgNVBAMTFUJyayBMaXRlIFRlc3QgUm9vdCBDQYIQRggGmrpGj4pCblTanQRNUjAJBgUrDgMCHQUAA4IBAQCGyHhMdygS0g2tEUtRT4KFM+qqUY5HBpbIXNAav1a1dmXpHQCziuuxxzu3iq4XwnWUF1OabdDE2cpxNDOWxSsIxfEBf9ifaoz/O1ToJ0K757q2Rm2NWqQ7bNN8ArhvkNWa95S9gk9ZHZLUcjqanf0F8taJCYgzcbUSp+VBe9DcN89sJpYvfiBiAsMVqGPc/fHJgTScK+8QYrTRMubtFmXHbzBSO/KTAP5rBTxse88EGjK5F8wcedvge2Ksk6XjL3sZ19+Oj8KTQ72wihN900p1WQldHrrnbixSpmHBXbHr9U0NQigrJp5NphfuU5j81C8ixvfUdwyLmTv7rNA7GTAD"
		$p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $MyP2SRootCertPubKeyBase64

11. Créez la passerelle de réseau virtuel pour votre réseau virtuel. Le paramètre GatewayType doit être défini sur la valeur Vpn, tandis que le paramètre VpnType doit être défini sur la valeur RouteBased.

		New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Standard -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## Configuration de client

Chaque client qui se connecte à Azure à l’aide d’une connexion point à site doit remplir deux conditions : le client VPN doit être configuré pour se connecter, et il doit disposer d’un certificat client installé. Des packages de configuration de client VPN sont disponibles pour les clients Windows. Pour plus d’informations, consultez le [FAQ sur la passerelle VPN](vpn-gateway-vpn-faq.md#point-to-site-connections).

1. Téléchargez le package de configuration du client VPN. Dans cette étape, utilisez l’exemple suivant pour télécharger le package de configuration du client.

		Get-AzureRmVpnClientPackage -ResourceGroupName $RG -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

	L’applet de commande PowerShell renvoie un lien URL. Effectuez un copier-coller de ce lien renvoyé dans un navigateur web pour télécharger le package sur votre ordinateur. Ci-après figure un exemple d’URL renvoyée.

    	"https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
	
2. Générez et installez les certificats clients (*.pfx) créés à partir du certificat racine sur les ordinateurs clients. Vous pouvez choisir la méthode d’installation qui vous est familière. Si vous utilisez un certificat racine auto-signé et que vous ne savez pas comment procéder, vous pouvez vous reporter aux instructions indiquées à la fin de cet article.

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

## Pour créer un certificat racine auto-signé à l’aide de makecert

Makecert est l’une des méthodes permettant de créer un certificat racine auto-signé. Les étapes ci-dessous vous guident tout au long de la procédure.

1. Sur un ordinateur exécutant Windows 10, téléchargez et installez le Kit de développement logiciel Windows 10 à partir de [ce lien](https://dev.windows.com/fr-FR/downloads/windows-10-sdk).

2. Après l’installation, vous trouvez l’utilitaire makecert.exe dans ce chemin d’accès : C:\\Program Files (x86)\\Windows Kits\\10\\bin<arch>.
		
	Exemple :
	
		C:\Program Files (x86)\Windows Kits\10\bin\x64\makecert.exe

3. Créez et installez un certificat racine dans le magasin de certificats personnels sur votre ordinateur. L’exemple ci-dessous illustre la création d’un fichier *.cer* correspondant que vous chargerez plus tard. Exécutez la commande suivante en tant qu’administrateur, où *RootCertificateName* est le nom que vous souhaitez utiliser pour le certificat.

	Remarque : l’exécution de l’exemple suivant sans aucune modification entraîne la création d’un certificat racine et du fichier *RootCertificateName.cer* correspondant. Vous trouverez le fichier .cer dans le répertoire à partir duquel vous avez exécuté la commande. Le certificat se trouve dans votre dossier Certificates : Current User\\Personal\\Certificates.

    	makecert -sky exchange -r -n "CN=RootCertificateName" -pe -a sha1 -len 2048 -ss My "RootCertificateName.cer"

	>[AZURE.NOTE]Comme vous avez créé un certificat racine permettant de générer des certificats clients, il peut être utile d'exporter ce certificat avec sa clé privée et de l'enregistrer à un emplacement sûr à partir duquel il pourra être récupéré.

## Pour générer un certificat client à partir d’un certificat racine auto-signé

Les étapes ci-dessous vous aideront à générer un certificat client à partir d’un certificat racine auto-signé, puis à l’exporter et à l’installer.

### Générer un certificat client

Les étapes ci-dessous vous guident dans la génération d’un certificat client à partir d’un certificat racine auto-signé. Vous pouvez générer plusieurs certificats clients à partir d’un même certificat racine. Chaque certificat client peut alors être exporté et installé sur l’ordinateur client.

1. Sur l’ordinateur que vous avez utilisé pour créer le certificat racine auto-signé, ouvrez une invite de commandes en tant qu’administrateur.
2. Accédez à l’emplacement où vous souhaitez enregistrer le fichier de certificat client. *RootCertificateName* fait référence au certificat racine auto-signé que vous avez généré. Si vous exécutez l’exemple suivant (en remplaçant « RootCertificateName » par le nom de votre certificat racine), cette opération entraînera la création d’un certificat client nommé « ClientCertificateName » dans votre magasin de certificats Personnel.
3. Tapez la commande suivante :

    	makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "RootCertificateName" -is my -a sha1

4. Tous les certificats sont stockés sur votre ordinateur, dans votre dossier Certificates : Current User\\Personal\\Certificates store. Vous pouvez générer autant de certificats clients que nécessaire d’après cette procédure.

### Exporter et installer un certificat client

L’installation d’un certificat client sur chaque ordinateur que vous souhaitez connecter au réseau virtuel est une étape obligatoire. Les étapes ci-dessous vont vous guider dans l’installation du certificat client manuellement.

1. Pour exporter un certificat client, utilisez *certmgr.msc*. Cliquez avec le bouton droit sur le certificat client à exporter, cliquez sur **Toutes les tâches**, puis cliquez sur **Exporter**. L’Assistant Exportation de certificat s’ouvre.
2. Dans l’Assistant, cliquez sur **Suivant**, sélectionnez **Oui, exporter la clé privée**, puis cliquez sur **Suivant**.
3. Dans la page **Format de fichier d’exportation**, vous pouvez laisser les valeurs par défaut sélectionnées. Cliquez ensuite sur **Suivant**.  
4. Dans la page **Sécurité**, vous devez protéger la clé privée. Si vous choisissez d’utiliser un mot de passe, veillez à enregistrer ou à mémoriser celui que vous définissez pour ce certificat, puis cliquez sur **Suivant**.
5. Dans **Fichier à exporter**, cliquez sur **Parcourir** pour accéder à l’emplacement vers lequel vous souhaitez exporter le certificat. Pour la zone **Nom de fichier**, nommez le fichier de certificat. Cliquez ensuite sur **Suivant**.
6. Cliquez sur **Terminer** pour exporter le certificat.	
3. Recherchez le fichier *.pfx* et copiez-le sur l’ordinateur client. Sur l’ordinateur client, double-cliquez sur le fichier *.pfx* à installer. Laissez la zone **Emplacement du magasin** définie en tant qu’**Utilisateur actuel**, puis cliquez sur **Suivant**.
4. N’apportez aucune modification à la page **Fichier à importer**. Cliquez sur **Next**.
5. Dans la page **Protection de clé privée**, entrez le mot de passe du certificat si vous en avez utilisé un, ou vérifiez que le principal de sécurité qui installe le certificat est correct, puis cliquez sur **Suivant**.
6. Dans la page **Magasin de certificats**, laissez l’emplacement par défaut, puis cliquez sur **Suivant**.
7. Cliquez sur **Terminer**. Dans la page **Avertissement de sécurité** relative à l’installation du certificat, cliquez sur **Oui**. Le certificat est désormais importé.

## Étapes suivantes

Vous pouvez ajouter une machine virtuelle à votre réseau virtuel. Consultez [Création d’une machine virtuelle](../virtual-machines/virtual-machines-windows-tutorial.md) pour connaître les différentes étapes.

<!---HONumber=AcomDC_0114_2016-->