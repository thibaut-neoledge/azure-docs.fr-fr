<properties linkid="manage-services-create-a-virtual-network" urlDisplayName="Tutorial: Create a cloud-only virtual network" pageTitle="Tutorial: Create a cloud-only virtual network" metaKeywords="" description="Learn how to create an example cloud-only Azure Virtual Network in this tutorial." metaCanonical="" services="virtual-machines,virtual-network" documentationCenter="" title="Tutorial: Create a Clound-only Virtual Network in Azure" authors="cherylmc" solutions="" manager="adinah" editor="" />

<tags ms.service="virtual-network" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/29/2014" ms.author="cherylmc" />

# Didacticiel : Création d'un réseau virtuel cloud uniquement dans Azure

Ce didacticiel décrit les étapes à effectuer dans le portail de gestion Azure pour créer un exemple de réseau virtuel Azure cloud uniquement qui contient deux sous-réseaux. Le réseau virtuel obtenu ressemblera à ceci :

![createvnet][createvnet]

Par exemple, FrontEndSubnet pourrait être utilisé pour les serveurs web tandis que BackEndSubnet pourrait être utilisé pour les serveurs SQL ou les contrôleurs de domaine.

Ce didacticiel part du principe que vous n'avez jamais utilisé Azure. Il a pour but de vous familiariser avec la procédure qui vous permettra de créer votre propre réseau virtuel en décrivant étape par étape un exemple de configuration. Si vous voulez créer un réseau virtuel cloud uniquement qui fonctionne pour votre configuration spécifique, consultez [Configuration d'un réseau virtuel cloud uniquement dans le Portail de gestion][Configuration d'un réseau virtuel cloud uniquement dans le Portail de gestion]. Si vous recherchez des scénarios de conception et des informations avancées sur le réseau virtuel, consultez [Présentation du réseau virtuel][Présentation du réseau virtuel].

<div class="dev-callout"> 
<b>Remarque</b> 
<p>Ce didacticiel ne d&eacute;crit pas comment cr&eacute;er une configuration entre diff&eacute;rents locaux, dans laquelle le r&eacute;seau virtuel est connect&eacute; au r&eacute;seau de votre organisation. Pour acc&eacute;der &agrave; un didacticiel pr&eacute;sentant les &eacute;tapes de cr&eacute;ation d'un r&eacute;seau virtuel avec une connexion entre diff&eacute;rents locaux et une connexion VPN de site &agrave; site (par exemple, une connexion &agrave; Active Directory ou SharePoint au sein de votre soci&eacute;t&eacute;), consultez <a href="/fr-fr/manage/services/networking/cross-premises-connectivity/">Didacticiel&nbsp;: Cr&eacute;ation d'un r&eacute;seau virtuel pour une connectivit&eacute; entre diff&eacute;rents locaux de site &agrave; site</a>.</p> 
</div>

## Objectifs

Dans ce didacticiel, vous allez découvrir comment configurer un réseau virtuel Azure cloud uniquement de base avec deux sous-réseaux.

## Conditions préalables

-   Vous devez avoir un compte Microsoft avec au moins un abonnement Azure actif en cours de validité. Si vous n'avez pas encore d'abonnement Azure, vous pouvez obtenir une évaluation gratuite. Pour cela, accédez à la page [Essayer Azure][Essayer Azure]. Si vous avez un abonnement MSDN, consultez [Tarifs préférentiels Microsoft Azure : avantages MSDN, MPN et Bizspark][Tarifs préférentiels Microsoft Azure : avantages MSDN, MPN et Bizspark].

## Création du réseau virtuel pour ce didacticiel

Pour créer cet exemple de réseau virtuel cloud uniquement, procédez comme suit.

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].

2.  En bas à gauche de l'écran, cliquez sur **Nouveau** \> **Services de réseau** \> **Réseau virtuel**, puis sur **Création personnalisée** pour lancer l'Assistant Configuration.

    ![][0]

3.  Dans la page **Détails du réseau virtuel**, entrez les informations suivantes :

-   **Nom -** Tapez **VotreRéseauVirtuel**.

-   **Région -** Le réseau virtuel sera créé dans un centre de données situé dans la région indiquée. Pour de meilleures performances, sélectionnez la région dans laquelle vous vous trouvez dans la liste déroulante.

    ![][1]

1.  Cliquez sur la flèche Suivant située en bas à droite. Pour plus d'informations sur les paramètres de cette page, consultez la section sur la page Détails du réseau virtuel dans [﻿À propos des paramètres de réseau virtuel dans le Portail de gestion][﻿À propos des paramètres de réseau virtuel dans le Portail de gestion].

2.  Dans la page **Serveurs DNS et connectivité VPN**, cliquez sur la flèche Suivant en bas à droite. Azure assignera un serveur DNS Azure basé sur Internet aux nouvelles machines virtuelles qui sont ajoutées à ce réseau virtuel, ce qui leur permettra d'accéder aux ressources sur Internet. Pour plus d'informations sur les paramètres de cette page, consultez la section Serveurs DNS et connectivité VPN dans [À propos des paramètres de réseau virtuel dans le Portail de gestion][﻿À propos des paramètres de réseau virtuel dans le Portail de gestion].

3.  Tout comme un vrai réseau, le réseau virtuel a besoin d'une plage d'adresses IP (appelée espace d'adressage) à assigner aux machines virtuelles que vous y incluez. Le réseau virtuel prend aussi en charge les sous-réseaux, qui doivent avoir leurs propres espaces d'adressage, dérivés de l'espace d'adressage du réseau virtuel. Pour ce didacticiel, nous allons créer BackEndSubnet et FrontEndSubnet. Dans la page **Espaces d'adressage du réseau virtuel**, configurez ce qui suit :

	- Pour Espace d'adressage, sélectionnez **/16 (65535)** dans **CIDR (nombre d'adresses)**.

	- Pour les sous-réseaux, sur la première ligne, tapez **BackEndSubnet** en remplacement du nom existant et **10.0.1.0** comme adresse IP de départ, puis sélectionnez **/24 (256)** dans **CIDR (nombre d'adresses)**. Cliquez sur **Ajouter un sous-réseau**, puis tapez **FrontEndSubnet** comme nom et **10.0.2.0** comme adresse IP de départ.
		
	![][2]

 Si nous revenons à notre schéma de réseau virtuel, nous constatons que nous avons configuré les espaces d'adressage suivants :
 
	![][Image7]	

 Notez que l'espace d'adressage d'un réseau virtuel doit provenir des espaces d'adressage privés 10.0.0.0/8, 172.16.0.0/12 ou 192.168.0.0/16 et qu'il doit être spécifié avec la notation CIDR (Classless Inter-Domain Routing), également appelée notation de préfixe réseau. Pour plus d'informations sur les paramètres de cette page, consultez la page Espaces d'adressage de réseau virtuel dans [﻿À propos des paramètres de réseau virtuel dans le Portail de gestion][﻿À propos des paramètres de réseau virtuel dans le Portail de gestion].

1.  Cliquez sur la coche en bas à droite de la page pour créer votre réseau virtuel. Une fois votre réseau virtuel créé, le statut **Créé** apparaît sous Statut dans la page **Réseaux** du portail de gestion Azure.

    ![][3]

Vous pouvez continuer à découvrir les services d'infrastructure Azure en consultant les ressources suivantes :

-   [Création d'une machine virtuelle personnalisée][Création d'une machine virtuelle personnalisée] Cette rubrique vous aide à installer une machine virtuelle sur votre réseau virtuel. Pour plus d'informations sur les machines virtuelles et les options d'installation, consultez [Machines virtuelles Azure][Machines virtuelles Azure].

-   [Installation d'une nouvelle forêt Active Directory sur un réseau virtuel Azure][Installation d'une nouvelle forêt Active Directory sur un réseau virtuel Azure] - Cette rubrique vous aide à installer une nouvelle forêt Windows Server Active Directory (AD) sans connectivité avec un autre réseau. Il décrit les étapes nécessaires à la création d'une machine virtuelle pour une nouvelle installation de forêt. Si vous envisagez de suivre ce didacticiel, ne créez pas de machines virtuelles à l'aide du portail de gestion. Pour plus d'informations, consultez [Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Azure][Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Azure].

Pour supprimer ce réseau virtuel, sélectionnez-le, cliquez sur **Supprimer**, puis cliquez sur **Oui**.

Quand vous êtes prêt à créer un réseau virtuel cloud uniquement qui fonctionne pour votre propre configuration, consultez [Configuration d'un réseau virtuel cloud uniquement dans le Portail de gestion][Configuration d'un réseau virtuel cloud uniquement dans le Portail de gestion].

Si vous recherchez des scénarios de conception et des informations avancées sur le réseau virtuel, consultez [Présentation du réseau virtuel][Présentation du réseau virtuel].

Pour plus d'informations sur les paramètres et procédures de configuration du réseau virtuel, consultez [Tâches de configuration du réseau virtuel][Tâches de configuration du réseau virtuel].

## Voir aussi

-   [FAQ sur le réseau virtuel][FAQ sur le réseau virtuel]

-   [Tâches de configuration du réseau virtuel][4]

-   [Configurer un réseau virtuel à l'aide d'un fichier de configuration réseau][Configurer un réseau virtuel à l'aide d'un fichier de configuration réseau]

-   [Présentation de la résolution de noms Azure][Présentation de la résolution de noms Azure]

  [createvnet]: ./media/create-virtual-network/createVNet_06_VNetExample.png
  [Configuration d'un réseau virtuel cloud uniquement dans le Portail de gestion]: http://msdn.microsoft.com/library/azure/dn631643.aspx
  [Présentation du réseau virtuel]: http://msdn.microsoft.com/library/windowsazure/jj156007.aspx
  [Essayer Azure]: http://www.windowsazure.com/pricing/free-trial/
  [Tarifs préférentiels Microsoft Azure : avantages MSDN, MPN et Bizspark]: http://azure.microsoft.com/fr-fr/pricing/member-offers/msdn-benefits-details/
  [portail de gestion Azure]: http://manage.windowsazure.com/
  [0]: ./media/create-virtual-network/createVNet_01_OpenVirtualNetworkWizard.png
  [1]: ./media/create-virtual-network/createVNet_02_VirtualNetworkDetails.png
  [﻿À propos des paramètres de réseau virtuel dans le Portail de gestion]: http://go.microsoft.com/fwlink/?linkid=248092&clcid=0x409
  [2]: ./media/create-virtual-network/createVNet_04_VirtualNetworkAddressSpaces.png
  [3]: ./media/create-virtual-network/createVNet_05_VirtualNetworkCreatedStatus.png
  [Création d'une machine virtuelle personnalisée]: http://www.windowsazure.com/fr-fr/manage/windows/how-to-guides/custom-create-a-vm/
  [Machines virtuelles Azure]: http://www.windowsazure.com/fr-fr/manage/windows/
  [Installation d'une nouvelle forêt Active Directory sur un réseau virtuel Azure]: http://www.windowsazure.com/fr-fr/manage/services/networking/active-directory-forest/
  [Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj156090.aspx
  [Tâches de configuration du réseau virtuel]: http://go.microsoft.com/fwlink/?linkid=296652&clcid=0x409
  [FAQ sur le réseau virtuel]: http://go.microsoft.com/fwlink/?LinkId=296650
  [4]: http://go.microsoft.com/fwlink/?LinkId=296652
  [Configurer un réseau virtuel à l'aide d'un fichier de configuration réseau]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj156097.aspx
  [Présentation de la résolution de noms Azure]: http://go.microsoft.com/fwlink/?LinkId=248097
  [Image7]: ./media/create-virtual-network/createVNet_07_VNetExampleSpaces.png
