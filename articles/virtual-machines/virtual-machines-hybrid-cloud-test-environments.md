<properties
	pageTitle="Environnement de test de cloud hybride dans Azure| Microsoft Azure"
	description="Recherchez des articles décrivant comment concevoir des environnements professionnels de développement/de test ou de validation technique pour votre cloud hybride Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="index-page"
	ms.date="09/16/2015"
	ms.author="josephd"/>

# Environnements de test de cloud hybride Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Cet article traite de la création de ressources avec le modèle de déploiement classique.

Pour le développement, le test ou une validation technique, les environnements de test de cloud hybride utilisent votre connexion Internet locale ainsi que l’une de vos adresses IP publiques et vous guident pour configurer un réseau virtuel Azure (VNet) opérationnel entre différents locaux. Lorsque vous avez terminé, vous pouvez développer et tester des applications, faire des essais avec des charges de travail informatique simplifiées et évaluer les performances d’une connexion de réseau privé virtuel (VPN) de site à site relative à votre emplacement sur Internet.

## Configuration de base de cloud hybride

La [configuration de base de cloud hybride](../virtual-network/virtual-networks-setup-hybrid-cloud-environment-testing.md) se compose des éléments suivants :

- un réseau local simplifié avec quatre machines virtuelles (un contrôleur de domaine, un serveur d’applications, un ordinateur client et un périphérique VPN exécutant Windows Server et un routage et accès distant) ;
- un réseau virtuel Azure avec un contrôleur de domaine de réplication ;
- une connexion VPN de site à site.

## Batterie de serveurs SharePoint intranet dans un cloud hybride

La [batterie de serveurs SharePoint intranet dans un environnement de test de cloud hybride](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md) ajoute un serveur SQL Server 2014 et un serveur SharePoint Server 2013 à la configuration de base de cloud hybride. Cela crée une batterie de serveurs SharePoint à deux niveaux auquel vous pouvez accéder à partir de l’ordinateur client sur le réseau local simplifié.

## Application métier web dans un cloud hybride

L’[application métier web dans un environnement de test de cloud hybride](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md) ajoute un serveur SQL Server 2014 et IIS (Internet Information Services) à la configuration de base de cloud hybride. Cela crée l’infrastructure dans laquelle vous pouvez déployer et tester une application métier Web à plusieurs niveaux.

## Serveur de synchronisation d’annuaires Office 365 (DirSync) dans un cloud hybride

Le [serveur Office 365 DirSync dans un environnement de test de cloud hybride](../virtual-network/virtual-networks-setup-dirsync-hybrid-cloud-testing.md) ajoute un serveur DirSync à la configuration de base de cloud hybride et illustre Office 365 DirSync avec synchronisation de mot de passe sur un abonnement d’évaluation d’Office 365.

## Simulation d’environnement de test de cloud hybride

Pour les organisations et les personnes ne disposant pas d’une connexion directe à Internet et d’une adresse IP publique, la [simulation d’environnement de test de cloud hybride](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md) génère le réseau local simplifié dans un réseau virtuel Azure distinct et connecte les deux réseaux virtuels à l’aide d’une connexion VPN de réseau virtuel à réseau virtuel.


## Ressources supplémentaires

[Batteries de serveurs SharePoint hébergés dans des services d’infrastructure Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Charge de travail des services d’infrastructure Azure : applications métier à haute disponibilité](virtual-machines-workload-high-availability-LOB-application.md)

[Déploiement de la synchronisation d’annuaires (DirSync) d’Office 365 dans Microsoft Azure](https://technet.microsoft.com/library/dn635310.aspx)

[Instructions d’implémentation des services d’infrastructure Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

<!---HONumber=Sept15_HO3-->