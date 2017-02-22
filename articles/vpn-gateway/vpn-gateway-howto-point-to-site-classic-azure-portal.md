---
title: "Connecter un ordinateur à un réseau virtuel Azure à l’aide du point à site : Portail Azure : classique | Microsoft Docs"
description: "Connectez-vous de façon sécurisée à votre réseau virtuel Azure classique en créant une connexion par passerelle VPN point à site à l’aide du portail Azure."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: ac0d7d9aaf1208c97e0ae797ac7c2b0ffecb88ae
ms.openlocfilehash: 19c997cabc780bc6f115658e6e0952578142c18c


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Configuration d’une connexion point à site à un réseau virtuel à l’aide du portail Azure
> [!div class="op_single_selector"]
> * [Resource Manager - Portail Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Classic - Portail Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>
>

Cet article vous guide dans le processus de création d’un réseau virtuel avec une connexion point à site dans le modèle de déploiement classique à l’aide du portail Azure. Une configuration point à site (P2S) vous permet de connecter de manière sécurisée un ordinateur client individuel à un réseau virtuel. Une connexion P2S est utile lorsque vous souhaitez vous connecter à votre réseau virtuel à partir d’un emplacement distant, comme depuis votre domicile ou pendant une conférence. Ou lorsque seulement quelques clients doivent se connecter à un réseau virtuel.

Les connexions de ce type ne nécessitent pas de périphérique VPN ou d’adresse IP publique pour fonctionner. Le démarrage de la connexion à partir de l’ordinateur client permet d’établir une connexion VPN. Pour plus d’informations sur les connexions point à site, consultez [FAQ sur la passerelle VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) et [À propos de la passerelle VPN](vpn-gateway-about-vpngateways.md#P2S).

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Méthodes et modèles de déploiement pour les connexions P2S
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Le tableau suivant présente les deux modèles de déploiement et les méthodes de déploiement disponibles pour les configurations P2S. Quand un article avec les étapes de configuration est disponible, le lien vers cet article est ajouté à ce tableau.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Flux de travail de base
![Diagramme point à site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

Les sections suivantes vous guident dans les étapes de création d’une connexion point à site sécurisée à un réseau virtuel.

1. Créer un réseau virtuel et une passerelle VPN
2. Générer des certificats
3. Charger le fichier .cer
4. Générer le package de configuration du client VPN
5. Configurer l’ordinateur client
6. Connexion à Azure

### <a name="example-settings"></a>Exemples de paramètres
Vous pouvez utiliser les exemples de paramètres suivants :

* **Nom : VNet1**
* **Espace d’adressage : 192.168.0.0/16**
* **Nom du sous-réseau : FrontEnd**
* **Plage d’adresses de sous-réseau : 192.168.1.0/24**
* **Abonnement :** vérifiez que vous utilisez l’abonnement approprié si vous en possédez plusieurs.
* **Groupe de ressources : TestRG**
* **Emplacement : États-Unis de l’Est**
* **Type de connexion : point à site**
* **Espace d’adressage du client : 172.16.201.0/24**. Les clients VPN qui se connectent au réseau virtuel à l’aide de cette connexion point à site reçoivent une adresse IP de ce pool.
* **Sous-réseau de passerelle : 192.168.200.0/24**. Le sous-réseau de passerelle doit être nommé « GatewaySubnet ».
* **Taille :** la référence de la passerelle que vous souhaitez utiliser.
* **Type de routage : dynamique**

## <a name="a-namevnetvpnasection-1---create-a-virtual-network-and-a-vpn-gateway"></a><a name="vnetvpn"></a>Section 1 : créer un réseau virtuel et une passerelle VPN
### <a name="a-namecreatevnetapart-1-create-a-virtual-network"></a><a name="createvnet"></a>Partie 1 : création d’un réseau virtuel
Si vous n’avez pas de réseau virtuel, créez-en un. Les captures d’écran sont fournies à titre d’exemple. Assurez-vous de remplacer ces valeurs par les vôtres. Pour créer un réseau virtuel à l’aide du portail Azure, procédez comme suit :

1. Dans un navigateur, accédez au [portail Azure](http://portal.azure.com) et, si nécessaire, connectez-vous avec votre compte Azure.
2. Cliquez sur **Nouveau**. Dans le champ **Rechercher dans le marketplace**, tapez « réseau virtuel ». Localisez **Réseau virtuel** dans la liste renvoyée et cliquez pour ouvrir le panneau **Réseau virtuel**.

    ![Rechercher le panneau Réseau virtuel](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvnetportal700.png)
3. En bas du panneau Réseau virtuel, à partir de la liste **Sélectionner un modèle de déploiement**, choisissez **Classique** puis cliquez sur **Créer**.

    ![Sélectionner le modèle de déploiement](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png)
4. Dans le panneau **Créer un réseau virtuel** , configurez les paramètres du réseau virtuel. Dans ce panneau, vous allez ajouter votre premier espace d’adressage et une plage d’adresses de sous-réseau unique. Après avoir créé le réseau virtuel, vous pouvez revenir en arrière et ajouter des espaces d’adressage et des sous-réseaux supplémentaires.

    ![Panneau Créer un réseau virtuel](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)
5. Vérifiez qu’il s’agit de l’ **abonnement** approprié. Vous pouvez modifier des abonnements à l’aide de la liste déroulante.
6. Cliquez sur **Groupe de ressources** et sélectionnez un groupe de ressources existant, ou créez un groupe de ressources en tapant un nom pour ce dernier. Si vous créez un groupe, nommez-le en fonction de vos valeurs de configuration planifiée. Pour plus d’informations sur les groupes de ressources, consultez [Présentation d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Ensuite, sélectionnez les paramètres d’ **emplacement** pour votre réseau virtuel. L’emplacement déterminera où se trouveront les ressources que vous déployez sur ce réseau virtuel.
8. Sélectionnez **Épingler au tableau de bord** si vous souhaitez être en mesure de trouver votre réseau virtuel facilement sur le tableau de bord, puis cliquez sur **Créer**.

    ![Épingler au tableau de bord](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png)
9. Après avoir cliqué sur Créer, vous verrez une vignette apparaître sur votre tableau de bord. Celle-ci indique la progression de votre réseau virtuel. La vignette change lorsque le réseau virtuel est créé.

    ![Mosaïque de création du réseau virtuel](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png)
10. Après avoir créé votre réseau virtuel, vous pouvez ajouter l’adresse IP d’un serveur DNS afin de gérer la résolution de noms. Ouvrez les paramètres de votre réseau virtuel, cliquez sur les serveurs DNS et ajoutez l’adresse IP du serveur DNS que vous souhaitez utiliser. Ce paramètre n’entraîne pas la création de serveur DNS. Veillez à ajouter un serveur DNS avec lequel vos ressources peuvent communiquer.

Une fois votre réseau virtuel créé, la mention **Créé** apparaît sous **État** sur la page Réseaux du portail Azure Classic.

### <a name="a-namegatewayapart-2-create-gateway-subnet-and-a-dynamic-routing-gateway"></a><a name="gateway"></a>Partie 2 : création d’un sous-réseau de passerelle et d’une passerelle de routage dynamique
Vous allez maintenant créer un sous-réseau de passerelle et une passerelle de routage dynamique. Dans le portail Azure pour le modèle de déploiement classique, la création du sous-réseau de passerelle et de la passerelle peut être effectuée via les mêmes panneaux de configuration.

1. Dans le portail, accédez au réseau virtuel pour lequel vous souhaitez créer une passerelle.
2. Dans le panneau de votre réseau virtuel, dans le panneau **Vue d’ensemble**, puis dans la section Connexions VPN, cliquez sur **Passerelle**.

    ![Cliquer ici pour créer une passerelle](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. Dans le panneau **Nouvelle connexion VPN**, sélectionnez **Point à site**.

    ![Type de connexion de point à site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. Pour **l’espace d’adressage du client**, ajoutez la plage d’adresses IP. Il s’agit de la plage à partir de laquelle les clients VPN reçoivent une adresse IP lorsqu’ils se connectent. Supprimez la plage renseignée automatiquement et ajoutez la vôtre.

    ![Espace d’adressage du client](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Cochez la case **Créer une passerelle immédiatement**.

    ![Créer une passerelle immédiatement](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/creategwimm.png)
6. Cliquez sur **Configuration de passerelle facultative** pour ouvrir le panneau **Configuration de la passerelle**.

    ![Cliquer sur Configuration de passerelle facultative](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)
7. Cliquez sur **Subnet Configure required settings** (Configurer les paramètres requis pour le sous-réseau) pour ajouter le **sous-réseau de passerelle**. Bien qu’il soit possible de créer un sous-réseau de passerelle aussi petit que /29, nous vous recommandons de créer un sous-réseau plus vaste qui inclut un plus grand nombre d’adresses en sélectionnant au moins /28 ou /27. Cela permettra à un nombre suffisant d’adresses de s’adapter à de possibles configurations supplémentaires possibles que vous êtes susceptible de souhaiter par la suite.

   > [!IMPORTANT]
   > Lorsque vous travaillez avec des sous-réseaux de passerelle, évitez d’associer un groupe de sécurité réseau (NSG) au sous-réseau de passerelle. Si vous associez un groupe de sécurité réseau à ce sous-réseau, votre passerelle VPN cessera peut-être de fonctionner normalement. Pour plus d’informations sur les groupes de sécurité réseau, consultez [Présentation du groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md)
   >
   >

    ![Ajouter le sous-réseau de passerelle](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
8. Sélectionnez la **taille** de la passerelle. Il s’agit de la référence de passerelle que vous utiliserez pour créer votre passerelle de réseau virtuel. Dans le portail, la référence par défaut est **De base**. Pour plus d’informations sur les références de passerelle, consultez [À propos des paramètres de la passerelle VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

    ![taille de la passerelle](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
9. Sélectionnez le **type de routage** pour votre passerelle. Les configurations P2S nécessitent un type de routage **dynamique**. Cliquez sur **OK** lorsque vous avez terminé la configuration de ce panneau.

    ![Configurer le type de routage](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)
10. Dans le panneau **Nouvelle connexion VPN**, cliquez sur **OK** en bas du panneau pour commencer à créer votre passerelle de réseau virtuel. Cette opération peut prendre jusqu’à 45 minutes.

## <a name="a-namegeneratecertsasection-2---generate-certificates"></a><a name="generatecerts"></a>Section 2 - Générer des certificats
Les certificats sont utilisés par Azure pour authentifier les clients VPN pour les VPN point à site. Vous exportez des données de certificat public (pas la clé privée) sous forme de fichier .cer codé Base64 X.509 à partir d’un certificat racine généré par une solution de certificat d’entreprise ou d’un certificat racine auto-signé. Vous importez ensuite les données de certificat public à partir du certificat racine dans Azure. Vous devez également générer un certificat client à partir du certificat racine pour les clients. Chaque client souhaitant se connecter au réseau virtuel à l’aide d’une connexion P2S doit avoir un certificat client, qui a été généré à partir du certificat racine, installé.

### <a name="a-namecerapart-1-obtain-the-cer-file-for-the-root-certificate"></a><a name="cer"></a>Partie 1 : obtenir le fichier .cer pour le certificat racine
Si vous utilisez une solution d’entreprise, vous pouvez utiliser votre chaîne de certificats existante. Si vous n’utilisez pas une solution d’entreprise pour l’authentification par certificat, vous pouvez créer un certificat racine auto-signé. La méthode recommandée pour créer un certificat auto-signé pour les connexions P2S est makecert. Bien qu’il soit possible d’utiliser PowerShell pour créer des certificats auto-signés, le certificat généré à l’aide de PowerShell ne contient pas les champs nécessaires pour les connexions P2S.

* Si vous utilisez un système de certificat d’entreprise, il vous faut obtenir le fichier .cer pour le certificat racine que vous souhaitez utiliser.
* Si vous n’utilisez pas de solution de certificat d’entreprise, vous devez générer un certificat racine auto-signé. Pour connaître les étapes relatives à Windows 10, référez-vous à l’article [Utilisation des certificats racine auto-signés pour les configurations point à site](vpn-gateway-certificates-point-to-site.md).

1. Pour obtenir un fichier .cer à partir d’un certificat, ouvrez **certmgr.msc** et recherchez le certificat racine. Cliquez avec le bouton droit sur le certificat racine auto-signé, puis cliquez sur **toutes les tâches** et sur **exporter**. Cette opération ouvre **l’Assistant Exportation de certificat**.
2. Dans l’Assistant, cliquez sur **Suivant**, sélectionnez **Non, ne pas exporter la clé privée**, puis cliquez sur **Suivant**.
3. Sur la page **Format de fichier d’exportation**, sélectionnez **Codé à base&64; X.509 (.cer).** Cliquez ensuite sur **Suivant**.
4. Dans **Fichier à exporter**, cliquez sur **Parcourir** pour accéder à l’emplacement vers lequel vous souhaitez exporter le certificat. Pour la zone **Nom de fichier**, nommez le fichier de certificat. Cliquez ensuite sur **Suivant**.
5. Cliquez sur **Terminer** pour exporter le certificat.

### <a name="a-namegenclientcertapart-2-generate-a-client-certificate"></a><a name="genclientcert"></a>Partie 2 : génération d’un certificat client
Vous pouvez générer un certificat unique pour chaque client qui se connecte, ou utiliser le même certificat pour plusieurs clients. Générer des certificats clients uniques vous offre la possibilité de révoquer un seul certificat si nécessaire. Dans le cas contraire, si tous les clients utilisent le même certificat client et que vous devez révoquer le certificat pour un client, vous devrez générer et installer de nouveaux certificats pour tous les clients qui utilisent ce certificat pour s’authentifier.

* Si vous utilisez une solution de certificat d’entreprise, générez un certificat client avec le format de valeur de nom commun 'name@yourdomain.com',, plutôt que le format « nom_domaine\nom_utilisateur ».
* Si vous utilisez un certificat auto-signé, consultez [Utilisation des certificats racine auto-signés pour les configurations point à site](vpn-gateway-certificates-point-to-site.md) pour générer un certificat client.

### <a name="a-nameexportclientcertapart-3-export-the-client-certificate"></a><a name="exportclientcert"></a>Partie 3 : exportation du certificat client
Installez un certificat client sur chaque ordinateur que vous souhaitez connecter au réseau virtuel. Un certificat client est requis pour l’authentification. Vous pouvez automatiser l’installation du certificat client, ou l’installer manuellement. Les étapes ci-dessous vous guident dans l’exportation et l’installation manuelle du certificat client.

1. Pour exporter un certificat client, vous pouvez utiliser *certmgr.msc*. Cliquez avec le bouton droit sur le certificat client à exporter, cliquez sur **Toutes les tâches**, puis sur **Exporter**.
2. Exportez le certificat client avec la clé privée. Il s’agit d’un fichier *.pfx* . Prenez soin d’enregistrer ou de mémoriser le mot de passe (clé) que vous définissez pour ce certificat.

## <a name="a-nameuploadasection-3---upload-the-root-certificate-cer-file"></a><a name="upload"></a>Section 3 - Charger le fichier .cer de certificat racine
Une fois la passerelle créée, vous pouvez charger le fichier .cer pour un certificat racine approuvé dans Azure. Vous pouvez charger des fichiers pour 20 certificats racine maximum. Vous ne chargez pas la clé privée pour le certificat racine dans Azure. Une fois le fichier .cer chargé, Azure l’utilise pour authentifier les clients qui se connectent au réseau virtuel.

1. Dans la section **Connexions VPN** du panneau de votre réseau virtuel, cliquez sur le graphique des **clients** pour ouvrir le panneau **Connexion VPN point à site**.

    ![Clients](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. Dans le panneau**Connexion point à site**, cliquez sur **Gérer les certificats** pour ouvrir le panneau **Certificats**.<br>

    ![Panneau Certificats](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. Dans le panneau **Certificats**, cliquez sur **Télécharger** pour ouvrir le panneau **Télécharger un certificat**.<br>

    ![Panneau Télécharger un certificat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)<br>
4. Cliquez sur le graphique de dossier pour rechercher le fichier .cer. Sélectionnez le fichier, puis cliquez sur **OK**. Actualisez la page pour afficher le certificat chargé dans le Panneau **Certificats**.

    ![Téléchargement d’un certificat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)<br>

## <a name="a-namevpnclientconfigasection-4---generate-the-vpn-client-configuration-package"></a><a name="vpnclientconfig"></a>Section 4 - Générer le package de configuration du client VPN
Pour vous connecter au réseau virtuel, vous devez également configurer un client VPN. L’ordinateur client nécessite un certificat client et le package de configuration approprié du client VPN pour la connexion.

Le package client VPN contient des informations de configuration pour configurer le logiciel client VPN intégré à Windows. Le package n’installe aucun logiciel supplémentaire. Les paramètres sont spécifiques au réseau virtuel auquel vous souhaitez vous connecter. Pour obtenir la liste des systèmes d’exploitation clients pris en charge, consultez la section [Connexions de point à site](vpn-gateway-vpn-faq.md#point-to-site-connections) du FAQ sur la passerelle VPN. 

### <a name="to-generate-the-vpn-client-configuration-package"></a>Pour générer le package de configuration du client VPN
1. Dans le portail Azure, dans le panneau **Vue d’ensemble** de votre réseau virtuel, puis dans **Connexions VPN**, cliquez sur le graphique de client pour ouvrir le panneau **Connexion VPN point à site**.
2. En haut du panneau **Connexion VPN point à site**, cliquez sur le package de téléchargement correspondant au système d’exploitation client sur lequel il sera installé :

   * Pour les clients 64 bits, sélectionnez **Client VPN (64 bits)**.
   * Pour les clients 32 bits, sélectionnez **Client VPN (32 bits)**.

     ![Charger le package de configuration du client VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)<br>
3. Vous verrez un message indiquant qu’Azure génère le package de configuration du client VPN pour le réseau virtuel. Après quelques minutes, le package est généré et vous verrez un message sur votre ordinateur local indiquant que le package a été chargé. Enregistrez le fichier de package de configuration. Vous l’installerez ensuite sur chaque ordinateur client qui se connecte au réseau virtuel à l’aide de P2S.

## <a name="a-nameclientconfigurationasection-5---configure-the-client-computer"></a><a name="clientconfiguration"></a>Section 5 - Configurer l’ordinateur client
### <a name="part-1-install-the-client-certificate"></a>Partie 1 : installation du certificat client
Chaque ordinateur client doit avoir un certificat client pour s’authentifier. Lorsque vous installez le certificat client, vous avez besoin du mot de passe qui a été créé lorsque le certificat client a été exporté.

1. Copiez le fichier .pfx sur l’ordinateur client.
2. Double-cliquez sur le fichier .pfx pour l’installer. Ne modifiez pas l’emplacement d’installation.

### <a name="part-2-install-the-vpn-client-configuration-package"></a>Partie 2 : génération du package de configuration du client VPN
Vous pouvez utiliser le même package de configuration du client VPN sur chaque ordinateur client, sous réserve que la version corresponde à l’architecture du client.

1. Copiez le fichier de configuration localement sur l’ordinateur que vous souhaitez connecter à votre réseau virtuel, puis double-cliquez sur le fichier .exe.
2. Une fois le package installé, vous pouvez démarrer la connexion VPN. Le package de configuration n’est pas signé par Microsoft. Si vous le souhaitez, vous pouvez signer le package à l’aide du service de signature de votre organisation ou le signer vous-même au moyen de l’outil [SignTool](http://go.microsoft.com/fwlink/p/?LinkId=699327). Il vous est possible d’utiliser le package sans le signer. Toutefois, si le package n’est pas signé, un avertissement s’affiche au moment où vous l’installez.
3. Sur l’ordinateur client, accédez à **Paramètres réseau**, puis cliquez sur **VPN**. La connexion apparaît dans la liste. Le nom du réseau virtuel auquel il se connectera s’affiche, sous une forme du type :

    ![Client VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vpn.png)

## <a name="a-nameconnectasection-6---connect-to-azure"></a><a name="connect"></a>Section 6 - Se connecter à Azure
### <a name="connect-to-your-vnet"></a>Se connecter à votre réseau virtuel
1. Pour vous connecter à votre réseau virtuel, sur l’ordinateur client, accédez aux connexions VPN et recherchez celle que vous avez créée. Elle porte le même nom que votre réseau virtuel. Cliquez sur **Connecter**. Un message contextuel faisant référence à l’utilisation du certificat peut s’afficher. Le cas échéant, cliquez sur **Continuer** pour utiliser des privilèges élevés.
2. Dans la page de statut **Connexion**, cliquez sur **Connecter** pour démarrer la connexion. Si un écran **Sélectionner un certificat** apparaît, vérifiez que le certificat client affiché est celui que vous souhaitez utiliser pour la connexion. Dans le cas contraire, utilisez la flèche déroulante pour sélectionner le certificat approprié, puis cliquez sur **OK**.

    ![Connexion client VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png)
3. À présent, votre connexion doit être établie.

    ![Connexion établie](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png)

### <a name="verify-the-vpn-connection"></a>Vérifier la connexion VPN
1. Pour vérifier que votre connexion VPN est active, ouvrez une invite de commandes avec élévation de privilèges, puis exécutez *ipconfig/all*.
2. Affichez les résultats. Notez que l’adresse IP que vous avez reçue est l’une des adresses de la plage d’adresses de connectivité point à site que vous avez spécifiée quand vous avez créé votre réseau virtuel. Les résultats doivent être semblables à ce qui suit :

Exemple :

    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled

## <a name="next-steps"></a>Étapes suivantes
Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Pour plus d’informations, consultez [Machines virtuelles](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).



<!--HONumber=Feb17_HO1-->


