<properties 
	pageTitle="Informations de configuration de la batterie de serveurs SharePoint" 
	description="Décrit la configuration par défaut des batteries de serveurs SharePoint" 
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
	ms.date="04/09/2015" 
	ms.author="josephd"/>


# Informations de configuration de la batterie de serveurs SharePoint

Avec la batterie de serveurs SharePoint, la version préliminaire du portail Microsoft Azure crée automatiquement une batterie SharePoint Server 2013 préconfigurée. Deux configurations de batterie sont disponibles :

- De base
- Haute disponibilité

Les sections suivantes fournissent des informations de configuration pour chaque batterie.

Pour plus d'informations, consultez la page [Batterie de serveurs SharePoint](virtual-machines-sharepoint-farm-azure-preview.md).

## Batterie de serveurs SharePoint de base

La batterie de serveurs SharePoint de base se compose de trois machines virtuelles dans cette configuration :

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_Basic.png)

Voici les informations de configuration :

-	Abonnement Azure : défini lors de la configuration initiale.
-	Noms de domaine Azure (également appelés services cloud) : des noms de domaines distincts sont automatiquement créés pour chaque machine virtuelle.
-	Compte de stockage : défini lors de la configuration initiale.
-	Réseau virtuel 	
	-   Type : cloud uniquement	
    -	Espace d’adressage : 192.168.16.0/26    

- Machines virtuelles
	-	*HostNamePrefix*-DC (contrôleur de domaine AD DS)
	-	*HostNamePrefix*- SQL (serveur SQL Server 2014)
	-	*HostNamePrefix*-SP (serveur SharePoint 2013)

- Contrôleur de domaine
	-	Image de machine virtuelle : Windows Server 2012 R2.
	-	Préfixe du nom d’hôte : défini lors de la configuration initiale.
	-	Taille : A1 (par défaut).
	-	Nom de domaine : contoso.com (par défaut)
	-	Nom de compte administrateur de domaine : défini lors de la configuration initiale.
	-	Mot de passe de compte administrateur de domaine : défini lors de la configuration initiale.

- SQL Server
	-	Image de machine virtuelle : SQL Server 2014 RTM Enterprise sur Windows Server 2012 R2.
	-	Préfixe du nom d’hôte : défini lors de la configuration initiale.
	-	Taille : A5 (par défaut).
	-	Nom de compte d’accès à la base de données : défini lors de la configuration initiale.
	-	Mot de passe de compte d’accès à la base de données : défini lors de la configuration initiale.
	-	Nom de compte de service SQL Server : défini lors de la configuration initiale.
	-	Mot de passe de compte de service SQL Server : défini lors de la configuration initiale.

- Serveur SharePoint
	-	Image de machine virtuelle : version d’évaluation de SharePoint Server 2013.
	-	Préfixe du nom d’hôte : défini lors de la configuration initiale.
	-	Taille : A2 (par défaut).
	-	Nom de compte de batterie de serveurs SharePoint : défini lors de la configuration initiale.
	-	Mot de passe de compte de batterie de serveurs SharePoint : défini lors de la configuration initiale.
	-	Phrase secrète de batterie de serveurs SharePoint : défini lors de la configuration initiale.


## Batterie de serveurs SharePoint haute disponibilité

La batterie de serveurs SharePoint à haute disponibilité se compose de neuf machines virtuelles dans cette configuration :

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_HighAvail.png)
 
Voici les informations de configuration :

-	Abonnement Azure : défini lors de la configuration initiale.
-	Noms de domaine Azure (également appelés services cloud) : des noms de domaines distincts sont créés en fonction de la figure ci-dessus.
-	Compte de stockage : défini lors de la configuration initiale.
-	Réseau virtuel	
	-	Type : cloud uniquement
	-	Espace d’adressage : 192.168.16.0/26	

-	Machines virtuelles
	-	*HostNamePrefix*-DC1 (contrôleur de domaine AD DS)
	-	*HostNamePrefix*-DC2 (contrôleur de domaine AD DS)
	-	*HostNamePrefix*-SQL1 (serveur SQL Server 2014)
	-	*HostNamePrefix*-SQL2 (serveur SQL Server 2014)
	-	*HostNamePrefix*-SQL0 (serveur Windows Server 2012 R2)
	-	*HostNamePrefix*-WEB1 (serveur SharePoint 2013)
	-	*HostNamePrefix*-WEB2 (serveur SharePoint 2013)
	-	*HostNamePrefix*-APP1 (serveur SharePoint 2013)
	-	*HostNamePrefix*-APP2 (serveur SharePoint 2013)

-	Contrôleurs de domaine
	-	Image de machine virtuelle : Windows Server 2012 R2.
	-	Préfixe du nom d’hôte : défini lors de la configuration initiale.
	-	Taille : A1 (par défaut).
	-	Nom de domaine : contoso.com (par défaut)
	-	Nom de compte administrateur de domaine : défini lors de la configuration initiale.
	-	Mot de passe de compte administrateur de domaine : défini lors de la configuration initiale.

-	Serveurs SQL
	-	Image de machine virtuelle : SQL Server 2014 RTM Enterprise sur Windows Server 2012 R2.
	-	Préfixe du nom d’hôte : défini lors de la configuration initiale.
	-	Taille : A5 (par défaut).
	-	Nom de compte d’accès à la base de données : défini lors de la configuration initiale.
	-	Mot de passe de compte d’accès à la base de données : défini lors de la configuration initiale.
	-	Nom de compte de service SQL Server : défini lors de la configuration initiale.
	-	Mot de passe de compte de service SQL Server : défini lors de la configuration initiale.

-	Serveurs SharePoint
	-	Image de machine virtuelle : version d’évaluation de SharePoint Server 2013.
	-	Préfixe du nom d’hôte : défini lors de la configuration initiale.
	-	Taille : A2 (par défaut).
	-	Nom de compte de batterie de serveurs SharePoint : défini lors de la configuration initiale.
	-	Mot de passe de compte de batterie de serveurs SharePoint : défini lors de la configuration initiale.		
	-	Phrase secrète de batterie de serveurs SharePoint : défini lors de la configuration initiale.

> [AZURE.NOTE]Les serveurs SharePoint sont créés à partir de l’image de la version d’évaluation de SharePoint Server 2013. Pour continuer à utiliser la machine virtuelle après l’arrivée à expiration de la version d’évaluation, vous devez convertir l’installation afin qu’elle utilise une clé de produit commercialisé ou une clé de produit de licence en volume pour les éditions Standard ou Enterprise de SharePoint Server 2013.

## Ressources supplémentaires

[Batterie de serveurs SharePoint](virtual-machines-sharepoint-farm-azure-preview.md)

[SharePoint Server sur les services d’infrastructure Azure](http://msdn.microsoft.com/library/azure/dn275955.aspx)

[Configuration d’une batterie de serveurs SharePoint intranet dans un cloud hybride à des fins de test](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)
 

<!---HONumber=58_postMigration-->