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
ms.date: 03/20/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 92e573d7f3ebfbe41c8012068a8262d6fc324da8
ms.lasthandoff: 03/21/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal-classic"></a>Configurer une connexion point à site vers un réseau virtuel à l’aide du portail Azure Classic
> [!div class="op_single_selector"]
> * [Resource Manager - Portail Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Classic - Portail Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>
>

Une configuration point à site (P2S) vous permet de connecter de manière sécurisée un ordinateur client individuel à un réseau virtuel. Le P2S est une connexion VPN sur SSTP (Secure Socket Tunneling Protocol). Les connexions point à site sont utiles lorsque vous souhaitez vous connecter à votre réseau virtuel à partir d’un site distant, comme depuis votre domicile ou une conférence ou lorsque seulement quelques clients doivent se connecter à un réseau virtuel. Les connexions de ce type ne nécessitent pas de périphérique VPN ou d’adresse IP publique. Vous établissez la connexion VPN depuis l’ordinateur client.

Cet article vous guide dans le processus de création d’un réseau virtuel avec une connexion point à site dans le modèle de déploiement classique à l’aide du portail Azure. Pour plus d’informations sur les connexions de point à site, consultez le [Forum Aux Questions sur les connexions point à site](#faq) à la fin de cet article.


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
* **Espace d’adressage : 192.168.0.0/16**<br>Pour cet exemple, nous n’utilisons qu’un seul espace d’adressage. Vous pouvez avoir plusieurs espaces d’adressage pour votre réseau virtuel.
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



## <a name="vnetvpn"></a>Section 1 : créer un réseau virtuel et une passerelle VPN

Avant de commencer, assurez-vous que vous disposez d’un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial).
### <a name="createvnet"></a>Partie 1 : création d’un réseau virtuel
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

### <a name="gateway"></a>Partie 2 : création d’un sous-réseau de passerelle et d’une passerelle de routage dynamique
Vous allez maintenant créer un sous-réseau de passerelle et une passerelle de routage dynamique. Dans le portail Azure pour le modèle de déploiement classique, la création du sous-réseau de passerelle et de la passerelle peut être effectuée via les mêmes panneaux de configuration.

1. Dans le portail, accédez au réseau virtuel pour lequel vous souhaitez créer une passerelle.
2. Dans le panneau de votre réseau virtuel, dans le panneau **Vue d’ensemble**, puis dans la section Connexions VPN, cliquez sur **Passerelle**.

    ![Cliquez pour créer une passerelle](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
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

## <a name="generatecerts"></a>Section 2 : créer des certificats
Les certificats sont utilisés par Azure pour authentifier les clients VPN pour les VPN point à site. Après avoir créé le certificat racine, vous exportez les données de certificat public (et non la clé privée) en tant que fichier .cer X.509 encodé en base 64. Vous chargez ensuite les données de certificat public à partir du certificat racine dans Azure.

Chaque ordinateur client qui se connecte à un réseau virtuel à l’aide d’une connexion point à site doit avoir un certificat client installé. Le certificat client est généré à partir du certificat racine et installé sur chaque ordinateur client. Si aucun certificat client valide n’est installé et que le client essaie de se connecter au réseau virtuel, l’authentification échoue.

### <a name="cer"></a>Partie 1 : obtention de la clé publique (.cer) pour le certificat racine

####<a name="enterprise-certificate"></a>Certificat d’entreprise
 
Si vous utilisez une solution d’entreprise, vous pouvez utiliser votre chaîne de certificats existante. Obtenez le fichier .cer pour le certificat racine que vous souhaitez utiliser.

####<a name="self-signed-root-certificate"></a>Certificat racine auto-signé

Si vous n’utilisez pas de solution de certificat d’entreprise, vous devez générer un certificat racine auto-signé. Pour créer un certificat auto-signé qui contient les champs nécessaires pour l’authentification P2S, vous pouvez utiliser PowerShell. La page [Create a self-signed certificate for Point-to-Site connections using PowerShell](vpn-gateway-certificates-point-to-site.md) (Créer un certificat auto-signé pour les connexions de point à site à l’aide de PowerShell) vous guide à travers les étapes permettant de créer un certificat racine auto-signé.

> [!NOTE]
> La méthode makecert était auparavant recommandée pour créer les certificats racine auto-signés et générer des certificats clients pour les connexions point à site. Vous pouvez désormais utiliser PowerShell pour créer ces certificats. L’un des avantages de l’utilisation de PowerShell est la possibilité de créer des certificats SHA-2. Pour connaître les valeurs requises, consultez la page [Create a self-signed certificate for Point-to-Site connections using PowerShell](vpn-gateway-certificates-point-to-site.md) (Créer un certificat auto-signé pour les connexions de point à site à l’aide de PowerShell).
>
>

#### <a name="to-export-the-public-key-for-a-self-signed-root-certificate"></a>Pour exporter la clé publique d’un certificat racine auto-signé

Les connexions point à site requièrent le chargement de la clé publique (.cer) dans Azure. Les étapes suivantes vous aideront à exporter le fichier .cer pour votre certificat racine auto-signé.

1. Pour obtenir un fichier .cer du certificat, ouvrez **certmgr.msc**. Localisez le certificat racine auto-signé, généralement dans « Certificates - Curent User\Personal\Certificates » et cliquez avec le bouton droit. Cliquez sur **Toutes les tâches**, puis cliquez sur **Exporter**. Cette opération ouvre **l’Assistant Exportation de certificat**.
2. Dans l’assistant, cliquez sur **Suivant**. Sélectionnez **Non, ne pas exporter la clé privée**, puis cliquez sur **Suivant**.
3. Sur la page **Format de fichier d’exportation**, sélectionnez **Codé à base 64 X.509 (.cer).**, puis cliquez sur **Suivant**. 
4. Dans **Fichier à exporter**, cliquez sur **Parcourir** pour accéder à l’emplacement vers lequel vous souhaitez exporter le certificat. Pour la zone **Nom de fichier**, nommez le fichier de certificat. Cliquez ensuite sur **Suivant**.
5. Cliquez sur **Terminer** pour exporter le certificat. Vous verrez **L’exportation a réussi**. Cliquez sur **OK** pour fermer l’assistant.

### <a name="genclientcert"></a>Partie 2 : génération d’un certificat client

Vous pouvez générer un certificat unique pour chaque client qui se connecte, ou utiliser le même certificat pour plusieurs clients. Générer des certificats clients uniques vous offre la possibilité de révoquer un seul certificat si nécessaire. Dans le cas contraire, si tous les clients utilisent le même certificat client et que vous devez révoquer le certificat pour un client, vous devrez générer et installer de nouveaux certificats pour tous les clients qui utilisent ce certificat pour s’authentifier.

####<a name="enterprise-certificate"></a>Certificat d’entreprise
- Si vous utilisez une solution de certificat d’entreprise, générez un certificat client avec le format de valeur de nom commun « name@yourdomain.com », plutôt que le format « nom_domaine\nom_utilisateur ».
- Assurez-vous que le certificat de client que vous émettez repose sur le modèle de certificat 'Utilisateur' ayant « Authentification client » comme premier élément dans d’usages, plutôt que connexion par carte à puce ou autre. Vous pouvez vérifier le certificat en double-cliquant sur le certificat client et en affichant **Détails > Utilisation avancée de la clé**.

####<a name="self-signed-root-certificate"></a>Certificat racine auto-signé 
Si vous utilisez un certificat racine auto-signé, consultez la section [Generate a client certificate using PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert) (Générer un certificat client à l’aide de PowerShell) pour savoir comment générer un certificat client compatible avec les connexions point à site.

### <a name="exportclientcert"></a>Partie 3 : exportation du certificat client
Si vous générez un certificat client à partir d’un certificat racine auto-signé à l’aide des instructions [PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert), il est automatiquement installé sur l’ordinateur que vous avez utilisé pour le générer. Si vous souhaitez installer un certificat client sur un autre ordinateur client, vous devez l’exporter.

1. Pour exporter un certificat client, ouvrez **certmgr.msc**. Cliquez avec le bouton droit sur le certificat client à exporter, cliquez sur **Toutes les tâches**, puis sur **Exporter**. Cette opération ouvre **l’Assistant Exportation de certificat**.
2. Dans l’Assistant, cliquez sur **Suivant**, sélectionnez **Oui, exporter la clé privée**, puis cliquez sur **Suivant**.
3. Dans la page **Format de fichier d’exportation**, laissez les valeurs par défaut sélectionnées. Assurez-vous que l’option **Inclure tous les certificats dans le chemin d’accès de certification si possible** est sélectionnée. Cliquez ensuite sur **Suivant**.
4. Dans la page **Sécurité** , vous devez protéger la clé privée. Si vous choisissez d’utiliser un mot de passe, veillez à enregistrer ou à mémoriser celui que vous définissez pour ce certificat. Cliquez ensuite sur **Suivant**.
5. Dans **Fichier à exporter**, cliquez sur **Parcourir** pour accéder à l’emplacement vers lequel vous souhaitez exporter le certificat. Pour la zone **Nom de fichier**, nommez le fichier de certificat. Cliquez ensuite sur **Suivant**.
6. Cliquez sur **Terminer** pour exporter le certificat.

## <a name="upload"></a>Section 3 - Charger le fichier .cer de certificat racine
Une fois la passerelle créée, vous pouvez charger le fichier .cer pour un certificat racine approuvé dans Azure. Vous pouvez charger des fichiers pour 20 certificats racine maximum. Vous ne chargez pas la clé privée pour le certificat racine dans Azure. Une fois le fichier .cer chargé, Azure l’utilise pour authentifier les clients qui se connectent au réseau virtuel.

1. Dans la section **Connexions VPN** du panneau de votre réseau virtuel, cliquez sur le graphique des **clients** pour ouvrir le panneau **Connexion VPN point à site**.

    ![Clients](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. Dans le panneau**Connexion point à site**, cliquez sur **Gérer les certificats** pour ouvrir le panneau **Certificats**.<br>

    ![Panneau Certificats](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. Dans le panneau **Certificats**, cliquez sur **Télécharger** pour ouvrir le panneau **Télécharger un certificat**.<br>

    ![Panneau Télécharger un certificat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)<br>
4. Cliquez sur le graphique de dossier pour rechercher le fichier .cer. Sélectionnez le fichier, puis cliquez sur **OK**. Actualisez la page pour afficher le certificat chargé dans le Panneau **Certificats**.

    ![Téléchargement d’un certificat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)<br>

## <a name="vpnclientconfig"></a>Section 4 - Générer le package de configuration du client VPN
Pour vous connecter au réseau virtuel, vous devez également configurer un client VPN. L’ordinateur client nécessite un certificat client et le package de configuration approprié du client VPN pour la connexion.

Le package client VPN contient des informations de configuration pour configurer le logiciel client VPN intégré à Windows. Le package n’installe aucun logiciel supplémentaire. Les paramètres sont spécifiques au réseau virtuel auquel vous souhaitez vous connecter. Pour obtenir la liste des systèmes d’exploitation clients pris en charge, consultez la section [Forum Aux Questions sur les connexions point à site](#faq) à la fin de cet article.

### <a name="to-generate-the-vpn-client-configuration-package"></a>Pour générer le package de configuration du client VPN
1. Dans le portail Azure, dans le panneau **Vue d’ensemble** de votre réseau virtuel, puis dans **Connexions VPN**, cliquez sur le graphique de client pour ouvrir le panneau **Connexion VPN point à site**.
2. En haut du panneau **Connexion VPN point à site**, cliquez sur le package de téléchargement correspondant au système d’exploitation client sur lequel il sera installé :

   * Pour les clients 64 bits, sélectionnez **Client VPN (64 bits)**.
   * Pour les clients 32 bits, sélectionnez **Client VPN (32 bits)**.

     ![Charger le package de configuration du client VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)<br>
3. Vous verrez un message indiquant qu’Azure génère le package de configuration du client VPN pour le réseau virtuel. Après quelques minutes, le package est généré et vous verrez un message sur votre ordinateur local indiquant que le package a été chargé. Enregistrez le fichier de package de configuration. Vous l’installerez ensuite sur chaque ordinateur client qui se connecte au réseau virtuel à l’aide de P2S.

## <a name="clientconfiguration"></a>Section 5 - Configurer l’ordinateur client
### <a name="part-1-install-an-exported-client-certificate"></a>Partie 1 : installation d’un certificat client exporté

Si vous souhaitez créer une connexion P2S à partir d’un ordinateur client différent de celui que vous avez utilisé pour générer les certificats clients, vous devez installer un certificat client. Lorsque vous installez un certificat client, vous avez besoin du mot de passe créé lors de l’exportation du certificat client.

1. Recherchez le fichier *.pfx* et copiez-le sur l’ordinateur client. Sur l’ordinateur client, double-cliquez sur le fichier *.pfx* à installer. Laissez la zone **Emplacement du magasin** définie sur **Utilisateur actuel**, puis cliquez sur **Suivant**.
2. N’apportez aucune modification à la page **Fichier à importer** . Cliquez sur **Next**.
3. Dans la page **Protection de clé privée**, entrez le mot de passe du certificat si vous en avez utilisé un, ou vérifiez que le principal de sécurité qui installe le certificat est correct, puis cliquez sur **Suivant**.
4. Dans la page **Magasin de certificats**, laissez l’emplacement par défaut, puis cliquez sur **Suivant**.
5. Cliquez sur **Terminer**. Dans la page **Avertissement de sécurité** relative à l’installation du certificat, cliquez sur **Oui**. Vous pouvez cliquer sur « Oui » sans hésitation, car vous avez généré le certificat. Le certificat est désormais importé.

### <a name="part-2-install-the-vpn-client-configuration-package"></a>Partie 2 : génération du package de configuration du client VPN
Vous pouvez utiliser le même package de configuration du client VPN sur chaque ordinateur client, sous réserve que la version corresponde à l’architecture du client.

1. Copiez le fichier de configuration localement sur l’ordinateur que vous souhaitez connecter à votre réseau virtuel. 
2. Double-cliquez sur le fichier .exe pour installer le package sur l’ordinateur client. Le package de configuration n’est pas signé, car vous l’avez créé. Un avertissement peut alors s’afficher. Si une fenêtre contextuelle Windows SmartScreen s’affiche, cliquez sur **Plus d’infos** (à gauche), puis sur **Exécuter quand même** pour installer le package.
3. Sur l’ordinateur client, accédez à **Paramètres réseau**, puis cliquez sur **VPN**. La connexion apparaît dans la liste. Le nom du réseau virtuel auquel il se connectera s’affiche, sous une forme du type :

    ![Client VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vpn.png)

## <a name="connect"></a>Section 6 - Se connecter à Azure
### <a name="connect-to-your-vnet"></a>Se connecter à votre réseau virtuel
1. Pour vous connecter à votre réseau virtuel, sur l’ordinateur client, accédez aux connexions VPN et recherchez celle que vous avez créée. Elle porte le même nom que votre réseau virtuel. Cliquez sur **Connecter**. Un message contextuel faisant référence à l’utilisation du certificat peut s’afficher. Le cas échéant, cliquez sur **Continuer** pour utiliser des privilèges élevés.
2. Dans la page de statut **Connexion**, cliquez sur **Connecter** pour démarrer la connexion. Si un écran **Sélectionner un certificat** apparaît, vérifiez que le certificat client affiché est celui que vous souhaitez utiliser pour la connexion. Dans le cas contraire, utilisez la flèche déroulante pour sélectionner le certificat approprié, puis cliquez sur **OK**.

    ![Connexion client VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png)
3. À présent, votre connexion doit être établie.

    ![Connexion établie](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png)

> [!NOTE]
> Si vous utilisez un certificat qui a été émis à l’aide d’une autorité de certification d’entreprise et que vous rencontrez des problèmes pour l’authentification, vérifiez l’ordre de l’authentification sur le certificat client. Vous pouvez vérifier l’ordre de la liste d’authentification en double-cliquant sur le certificat client et en accédant à **Détails > Utilisation avancée de la clé**. Vérifiez que la liste affiche « Authentification client » comme premier élément. Si ce n’est pas le cas, vous devez émettre un certificat client basé sur le modèle Utilisateur disposant de l’authentification client comme premier élément dans la liste. 
>
>

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

## <a name="add"></a>Ajout ou suppression de certificats racine approuvés

Vous pouvez ajouter et supprimer des certificats racines approuvés à partir d'Azure. Lorsque vous supprimez un certificat approuvé, les certificats clients qui ont été générés à partir du certificat racine ne pourront plus se connecter à Azure via la connexion de point à site. Si vous souhaitez que des clients se connectent, ils doivent installer un nouveau certificat client généré à partir d’un certificat approuvé dans Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Ajout d’un certificat racine approuvé

Vous pouvez ajouter jusqu’à 20 fichiers .cer de certificat racine approuvés dans Azure. Pour obtenir des instructions, consultez [Section 3 - Charger le fichier .cer de certificat racine](#upload).

### <a name="to-remove-a-trusted-root-certificate"></a>Suppression d’un certificat racine approuvé


1. Dans la section **Connexions VPN** du panneau de votre réseau virtuel, cliquez sur le graphique des **clients** pour ouvrir le panneau **Connexion VPN point à site**.

    ![Clients](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. Dans le panneau**Connexion point à site**, cliquez sur **Gérer les certificats** pour ouvrir le panneau **Certificats**.<br>

    ![Panneau Certificats](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. Dans le panneau **Certificats**, cliquez sur le bouton de sélection correspondant au certificat que vous souhaitez supprimer, puis cliquez sur **Supprimer**.

     ![Suppression d'un certificat racine](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)<br>


## <a name="revokeclient"></a>Révocation d'un certificat client
Vous pouvez révoquer des certificats clients. La liste de révocation de certificat vous permet de refuser sélectivement la connexion point à site en fonction des certificats clients individuels. Cela est différent de la suppression d’un certificat racine approuvé. Si vous supprimez un fichier .cer de certificat racine approuvé d’Azure, vous révoquez l’accès pour tous les certificats clients générés/signés par le certificat racine révoqué. Révoquer un certificat client plutôt que le certificat racine permet de continuer à utiliser les autres certificats générés à partir du certificat racine pour l’authentification de la connexion Point à site.

La pratique courante consiste à utiliser le certificat racine pour gérer l'accès aux niveaux de l'équipe ou de l'organisation, tout en utilisant des certificats clients révoqués pour le contrôle d'accès précis des utilisateurs individuels.

### <a name="to-revoke-a-client-certificate"></a>Révocation d'un certificat client

Vous pouvez révoquer un certificat client en ajoutant son empreinte à la liste de révocation.

1. Récupérez l’empreinte du certificat client. Pour plus d’informations, consultez l’article [Comment : récupérer l’empreinte numérique d’un certificat](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copiez les informations dans un éditeur de texte et supprimez tous les espaces afin d’obtenir une chaîne continue.
3. Accédez au panneau **« nom du réseau virtuel classique » > Connexion VPN Point à site > Certificats**, puis cliquez sur **Liste de révocation** pour ouvrir le panneau Liste de révocation. 
4. Dans le panneau **Liste de révocation**, cliquez sur **+ Ajouter un certificat** pour ouvrir le panneau **Ajouter un certificat à la liste de révocation**.
5. Dans le panneau **Ajouter un certificat à la liste de révocation**, collez l’empreinte numérique du certificat sur une seule ligne continue de texte, sans espaces. Cliquez sur **OK** au bas du panneau.
6. Une fois la mise à jour terminée, le certificat ne peut plus être utilisé pour se connecter. Les clients qui tentent de se connecter à l’aide de ce certificat recevront un message indiquant que le certificat n’est plus valide.


## <a name="faq"></a>Forum Aux Questions sur les connexions point à site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Étapes suivantes
Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Pour plus d’informations, consultez [Machines virtuelles](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Pour plus d’informations sur la mise en réseau et les machines virtuelles, consultez [Vue d’ensemble du réseau de machines virtuelles Azure et Linux](../virtual-machines/virtual-machines-linux-azure-vm-network-overview.md).

