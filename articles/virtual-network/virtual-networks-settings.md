<properties 
   pageTitle="Gestion des propriétés du réseau virtuel"
   description="Découvrez comment afficher et modifier les paramètres du réseau virtuel"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/02/2015"
   ms.author="telmos" />

# Gestion des propriétés du réseau virtuel
Vous pouvez afficher et modifier les paramètres du réseau virtuel à l’aide du portail Azure.

## Afficher les paramètres du réseau virtuel

1. Ouvrez une session sur le portail de gestion.

1. Dans le volet de navigation, cliquez sur **Réseaux**, puis sur le nom du réseau virtuel dans la colonne **Nom**.

1. Cliquez sur **Tableau de bord** pour afficher les informations suivantes :

	- **réseau virtuel :** le réseau virtuel et les sous-réseaux accompagnés du réseau local et du serveur DNS.

	- **données entrantes, données sortantes :** visible lorsque vous disposez d’une connectivité entre différents locaux. Il s'agit des données entrantes et sortantes de votre réseau virtuel.

	- **adresse IP de la passerelle :** visible lorsque vous avez configuré une passerelle. Il s’agit ici de l'adresse IP de la passerelle. Elle représente l'adresse IP que vous utiliserez pour configurer votre périphérique VPN pour une connectivité entre différents locaux.

	- **ressources :** les ressources Azure connectées à ce réseau virtuel.

	- **aperçu rapide :** l’emplacement vous permettant de trouver rapidement les informations suivantes :

		- **Télécharger le package VPN client :** ce package est utilisé lors de la création de connexions VPN de point à site, entre différents locaux. Télécharger le package correspondant au client.

		- **Télécharger le script du périphérique VPN :** ces modèles sont utilisés pour faciliter la configuration des périphériques VPN pour des connexions de site à site, entre différents locaux.

		- **statut**

		- **ID d'abonnement**
		
		- **ID du réseau virtuel**
		
		- **emplacement**

1. Cliquez sur **Configurer** pour afficher les informations suivantes :

	- **serveurs dns :** les serveurs DNS et leur adresse IP correspondante que les clients de votre réseau virtuel utiliseront pour la résolution de noms.

	- **connectivité de point à site :** visible lorsque vous avez configuré votre réseau virtuel pour les connexions de point à site.

	- **connectivité de site à site :** visible lorsque vous avez configuré votre réseau virtuel pour les connexions de site à site.

	- **réseau local :** celui-ci s'affiche si vous disposez d’une connectivité de site à site. Il s'agit ici du réseau local sélectionné qui représente votre site local. Les paramètres de ce réseau local peuvent être configurés dans **réseaux**, sur la page **réseaux locaux**.
	
	- **espaces d'adressage du réseau virtuel :** l'espace d'adressage et les sous-réseaux de votre réseau virtuel.

1. Cliquez sur **Certificats** pour afficher les informations suivantes :

	- **Certificats racines téléchargés :** pour les configurations VPN de point à site
	
	- **Empreinte de certificat**
	
	- **Expiration du certificat**
	
	- **Télécharger :** télécharger un certificat racine
	
	- **Supprimer :** supprimer un certificat racine sélectionné

## Modifier les paramètres du réseau virtuel

Après avoir déployé votre réseau virtuel, seuls certains paramètres peuvent être modifiés. La procédure suivante vous aidera à modifier vos paramètres de configuration du réseau virtuel à l'aide du portail de gestion.

1. Ouvrez une session sur le portail de gestion.

1. Dans le volet de navigation, cliquez sur **Réseaux**, puis sur le nom du réseau virtuel dans la colonne **Nom**.

1. Cliquez sur **Configurer**.

	- Dans **serveurs dns**, vous pouvez ajouter des serveurs DNS supplémentaires pour la résolution de noms. Si vous ajoutez des serveurs DNS supplémentaires après avoir déployé des machines virtuelles sur votre réseau virtuel, vous devez redémarrer chaque machine virtuelle pour qu'elle détecte le nouveau serveur DNS.
	
	- Dans la section **Connectivité de point à site**, vous pouvez configurer l'espace d'adressage que vous souhaitez désigner pour les connexions de point à site.
	
	- Dans la section **Connectivité de site à site**, vous pouvez configurer les paramètres de site à site.
	
	- Dans la section **Espace d'adressage du réseau virtuel**, vous pouvez ajouter un espace d'adressage supplémentaire ainsi que des sous-réseaux. Pour ajouter des sous-réseaux, cliquez sur **Ajouter des sous-réseaux** et configurez l'espace d'adressage que vous souhaitez utiliser. Pour ajouter un sous-réseau de passerelle pour la connectivité entre différents locaux, cliquez sur **Ajouter un sous-réseau de passerelle**. Vous ne pouvez ajouter qu’un seul sous-réseau de passerelle par réseau virtuel.

1. Cliquez sur **Enregistrer** en bas de l’écran pour enregistrer les modifications apportées à votre configuration.

## Étapes suivantes

[Gestion des serveurs DNS utilisés par un réseau virtuel](../virtual-networks-manage-dns-in-vnet)

[Utilisation d’adresses IP publiques dans un réseau virtuel](../virtual-networks-public-ip-within-vnet)

[Suppression d'un réseau virtuel](../virtual-networks-delete-vnet)

<!---HONumber=Oct15_HO3-->