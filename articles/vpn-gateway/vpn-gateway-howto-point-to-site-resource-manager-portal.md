---
title: "Connexion d’un ordinateur à un réseau virtuel Azure à l’aide d’une passerelle point à site : portail Azure | Microsoft Docs"
description: "Connectez-vous de façon sécurisée à votre réseau virtuel Azure en créant une connexion par passerelle VPN point à site à l’aide de Resource Manager et du portail Azure."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a15ad327-e236-461f-a18e-6dbedbf74943
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 70b6c9b8cb14e4e0b1162162071c03174bb150c1
ms.openlocfilehash: e95f972f7605d2af79f4bd0e43f78aac853e2f38


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Configuration d’une connexion point à site à un réseau virtuel à l’aide du portail Azure
> [!div class="op_single_selector"]
> * [Resource Manager - Portail Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Classic - Portail Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Une configuration point à site (P2S) vous permet de connecter de manière sécurisée un ordinateur client individuel à un réseau virtuel. Une connexion P2S est utile lorsque vous souhaitez vous connecter à votre réseau virtuel à partir d’un site distant, comme depuis votre domicile ou une conférence ou lorsque seulement quelques clients doivent se connecter à un réseau virtuel. 

Les connexions de ce type ne nécessitent pas de périphérique VPN ou d’adresse IP publique pour fonctionner. Le démarrage de la connexion à partir de l’ordinateur client permet d’établir une connexion VPN. Pour plus d’informations sur les connexions de point à site, consultez la page [FAQ sur la passerelle VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) et [Planifier et concevoir](vpn-gateway-plan-design.md).

Cet article vous guide dans le processus de création d’un réseau virtuel avec une connexion point à site dans le modèle de déploiement Resource Manager à l’aide du portail Azure.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Méthodes et modèles de déploiement pour les connexions P2S
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Le tableau suivant présente les deux modèles de déploiement et les méthodes de déploiement disponibles pour les configurations P2S. Quand un article avec les étapes de configuration est disponible, le lien vers cet article est ajouté à ce tableau.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Flux de travail de base
![Diagramme point à site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-connection-diagram.png)

### <a name="a-nameexampleaexample-values"></a><a name="example"></a>Exemples de valeurs
* **Nom : VNet1**
* **Espace d’adressage : 192.168.0.0/16**<br>Pour cet exemple, nous n’utilisons qu’un seul espace d’adressage. Vous pouvez avoir plusieurs espaces d’adressage pour votre réseau virtuel.
* **Nom du sous-réseau : FrontEnd**
* **Plage d’adresses de sous-réseau : 192.168.1.0/24**
* **Abonnement :** vérifiez que vous utilisez l’abonnement approprié si vous en possédez plusieurs.
* **Groupe de ressources : TestRG**
* **Emplacement : États-Unis de l’Est**
* **Sous-réseau de passerelle : 192.168.200.0/24**
* **Nom de passerelle de réseau virtuel : VNet1GW**
* **Type de passerelle : VPN**
* **Type de VPN : Route-based**
* **Adresse IP publique : VNet1GWpip**
* **Type de connexion : point à site**
* **Pool d’adresses des clients : 172.16.201.0/24**<br>Les clients VPN qui se connectent au réseau virtuel à l’aide de cette connexion point à site reçoivent une adresse IP de ce pool d’adresses des clients.

## <a name="before-beginning"></a>Avant tout chose
* Assurez-vous de disposer d’un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial).

## <a name="a-namecreatevnetapart-1---create-a-virtual-network"></a><a name="createvnet"></a>Partie 1 : création d’un réseau virtuel
Si vous créez cette configuration dans le cadre d’un exercice, vous pouvez vous reporter aux [exemples de valeurs](#example).

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="a-nameaddressapart-2---specify-address-space-and-subnets"></a><a name="address"></a>Partie 2 : Spécifier l’espace d’adressage et les sous-réseaux
Vous pouvez ajouter des sous-réseaux et des espaces d’adressage supplémentaires pour votre réseau virtuel une fois qu’il a été créé.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="a-namegatewaysubnetapart-3---add-a-gateway-subnet"></a><a name="gatewaysubnet"></a>Partie 3 : Ajout d’un sous-réseau de passerelle

Avant de connecter votre réseau virtuel à une passerelle, vous devez créer le sous-réseau de passerelle pour le réseau virtuel auquel vous souhaitez vous connecter. Si possible, il est préférable de créer un sous-réseau de passerelle à l’aide d’un bloc CIDR de /28 ou /27 afin de fournir suffisamment d’adresses IP pour satisfaire les exigences de configuration future supplémentaires.

Les captures d’écran de cette section fournissent un exemple de référence. Veillez à utiliser la plage d’adresses GatewaySubnet correspondant aux valeurs requises pour votre configuration.

###<a name="to-create-a-gateway-subnet"></a>Pour créer un sous-réseau de passerelle

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="a-namednsapart-4---specify-a-dns-server-optional"></a><a name="dns"></a>Partie 4 : Spécifier un serveur DNS (facultatif)
[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="a-namecreategwapart-5---create-a-virtual-network-gateway"></a><a name="creategw"></a>Partie 5 : Créer une passerelle de réseau virtuel
Les connexions point à site nécessitent les paramètres suivants :

* Type de passerelle : VPN
* Type de VPN : Route-based

### <a name="to-create-a-virtual-network-gateway"></a>Pour créer une passerelle de réseau virtuel
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="a-namegeneratecertapart-6---generate-certificates"></a><a name="generatecert"></a>Partie 6 : Générer des certificats
Les certificats sont utilisés par Azure pour authentifier les clients VPN pour les VPN point à site. Vous exportez des données de certificat public (pas la clé privée) sous forme de fichier .cer codé Base64 X.509 à partir d’un certificat racine généré par une solution de certificat d’entreprise ou d’un certificat racine auto-signé. Vous importez ensuite les données de certificat public à partir du certificat racine dans Azure. Vous devez également générer un certificat client à partir du certificat racine pour les clients. Chaque client souhaitant se connecter au réseau virtuel à l’aide d’une connexion P2S doit avoir un certificat client, qui a été généré à partir du certificat racine, installé.

### <a name="a-namegetcerastep-1---obtain-the-cer-file-for-the-root-certificate"></a><a name="getcer"></a>Étape 1 : Obtenir le fichier .cer pour le certificat racine
Si vous utilisez une solution d’entreprise, vous pouvez utiliser votre chaîne de certificats existante. Si vous n’utilisez pas une solution d’entreprise pour l’authentification par certificat, vous pouvez créer un certificat racine auto-signé. La méthode recommandée pour créer un certificat auto-signé pour les connexions P2S est makecert. Bien qu’il soit possible d’utiliser PowerShell pour créer des certificats auto-signés, le certificat généré à l’aide de PowerShell ne contient pas les champs nécessaires pour les connexions P2S.

* Si vous utilisez un système de certificat d’entreprise, il vous faut obtenir le fichier .cer pour le certificat racine que vous souhaitez utiliser. 
* Si vous n’utilisez pas de solution de certificat d’entreprise, vous devez générer un certificat racine auto-signé. Pour connaître les étapes relatives à Windows 10, référez-vous à l’article [Utilisation des certificats racine auto-signés pour les configurations point à site](vpn-gateway-certificates-point-to-site.md).

1. Pour obtenir un fichier .cer à partir d’un certificat, ouvrez **certmgr.msc** et recherchez le certificat racine. Cliquez avec le bouton droit sur le certificat racine auto-signé, puis cliquez sur **toutes les tâches** et sur **exporter**. Cette opération ouvre **l’Assistant Exportation de certificat**.
2. Dans l’Assistant, cliquez sur **Suivant**, sélectionnez **Non, ne pas exporter la clé privée**, puis cliquez sur **Suivant**.
3. Sur la page **Format de fichier d’exportation**, sélectionnez **Codé à base&64; X.509 (.cer).** Cliquez ensuite sur **Suivant**. 
4. Dans **Fichier à exporter**, cliquez sur **Parcourir** pour accéder à l’emplacement vers lequel vous souhaitez exporter le certificat. Pour la zone **Nom de fichier**, nommez le fichier de certificat. Cliquez ensuite sur **Suivant**.
5. Cliquez sur **Terminer** pour exporter le certificat.

### <a name="a-namegenerateclientcertastep-2---generate-a-client-certificate"></a><a name="generateclientcert"></a>Étape 2 : Générer un certificat client
Vous pouvez générer un certificat unique pour chaque client qui se connecte, ou utiliser le même certificat pour plusieurs clients. Générer des certificats clients uniques vous offre la possibilité de révoquer un seul certificat si nécessaire. Dans le cas contraire, si tous les clients utilisent le même certificat client et que vous devez révoquer le certificat pour un client, vous devrez générer et installer de nouveaux certificats pour tous les clients qui utilisent ce certificat pour s’authentifier.

* Si vous utilisez une solution de certificat d’entreprise, générez un certificat client avec le format de valeur de nom commun 'name@yourdomain.com',, plutôt que le format « nom_domaine\nom_utilisateur ». 
* Si vous utilisez un certificat auto-signé, consultez [Utilisation des certificats racine auto-signés pour les configurations point à site](vpn-gateway-certificates-point-to-site.md) pour générer un certificat client.

### <a name="a-nameexportclientcertastep-3---export-the-client-certificate"></a><a name="exportclientcert"></a>Étape 3 : Exporter le certificat client
Un certificat client est requis pour l’authentification. Après avoir généré le certificat client, exportez-le. Le certificat client que vous exportez sera installé plus tard sur chaque ordinateur client.

1. Pour exporter un certificat client, vous pouvez utiliser *certmgr.msc*. Cliquez avec le bouton droit sur le certificat client à exporter, cliquez sur **Toutes les tâches**, puis sur **Exporter**.
2. Exportez le certificat client avec la clé privée. Il s’agit d’un fichier *.pfx* . Prenez soin d’enregistrer ou de mémoriser le mot de passe (clé) que vous définissez pour ce certificat.

## <a name="a-nameaddresspoolapart-7---add-the-client-address-pool"></a><a name="addresspool"></a>Partie 7 : Ajouter le pool d’adresses des clients
1. Une fois la passerelle de réseau virtuel créée, accédez à la section **Paramètres** du panneau Passerelle de réseau virtuel. Dans la section **Paramètres**, cliquez sur **Configuration de point à site** pour ouvrir le panneau **Configuration**.
   
    ![Panneau Point à site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configuration.png)
2. **Pool d’adresses** est le pool d’adresses IP duquel les clients qui se connectent recevront une adresse IP. Ajoutez le pool d’adresses, puis cliquez sur **Enregistrer**.
   
    ![Pool d’adresses des clients](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/addresspool.png)

## <a name="a-nameuploadfileapart-8---upload-the-root-certificate-cer-file"></a><a name="uploadfile"></a>Partie 8 : Charger le fichier .cer de certificat racine
Une fois la passerelle créée, vous pouvez charger le fichier .cer pour un certificat racine approuvé dans Azure. Vous pouvez charger des fichiers pour 20 certificats racine maximum. Vous ne chargez pas la clé privée pour le certificat racine dans Azure. Une fois le fichier .cer chargé, Azure l’utilise pour authentifier les clients qui se connectent au réseau virtuel.

1. Accédez au panneau **Configuration de point à site**. Vous allez ajouter les fichiers .cer dans la section **Certificat racine** de ce panneau.
   
    ![Certificat racine du panneau Point à site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/rootcert.png)
2. Vérifiez que vous avez exporté le certificat racine en tant que fichier Base-64 codé X.509 (.cer). Vous devez l’exporter dans ce format pour pouvoir ouvrir le certificat avec un éditeur de texte.
3. Ouvrez le certificat avec un éditeur de texte, Bloc-notes par exemple. Copiez uniquement la section suivante :
   
    ![Données du certificat](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/copycert.png)
4. Collez les données du certificat dans la section **Données du certificat public** du portail. Placez le nom du certificat dans la zone **Nom**, puis cliquez sur **Enregistrer**. Vous pouvez ajouter jusqu’à 20 certificats racine approuvés.
   
    ![Chargement d’un certificat](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploadcert.png)

## <a name="a-nameclientconfigapart-9---download-and-install-the-vpn-client-configuration-package"></a><a name="clientconfig"></a>Partie 9 : Télécharger et installer le package de configuration du client VPN
Un certificat client et un package de configuration du client VPN doivent être installés sur les clients se connectant à Azure via P2S. Des packages de configuration de client VPN sont disponibles pour les clients Windows. 

Le package client VPN contient des informations pour configurer le logiciel client VPN qui est intégré à Windows. La configuration est spécifique au VPN auquel vous souhaitez vous connecter. Le package n’installe aucun logiciel supplémentaire. Pour plus d’informations, consultez la [FAQ sur la passerelle VPN](vpn-gateway-vpn-faq.md#point-to-site-connections).

1. Dans la panneau **Configuration de point à site**, cliquez sur **Télécharger le client VPN** pour ouvrir le panneau **Télécharger le client VPN**.
   
    ![Téléchargement du client VPN](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/downloadclient.png)
2. Sélectionnez le package approprié pour votre client, puis cliquez sur **télécharger**. Pour les clients 64 bits, sélectionnez **AMD64**. Pour les clients 32 bits, sélectionnez **x86**.
3. Installez le package sur l’ordinateur client. Si une fenêtre contextuelle SmartScreen s’affiche, cliquez sur **Plus d’infos**, puis sur **Exécuter quand même** afin d’installer le package.
4. Sur l’ordinateur client, accédez à **Paramètres réseau**, puis cliquez sur **VPN**. La connexion apparaît dans la liste. Le nom du réseau virtuel auquel il se connectera s’affiche, sous une forme semblable à cet exemple : 
   
    ![Client VPN](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpn.png)

## <a name="a-nameinstallclientcertapart-10---install-the-client-certificate"></a><a name="installclientcert"></a>Partie 10 : Installer le certificat client
Chaque ordinateur client doit avoir un certificat client pour s’authentifier. Lorsque vous installez le certificat client, vous avez besoin du mot de passe qui a été créé lorsque le certificat client a été exporté.

1. Copiez le fichier .pfx sur l’ordinateur client.
2. Double-cliquez sur le fichier .pfx pour l’installer. Ne modifiez pas l’emplacement d’installation.

## <a name="a-nameconnectapart-11---connect-to-azure"></a><a name="connect"></a>Partie 11 : Se connecter à Azure
1. Pour vous connecter à votre réseau virtuel, sur l’ordinateur client, accédez aux connexions VPN et recherchez celle que vous avez créée. Elle porte le même nom que votre réseau virtuel. Cliquez sur **Connecter**. Un message contextuel faisant référence à l’utilisation du certificat peut s’afficher. Le cas échéant, cliquez sur **Continuer** pour utiliser des privilèges élevés. 
2. Dans la page de statut **Connexion**, cliquez sur **Connecter** pour démarrer la connexion. Si un écran **Sélectionner un certificat** apparaît, vérifiez que le certificat client affiché est celui que vous souhaitez utiliser pour la connexion. Dans le cas contraire, utilisez la flèche déroulante pour sélectionner le certificat approprié, puis cliquez sur **OK**.
   
    ![Connexion du client VPN à Azure](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png)
3. À présent, votre connexion doit être établie.
   
    ![Client VPN connecté à Azure](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png)

## <a name="a-nameverifyapart-12---verify-your-connection"></a><a name="verify"></a>Partie 12 : Vérifier votre connexion
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

## <a name="a-nameaddato-add-or-remove-trusted-root-certificates"></a><a name="add"></a>Pour ajouter ou supprimer des certificats racine approuvés
Vous pouvez supprimer un certificat racine approuvé à partir d'Azure. Lorsque vous supprimez un certificat approuvé, les certificats clients qui ont été générés à partir du certificat racine ne pourront plus se connecter à Azure via la connexion de point à site. Si vous souhaitez que des clients se connectent, ils doivent installer un nouveau certificat client généré à partir d’un certificat approuvé dans Azure.

Vous pouvez gérer la liste des certificats clients révoqués dans le panneau **Configuration de point à site**. Il s’agit du panneau que vous avez utilisé pour [télécharger un certificat racine approuvé](#uploadfile).

## <a name="a-namerevokeclientato-manage-the-list-of-revoked-client-certificates"></a><a name="revokeclient"></a>Pour gérer la liste des certificats clients révoqués
Vous pouvez révoquer des certificats clients. La liste de révocation de certificat vous permet de refuser sélectivement la connexion point à site en fonction des certificats clients individuels. Si vous supprimez un fichier .cer de certificat racine d’Azure, vous révoquez l’accès pour tous les certificats clients générés/signés par le certificat racine révoqué. Il est possible de révoquer un certificat client en particulier et non le certificat racine. Ainsi, les autres certificats générés à partir du certificat racine seront toujours valables. 

La pratique courante consiste à utiliser le certificat racine pour gérer l'accès au niveaux de l'équipe ou de l'organisation, tout en utilisant des certificats clients révoqués pour le contrôle d'accès précis des utilisateurs individuels.

Vous pouvez gérer la liste des certificats clients révoqués dans le panneau **Configuration de point à site**. Il s’agit du panneau que vous avez utilisé pour [télécharger un certificat racine approuvé](#uploadfile).

## <a name="next-steps"></a>Étapes suivantes
Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Pour plus d’informations, consultez [Machines virtuelles](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).




<!--HONumber=Jan17_HO4-->


