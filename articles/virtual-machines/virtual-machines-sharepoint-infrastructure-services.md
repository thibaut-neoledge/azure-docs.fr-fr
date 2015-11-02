<properties
	pageTitle="Batteries de serveurs SharePoint Server 2013 dans Azure | Microsoft Azure"
	description="Retrouvez les articles décrivant la configuration d'un environnement de développement/test ou d'une batterie de serveurs SharePoint 2013 de production dans Microsoft Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="index-page"
	ms.date="10/20/2015"
	ms.author="josephd"/>

# Batteries de serveurs SharePoint hébergés dans des services d’infrastructure Azure

[AZURE.INCLUDE [learn-about-deployment-models-both-include](../../includes/learn-about-deployment-models-both-include.md)]

Configurez votre première ou prochaine batterie de serveurs SharePoint de développement/test ou de production dans les services d’infrastructure Microsoft Azure, où vous pouvez tirer parti de la facilité de configuration et de la possibilité d’étendre rapidement la batterie de serveurs afin d’inclure de nouvelles capacités ou d’optimiser la fonctionnalité clé.

> [AZURE.NOTE]Microsoft a publié SharePoint Server 2016 IT Preview. Pour faciliter l’installation et le test de cette version d’évaluation, vous pouvez utiliser une image de la galerie de machines virtuelles Azure avec SharePoint Server 2016 IT Preview et ses composants requis préinstallés. Pour plus d’informations, consultez le billet de blog [Test the SharePoint Server 2016 IT Preview in Azure](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/).

## Batterie de serveurs de développement/test SharePoint de base

Pour les machines virtuelles créées à l’aide du modèle de déploiement Resource Manager, consultez l’élément [Batterie de serveurs non hautement disponible SharePoint 2013](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/) dans Azure Marketplace du portail Azure en version préliminaire. Cela crée une batterie de serveurs de développement/test de base pour un site web SharePoint sur Internet.

Vous pouvez également utiliser un modèle Azure Resource Manager. Consultez la page [Déployer une batterie de serveurs SharePoint à trois serveurs](virtual-machines-workload-template-sharepoint.md#deploy-a-three-server-sharepoint-farm).

L’environnement créé automatiquement se compose de trois serveurs pour un contrôleur de domaine, d’un serveur SQL et du serveur SharePoint sur un réseau virtuel Azure cloud uniquement.

Pour créer une configuration similaire avec le modèle de déploiement classique, utilisez l’élément [Batterie de serveurs SharePoint](virtual-machines-sharepoint-farm-azure-preview.md) dans Azure Marketplace du portail Azure en version préliminaire.


## Batterie de serveurs de développement/test SharePoint haute disponibilité

Pour les machines virtuelles créées à l’aide du modèle de déploiement Resource Manager, consultez l’élément [Batterie de serveurs hautement disponible SharePoint 2013](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/) dans Azure Marketplace du portail Azure en version préliminaire. Cela crée une batterie de serveurs hautement disponible pour un site web SharePoint sur Internet.

Vous pouvez également utiliser un modèle Azure Resource Manager. Consultez la page [Déployer une batterie de serveurs SharePoint à neuf serveurs](virtual-machines-workload-template-sharepoint.md#deploy-a-nine-server-sharepoint-farm).

L’environnement créé automatiquement se compose de neuf serveurs sur un réseau virtuel Azure cloud uniquement : deux pour les contrôleurs de domaine, trois pour un cluster SQL Server, deux serveurs SharePoint de couche Application et deux serveurs SharePoint de niveau web.

Pour créer une configuration similaire avec le modèle de déploiement classique, utilisez l’élément [Batterie de serveurs SharePoint](virtual-machines-sharepoint-farm-azure-preview.md) dans Azure Marketplace du portail Azure en version préliminaire.


## Batterie de serveurs de développement/test de cloud hybride

La [batterie de serveurs SharePoint intranet dans un environnement de développement/test de cloud hybride](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md) vous permet de créer une simulation de configuration de cloud hybride qui héberge une batterie SharePoint simple, à deux niveaux, que vous pouvez utiliser pour tester une batterie de serveurs SharePoint intranet hébergée dans Azure à partir de votre emplacement sur Internet.

Cette configuration utilise des machines virtuelles classiques.

## Batterie de serveurs de production SharePoint intranet haute disponibilité

Le déploiement de [SharePoint 2013 avec des groupes de disponibilité AlwaysOn SQL Server dans Azure](virtual-machines-workload-intranet-sharepoint-overview.md) permet de générer une batterie de serveurs SharePoint Server 2013 intranet haute disponibilité et prête pour la production dans Azure.

Cette configuration utilise des machines virtuelles classiques.

## Ressources supplémentaires

[Architectures Microsoft Azure pour SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Sites Internet dans Microsoft Azure qui utilisent SharePoint Server 2013](https://technet.microsoft.com/library/dn635307.aspx)

[Récupération d’urgence SharePoint Server 2013 dans Microsoft Azure](https://technet.microsoft.com/library/dn635313.aspx)

[Utilisation de Microsoft Azure Active Directory pour l’authentification SharePoint 2013](https://technet.microsoft.com/library/dn635311.aspx)

[Déploiement de la synchronisation d’annuaires (DirSync) d’Office 365 dans Microsoft Azure](https://technet.microsoft.com/library/dn635310.aspx)

<!---HONumber=Oct15_HO4-->