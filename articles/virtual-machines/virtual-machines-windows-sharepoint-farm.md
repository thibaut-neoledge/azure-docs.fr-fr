<properties
	pageTitle="Créer des batteries de serveurs SharePoint | Microsoft Azure"
	description="Créez rapidement une batterie de serveurs SharePoint Server 2013 simple ou à haute disponibilité à l'aide de la fonctionnalité Marketplace du portail Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="josephd"/>

# Création de batteries de serveurs SharePoint

Avec Microsoft Azure Marketplace du portail Azure, vous pouvez créer rapidement des batteries de serveurs SharePoint Server 2013 préconfigurées. Cela peut vous faire gagner un temps précieux lorsque vous avez besoin d'une batterie de serveurs SharePoint de base ou à haute disponibilité pour un environnement de test et de développement ou si vous envisagez l'adoption de SharePoint Server 2013 comme solution de collaboration pour votre entreprise.

> [AZURE.NOTE] L'élément **Batterie de serveurs SharePoint** dans Azure Marketplace du portail Azure a été supprimé. Il a été remplacé par les éléments **Batterie de serveurs SharePoint 2013 non hautement disponible** et **Batterie de serveurs SharePoint 2013 hautement disponible**.

La batterie de serveurs SharePoint de base se compose de trois machines virtuelles dans cette configuration.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/Non-HAFarm.png)

Vous pouvez utiliser cette configuration de batterie comme configuration simplifiée pour le développement d'applications SharePoint ou pour votre évaluation initiale de SharePoint 2013.

Pour créer la batterie de serveurs SharePoint de base (trois serveurs) :

1. Cliquez [ici](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/).
2. Cliquez sur **Déployer**.
3. Dans le volet **Batterie de serveurs SharePoint 2013 non hautement disponible**, cliquez sur **Créer**.
4. Spécifiez les paramètres des 7 étapes du panneau de **création d’une batterie de serveurs SharePoint 2013 non hautement disponible**, puis cliquez sur **Créer**.

La batterie de serveurs SharePoint à haute disponibilité se compose de neuf machines virtuelles dans cette configuration.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/HAFarm.png)

Vous pouvez utiliser cette configuration de batterie de serveurs pour tester des charges de clients plus élevées, la haute disponibilité du site SharePoint externe et SQL Server Always On pour une batterie de serveurs SharePoint. Vous pouvez également l'utiliser pour le développement d'applications SharePoint dans un environnement à haute disponibilité.

Pour créer la batterie de serveurs SharePoint à haute disponibilité (neuf serveurs) :

1. Cliquez [ici](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/).
2. Cliquez sur **Déployer**.
3. Dans le panneau **Batterie de serveurs SharePoint 2013 hautement disponible**, cliquez sur **Créer**.
4. Spécifiez les paramètres des 7 étapes du panneau **de création d’une batterie de serveurs SharePoint 2013 hautement disponible**, puis cliquez sur **Créer**.

> [AZURE.NOTE] Vous ne pouvez pas créer la **batterie de serveurs SharePoint 2013 non hautement disponible** ou la **batterie de serveurs SharePoint 2013 hautement disponible** avec un essai gratuit d’Azure.

Le portail Azure crée ces deux batteries de serveurs dans un réseau virtuel sur le cloud uniquement avec une présence Web accessible sur Internet. Il n’existe pas de connexion VPN de site à site ou ExpressRoute en amont vers le réseau de votre organisation.

## Gestion des batteries de serveurs SharePoint

Vous pouvez gérer les serveurs de ces batteries par le biais de connexions Bureau à distance. Pour plus d'informations, consultez [Connexion à la machine virtuelle](virtual-machines-windows-hero-tutorial.md#log-on-to-the-virtual-machine).

Sur le site d'administration centrale de SharePoint, vous pouvez configurer Mes sites, des applications SharePoint, ainsi que d'autres fonctionnalités. Pour plus d'informations, consultez la page [Configurer SharePoint 2013](http://technet.microsoft.com/library/ee836142.aspx).

> [AZURE.NOTE] Vous pouvez utiliser l’[image de la version d’évaluation de SharePoint Server 2016](https://azure.microsoft.com/blog/test-sharepoint-server-2016/) pour créer une machine virtuelle exécutant SharePoint Server 2016.

## Étapes suivantes

- Découvrez d’autres configurations de [SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) dans les services d’infrastructure Azure.

<!---HONumber=AcomDC_0601_2016-->