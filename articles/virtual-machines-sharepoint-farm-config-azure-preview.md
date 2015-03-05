<properties 
	pageTitle="Informations de configuration de la batterie de serveurs SharePoint" 
	description="Décrit la configuration par défaut des batteries de serveurs SharePoint" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-sharepoint" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="1/26/2015" 
	ms.author="josephd"/>


#Informations de configuration de la batterie de serveurs SharePoint#

Avec la batterie de serveurs SharePoint, la version préliminaire du portail Microsoft Azure crée automatiquement une batterie SharePoint Server 2013 préconfigurée. Deux configurations de batterie sont disponibles :

- De base
- Haute disponibilité

Les sections suivantes fournissent des informations de configuration pour chaque batterie.

Pour plus d'informations, consultez [Batterie de serveurs SharePoint](../virtual-machines-sharepoint-farm-azure-preview/)

##Batterie de serveurs SharePoint de base##

La batterie de serveurs SharePoint de base se compose de trois machines virtuelles dans cette configuration :

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_Basic.png) 

Voici les informations de configuration :

-	Abonnement Azure : défini lors de la configuration initiale.
-	Noms de domaines Azure (également appelés services cloud) : des noms de domaines distincts sont automatiquement créés pour chaque machine virtuelle.
-	Compte de stockage : défini lors de la configuration initiale.
-	Réseau virtuel 	
	-   Type : cloud uniquement	
    -	Espace d'adressage : 192.168.16.0/26    

- Machines virtuelles
	-	*HostNamePrefix*-DC (contrôleur de domaine AD DS)
	-	*HostNamePrefix*- SQL (serveur SQL Server 2014)
	-	*HostNamePrefix*-SP (serveur SharePoint 2013)

- Contrôleur de domaine
	-	Préfixe du nom d'hôte : défini lors de la configuration initiale.
	-	Taille : A1 (par défaut)
	-	Nom de domaine : contoso.com (par défaut)
	-	Nom de compte de l'administrateur du domaine : défini lors de la configuration initiale.
	-	Mot de passe du compte de l'administrateur du domaine : défini lors de la configuration initiale.

- SQL Server
	-	Préfixe du nom d'hôte : défini lors de la configuration initiale.
	-	Taille : A5 (par défaut)
	-	Nom du compte d'accès à la base de données : défini lors de la configuration initiale.
	-	Mot de passe du compte d'accès à la base de données : défini lors de la configuration initiale.
	-	Nom du compte du service SQL Server : défini lors de la configuration initiale.
	-	Mot de passe du compte du service SQL Server : défini lors de la configuration initiale.

- Serveur SharePoint
	-	Préfixe du nom d'hôte : défini lors de la configuration initiale.
	-	Taille : A2 (par défaut)
	-	Nom du compte de la batterie de serveurs SharePoint : défini lors de la configuration initiale.
	-	Mot de passe du compte de la batterie de serveurs SharePoint : défini lors de la configuration initiale.
	-	Phrase secrète de la batterie de serveurs SharePoint : défini lors de la configuration initiale.


##Batterie de serveurs SharePoint haute disponibilité##

La batterie de serveurs SharePoint à haute disponibilité se compose de neuf machines virtuelles dans cette configuration :

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_HighAvail.png)
 
Voici les informations de configuration :

-	Abonnement Azure : défini lors de la configuration initiale.
-	Noms de domaines Azure (également appelés services cloud) : Les noms de domaine distincts sont créés conformément à la figure ci-dessus.
-	Compte de stockage : défini lors de la configuration initiale.
-	Réseau virtuel	
	-	Type : cloud uniquement
	-	Espace d'adressage : 192.168.16.0/26	

-	Machines virtuelles
	-	*HostNamePrefix*-DC1 (contrôleur de domaine AD DS)
	-	*HostNamePrefix*-DC2 (contrôleur de domaine AD DS)
	-	*HostNamePrefix*- SQL1 (serveur SQL Server 2014)
	-	*HostNamePrefix*- SQL2 (serveur SQL Server 2014)
	-	*HostNamePrefix*-SQL0 (serveur Windows Server 2012 R2)
	-	*HostNamePrefix*-WEB1 (serveur SharePoint 2013)
	-	*HostNamePrefix*-WEB2 (serveur SharePoint 2013)
	-	*HostNamePrefix*-APP1 (serveur SharePoint 2013)
	-	*HostNamePrefix*-APP2 (serveur SharePoint 2013)

-	Contrôleurs de domaine
	-	Préfixe du nom d'hôte : défini lors de la configuration initiale.
	-	Taille : A1 (par défaut)
	-	Nom de domaine : contoso.com (par défaut)
	-	Nom de compte de l'administrateur du domaine : défini lors de la configuration initiale.
	-	Mot de passe du compte de l'administrateur du domaine : défini lors de la configuration initiale.

-	Serveurs SQL
	-	Préfixe du nom d'hôte : défini lors de la configuration initiale.
	-	Taille : A5 (par défaut)
	-	Nom du compte d'accès à la base de données : défini lors de la configuration initiale.
	-	Mot de passe du compte d'accès à la base de données : défini lors de la configuration initiale.
	-	Nom du compte du service SQL Server : défini lors de la configuration initiale.
	-	Mot de passe du compte du service SQL Server : défini lors de la configuration initiale.

-	Serveurs SharePoint
	-	Préfixe du nom d'hôte : défini lors de la configuration initiale.
	-	Taille : A2 (par défaut)
	-	Nom du compte de la batterie de serveurs SharePoint : défini lors de la configuration initiale.
	-	Mot de passe du compte de la batterie de serveurs SharePoint : défini lors de la configuration initiale.		
	-	Phrase secrète de la batterie de serveurs SharePoint : défini lors de la configuration initiale.

##Ressources supplémentaires##

[Batterie de serveurs SharePoint](../virtual-machines-sharepoint-farm-azure-preview/)

[SharePoint sur les services d'infrastructure Azure](http://msdn.microsoft.com/library/azure/dn275955.aspx)

<!--HONumber=45--> 
