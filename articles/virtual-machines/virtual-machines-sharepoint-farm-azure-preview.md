<properties
	pageTitle="Créer des batteries de serveurs SharePoint | Microsoft Azure"
	description="Créez rapidement une batterie de serveurs SharePoint Server 2013 simple ou à haute disponibilité à l'aide de la fonctionnalité Marketplace du portail Azure en version préliminaire."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/29/2015"
	ms.author="josephd"/>

# Création de batteries de serveurs SharePoint

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle classique.
 
Avec Microsoft Azure Marketplace du portail Azure en version préliminaire, vous pouvez créer rapidement des batteries de serveurs SharePoint Server 2013 préconfigurées. Cela peut vous faire gagner un temps précieux lorsque vous avez besoin d'une batterie de serveurs SharePoint de base ou à haute disponibilité pour un environnement de test et de développement ou si vous envisagez l'adoption de SharePoint Server 2013 comme solution de collaboration pour votre entreprise.

> [AZURE.NOTE]L'élément **Batterie de serveurs SharePoint** dans Azure Marketplace du portail Azure en version préliminaire a été supprimé.

La batterie de serveurs SharePoint de base se compose de trois machines virtuelles dans cette configuration.

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/Non-HAFarm.png)

Vous pouvez utiliser cette configuration de batterie comme configuration simplifiée pour le développement d'applications SharePoint ou pour votre évaluation initiale de SharePoint 2013.

Pour créer la batterie de serveurs SharePoint de base (trois serveurs) :

1. Cliquez [ici](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/).
2. Cliquez sur **Déployer**.
3. Dans le volet **Batterie de serveurs SharePoint 2013 non hautement disponible**, cliquez sur **Créer**.
4. Spécifiez les paramètres des 7 étapes du panneau **Créer une batterie de serveurs SharePoint 2013 non hautement disponible**, puis cliquez sur **Créer**.

La batterie de serveurs SharePoint à haute disponibilité se compose de neuf machines virtuelles dans cette configuration.

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/HAFarm.png)

Vous pouvez utiliser cette configuration de batterie pour tester des charges de clients plus élevées, la haute disponibilité du site SharePoint externe et SQL Server AlwaysOn pour une batterie SharePoint. Vous pouvez également l'utiliser pour le développement d'applications SharePoint dans un environnement à haute disponibilité.

Pour créer la batterie de serveurs SharePoint à haute disponibilité (neuf serveurs) :

1. Cliquez [ici](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/).
2. Cliquez sur **Déployer**.
3. Dans le panneau **Batterie de serveurs SharePoint 2013 hautement disponible**, cliquez sur **Créer**.
4. Spécifiez les paramètres des 7 étapes du panneau **Créer une batterie de serveurs SharePoint 2013 hautement disponible**, puis cliquez sur **Créer**.

> [AZURE.NOTE]Microsoft a publié SharePoint Server 2016 IT Preview. Pour faciliter l’installation et le test de cette version d’évaluation, vous pouvez utiliser une image de la galerie de machines virtuelles Azure avec SharePoint Server 2016 IT Preview et ses composants requis préinstallés. Pour plus d'informations, consultez le billet de blog [Test the SharePoint Server 2016 IT Preview in Azure](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/).

## Gestion des batteries de serveurs SharePoint

Vous pouvez gérer les serveurs de ces batteries par le biais de connexions Bureau à distance. Pour plus d'informations, consultez [Connexion à la machine virtuelle](virtual-machines-windows-tutorial.md#log-on-to-the-virtual-machine).

Sur le site d'administration centrale de SharePoint, vous pouvez configurer Mes sites, des applications SharePoint, ainsi que d'autres fonctionnalités. Pour plus d'informations, consultez la page [Configurer SharePoint 2013](http://technet.microsoft.com/library/ee836142.aspx).

> [AZURE.NOTE]La version préliminaire du portail Azure crée ces deux batteries de serveurs dans un réseau virtuel sur le cloud uniquement avec une présence web accessible sur Internet. Il n’existe pas de connexion VPN de site à site ou ExpressRoute en amont vers le réseau de votre organisation.

## Ressources supplémentaires

[Batteries de serveurs SharePoint hébergés dans des services d’infrastructure Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Configuration d’une batterie de serveurs SharePoint intranet dans un cloud hybride à des fins de test](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[Architectures Microsoft Azure pour SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

<!---HONumber=Nov15_HO2-->