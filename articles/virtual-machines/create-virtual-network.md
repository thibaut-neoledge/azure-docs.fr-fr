<properties 
	pageTitle="Didacticiel : création d’un réseau virtuel dans le cloud uniquement" 
	description="Ce didacticiel vous explique comment créer un exemple de Azure Virtual Network pour le cloud uniquement." 
	services="virtual-machines, virtual-network" 
	documentationCenter="" 
	authors="cherylmc" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2015" 
	ms.author="cherylmc"/>

# Didacticiel : Création d’un réseau virtuel cloud uniquement dans Azure

Ce didacticiel décrit les étapes à effectuer dans le portail de gestion Azure pour créer un exemple de réseau virtuel Azure cloud uniquement qui contient deux sous-réseaux. Le réseau virtuel obtenu ressemblera à ceci :

![createvnet](./media/create-virtual-network/createVNet_06_VNetExample.png)

Par exemple, FrontEndSubnet pourrait être utilisé pour les serveurs web tandis que BackEndSubnet pourrait être utilisé pour les serveurs SQL ou les contrôleurs de domaine.

Ce didacticiel part du principe que vous n'avez jamais utilisé Azure. Il a pour but de vous familiariser avec la procédure qui vous permettra de créer votre propre réseau virtuel en décrivant étape par étape un exemple de configuration. Si vous voulez créer un réseau virtuel cloud uniquement qui fonctionne pour votre configuration spécifique, consultez [Configuration d'un réseau virtuel cloud uniquement dans le Portail de gestion](http://msdn.microsoft.com/library/azure/dn631643.aspx). Si vous recherchez des scénarios de conception et des informations avancées sur Virtual Network, consultez la page [Vue d'ensemble d'Azure Virtual Network](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx).


> [AZURE.NOTE]Ce didacticiel ne décrit pas comment créer une configuration entre différents locaux, dans laquelle le réseau virtuel est connecté au réseau de votre organisation. Pour accéder à un didacticiel présentant les étapes de création d'un réseau virtuel avec une connexion entre différents locaux et une connexion VPN de site à site (par exemple, une connexion à Active Directory ou SharePoint au sein de votre société), consultez [Didacticiel : Création d'un réseau virtuel pour une connectivité entre différents locaux de site à site](../virtual-network/virtual-networks-create-site-to-site-cross-premises-connectivity.md).


##  Objectifs

Dans ce didacticiel, vous allez découvrir comment configurer un réseau virtuel Azure cloud uniquement de base avec deux sous-réseaux.

##  Conditions préalables

*  Vous devez avoir un compte Microsoft avec au moins un abonnement actif en cours de validité. Si vous ne disposez pas déjà d'un abonnement Azure, vous pouvez obtenir une évaluation gratuite sur la page [Essai d'Azure](http://azure.microsoft.com/pricing/free-trial/). Si vous disposez d’un abonnement MSDN, consultez la rubrique [Tarifs préférentiels Microsoft Azure : avantages MSDN, MPN et Bizspark](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

##  Création du réseau virtuel pour ce didacticiel

Pour créer cet exemple de réseau virtuel cloud uniquement, procédez comme suit.

1. Connectez-vous au portail de gestion.

2. En bas à gauche de l'écran, cliquez sur **Nouveau** > **Services de réseau** > **Réseau virtuel**, puis sur **Création personnalisée** pour lancer l'Assistant Configuration.

	![][Image1]

3. Dans la page **Détails du réseau virtuel**, entrez les informations suivantes :

- **Nom -** Tapez **VotreRéseauVirtuel**.

- **Région -** Le réseau virtuel sera créé dans un centre de données situé dans la région indiquée. Pour de meilleures performances, sélectionnez la région dans laquelle vous vous trouvez dans la liste déroulante.
 

	![][Image2]

4. Cliquez sur la flèche Suivant située en bas à droite. Pour plus d'informations sur les paramètres de cette page, consultez la section sur la page Détails du réseau virtuel dans [À propos des paramètres de réseau virtuel dans le Portail de gestion](http://go.microsoft.com/fwlink/p/?linkid=248092&clcid=0x409).

5. Dans la page **Serveurs DNS et connectivité VPN**, cliquez sur la flèche Suivant en bas à droite. Azure assignera un serveur DNS Azure basé sur Internet aux nouvelles machines virtuelles qui sont ajoutées à ce réseau virtuel, ce qui leur permettra d'accéder aux ressources sur Internet. Pour plus d'informations sur les paramètres de cette page, consultez la section Serveurs DNS et connectivité VPN dans [À propos des paramètres de réseau virtuel dans le Portail de gestion](http://go.microsoft.com/fwlink/p/?linkid=248092&clcid=0x409).
	
6.	Tout comme un vrai réseau, le réseau virtuel a besoin d'une plage d'adresses IP (appelée espace d'adressage) à assigner aux machines virtuelles que vous y incluez. Le réseau virtuel prend aussi en charge les sous-réseaux, qui doivent avoir leurs propres espaces d'adressage, dérivés de l'espace d'adressage du réseau virtuel. Pour ce didacticiel, nous allons créer BackEndSubnet et FrontEndSubnet. Dans la page **Espaces d'adressage du réseau virtuel**, configurez ce qui suit :

	- Pour Espace d'adressage, sélectionnez **/16 (65535)** dans **CIDR (nombre d'adresses)**.

	- Pour les sous-réseaux, sur la première ligne, tapez **BackEndSubnet** en remplacement du nom existant et **10.0.1.0** comme adresse IP de départ, puis sélectionnez **/24 (256)** dans **CIDR (nombre d'adresses)**. Cliquez sur **Ajouter un sous-réseau**, puis tapez **FrontEndSubnet** comme nom et **10.0.2.0** comme adresse IP de départ.
		
	![][Image4]

 Si nous revenons à notre schéma de réseau virtuel, nous constatons que nous avons configuré les espaces d'adressage suivants :
 
	
- FrontEndSubnet : 10.0.2.0/24
- BackEndSubnet : 10.0.1.0/24

 Pour plus d'informations sur les paramètres de cette page, consultez la page Espaces d'adressage de réseau virtuel dans [À propos des paramètres de réseau virtuel dans le Portail de gestion](http://go.microsoft.com/fwlink/p/?linkid=248092&clcid=0x409).


7. Cliquez sur la coche en bas à droite de la page pour créer votre réseau virtuel. Une fois votre réseau virtuel créé, le statut **Créé** apparaît sous Statut dans la page **Réseaux** du portail de gestion Azure.  

	![][Image5]

Vous pouvez continuer à découvrir les services d'infrastructure Azure en consultant les ressources suivantes :

- [Création d'une machine virtuelle personnalisée](virtual-machines-create-custom.md) Cette rubrique vous aide à installer une machine virtuelle sur votre réseau virtuel. Pour plus d'informations sur les machines virtuelles et les options d'installation, consultez [Machines virtuelles Azure](http://azure.microsoft.com/documentation/services/virtual-machines/).

- [Installation d'une nouvelle forêt Active Directory sur un réseau virtuel Azure](../active-directory-new-forest-virtual-machine.md) - Cette rubrique vous aide à installer une nouvelle forêt Windows Server Active Directory (AD) sans connectivité avec un autre réseau. Il décrit les étapes nécessaires à la création d'une machine virtuelle pour une nouvelle installation de forêt. Si vous envisagez de suivre ce didacticiel, ne créez pas de machines virtuelles à l'aide du portail de gestion. Pour plus d'informations, consultez [Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Azure](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).

Pour supprimer ce réseau virtuel, sélectionnez-le, cliquez sur **Supprimer**, puis cliquez sur **Oui**.

Quand vous êtes prêt à créer un réseau virtuel cloud uniquement qui fonctionne pour votre propre configuration, consultez [Configuration d'un réseau virtuel cloud uniquement dans le Portail de gestion](http://msdn.microsoft.com/library/azure/dn631643.aspx).

Si vous recherchez des scénarios de conception et des informations avancées sur le réseau virtuel, consultez [Présentation du réseau virtuel](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx).

Pour plus d'informations sur les paramètres et procédures de configuration du réseau virtuel, consultez [Tâches de configuration du réseau virtuel](http://go.microsoft.com/fwlink/p/?linkid=296652&clcid=0x409).


## Voir aussi

-  [FAQ sur le réseau virtuel](http://go.microsoft.com/fwlink/p/?LinkId=296650)

-  [Tâches de configuration du réseau virtuel](http://go.microsoft.com/fwlink/p/?LinkId=296652)

-  [Configuration d’un réseau virtuel à l’aide d’un fichier de configuration réseau](../virtual-network/virtual-networks-using-network-configuration-file.md)

-  [Résolution de noms (DNS)](http://go.microsoft.com/fwlink/?LinkId=248097)


[Image1]: ./media/create-virtual-network/createVNet_01_OpenVirtualNetworkWizard.png
[Image2]: ./media/create-virtual-network/createVNet_02_VirtualNetworkDetails.png
[Image3]: ./media/create-virtual-network/createVNet_03_DNSServersandVPNConnectivity.png
[Image4]: ./media/create-virtual-network/createVNet_04_VirtualNetworkAddressSpaces.png
[Image5]: ./media/create-virtual-network/createVNet_05_VirtualNetworkCreatedStatus.png
[Image7]: ./media/create-virtual-network/createVNet_07_VNetExampleSpaces.png
[Image8]: ./media/create-virtual-network/createVNet_07_VNetExampleSpaces.png

 

<!---HONumber=July15_HO3-->