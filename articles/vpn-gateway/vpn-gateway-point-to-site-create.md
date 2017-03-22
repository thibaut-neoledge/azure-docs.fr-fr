---
title: "Connexion d’un ordinateur à un réseau virtuel Azure à l’aide d’une passerelle point à site : portail Azure Classic | Microsoft Docs"
description: "Connectez-vous de façon sécurisée à votre réseau virtuel Azure classique en créant une connexion par passerelle VPN point à site à l’aide du portail Classic."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 4f5668a5-9b3d-4d60-88bb-5d16524068e0
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: ffd5690853a7344c7122940ad9676d1903ba2d4a
ms.lasthandoff: 03/09/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-classic-portal-classic"></a>Configurer une connexion point à site vers un réseau virtuel à l’aide du portail Classic
> [!div class="op_single_selector"]
> * [Resource Manager - Portail Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Classic - Portail Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> * [Classic - Portail Classic](vpn-gateway-point-to-site-create.md)
> 
> 

Une configuration point à site (P2S) vous permet de connecter de manière sécurisée un ordinateur client individuel à un réseau virtuel. Le P2S est une connexion VPN sur SSTP (Secure Socket Tunneling Protocol). Les connexions point à site sont utiles lorsque vous souhaitez vous connecter à votre réseau virtuel à partir d’un site distant, comme depuis votre domicile ou une conférence ou lorsque seulement quelques clients doivent se connecter à un réseau virtuel. Les connexions de ce type ne nécessitent pas de périphérique VPN ou d’adresse IP publique. Vous établissez la connexion VPN depuis l’ordinateur client.

Cet article vous guide dans le processus de création d’un réseau virtuel avec une connexion de point à site dans le modèle de déploiement Classic à l’aide du portail Classic. Pour plus d’informations sur les connexions de point à site, consultez le [Forum Aux Questions sur les connexions point à site](#faq) à la fin de cet article.



### <a name="deployment-models-and-methods-for-p2s-connections"></a>Méthodes et modèles de déploiement pour les connexions P2S
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Le tableau suivant présente les deux modèles de déploiement et les méthodes de déploiement disponibles pour les configurations P2S. Quand un article avec les étapes de configuration est disponible, le lien vers cet article est ajouté à ce tableau.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Flux de travail de base
![Diagramme point à site](./media/vpn-gateway-point-to-site-create/p2sclassic.png "point à site")

Les étapes suivantes vous guident dans les étapes de création d’une connexion de point à site sécurisée à un réseau virtuel. La configuration d’une connexion de point à site est divisée en quatre sections. L’ordre dans lequel vous configurez chacune de ces sections est important. Aucune étape ne doit être sautée ou ignorée.

* **Section 1** Créer un réseau virtuel et une passerelle VPN.
* **Section 2** Créer les certificats utilisés pour l’authentification et les charger.
* **Section 3** Exporter et installer vos certificats clients.
* **Section 4** Configurer votre client VPN.



## <a name="vnetvpn"></a>Section 1 : créer un réseau virtuel et une passerelle VPN

Avant de commencer, assurez-vous que vous disposez d’un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial).

### <a name="part-1-create-a-virtual-network"></a>Partie 1 : créer un réseau virtuel
1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com). Ces étapes utilisent le portail Classic, et non le portail Azure. Pour le moment, vous ne pouvez pas créer de connexion P2S à l’aide du portail Azure.
2. Dans le coin inférieur gauche de l’écran, cliquez sur **Nouveau**. Dans le volet de navigation, cliquez sur **Services réseau**, puis sur **Réseau virtuel**. Cliquez sur **Custom Create** pour démarrer l'Assistant Configuration.
3. Sur la page **Détails du réseau virtuel** , entrez les informations suivantes, puis cliquez sur la flèche Suivant située dans le coin inférieur droit.
   
   * **Nom**: nommez votre réseau virtuel. Par exemple : « VNet1 ». Il s’agit du nom auquel vous ferez référence lors du déploiement de machines virtuelles sur ce réseau virtuel.
   * **Emplacement**: l’emplacement est directement associé à l’emplacement physique (région) où vous souhaitez que vos ressources (machines virtuelles) résident. Par exemple, si les machines virtuelles que vous déployez dans ce réseau virtuel doivent être situées physiquement dans la région Est des États-Unis, sélectionnez cet emplacement. Après avoir créé votre réseau virtuel, vous ne pourrez plus modifier la région qui lui est associée.
4. Sur la page **Serveurs DNS et connectivité VPN** , entrez les informations suivantes, puis cliquez sur la flèche Suivant située dans le coin inférieur droit.
   
   * **Serveurs DNS**: entrez le nom et l’adresse IP du serveur DNS, ou sélectionnez un serveur DNS précédemment inscrit dans le menu contextuel. Ce paramètre n’entraîne pas la création de serveur DNS. Il vous permet de spécifier le serveur DNS que vous souhaitez utiliser pour la résolution de noms pour ce réseau virtuel. Si vous souhaitez utiliser le service de résolution de noms Azure par défaut, laissez cette section vide.
   * **Configuration VPN de point à site**: cochez cette case.
5. Dans la page **Connectivité de point à site** , spécifiez la plage d’adresses IP qui déterminera l’adresse IP de vos clients VPN au moment de la connexion. Il existe quelques règles concernant les plages d’adresses que vous pouvez spécifier. Il est important de vérifier que la plage que vous spécifiez ne recouvre aucune des plages situées sur votre réseau local.
6. Entrez les informations ci-dessous, puis cliquez sur la flèche Suivant.
   
   * **Espace d’adressage**: incluez l’adresse IP de départ et le CIDR (nombre d’adresses).
   * **Ajouter un espace d’adressage**: procédez à cette opération uniquement si la conception de votre réseau l’exige.
7. Dans la page **Espaces d’adresses du réseau virtuel** , indiquez la plage d’adresses que vous voulez utiliser pour votre réseau virtuel. Il s’agit des adresses IP dynamiques (DIP) qui seront affectées aux machines virtuelles et autres instances de rôle que vous déployez sur ce réseau virtuel.<br><br>Il est particulièrement important de sélectionner une plage qui ne chevauche aucune des plages utilisées pour votre réseau local. Vous devez contacter votre administrateur réseau, qui peut avoir besoin de retirer une plage d’adresses IP de l’espace d’adressage de votre réseau local pour que vous puissiez l’utiliser pour votre réseau virtuel.
8. Entrez les informations ci-après, puis cliquez sur la coche pour commencer à créer votre réseau virtuel.
   
   * **Espace d’adressage**: ajoutez la plage d’adresses IP internes que vous voulez utiliser pour ce réseau virtuel, notamment l’adresse IP de départ et le nombre d’adresses. Il est important de sélectionner une plage qui ne chevauche aucune des plages utilisées pour votre réseau local. 
   * **Ajouter un sous-réseau**: aucun sous-réseau supplémentaire n’est requis, mais vous pouvez créer un sous-réseau distinct pour les machines virtuelles qui disposeront d’adresses IP dédiées statiques. Vous pouvez également placer vos machines virtuelles dans un sous-réseau séparé de vos autres instances de rôle.
   * **Ajouter un sous-réseau de passerelle**: le sous-réseau de passerelle est requis pour un VPN point à site. Cliquez sur cette option pour ajouter le sous-réseau de passerelle. Ce sous-réseau est uniquement utilisé pour la passerelle de réseau virtuel.
9. Une fois votre réseau virtuel créé, la mention **Créé** apparaît sous **État** sur la page Réseaux du portail Azure Classic. Une fois votre réseau virtuel créé, vous pouvez procéder à la création de votre passerelle de routage dynamique.

### <a name="part-2-create-a-dynamic-routing-gateway"></a>Partie 2 : créer une passerelle de routage dynamique
Le type de passerelle doit être configuré comme dynamique. Les passerelles de routage statique ne fonctionnent pas avec cette fonctionnalité.

1. Dans la page **Réseaux** du portail Azure Classic, cliquez sur le réseau virtuel que avez créé, puis accédez à la page **Tableau de bord**.
2. En bas de la page **Tableau de bord**, cliquez sur **Créer une passerelle**. Vous voyez apparaître le message suivant : **Souhaitez-vous créer une passerelle pour réseau virtuel « VNet1 » ?**. Cliquez sur **Oui** pour initialiser la création de la passerelle. La création de la passerelle peut durer jusqu’à 45 minutes.

## <a name="generate"></a>Section 2 : générer et télécharger des certificats
Les certificats sont utilisés par Azure pour authentifier les clients VPN pour les VPN point à site. Après avoir créé le certificat racine, vous exportez les données de certificat public (et non la clé privée) en tant que fichier .cer X.509 encodé en base&64;. Vous chargez ensuite les données de certificat public à partir du certificat racine dans Azure.

Chaque ordinateur client qui se connecte à un réseau virtuel à l’aide d’une connexion point à site doit avoir un certificat client installé. Le certificat client est généré à partir du certificat racine et installé sur chaque ordinateur client. Si aucun certificat client valide n’est installé et que le client essaie de se connecter au réseau virtuel, l’authentification échoue.

Dans cette section, vous allez effectuer les tâches suivantes :

* Obtenir le fichier .cer pour un certificat racine. Il peut s’agir d’un certificat racine auto-signé, ou vous pouvez utiliser votre système de certificat d’entreprise.
* Télécharger le fichier .cer sur Azure.
* Générer des certificats clients.

### <a name="root"></a>Partie 1 : obtenir le fichier .cer pour le certificat racine


Si vous utilisez une solution d’entreprise, vous pouvez utiliser votre chaîne de certificats existante. Obtenez le fichier .cer pour le certificat racine que vous souhaitez utiliser.


Si vous n’utilisez pas de solution de certificat d’entreprise, vous devez générer un certificat racine auto-signé. Pour créer un certificat racine auto-signé qui contient les champs nécessaires pour l’authentification P2S, vous pouvez utiliser PowerShell. La page [Create a self-signed root certificate for Point-to-Site connections using PowerShell](vpn-gateway-certificates-point-to-site.md) (Créer un certificat auto-signé pour les connexions de point à site à l’aide de PowerShell) vous guide à travers les étapes permettant de créer un certificat racine auto-signé.

> [!NOTE]
> La méthode makecert était auparavant recommandée pour créer les certificats racine auto-signés et générer des certificats clients pour les connexions point à site. Vous pouvez désormais utiliser PowerShell pour créer ces certificats. L’un des avantages de l’utilisation de PowerShell est la possibilité de créer des certificats SHA-2. Pour connaître les valeurs requises, consultez la page [Create a self-signed root certificate for Point-to-Site connections using PowerShell](vpn-gateway-certificates-point-to-site.md) (Créer un certificat auto-signé pour les connexions de point à site à l’aide de PowerShell).
>
>


#### <a name="to-export-the-public-key-for-a-self-signed-root-certificate"></a>Pour exporter la clé publique d’un certificat racine auto-signé.

Les connexions point à site requièrent le chargement de la clé publique (.cer) dans Azure. Les étapes suivantes vous aideront à exporter le fichier .cer pour votre certificat racine auto-signé.

1. Pour obtenir un fichier .cer du certificat, ouvrez **certmgr.msc**. Localisez le certificat racine auto-signé, généralement dans « Certificates - Curent User\Personal\Certificates » et cliquez avec le bouton droit. Cliquez sur **Toutes les tâches**, puis cliquez sur **Exporter**. Cette opération ouvre **l’Assistant Exportation de certificat**.
2. Dans l’assistant, cliquez sur **Suivant**. Sélectionnez **Non, ne pas exporter la clé privée**, puis cliquez sur **Suivant**.
3. Sur la page **Format de fichier d’exportation**, sélectionnez **Codé à base&64; X.509 (.cer).**, puis cliquez sur **Suivant**. 
4. Dans **Fichier à exporter**, cliquez sur **Parcourir** pour accéder à l’emplacement vers lequel vous souhaitez exporter le certificat. Pour la zone **Nom de fichier**, nommez le fichier de certificat. Cliquez ensuite sur **Suivant**.
5. Cliquez sur **Terminer** pour exporter le certificat. Vous verrez **L’exportation a réussi**. Cliquez sur **OK** pour fermer l’assistant.

### <a name="upload"></a>Partie 2 : charger le fichier de certificat racine .cer dans le portail Azure Classic

Ajoutez un certificat approuvé pour Azure. Lorsque vous ajoutez un fichier codé en Base64 X.509 (.cer) pour Azure, vous indiquez à Azure de faire confiance au certificat racine que le fichier représente. Vous pouvez charger des fichiers pour 20 certificats racine maximum. Vous ne chargez pas la clé privée pour le certificat racine dans Azure. Une fois le fichier .cer chargé, Azure l’utilise pour authentifier les clients qui se connectent au réseau virtuel.

1. Dans le Portail Azure Classic, cliquez dans la page **Certificats** de votre réseau virtuel sur **Télécharger un certificat racine**.
2. Dans la page **Télécharger un certificat** , recherchez le certificat racine .cer, puis cliquez sur la coche correspondante.

### <a name="createclientcert"></a>Partie 3 : générer un certificat client
Vous pouvez générer un certificat unique pour chaque client qui se connecte, ou utiliser le même certificat pour plusieurs clients. Générer des certificats clients uniques vous offre la possibilité de révoquer un seul certificat si nécessaire. Dans le cas contraire, si tous les clients utilisent le même certificat client et que vous devez révoquer le certificat pour un client, vous devrez générer et installer de nouveaux certificats pour tous les clients qui utilisent ce certificat pour s’authentifier.

####<a name="enterprise-certificate"></a>Certificat d’entreprise
- Si vous utilisez une solution de certificat d’entreprise, générez un certificat client avec le format de valeur de nom commun « name@yourdomain.com », plutôt que le format « nom_domaine\nom_utilisateur ».
- Assurez-vous que le certificat de client que vous émettez repose sur le modèle de certificat 'Utilisateur' ayant « Authentification client » comme premier élément dans d’usages, plutôt que connexion par carte à puce ou autre. Vous pouvez vérifier le certificat en double-cliquant sur le certificat client et en affichant **Détails > Utilisation avancée de la clé**.

####<a name="self-signed-root-root-certificate"></a>Certificat racine auto-signé 
Si vous utilisez un certificat racine auto-signé, consultez la section [Generate a client certificate using PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert) (Générer un certificat client à l’aide de PowerShell) pour savoir comment générer un certificat client compatible avec les connexions point à site.


## <a name="installclientcert"></a>Section 3 : exporter et installer le certificat client

Après avoir généré un certificat client, vous devez exporter le certificat dans un fichier .pfx et l’installer sur l’ordinateur client. Chaque ordinateur client doit avoir un certificat client pour s’authentifier. Vous pouvez automatiser l’installation du certificat client, ou installer le certificat manuellement. Les étapes ci-dessous vous guident dans l’exportation et l’installation manuelle du certificat client.

### <a name="export-the-client-certificate"></a>Exporter le certificat client

1. Pour exporter un certificat client, ouvrez **certmgr.msc**. Cliquez avec le bouton droit sur le certificat client à exporter, cliquez sur **Toutes les tâches**, puis sur **Exporter**. Cette opération ouvre **l’Assistant Exportation de certificat**.
2. Dans l’Assistant, cliquez sur **Suivant**, sélectionnez **Oui, exporter la clé privée**, puis cliquez sur **Suivant**.
3. Dans la page **Format de fichier d’exportation** , vous pouvez laisser les valeurs par défaut sélectionnées. Cliquez ensuite sur **Suivant**. 
4. Dans la page **Sécurité** , vous devez protéger la clé privée. Si vous choisissez d’utiliser un mot de passe, veillez à enregistrer ou à mémoriser celui que vous définissez pour ce certificat. Cliquez ensuite sur **Suivant**.
5. Dans **Fichier à exporter**, cliquez sur **Parcourir** pour accéder à l’emplacement vers lequel vous souhaitez exporter le certificat. Pour la zone **Nom de fichier**, nommez le fichier de certificat. Cliquez ensuite sur **Suivant**.
6. Cliquez sur **Terminer** pour exporter le certificat.

### <a name="install-the-client-certificate"></a>Installer le certificat client

Lorsque vous installez le certificat client, vous avez besoin du mot de passe qui a été créé lorsque le certificat client a été exporté.

1. Recherchez le fichier *.pfx* et copiez-le sur l’ordinateur client. Sur l’ordinateur client, double-cliquez sur le fichier *.pfx* à installer. Laissez la zone **Emplacement du magasin** définie sur **Utilisateur actuel**, puis cliquez sur **Suivant**.
2. N’apportez aucune modification à la page **Fichier à importer** . Cliquez sur **Next**.
3. Dans la page **Protection de clé privée**, entrez le mot de passe du certificat si vous en avez utilisé un, ou vérifiez que le principal de sécurité qui installe le certificat est correct, puis cliquez sur **Suivant**.
4. Dans la page **Magasin de certificats**, laissez l’emplacement par défaut, puis cliquez sur **Suivant**.
5. Cliquez sur **Terminer**. Dans la page **Avertissement de sécurité** relative à l’installation du certificat, cliquez sur **Oui**. Vous pouvez cliquer sur « Oui » sans hésitation, car vous avez généré le certificat. Le certificat est désormais importé.

## <a name="vpnclientconfig"></a>Section 4 : configurer votre client VPN
Pour vous connecter au réseau virtuel, vous devez également configurer un client VPN. Le client nécessite un certificat client et le package de configuration approprié du client VPN pour que la connexion réussisse. Pour configurer un client VPN, exécutez les étapes suivantes dans l’ordre.

### <a name="part-1-create-the-vpn-client-configuration-package"></a>Partie 1 : créer le package de configuration du client VPN
1. Dans le Portail Azure Classic, sur le **tableau de bord** de votre réseau virtuel, accédez au menu d’aperçu rapide dans le coin supérieur droit. Le package client VPN contient des informations de configuration pour configurer le logiciel client VPN intégré à Windows. Le package n’installe aucun logiciel supplémentaire. Les paramètres sont spécifiques au réseau virtuel auquel vous souhaitez vous connecter. Pour obtenir la liste des systèmes d’exploitation clients pris en charge, consultez la section [Forum Aux Questions sur les connexions point à site](#faq) à la fin de cet article.<br><br>Sélectionnez le package de téléchargement correspondant au système d’exploitation client sur lequel il sera installé :
   
   * Pour les clients 32 bits, sélectionnez **Télécharger le package VPN client 32 bits**.
   * Pour les clients 64 bits, sélectionnez **Télécharger le package VPN client 64 bits**.
2. La création du package client prend quelques minutes. Une fois le package généré, vous pouvez télécharger le fichier. Le fichier *.exe* que vous téléchargez peut être stocké en toute sécurité sur votre ordinateur local.
3. Après avoir généré et téléchargé le package client VPN à partir du portail Azure Classic, vous pouvez l’installer sur l’ordinateur client que vous souhaitez utiliser pour vous connecter à votre réseau virtuel. Si vous prévoyez d'installer le package client VPN sur plusieurs ordinateurs clients, assurez-vous que chacun d'entre eux dispose également d'un certificat client.

### <a name="part-2-install-the-vpn-configuration-package-on-the-client"></a>Partie 2 : installer le package de configuration VPN sur le client
1. Copiez le fichier de configuration localement sur l’ordinateur que vous souhaitez connecter à votre réseau virtuel, puis double-cliquez sur le fichier .exe. 
2. Une fois le package installé, vous pouvez démarrer la connexion VPN. Le package de configuration n’est pas signé par Microsoft. Si vous le souhaitez, vous pouvez signer le package à l’aide du service de signature de votre organisation ou le signer vous-même au moyen de l’outil [SignTool](http://go.microsoft.com/fwlink/p/?LinkId=699327). Il vous est possible d’utiliser le package sans le signer. Toutefois, si le package n’est pas signé, un avertissement s’affiche au moment où vous l’installez.
3. Sur l’ordinateur client, accédez à **Paramètres réseau**, puis cliquez sur **VPN**. La connexion apparaît dans la liste. Le nom du réseau virtuel auquel il se connectera s’affiche, sous une forme du type : 
   
    ![Client VPN](./media/vpn-gateway-point-to-site-create/vpn.png "Client VPN")

### <a name="part-3-connect-to-azure"></a>Partie 3 : se connecter à Azure
1. Pour vous connecter à votre réseau virtuel, sur l’ordinateur client, accédez aux connexions VPN et recherchez celle que vous avez créée. Elle porte le même nom que votre réseau virtuel. Cliquez sur **Connecter**. Un message contextuel faisant référence à l’utilisation du certificat peut s’afficher. Le cas échéant, cliquez sur **Continuer** pour utiliser des privilèges élevés. 
2. Dans la page de statut **Connexion**, cliquez sur **Connecter** pour démarrer la connexion. Si un écran **Sélectionner un certificat** apparaît, vérifiez que le certificat client affiché est celui que vous souhaitez utiliser pour la connexion. Dans le cas contraire, utilisez la flèche déroulante pour sélectionner le certificat approprié, puis cliquez sur **OK**.
   
    ![Client VPN 2](./media/vpn-gateway-point-to-site-create/clientconnect.png "Connexion client VPN")
3. À présent, votre connexion doit être établie.
   
    ![Client VPN 3](./media/vpn-gateway-point-to-site-create/connected.png "Connexion client VPN 2")

> [!NOTE]
> Si vous utilisez un certificat qui a été émis à l’aide d’une autorité de certification d’entreprise et que vous rencontrez des problèmes pour l’authentification, vérifiez l’ordre de l’authentification sur le certificat client. Vous pouvez vérifier l’ordre de la liste d’authentification en double-cliquant sur le certificat client et en accédant à **Détails > Utilisation avancée de la clé**. Vérifiez que la liste affiche « Authentification client » comme premier élément. Si ce n’est pas le cas, vous devez émettre un certificat client basé sur le modèle Utilisateur disposant de l’authentification client comme premier élément dans la liste. 
>
>

### <a name="part-4-verify-the-vpn-connection"></a>Partie 4 : vérifier la connexion VPN
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

## <a name="faq"></a>Forum Aux Questions sur les connexions point à site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Pour plus d’informations, consultez [Machines virtuelles](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Pour plus d’informations sur la mise en réseau et les machines virtuelles, consultez [Vue d’ensemble du réseau de machines virtuelles Azure et Linux](../virtual-machines/virtual-machines-linux-azure-vm-network-overview.md).


