<properties 
	pageTitle="Batteries de serveurs SharePoint hébergés dans des services d’infrastructure Azure" 
	description="Accédez aux rubriques essentielles décrivant comment configurer une batterie de serveurs SharePoint 2013 de développement/test ou de production dans les services d’infrastructure Azure." 
	documentationCenter="" 
	services="virtual-machines"
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/06/2015" 
	ms.author="josephd"/>

# Batteries de serveurs SharePoint hébergés dans des services d’infrastructure Azure

Configurez votre première ou prochaine batterie de serveurs SharePoint de développement/test ou de production dans les services d’infrastructure Microsoft Azure, où vous pouvez tirer parti de la facilité de configuration et de la possibilité d’étendre rapidement la batterie de serveurs afin d’inclure de nouvelles capacités ou d’optimiser la fonctionnalité clé.

## Batterie de serveurs de développement/test SharePoint de base 

Vous pouvez utiliser le modèle [Batterie de serveurs SharePoint](virtual-machines-sharepoint-farm-azure-preview.md) du portail Azure en version préliminaire pour créer une batterie de serveurs de développement/test de base pour un site web SharePoint accessible sur Internet.

L’environnement créé automatiquement se compose de trois serveurs pour un contrôleur de domaine, d’un serveur SQL et du serveur SharePoint sur un réseau virtuel Azure cloud uniquement.

## Batterie de serveurs de développement/test SharePoint hautement disponible

Vous pouvez également utiliser le modèle [Batterie de serveurs SharePoint](virtual-machines-sharepoint-farm-azure-preview.md) du portail Azure en version préliminaire pour créer une batterie de serveurs de développement/test SharePoint à haute disponibilité pour un site web SharePoint accessible sur Internet.

L’environnement créé automatiquement est composé de neuf serveurs dans un réseau virtuel Azure cloud uniquement : deux pour les contrôleurs de domaine, trois pour un cluster SQL server, deux serveurs SharePoint de couche application et deux serveurs SharePoint de niveau web.

## Batterie de serveurs de développement/test de cloud hybride

La [batterie de serveurs SharePoint intranet dans un environnement de développement/test de cloud hybride](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md) vous permet de créer une simulation de configuration de cloud hybride qui héberge une batterie SharePoint simple, à deux niveaux, que vous pouvez utiliser pour tester une batterie de serveurs SharePoint intranet hébergée dans Azure à partir de votre emplacement sur Internet.

## Batterie de serveurs de production SharePoint intranet hautement disponible

Le [déploiement de SharePoint 2013 avec des groupes de disponibilité AlwaysOn SQL Server dans Azure](https://msdn.microsoft.com/library/dn275959.aspx) permet de générer une batterie de serveurs SharePoint Server 2013 intranet hautement disponible et prête pour la production dans Azure.

## Ressources supplémentaires

[SharePoint Server sur les services d’infrastructure Azure](https://msdn.microsoft.com/library/dn275955.aspx)

[Planification pour SharePoint 2013 sur les services d’infrastructure Azure](https://msdn.microsoft.com/library/dn275958.aspx)

[Architectures Microsoft Azure pour SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

<!--HONumber=52-->
 