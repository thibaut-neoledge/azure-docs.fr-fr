<properties 
	pageTitle="Didacticiel : créer un réseau virtuel entre différents locaux pour une connectivité de site à site" 
	description="Ce didacticiel vous permet de découvrir comment créer un Azure Virtual Network avec la connectivité entre différents locaux." 
	services="virtual-network" 
	documentationCenter="" 
	authors="cherylmc" 
	manager="adinah" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/28/2015" 
	ms.author="cherylmc"/>



# Didacticiel : créer un réseau virtuel entre différents locaux pour une connectivité de site à site

Ce didacticiel vous familiarise avec les étapes visant à créer un réseau virtuel entre différents locaux pour une connectivité de site à site.

Si vous souhaitez créer un réseau virtuel cloud uniquement, consultez l’article [Didacticiel : Création d’un réseau virtuel cloud uniquement dans Azure](../virtual-machines/create-virtual-network.md). Si vous voulez créer un VPN pointant vers un site en utilisant des certificats et un client VPN, consultez la page [Configuration d'un VPN pointant vers un site dans le portail de gestion](http://go.microsoft.com/fwlink/p/?LinkId=296653).

Ce didacticiel part du principe que vous n'avez pas d'expérience en tant qu'utilisateur d'Azure. Il a pour but de vous familiariser avec la procédure requise pour créer un exemple de réseau virtuel entre différents locaux. Si vous recherchez des scénarios de conception et des informations supplémentaires sur Virtual Network, consultez l'article [Présentation d'Azure Virtual Network](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx).

Après avoir terminé ce didacticiel, vous aurez un exemple de réseau virtuel intersite. La figure suivante en illustre les détails, selon les paramètres de l’exemple de didacticiel.

![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_12_TutorialCrossPremVNet.png)

Pour obtenir une copie de cette figure et une autre figure que vous pouvez utiliser pour représenter votre propre réseau virtuel entre locaux, accédez à la page [Figure d’exemple d’un didacticiel illustrant un réseau virtuel entre différents locaux](http://gallery.technet.microsoft.com/Example-cross-premises-e5ecb8bb) (en anglais).

Notez que les paramètres de configuration illustratifs utilisés dans ce didacticiel ne sont pas spécialement adaptés au réseau de votre organisation. Si vous configurez le réseau virtuel et la connexion de site à site en utilisant les paramètres de configuration décrits dans cette rubrique, cela ne fonctionnera pas. Pour configurer un réseau virtuel entre différents locaux fonctionnel, veuillez collaborer avec votre service de TI et votre administrateur réseau pour obtenir les paramètres appropriés. Pour plus d'informations, consultez la section **Configuration requise** de cette rubrique.

Pour plus d'informations sur l'ajout d'une machine virtuelle et l'extension locale d'Active Directory sur Azure Virtual Network, consultez les pages suivantes :

-  [Création d’une machine virtuelle personnalisée](http://go.microsoft.com/fwlink/p/?LinkID=294356)

-  [Installation d’un contrôleur de domaine Active Directory de réplication dans un réseau virtuel Azure](http://go.microsoft.com/fwlink/p/?LinkId=299877)

Pour obtenir des instructions sur le déploiement des services de domaine Active Directory (AD DS) dans Azure Virtual Machines, consultez la page [Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Windows Azure](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).

Pour plus d'informations sur les paramètres et procédures de configuration de Virtual Network, consultez la page [Tâches de configuration d'Azure Virtual Network](http://go.microsoft.com/fwlink/p/?LinkId=296652).

##  Objectifs

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-  Configuration d'un réseau virtuel Azure d'illustration entre différents locaux, auquel vous pouvez ajouter des services Azure

-  Configuration du réseau virtuel pour qu'il puisse communiquer avec le réseau de votre organisation

##  Configuration requise

-  Vous devez avoir un compte Microsoft avec au moins un abonnement actif en cours de validité. Si vous ne disposez pas déjà d'un abonnement Azure, vous pouvez obtenir une évaluation gratuite sur la page [Essai d'Azure](http://azure.microsoft.com/pricing/free-trial/). Si vous disposez d’un abonnement MSDN, consultez la page présentant les [tarifs préférentiels Microsoft Azure : avantages MSDN, MPN et Bizspark](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

Si vous utilisez ce didacticiel pour configurer un réseau virtuel fonctionnel entre différents locaux et adapté aux besoins de votre entreprise, vous aurez besoin des éléments suivants&nbsp;:

-  Les espaces d'adresse privés IPv4 (en notation CIDR) pour le réseau virtuel et ses sous-réseaux.

-  Le nom et l'adresse IP d'un serveur DNS local.

-  Vous devez avoir un périphérique VPN avec une adresse IPv4 publique. Une adresse IP est obligatoire pour terminer l'Assistant. Le périphérique VPN doit respecter les standards minimum de périphérique et ne doit pas se trouver derrière un traducteur d’adresses réseau (NAT). Pour plus d'informations, consultez la page [À propos des périphériques VPN pour Virtual Network](http://go.microsoft.com/fwlink/p/?LinkID=248098).

	Remarque : vous pouvez utiliser le service Routage et accès distant (RRAS) dans Windows Server dans le cadre de votre solution VPN. Cependant, ce didacticiel ne présente pas la procédure de configuration RRAS.
	Pour obtenir des informations sur la configuration RRAS, consultez la page [Modèles de service Routage et accès distant](http://msdn.microsoft.com/library/windowsazure/dn133801.aspx).

-  Une expérience préalable ou une collaboration avec une personne expérimentée dans la configuration de routeur pour une connexion en mode de tunnel IPsec peut vous être utile.

-  L'ensemble des espaces d'adresse (en notation CIDR) qui résume les emplacements accessibles de votre réseau sur site (aussi appelé réseau local).


## Procédure générale

1.	[Créer un réseau virtuel](#CreateVN)

2.	[Démarrer la passerelle et collecter des informations pour votre administrateur réseau](#StartGateway)

3.  [Configurer votre périphérique VPN](#ConfigVPN)

##  <a name="CreateVN">Créer un réseau virtuel</a>

Pour créer un réseau virtuel d'illustration connecté à un réseau d'entreprise :

1.	Connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com/).

2.	Dans le coin inférieur gauche de l'écran, cliquez sur **Nouveau**. Dans le volet de navigation, cliquez sur **Réseaux**, puis sur **Virtual Network**. Cliquez sur **Custom Create** pour démarrer l'Assistant Configuration.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_01_OpenVirtualNetworkWizard.png)

3.	Sur la page **Détails du réseau virtuel**, entrez les informations suivantes, puis cliquez sur la flèche Suivant située dans le coin inférieur droit. Pour plus d'informations sur les paramètres de la page des détails, consultez la section **Détails du réseau virtuel** sur la page [À propos de la configuration d'un réseau virtuel à l'aide du portail de gestion](http://go.microsoft.com/fwlink/p/?LinkID=248092).

	-  **Nom** : nommez votre réseau virtuel. Pour l'exemple de ce didacticiel, tapez **YourVirtualNetwork**.

	-  **Emplacement** : dans la liste déroulante, sélectionnez la région de votre choix. Votre réseau virtuel sera créé dans le centre de données Azure situé dans la région indiquée.

	
4.	Sur la page **Serveurs DNS et connectivité VPN**, entrez les informations suivantes, puis cliquez sur la flèche Suivant située dans le coin inférieur droit.

> [AZURE.NOTE] Sur cette page, vous pouvez sélectionner simultanément les configurations **Pointer vers un site** et **Site à site.** Dans le cadre de ce didacticiel, seule la configuration **Site à site** est sélectionnée. Pour plus d'informations sur la configuration de cette page, consultez la section **Serveurs DNS et connectivité VPN** dans [À propos de la configuration d'un réseau virtuel à l'aide du portail de gestion](http://go.microsoft.com/fwlink/p/?LinkID=248092).

	-  **DNS SERVERS:** Enter the DNS server name and IP address that you want to use for name resolution. Typically this would be a DNS server that you use for on-premises name resolution. This setting does not create a DNS server. For the example in this tutorial, type **YourDNS** for the name and **10.1.0.4** for the IP address.
	-  **Configure Point-To-Site VPN:** Leave this field blank. 
	-  **Configure Site-To-Site VPN:** Select checkbox.
	-  **LOCAL NETWORK:** Select **Specify a New Local Network** from the drop-down list.
 
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_03_DNSServersandVPNConnectivity.png)

5.	Sur la page **Connectivité de site à site**, entrez les informations suivantes, puis cliquez sur la coche située dans le coin inférieur droit de la page. Pour plus d'informations sur les paramètres de cette page, consultez la section **Connectivité de site à site** sur la page [À propos de la configuration d'un réseau virtuel à l'aide du portail de gestion](http://go.microsoft.com/fwlink/p/?LinkID=248092). 

	-  **NOM** : pour l’exemple de ce didacticiel, tapez **YourCorpHQ**.

	-  **ADRESSE IP DU PÉRIPHÉRIQUE VPN** : pour l’exemple de ce didacticiel, tapez **3.2.1.1**. Sinon, entrez l'adresse IP publique de votre périphérique VPN. Si vous n'avez pas cette information, vous devrez l'obtenir avant de passer aux étapes suivantes de l'Assistant. Le périphérique VPN ne peut pas figurer derrière une traduction d’adresses réseau. Pour plus d’informations sur les périphériques VPN, voir l’article [À propos des périphériques VPN pour la connectivité de réseau virtuel](http://msdn.microsoft.com/library/windowsazure/jj156075.aspx).

	-  **ESPACE D’ADRESSAGE** : pour l’exemple de ce didacticiel, tapez **10.1.0.0/16**.
	-  **Ajouter un espace d’adressage** : ce didacticiel ne nécessite pas d’espace d’adressage supplémentaire.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_04_SitetoSite.png)

6.  Sur la page **Virtual Network Address Spaces**, entrez les informations ci-dessous, puis cliquez sur la case à cocher située dans le coin inférieur droit pour configurer votre réseau.

	L'espace d'adressage doit avoir une plage d'adresses privée, indiquée selon la notation CIDR des espaces d'adresse 10.0.0.0/8, 172.16.0.0/12 ou 192.168.0.0/16 (comme indiqué par RFC 1918). Pour plus d'informations sur les paramètres de cette page, consultez la section **Page d'espaces d'adressage de Virtual Network** sur la page [À propos de la configuration d'un réseau virtuel à l'aide du portail de gestion](http://go.microsoft.com/fwlink/?LinkID=248092).

	-  **Espace d’adressage** : pour l’exemple de ce didacticiel, cliquez sur **CIDR** dans le coin supérieur droit, puis entrez les informations suivantes :
		-  **Adresse IP de départ** : 10.4.0.0
		-  **CIDR** : /16
	-  **Ajouter un sous-réseau** : pour l’exemple de ce didacticiel, entrez les informations suivantes :
		-  Renommez **Subnet-1** en **FrontEndSubnet** avec l'IP de démarrage **10.4.2.0/24**.
		-  Ajoutez un sous-réseau appelé **BackEndSubnet** avec l'IP de démarrage **10.4.3.0/24**.
		-  Ajoutez un sous-réseau appelé **ADDNSSubnet** avec l'IP de démarrage **10.4.4.0/24**.
		-  Ajoutez un sous-réseau de passerelle avec l'IP de démarrage **10.4.1.0/24**.
	-  Pour l'exemple de ce didacticiel, vérifiez que vous avez bien créé ces trois sous-réseaux ainsi qu'un sous-réseau de passerelle, puis cliquez sur la coche située dans le coin inférieur droit pour créer votre réseau virtuel.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_05_VirtualNetworkAddressSpaces.png)

7.	La création de votre réseau virtuel commence une fois que vous avez cliqué sur cette coche. Après la création de votre réseau virtuel, le statut **Créé** apparaît sous **Statut** sur la page des réseaux dans le portail de gestion.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_06_VirtualNetworkCreatedStatus.png)

##  <a name="StartGateway">Démarrer la passerelle</a>

Une fois votre réseau Azure Virtual Network créé, procédez comme suit pour configurer la passerelle de réseau virtuel afin de créer votre VPN de site à site. Pour cela, vous devez avoir un périphérique VPN répondant à la configuration minimale requise. Pour plus d'informations sur les périphériques VPN et la configuration de votre périphérique, consultez la page [À propos des périphériques VPN pour Virtual Network](http://go.microsoft.com/fwlink/p/?LinkID=248098).

**Pour démarrer la passerelle :**

1.	Après la création de votre réseau virtuel, la page **réseaux** affiche le statut **Créé** pour votre réseau virtuel.

	Dans la colonne **NOM**, cliquez sur **YourVirtualNetwork** (pour l'exemple créé dans ce didacticiel) pour ouvrir le tableau de bord.
 
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_07_ClickYourVirtualNetwork.png)

2.	Cliquez sur **TABLEAU DE BORD** en haut de la page. En bas de la page Tableau de bord, cliquez sur **CREATE GATEWAY**. Sélectionnez **Dynamic Routing** ou **Static Routing** pour le type de passerelle que vous voulez créer.

	Notez que si vous voulez utiliser ce réseau virtuel pour les connexions de pointage vers un site en plus des connexions de site à site, vous devez sélectionner le type de passerelle **Dynamic Routing**. Avant de créer la passerelle, vérifiez que votre périphérique VPN prend en charge le type de passerelle que vous voulez créer. Consultez la page [À propos des périphériques VPN pour Virtual Network](http://go.microsoft.com/fwlink/p/?LinkID=248098). Lorsque le système vous demande de confirmer la création de la passerelle, cliquez sur **OUI**.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_08_CreateGateway.png)

3.	Lorsque la création de la passerelle démarre, un message s'affiche pour vous indiquer le démarrage de la passerelle.

	La création de la passerelle peut durer jusqu'à 15 minutes.

4.	Une fois la passerelle créée, vous devez collecter les informations suivantes, car elles seront utilisées pour configurer le périphérique VPN.

	-  Adresse IP de la passerelle
	-  Clé partagée
	-  Modèle de script de configuration de périphérique VPN

	Les étapes suivantes vont vous guider dans la suite de cette procédure.

5.	Localisation de l’adresse IP de la passerelle : cette adresse est située sur la page **TABLEAU DE BORD** du réseau virtuel. Voici un exemple :

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_09_GatewayIP.png)

6.	Acquisition de la clé partagée : cette clé se trouve sur la page **TABLEAU DE BORD** du réseau virtuel. En bas de l'écran, cliquez sur **Gérer la clé**, puis copiez la clé affichée dans la boîte de dialogue. Vous aurez besoin de cette clé pour configurer le tunnel IPsec de le périphérique VPN de votre société.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_10_ManageSharedKey.png)

7.	Téléchargement du modèle de script de configuration de périphérique VPN : dans le tableau de bord, cliquez sur **Télécharger le script du périphérique VPN**.

8.	Dans la boîte de dialogue **Download a VPN Device Configuration Script**, sélectionnez le fournisseur, la plateforme et le système d'exploitation du périphérique VPN de votre société. Cliquez sur la coche, puis enregistrez le fichier.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_11_DownloadVPNDeviceScript.png)

Si votre périphérique VPN n'apparaît pas dans la liste déroulante, consultez la page [À propos des périphériques VPN pour Virtual Network](http://go.microsoft.com/fwlink/p/?LinkID=248098) de la bibliothèque MSDN pour obtenir des modèles de script supplémentaires.


##  <a name="ConfigVPN">Configurer le périphérique VPN (administrateur réseau)</a>

Cette procédure est simplement présente à titre indicatif, car chaque périphérique VPN est différent. Elle doit être effectuée par votre administrateur réseau.

Vous pouvez obtenir le script de configuration VPN à partir du portail de gestion ou sur la page [À propos des périphériques VPN pour Virtual Network](http://go.microsoft.com/fwlink/p/?LinkId=248098), qui décrit également les types de routage compatibles avec la configuration de routage de votre choix.

Pour plus d'informations sur la configuration d'une passerelle de réseau virtuel, consultez la page [Configuration de la passerelle de réseau virtuel dans le portail de gestion](http://go.microsoft.com/fwlink/p/?LinkId=299878), puis consultez la documentation de votre périphérique VPN.

Cette procédure part des principes suivants :

-  La personne configurant le périphérique VPN est expérimentée dans ce domaine. En raison du grand nombre de périphériques compatibles avec le réseau virtuel et des différentes configurations propres à chaque gamme de périphérique, cette procédure ne détaille pas toutes ces configurations. C'est pourquoi il est important que la personne chargée de la configuration du périphérique sache comment l'utiliser et le configurer. 

-  Le périphérique que vous avez sélectionné est compatible avec un réseau virtuel. Une liste des périphériques compatibles est disponible [ici](http://go.microsoft.com/fwlink/p/?LinkID=248098).


**Pour configurer le périphérique VPN :**

1.	Modifiez le script de configuration VPN. Vous allez configurer les éléments suivants :

	a. Stratégies de sécurité

	b. Tunnel entrant

	c. Tunnel sortant

2.	Exécutez le script de configuration VPN modifié pour configurer votre périphérique VPN.

3.	Testez votre connexion en exécutant l'une des commandes suivantes :

	<table border="1">
	<tr>
	<th>-</th>
	<th>Cisco ASA</th>
	<th>Cisco ISR/ASR</th>
	<th>Juniper SSG/ISG</th>
	<th>Juniper SRX/J</th>
	</tr>
	
	<tr>
	<td><b>Vérification de toutes les associations de sécurité en mode principal</b></td>
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


##  Étapes suivantes
Consultez les didacticiels suivants pour procéder à l'extension locale d'Active Directory sur le réseau virtuel que vous venez de créer :

-  [Création d’une machine virtuelle personnalisée](http://go.microsoft.com/fwlink/p/?LinkID=294356)

-  [Installation d’un contrôleur de domaine Active Directory de réplication dans un réseau virtuel Azure](http://go.microsoft.com/fwlink/p/?LinkId=299877)

Si vous voulez exporter les paramètres de votre réseau virtuel dans un fichier de configuration réseau pour sauvegarder votre configuration ou l'utiliser comme modèle, consultez la page [Exportation des paramètres de réseau virtuel vers un fichier de configuration réseau](http://go.microsoft.com/fwlink/p/?LinkID=299880).

## Voir aussi

-  [Présentation du réseau virtuel](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)

-  [FAQ sur le réseau virtuel](http://msdn.microsoft.com/library/windowsazure/dn133803.aspx)

-  [Configuration d’un réseau virtuel à l’aide d’un fichier de configuration réseau](virtual-networks-using-network-configuration-file.md)

-  [Création d’une machine virtuelle personnalisée](../virtual-machines/virtual-machines-create-custom.md)

-  [À propos des périphériques VPN pour la connectivité de réseau virtuel](http://msdn.microsoft.com/library/windowsazure/jj15] 75.aspx)

-  [Résolution de noms (DNS)](http://go.microsoft.com/fwlink/p/?LinkId=248097)
-  [Configuration d’un environnement de cloud hybride à des fins de test](virtual-networks-setup-hybrid-cloud-environment-testing.md)





 

<!-----HONumber=August15_HO6-->