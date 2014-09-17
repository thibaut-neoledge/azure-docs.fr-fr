<properties linkid="manage-services-cross-premises-connectivity" urlDisplayName="Cross-premises Connectivity" pageTitle="Create a cross-premises virtual network - Azure" metaKeywords="" description="Learn how to create an Azure Virtual Network with cross-premises connectivity." metaCanonical="" services="virtual-network" documentationCenter="" title="Create a Virtual Network for Site-to-Site Cross-Premises Connectivity" authors="" solutions="" manager="" editor="" />

Création d'un réseau virtuel pour une connectivité entre différents locaux de site à site
=========================================================================================

Ce didacticiel vous guide lors de la création d'un réseau virtuel entre différents locaux. Vous allez y créer une connexion de site à site. Si vous voulez créer un VPN pointant vers un site en utilisant des certificats et un client VPN, consultez la page [Configuration d'un VPN pointant vers un site dans le portail de gestion](http://go.microsoft.com/fwlink/?LinkId=296653).

Ce didacticiel part du principe que vous n'avez pas d'expérience en tant qu'utilisateur d'Azure. Il a pour but de vous familiariser avec la procédure requise pour créer un réseau virtuel de site à site. Si vous recherchez des scénarios de conception et des informations supplémentaires sur Virtual Network, consultez l'article [Présentation d'Azure Virtual Network](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx).

Une fois ce didacticiel terminé, vous disposerez d'un réseau virtuel sur lequel vous pouvez déployer vos services et machines virtuelles Azure, qui peuvent ensuite communiquer directement avec le réseau de votre société.

Pour plus d'informations sur l'ajout d'une machine virtuelle et l'extension locale d'Active Directory sur Azure Virtual Network, consultez les pages suivantes :

-   [Création d'une machine virtuelle personnalisée](http://go.microsoft.com/fwlink/?LinkID=294356)

-   [Installation d'un contrôleur de domaine Active Directory de réplication dans Azure Virtual Network](http://go.microsoft.com/fwlink/?LinkId=299877)

Pour obtenir des instructions sur le déploiement des services de domaine Active Directory (AD DS) dans Azure Virtual Machines, consultez la page [Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Windows Azure](http://msdn.microsoft.com/en-us/library/windowsazure/jj156090.aspx).

Pour plus d'informations sur les paramètres et procédures de configuration de Virtual Network, consultez la page [Tâches de configuration d'Azure Virtual Network](http://go.microsoft.com/fwlink/?LinkId=296652).

Objectifs
---------

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-   Configuration d'un réseau virtuel Azure de base auquel vous pouvez ajouter des services Azure

-   Configuration du réseau virtuel pour qu'il puisse communiquer avec le réseau de votre société

Conditions préalables
---------------------

-   Vous devez avoir un compte Windows Live avec au moins un abonnement actif en cours de validité.

-   Vous devez utiliser un espace d'adressage (en notation CIDR) pour le réseau virtuel et les sous-réseaux.

-   Vous devez connaître le nom et l'adresse IP de votre serveur DNS (si vous voulez utiliser votre serveur DNS local pour la résolution de noms).

-   Vous devez avoir un périphérique VPN avec une adresse IPv4 publique. Une adresse IP est obligatoire pour terminer l'Assistant. Le périphérique VPN doit respecter les standards minimum de périphérique et ne doit pas se trouver derrière un NAT. Pour plus d'informations, consultez la page [?À propos des périphériques VPN pour Virtual Network](http://go.microsoft.com/fwlink/?LinkID=248098).

    Remarque : Vous pouvez utiliser le service Routage et accès distant (RRAS) dans le cadre de votre solution VPN. Cependant, ce didacticiel ne présente pas la procédure de configuration RRAS.

    Pour obtenir des informations sur la configuration RRAS, consultez la page [Modèles de service Routage et accès distant](http://msdn.microsoft.com/library/windowsazure/dn133801.aspx).

-   Une expérience préalable ou une collaboration avec une personne expérimentée dans la configuration de routeur peut vous être utile.

-   Vous devez connaître l'espace d'adressage de votre réseau local.

Procédure générale
------------------

1.  [Création d'un réseau virtuel](#CreateVN)

2.  [Démarrage de la passerelle et collecte des informations pour votre administrateur réseau](#StartGateway)

3.  [Configuration de votre périphérique VPN](#ConfigVPN)

Création d'un réseau virtuel
----------------------------

**Pour créer un réseau virtuel qui se connecte au réseau de votre société :**

1.  Connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com/).

2.  Dans le coin inférieur gauche de l'écran, cliquez sur **Nouveau**. Dans le volet de navigation, cliquez sur **Réseaux**, puis sur **Virtual Network**. Cliquez sur **Custom Create** pour démarrer l'Assistant Configuration.

 	[](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_01_OpenVirtualNetworkWizard.png)

3.  Sur la page **Détails du réseau virtuel**, entrez les informations suivantes, puis cliquez sur la flèche Suivant située dans le coin inférieur droit. Pour plus d'informations sur les paramètres de la page des détails, consultez la section **Détails du réseau virtuel** sur la page [?À propos de la configuration d'un réseau virtuel à l'aide du portail de gestion](http://go.microsoft.com/fwlink/?LinkID=248092).
	-   **NOM :** nommez votre réseau virtuel. Tapez *YourVirtualNetwork*.
	-   **GROUPE D'AFFINITÉS :** dans la liste déroulante, sélectionnez **Créer un nouveau groupe d'affinités**. Les groupes d'affinités permettent de regrouper physiquement les services Azure dans un même centre de données pour améliorer les performances. Vous ne pouvez attribuer qu'un seul réseau virtuel par groupe d'affinités.
	-   **RÉGION :** dans la liste déroulante, sélectionnez la région de votre choix. Votre réseau virtuel sera créé dans un centre de données situé dans la région indiquée.
	-   **NOM DU GROUPE D’AFFINITÉS :** nommez le nouveau groupe d'affinités. Tapez *YourAffinityGroup*.

 	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_02_VirtualNetworkDetails.png)

4.  Sur la page **Serveurs DNS et connectivité VPN**, entrez les informations suivantes, puis cliquez sur la flèche Suivant située dans le coin inférieur droit.

	<div class="dev-callout"><b>Remarque</b>

    <p>Sur cette page, vous pouvez sélectionner simultanément les configurations **Pointer vers un site* et **Site à site**. Dans le cadre de ce didacticiel, seule la configuration **Site à site** est sélectionnée. Pour plus d'informations sur la configuration de cette page, consultez la section **Serveurs DNS et connectivité VPN** dans <a href="http://go.microsoft.com/fwlink/?LinkID=248092">À propos de la configuration d'un réseau virtuel à l'aide du portail de gestion</a>.</p>
	</div>
	-   **SERVEURS DNS :** entrez le nom du serveur DNS et l'adresse IP à utiliser pour la résolution de noms. Généralement, il s'agit d'un serveur DNS que vous utilisez pour la résolution de noms locale. Ce paramètre n'entraîne pas la création de serveur DNS. Tapez *YourDNS* pour le nom et *10.1.0.4* pour l'adresse IP.
	-   **Configure Point-To-Site VPN :** laissez ce champ vide.
	-   **Configure Site-To-Site VPN :** activez la case à cocher.
	-   **RÉSEAU LOCAL :** dans la liste déroulante, sélectionnez **Specify a New Local Network**.

 	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_03_DNSServersandVPNConnectivity.png)

5.  Sur la page **Connectivité de site à site**, entrez les informations suivantes, puis cliquez sur la coche située dans le coin inférieur droit de la page. Pour plus d'informations sur les paramètres de cette page, consultez la section **Connectivité de site à site** sur la page [À propos de la configuration d'un réseau virtuel à l'aide du portail de gestion](http://go.microsoft.com/fwlink/?LinkID=248092).
	-   **NOM :** entrez *YourCorpHQ*.
	-   **ADRESSE IP DE PÉRIPHÉRIQUE VPN :** entrez l'adresse IP publique de votre périphérique VPN. Si vous n'avez pas cette information, vous devrez l'obtenir avant de passer aux étapes suivantes de l'Assistant. Notez que votre périphérique VPN ne peut pas être situé derrière un NAT. Pour plus d'informations sur les périphériques VPN, consultez la page [À propos des périphériques VPN pour Virtual Network](http://msdn.microsoft.com/en-us/library/windowsazure/jj156075.aspx).
	-   **ESPACE D'ADRESSAGE :** entrez *10.1.0.0/16*.
	-   **Ajouter un espace d'adressage :** ce didacticiel ne requiert pas d'espace d'adressage supplémentaire.

 	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_04_SitetoSite.png)

6.  Sur la page **Virtual Network Address Spaces**, entrez les informations ci-dessous, puis cliquez sur la case à cocher située dans le coin inférieur droit pour configurer votre réseau.

    L'espace d'adressage doit avoir une plage d'adresses privée, indiquée selon la notation CIDR 10.0.0.0/8, 172.16.0.0/12 ou 192.168.0.0/16 (comme indiqué par RFC 1918). Pour plus d'informations sur les paramètres de cette page, consultez la section **Page d'espaces d'adressage de Virtual Network** sur la page [À propos de la configuration d'un réseau virtuel à l'aide du portail de gestion](http://go.microsoft.com/fwlink/?LinkID=248092).
	-   **Espace d'adressage :** cliquez sur **CIDR** dans le coin inférieur droit, puis entrez les informations suivantes :
		-   **IP de démarrage :** 10.4.0.0
		-   **CIDR :** /16
	-   **Ajouter un sous-réseau :** entrez les informations suivantes :
		-   **Renommez Subnet-1** en *FrontEndSubnet* avec l'IP de démarrage *10.4.2.0/24*, puis cliquez sur **ajouter un sous-réseau**.
		-   **Ajoutez un sous-réseau** nommé *BackEndSubnet* avec l'IP de démarrage *10.4.3.0/24*.
		-   **Ajoutez un sous-réseau** nommé *ADDNSSubnet* avec l'IP de démarrage *10.4.4.0/24*.
		-   **Ajoutez un sous-réseau de passerelle** avec l'IP de démarrage *10.4.1.0/24*.
		-   **Vérifiez** que vous avez bien créé ces trois sous-réseaux ainsi qu'un sous-réseau de passerelle, puis cliquez sur la coche située dans le coin inférieur droit pour créer votre réseau virtuel.

 	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_05_VirtualNetworkAddressSpaces.png)

7.  La création de votre réseau virtuel commence une fois que vous avez cliqué sur cette coche. Après la création de votre réseau virtuel, le statut Créé apparaît sous Statut sur la page des réseaux dans le portail de gestion.

 	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_06_VirtualNetworkCreatedStatus.png)

Démarrage de la passerelle
--------------------------

Une fois votre réseau Azure Virtual Network créé, procédez comme suit pour configurer la passerelle de réseau virtuel afin de créer votre VPN de site à site. Pour cela, vous devez avoir un périphérique VPN répondant à la configuration minimale requise. Pour plus d'informations sur les périphériques VPN et la configuration de votre périphérique, consultez la page [À propos des périphériques VPN pour Virtual Network](http://go.microsoft.com/fwlink/?LinkID=248098).

**Pour démarrer la passerelle :**

1.  Après la création de votre réseau virtuel, la page **réseaux** affiche le statut **Créé** pour votre réseau virtuel.

 	Dans la colonne **NOM**, cliquez sur **YourVirtualNetwork** pour ouvrir le tableau de bord.

 	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_07_ClickYourVirtualNetwork.png)

2.  Cliquez sur **TABLEAU DE BORD** en haut de la page. En bas de la page Tableau de bord, cliquez sur **CREATE GATEWAY**. Sélectionnez **Dynamic Routing** ou **Static Routing** pour le type de passerelle que vous voulez créer.

 	Notez que si vous voulez utiliser ce réseau virtuel pour les connexions de pointage vers un site en plus des connexions de site à site, vous devez sélectionner le type de passerelle Dynamic Routing. Avant de créer la passerelle, vérifiez que votre périphérique VPN prend en charge le type de passerelle que vous voulez créer. Consultez la page [À propos des périphériques VPN pour Virtual Network](http://go.microsoft.com/fwlink/?LinkID=248098). Lorsque le système vous demande de confirmer la création de la passerelle, cliquez sur **OUI**.

 	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_08_CreateGateway.png)

3.  Lorsque la création de la passerelle démarre, un message s'affiche pour vous indiquer le démarrage de la passerelle.

 	La création de la passerelle peut durer jusqu'à 15 minutes.

4.  Une fois la passerelle créée, vous devez collecter les informations suivantes, car elles seront utilisées pour configurer le périphérique VPN.
	-   Adresse IP de la passerelle
	-   Clé partagée
	-   Modèle de script de configuration de périphérique VPN

 	Les étapes suivantes vont vous guider dans la suite de cette procédure.

5.  Localisation de l'adresse IP de la passerelle : cette adresse est située sur la page **TABLEAU DE BORD** du réseau virtuel.

 	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_09_GatewayIP.png)

6.  Acquisition de la clé partagée : elle se trouve sur la page **TABLEAU DE BORD** du réseau virtuel. En bas de l'écran, cliquez sur Gérer la clé, puis copiez la clé affichée dans la boîte de dialogue.

 	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_10_ManageSharedKey.png)

3.  Téléchargement du modèle de script de configuration de périphérique VPN : sur le tableau de bord, cliquez sur **Download VPN Device Script**.

4.  Dans la boîte de dialogue **Download a VPN Device Configuration Script**, sélectionnez le fournisseur, la plateforme et le système d'exploitation du périphérique VPN de votre société. Cliquez sur la coche, puis enregistrez le fichier.

 	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_11_DownloadVPNDeviceScript.png)

Si votre périphérique VPN n'apparaît pas dans la liste déroulante, consultez la page [À propos des périphériques VPN pour Virtual Network](http://go.microsoft.com/fwlink/?LinkID=248098) de la bibliothèque MSDN pour obtenir des modèles de script supplémentaires.

Configuration du périphérique VPN (administrateur réseau)
---------------------------------------------------------

Cette procédure est simplement présente à titre indicatif, car chaque périphérique VPN est différent. Elle doit être effectuée par votre administrateur réseau.

Vous pouvez obtenir le script de configuration VPN à partir du portail de gestion ou sur la page [À propos des périphériques VPN pour Virtual Network](http://go.microsoft.com/fwlink/?LinkId=248098), qui décrit également les types de routage compatibles avec la configuration de routage de votre choix.

Pour plus d'informations sur la configuration d'une passerelle de réseau virtuel, consultez la page [Configuration de la passerelle de réseau virtuel dans le portail de gestion](http://go.microsoft.com/fwlink/?LinkId=299878), puis consultez la documentation de votre périphérique VPN.

Cette procédure part des principes suivants :

-   La personne configurant le périphérique VPN est expérimentée dans ce domaine. En raison du grand nombre de périphériques compatibles avec le réseau virtuel et des différentes configurations propres à chaque gamme de périphérique, cette procédure ne détaille pas toutes ces configurations. C'est pourquoi il est important que la personne chargée de la configuration du périphérique sache comment l'utiliser et le configurer.

-   Le périphérique que vous avez sélectionné est compatible avec un réseau virtuel. Une liste des périphériques compatibles est disponible [ici](http://go.microsoft.com/fwlink/?LinkID=248098).

**Pour configurer le périphérique VPN :**

1.  Modifiez le script de configuration VPN. Vous allez configurer les éléments suivants :

    a. Stratégies de sécurité

    b. Tunnel entrant

    c. Tunnel sortant

2.  Exécutez le script de configuration VPN modifié pour configurer votre périphérique VPN.

3.  Testez votre connexion en exécutant l'une des commandes suivantes :

<table border="1">
	<tr>
	<th>-</th>
	<th>Cisco ASA</th>
	<th>Cisco ISR/ASR</th>
	<th>Juniper SSG/ISG</th>
	<th>Juniper SRX/J</th>
	</tr>
	
	<tr>
	<td><b>Vérification de toutes les associations de sécurité de mode principal</b></td>
	<td><FONT FACE="courier" SIZE="-1">show crypto isakmp sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">show crypto isakmp sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">get ike cookie</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">show security ike security-association</FONT></td>
	</tr>
	
	<tr>
	<td><b>Vérification des associations de sécurité en mode rapide</b></td>
	<td><FONT FACE="courier" SIZE="-1">show crypto ipsec sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">show crypto ipsec sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">get sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">show security ipsec security-association</FONT></td>
	</tr>
	</table>

Étapes suivantes
----------------

Consultez les didacticiels suivants pour procéder à l'extension locale d'Active Directory sur le réseau virtuel que vous venez de créer :

-   [Création d'une machine virtuelle personnalisée](http://go.microsoft.com/fwlink/?LinkID=294356)

-   [Installation d'un contrôleur de domaine Active Directory de réplication dans Azure Virtual Network](http://go.microsoft.com/fwlink/?LinkId=299877)

Si vous voulez exporter les paramètres de votre réseau virtuel dans un fichier de configuration réseau pour sauvegarder votre configuration ou l'utiliser comme modèle, consultez la page [Exportation des paramètres de réseau virtuel vers un fichier de configuration réseau](http://go.microsoft.com/fwlink/?LinkID=299880).

Voir aussi
----------

-   [Azure Virtual Network](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx)

-   [FAQ Virtual Network](http://msdn.microsoft.com/library/windowsazure/dn133803.aspx)

-   [Configuration d'un réseau virtuel à l'aide de fichiers de configuration de réseau](http://msdn.microsoft.com/en-us/library/windowsazure/jj156097.aspx)

-   [Ajout d'une machine virtuelle à un réseau virtuel](http://www.windowsazure.com/en-us/manage/services/networking/add-a-vm-to-a-virtual-network/)

-   [À propos des périphériques VPN pour Virtual Network](http://msdn.microsoft.com/en-us/library/windowsazure/jj156075.aspx)

-   [Présentation de la résolution de noms Azure](http://go.microsoft.com/fwlink/?LinkId=248097)


