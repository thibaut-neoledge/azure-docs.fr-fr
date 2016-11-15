---
title: "Configuration d’une connexion par passerelle VPN point à site à un réseau virtuel à l’aide du modèle de déploiement Resource Manager | Microsoft Docs"
description: "Connectez-vous de façon sécurisée à votre réseau virtuel Azure en créant une connexion par passerelle VPN point à site."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 3eddadf6-2e96-48c4-87c6-52a146faeec6
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/17/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 43bb33c1a4386108056b909bef9608087167a30a


---
# <a name="configure-a-pointtosite-connection-to-a-vnet-using-powershell"></a>Configuration d’une connexion de point à site à un réseau virtuel à l’aide de PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - Portail Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Classic - Portail Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Une configuration point à site (P2S) vous permet de connecter de manière sécurisée un ordinateur client individuel à un réseau virtuel. Une connexion P2S est utile lorsque vous souhaitez vous connecter à votre réseau virtuel à partir d’un site distant, comme depuis votre domicile ou une conférence ou lorsque seulement quelques clients doivent se connecter à un réseau virtuel. 

Les connexions de ce type ne nécessitent pas de périphérique VPN ou d’adresse IP publique pour fonctionner. Le démarrage de la connexion à partir de l’ordinateur client permet d’établir une connexion VPN. Pour plus d’informations sur les connexions de point à site, consultez la page [FAQ sur la passerelle VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) et [Planifier et concevoir](vpn-gateway-plan-design.md). 

Cet article vous guide dans le processus de création d’un réseau virtuel avec une connexion point à site dans le modèle de déploiement Resource Manager à l’aide de PowerShell.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Méthodes et modèles de déploiement pour les connexions P2S
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Le tableau suivant présente les deux modèles de déploiement et les méthodes de déploiement disponibles pour les configurations P2S. Quand un article avec les étapes de configuration est disponible, le lien vers cet article est ajouté à ce tableau.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Flux de travail de base
![Diagramme point à site](./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "point-to-site")

Dans ce scénario, vous allez créer un réseau virtuel avec une connexion point à site. Les instructions vous aideront également à générer des certificats, qui sont nécessaires pour cette configuration. Une connexion P2S est composée des éléments suivants : un réseau virtuel avec une passerelle VPN, un fichier .cer de certificat racine (clé publique), un certificat client et la configuration VPN sur le client. 

Pour cette configuration, nous utilisons les valeurs suivantes. Nous avons défini les variables dans la section [1](#declare) de l’article. Vous pouvez suivre les étapes proposées en utilisant les valeurs sans les modifier ou modifier les valeurs pour les adapter à votre environnement. 

### <a name="a-nameexampleaexample-values"></a><a name="example"></a>Exemples de valeurs
* **Nom : VNet1**
* **Espace d’adressage :192.168.0.0/16** et **10.254.0.0/16**<br>Pour cet exemple, nous utilisons plusieurs espaces d’adressage pour montrer que cette configuration fonctionne avec des espaces d’adressage multiples. Toutefois, plusieurs espaces d’adressage ne sont pas nécessaires pour cette configuration.
* **Nom du sous-réseau : FrontEnd**
  * **Plage d’adresses de sous-réseau : 192.168.1.0/24**
* **Nom du sous-réseau : BackEnd**
  * **Plage d’adresses de sous-réseau : 10.254.1.0/24**
* **Nom du sous-réseau : GatewaySubnet**<br>Le nom du sous-réseau *GatewaySubnet* est obligatoire pour que la passerelle VPN fonctionne.
  * **Plage d’adresses de sous-réseau : 192.168.200.0/24** 
* **Pool d’adresses des clients VPN : 172.16.201.0/24**<br>Les clients VPN qui se connectent au réseau virtuel à l’aide de cette connexion point à site reçoivent une adresse IP de ce pool d’adresses des clients VPN.
* **Abonnement :** vérifiez que vous utilisez l’abonnement approprié si vous en possédez plusieurs.
* **Groupe de ressources : TestRG**
* **Emplacement : États-Unis de l’Est**
* **Serveur DNS : l’adresse IP** du serveur DNS que vous souhaitez utiliser pour la résolution de noms.
* **Nom de passerelle : Vnet1GW**
* **Nom d’adresse IP publique : VNet1GWPIP**
* **Type de VPN : RouteBased**

## <a name="before-beginning"></a>Avant tout chose
* Assurez-vous de disposer d’un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
* Installez la dernière version des applets de commande PowerShell Azure Resource Manager. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) . Si vous utilisez PowerShell pour cette configuration, vérifiez que vous exécutez PowerShell en tant qu’administrateur. 

## <a name="a-namedeclareapart-1-log-in-and-set-variables"></a><a name="declare"></a>Partie 1 - Connexion et définition des variables
Dans cette section, vous vous connectez et déclarez les valeurs utilisées pour cette configuration. Les valeurs déclarées sont utilisées dans les exemples de script. Modifiez les valeurs pour les adapter à votre propre environnement. Vous pouvez également utiliser les valeurs déclarées et suivre les étapes pour vous entraîner.

1. Dans la console PowerShell, connectez-vous à votre compte Azure. Cette applet de commande vous invite à saisir vos informations d’identification de connexion pour votre compte Azure. Une fois que vous êtes connecté, l'applet de commande télécharge vos paramètres de compte pour qu'ils soient reconnus par Azure PowerShell.
   
        Login-AzureRmAccount 
2. Obtenez la liste de vos abonnements Azure.
   
        Get-AzureRmSubscription
3. Spécifiez l’abonnement que vous souhaitez utiliser. 
   
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"
4. Déclarez les variables que vous souhaitez utiliser. Utilisez l’exemple ci-dessous en utilisant vos propres valeurs si nécessaire.
   
        $VNetName  = "VNet1"
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
        $GWName = "VNet1GW"
        $GWIPName = "VNet1GWPIP"
        $GWIPconfName = "gwipconf"

## <a name="a-nameconfigurevnetapart-2-configure-a-vnet"></a><a name="ConfigureVNet"></a>Partie 2 - Configuration d’un réseau virtuel
1. Créez un groupe de ressources.
   
        New-AzureRmResourceGroup -Name $RG -Location $Location
2. Créez les configurations de sous-réseau du réseau virtuel en les nommant *FrontEnd*, *BackEnd* et *GatewaySubnet*. Ces préfixes doivent faire partie de l’espace d’adressage du réseau virtuel que vous avez déclaré.
   
        $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
        $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
        $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
3. Création du réseau virtuel. Le serveur DNS spécifié doit pouvoir résoudre les noms des ressources auxquelles vous vous connectez. Pour cet exemple, nous avons utilisé une adresse IP publique. Veillez à utiliser vos propres valeurs.
   
        New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS
4. Spécifiez les variables pour le réseau virtuel que vous avez créé.
   
        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
5. Demandez une adresse IP publique attribuée dynamiquement. Cette adresse IP est nécessaire au bon fonctionnement de la passerelle. Vous connecterez ultérieurement la passerelle à la configuration IP de passerelle.
   
        $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

## <a name="a-namecertificatesapart-3-certificates"></a><a name="Certificates"></a>Partie 3 - Certificats
Les certificats sont utilisés par Azure pour authentifier les clients VPN pour les VPN point à site. Vous exportez des données de certificat public (pas la clé privée) sous forme de fichier .cer codé Base64 X.509 à partir d’un certificat racine généré par une solution de certificat d’entreprise ou d’un certificat racine auto-signé. Vous importez ensuite les données de certificat public à partir du certificat racine dans Azure. Vous devez également générer un certificat client à partir du certificat racine pour les clients. Chaque client souhaitant se connecter au réseau virtuel à l’aide d’une connexion P2S doit avoir un certificat client, qui a été généré à partir du certificat racine, installé.

### <a name="a-namecera1-obtain-the-cer-file-for-the-root-certificate"></a><a name="cer"></a>1. Obtenir le fichier .cer pour le certificat racine
Vous devez obtenir les données de certificat public pour le certificat racine que vous souhaitez utiliser.

* Si vous utilisez un système de certificat d’entreprise, il vous faut obtenir le fichier .cer pour le certificat racine que vous souhaitez utiliser. 
* Si vous n’utilisez pas de solution de certificat d’entreprise, vous devez générer un certificat racine auto-signé. Pour connaître les étapes relatives à Windows 10, référez-vous à l’article [Utilisation des certificats racine auto-signés pour les configurations point à site](vpn-gateway-certificates-point-to-site.md).

1. Pour obtenir un fichier .cer à partir d’un certificat, ouvrez **certmgr.msc** et recherchez le certificat racine. Cliquez avec le bouton droit sur le certificat racine auto-signé, puis cliquez sur **toutes les tâches** et sur **exporter**. Cette opération ouvre **l’Assistant Exportation de certificat**.
2. Dans l’Assistant, cliquez sur **Suivant**, sélectionnez **Non, ne pas exporter la clé privée**, puis cliquez sur **Suivant**.
3. Sur la page **Format de fichier d’exportation**, sélectionnez **Codé à base 64 X.509 (.cer).** Cliquez ensuite sur **Suivant**. 
4. Dans **Fichier à exporter**, cliquez sur **Parcourir** pour accéder à l’emplacement vers lequel vous souhaitez exporter le certificat. Pour la zone **Nom de fichier**, nommez le fichier de certificat. Cliquez ensuite sur **Suivant**.
5. Cliquez sur **Terminer** pour exporter le certificat.

### <a name="a-namegeneratea2-generate-the-client-certificate"></a><a name="generate"></a>2. Générer le certificat client
Ensuite, générez les certificats clients. Vous pouvez générer un certificat unique pour chaque client qui se connecte, ou utiliser le même certificat pour plusieurs clients. Générer des certificats clients uniques vous offre la possibilité de révoquer un seul certificat si nécessaire. Dans le cas contraire, si tous les clients utilisent le même certificat client et que vous devez révoquer le certificat pour un client, vous devrez générer et installer de nouveaux certificats pour tous les clients qui utilisent le certificat pour s’authentifier. Les certificats clients sont installés sur chaque ordinateur client plus loin dans cet exercice.

* Si vous utilisez une solution de certificat d’entreprise, générez un certificat client avec le format de valeur de nom commun 'name@yourdomain.com',, plutôt que le format ’DOMAINE\nom_utilisateur’ NetBIOS. 
* Si vous utilisez un certificat auto-signé, consultez [Utilisation des certificats racine auto-signés pour les configurations point à site](vpn-gateway-certificates-point-to-site.md) pour générer un certificat client.

### <a name="a-nameexportclientcerta3-export-the-client-certificate"></a><a name="exportclientcert"></a>3. Exporter le certificat client
Un certificat client est requis pour l’authentification. Après avoir généré le certificat client, exportez-le. Le certificat client que vous exportez sera installé plus tard sur chaque ordinateur client.

1. Pour exporter un certificat client, vous pouvez utiliser *certmgr.msc*. Cliquez avec le bouton droit sur le certificat client à exporter, cliquez sur **Toutes les tâches**, puis sur **Exporter**.
2. Exportez le certificat client avec la clé privée. Il s’agit d’un fichier *.pfx* . Prenez soin d’enregistrer ou de mémoriser le mot de passe (clé) que vous définissez pour ce certificat.

### <a name="a-nameuploada4-upload-the-root-certificate-cer-file"></a><a name="upload"></a>4. Charger le fichier .cer de certificat racine
Déclarez la variable pour le nom de votre certificat, en remplaçant la valeur par la vôtre :

        $P2SRootCertName = "Mycertificatename.cer"

Ajoutez les données de certificat public du certificat racine dans Azure. Vous pouvez charger des fichiers pour 20 certificats racine maximum. Vous ne chargez pas la clé privée pour le certificat racine dans Azure. Une fois le fichier .cer chargé, Azure l’utilise pour authentifier les clients qui se connectent au réseau virtuel. 

Remplacez le chemin d’accès de fichier par le vôtre et exécutez les applets de commande.

        $filePathForCert = "C:\cert\Mycertificatename.cer"
        $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
        $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
        $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## <a name="a-namecreategatewayapart-4-create-the-vpn-gateway"></a><a name="creategateway"></a>Partie 4 - Création de la passerelle VPN
Configurez et créez la passerelle de réseau virtuel pour votre réseau virtuel. Le paramètre *-GatewayType* doit être défini sur la valeur **Vpn**, tandis que le paramètre *-VpnType* doit être défini sur la valeur **RouteBased**. Cette opération peut prendre jusqu’à 45 minutes.

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
        -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
        -VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
        -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## <a name="a-nameclientconfigapart-5-download-the-vpn-client-configuration-package"></a><a name="clientconfig"></a>Partie 5 - Téléchargement du package de configuration du client VPN
Un certificat client et un package de configuration du client VPN doivent être installés sur les clients se connectant à Azure via P2S. Des packages de configuration de client VPN sont disponibles pour les clients Windows. Le package client VPN contient des informations permettant de configurer le logiciel client VPN intégré à Windows. Il est propre au VPN auquel vous souhaitez vous connecter. Le package n’installe aucun logiciel supplémentaire. Pour plus d’informations, consultez la [FAQ sur la passerelle VPN](vpn-gateway-vpn-faq.md#point-to-site-connections).

1. Une fois la passerelle créée, vous pouvez télécharger le package de configuration du client. Cet exemple télécharge le package pour les clients 64 bits. Si vous souhaitez télécharger le client 32 bits, remplacez « Amd64 » par « x86 ». Vous pouvez également télécharger le client VPN à l’aide du portail Azure.
   
        Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
        -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64
2. L’applet de commande PowerShell renvoie un lien URL. Voici un exemple d’URL renvoyée :
   
        "https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
3. Effectuez un copier-coller du lien renvoyé dans un navigateur web pour télécharger le package. Installez ensuite le package sur l’ordinateur client. Si une fenêtre contextuelle SmartScreen s’affiche, cliquez sur **Plus d’infos**, puis sur **Exécuter quand même** afin d’installer le package.
4. Sur l’ordinateur client, accédez à **Paramètres réseau**, puis cliquez sur **VPN**. La connexion apparaît dans la liste. Le nom du réseau virtuel auquel il se connectera s’affiche, sous une forme semblable à cet exemple : 
   
    ![Client VPN](./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png "VPN client")

## <a name="a-nameclientcertificateapart-6-install-the-client-certificate"></a><a name="clientcertificate"></a>Partie 6 - Installation d’un certificat client
Chaque ordinateur client doit avoir un certificat client pour s’authentifier. Lorsque vous installez le certificat client, vous avez besoin du mot de passe qui a été créé lorsque le certificat client a été exporté.

1. Copiez le fichier .pfx sur l’ordinateur client.
2. Double-cliquez sur le fichier .pfx pour l’installer. Ne modifiez pas l’emplacement d’installation.

## <a name="a-nameconnectapart-7-connect-to-azure"></a><a name="connect"></a>Partie 7 - Connexion à Azure
1. Pour vous connecter à votre réseau virtuel, sur l’ordinateur client, accédez aux connexions VPN et recherchez celle que vous avez créée. Elle porte le même nom que votre réseau virtuel. Cliquez sur **Connecter**. Un message contextuel faisant référence à l’utilisation du certificat peut s’afficher. Le cas échéant, cliquez sur **Continuer** pour utiliser des privilèges élevés. 
2. Dans la page de statut **Connexion**, cliquez sur **Connecter** pour démarrer la connexion. Si un écran **Sélectionner un certificat** apparaît, vérifiez que le certificat client affiché est celui que vous souhaitez utiliser pour la connexion. Dans le cas contraire, utilisez la flèche déroulante pour sélectionner le certificat approprié, puis cliquez sur **OK**.
   
    ![Connexion client VPN](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "VPN client connection")
3. À présent, votre connexion doit être établie.
   
    ![Connexion établie](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "Connection established")

## <a name="a-nameverifyapart-8-verify-your-connection"></a><a name="verify"></a>Partie 8 - Vérification de votre connexion
1. Pour vérifier que votre connexion VPN est active, ouvrez une invite de commandes avec élévation de privilèges, puis exécutez *ipconfig/all*.
2. Affichez les résultats. Notez que l’adresse IP que vous avez reçue est l’une des adresses du pool d’adresses de client VPN point à site que vous avez spécifiées dans votre configuration. Les résultats doivent être semblables à ce qui suit :
   
        PPP adapter VNet1:
            Connection-specific DNS Suffix .:
            Description.....................: VNet1
            Physical Address................:
            DHCP Enabled....................: No
            Autoconfiguration Enabled.......: Yes
            IPv4 Address....................: 172.16.201.3(Preferred)
            Subnet Mask.....................: 255.255.255.255
            Default Gateway.................:
            NetBIOS over Tcpip..............: Enabled

## <a name="a-nameaddremovecertato-add-or-remove-a-trusted-root-certificate"></a><a name="addremovecert"></a>Pour ajouter ou supprimer un certificat racine approuvé
Les certificats sont utilisés pour authentifier les clients VPN pour les VPN point à site. La procédure suivante vous guide dans l’ajout et la suppression des certificats racine. Lorsque vous ajoutez un fichier codé en Base64 X.509 (.cer) pour Azure, vous indiquez à Azure de faire confiance au certificat racine que le fichier représente. 

Vous pouvez ajouter ou supprimer des certificats racine approuvés à l’aide de PowerShell ou dans le portail Azure. Si vous souhaitez utiliser le portail Azure, accédez à votre **passerelle de réseau virtuel, puis sélectionnez Paramètres, Configuration de point à site, et enfin Certificats racine**. Les étapes suivantes vous guideront pour effectuer ces tâches à l’aide de PowerShell. 

### <a name="add-a-trusted-root-certificate"></a>Ajout ou suppression d’un certificat racine approuvé
Vous pouvez ajouter jusqu’à 20 fichiers .cer de certificat racine approuvés dans Azure. Suivez les étapes ci-dessous pour ajouter un certificat racine.

1. Créez et préparez le nouveau certificat racine que vous allez ajouter à Azure. Exportez la clé publique en tant que fichier X.509 codé en Base64 (.CER) et ouvrez-la dans un éditeur de texte. Copiez ensuite uniquement la section ci-dessous. 
   
    Copiez les valeurs, comme illustré dans l’exemple suivant :
   
    ![certificat](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png "certificate")
2. Indiquez le nom du certificat et les informations clés en tant que variable. Remplacez les informations par les vôtres, comme illustré dans l’exemple suivant :
   
        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
3. Ajoutez le nouveau certificat racine. Vous ne pouvez ajouter qu’un seul certificat à la fois.
   
        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
4. Vous pouvez vérifier que le nouveau certificat a été correctement ajouté à l'aide de l'applet de commande suivante.
   
        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

### <a name="to-remove-a-trusted-root-certificate"></a>Suppression d’un certificat racine approuvé
Vous pouvez supprimer un certificat racine approuvé à partir d'Azure. Lorsque vous supprimez un certificat approuvé, les certificats clients qui ont été générés à partir du certificat racine ne pourront plus se connecter à Azure via la connexion de point à site. Si vous souhaitez que des clients se connectent, ils doivent installer un nouveau certificat client généré à partir d’un certificat approuvé dans Azure.

1. Pour supprimer un certificat racine approuvé, modifiez l’exemple suivant :
   
    Déclarez les variables.
   
        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
2. Supprimez le certificat.
   
        Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
3. Utilisez l'applet de commande suivante pour vérifier que le certificat a été supprimé avec succès. 
   
        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

## <a name="a-namerevokeato-manage-the-list-of-revoked-client-certificates"></a><a name="revoke"></a>Pour gérer la liste des certificats clients révoqués
Vous pouvez révoquer des certificats clients. La liste de révocation de certificat vous permet de refuser sélectivement la connexion point à site en fonction des certificats clients individuels. Si vous supprimez un fichier .cer de certificat racine d’Azure, vous révoquez l’accès pour tous les certificats clients générés/signés par le certificat racine révoqué. Il est possible de révoquer un certificat client en particulier et non le certificat racine. Ainsi, les autres certificats générés à partir du certificat racine seront toujours valables.

La pratique courante consiste à utiliser le certificat racine pour gérer l'accès au niveaux de l'équipe ou de l'organisation, tout en utilisant des certificats clients révoqués pour le contrôle d'accès précis des utilisateurs individuels.

### <a name="revoke-a-client-certificate"></a>Révocation d'un certificat client
1. Obtenez l'empreinte numérique du certificat du client à révoquer.
   
        $RevokedClientCert1 = "ClientCert1"
        $RevokedThumbprint1 = "‎ef2af033d0686820f5a3c74804d167b88b69982f"
2. Ajoutez l'empreinte numérique à la liste d'empreintes numériques révoquées.
   
        Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
3. Vérifiez que l'empreinte numérique a été ajoutée à la liste de révocation de certificats. Vous devez ajouter une empreinte numérique à la fois.
   
        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### <a name="reinstate-a-client-certificate"></a>Réactivation d'un certificat client
Vous pouvez réactiver un certificat client en supprimant l'empreinte numérique de la liste des certificats clients révoqués.

1. Supprimez l'empreinte numérique de la liste d'empreintes numériques de certificats clients révoqués.
   
       Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
       -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
2. Vérifiez si l'empreinte numérique est supprimée de la liste de révocation.
   
        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez ajouter une machine virtuelle à votre réseau virtuel. Consultez [Création d’une machine virtuelle](../virtual-machines/virtual-machines-windows-hero-tutorial.md) pour connaître les différentes étapes.




<!--HONumber=Nov16_HO2-->


