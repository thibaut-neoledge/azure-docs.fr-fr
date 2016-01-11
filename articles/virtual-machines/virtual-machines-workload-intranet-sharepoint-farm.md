<properties
	pageTitle="Batterie de serveurs SharePoint Server 2013 dans Azure | Microsoft Azure"
	description="Découvrez la valeur d'une batterie de serveurs SharePoint Server 2013 dans Azure, configurez un environnement de test et déployez une configuration à haute disponibilité."
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
	ms.date="12/17/2015"
	ms.author="josephd"/>

# Charge de travail des services d'infrastructure Azure : batterie de serveurs SharePoint Intranet

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique

Configurez votre première ou votre prochaine batterie de serveurs SharePoint dans Microsoft Azure, et bénéficiez de la facilité de configuration et de la possibilité d’étendre rapidement la batterie de serveurs pour y ajouter de nouvelles capacités ou y optimiser des fonctionnalités importantes. Beaucoup de batteries de serveurs SharePoint évoluent d’une configuration standard à haute disponibilité et à trois niveaux vers une batterie de serveurs comprenant une dizaine de serveurs ou davantage, optimisés pour les performances ou pour des rôles distincts, comme la mise en cache distribuée ou la recherche.

Avec les machines virtuelles et les fonctionnalités des réseaux virtuels des services d'infrastructure Azure, vous pouvez déployer et exécuter rapidement une batterie de serveurs SharePoint connectée de façon transparente à votre réseau local. Vous pouvez par exemple configurer l’élément suivant :

![](./media/virtual-machines-workload-intranet-sharepoint-farm/workload-spsqlao.png)

Comme Azure Virtual Network est une extension de votre réseau local avec toutes les appellations correctes et le routage de trafic en place, vos utilisateurs y accèdent de la même façon que s’ils se trouvaient dans un centre de données local.

Cette configuration vous permet d’étendre facilement la batterie de serveurs SharePoint en ajoutant de nouvelles machines virtuelles Azure. Les coûts de cette batterie quant au matériel et à la maintenance sont inférieurs à ceux d’une batterie de serveurs équivalente dans votre centre de données.

L’hébergement d’une batterie de serveurs SharePoint intranet dans les services d’infrastructure Azure est un exemple d’application métier. Pour une vue d’ensemble, consultez le [plan de l’architecture des applications métier](http://msdn.microsoft.com/dn630664).

L’étape suivante consiste à configurer une batterie de serveurs SharePoint intranet de développement/test hébergée dans Azure.

> [AZURE.NOTE]Microsoft a publié SharePoint Server 2016 IT Preview. Pour faciliter l’installation et le test de cette version d’évaluation, vous pouvez utiliser une image de la galerie de machines virtuelles Azure avec SharePoint Server 2016 IT Preview et ses composants requis préinstallés. Pour plus d’informations, consultez le billet de blog [Test the SharePoint Server 2016 IT Preview in Azure](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/).

## Créer une batterie de serveurs SharePoint intranet de développement/test hébergée dans Azure

Vous disposez de deux manières de créer un environnement de développement/test pour une batterie de serveurs SharePoint hébergée dans Azure :

- Réseau virtuel cloud uniquement
- Réseau virtuel entre sites locaux

Vous pouvez créer ces environnements de développement / test gratuitement avec votre [abonnement Visual Studio](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/) ou avec un [abonnement d’évaluation Azure](http://azure.microsoft.com/pricing/free-trial/).

### Réseau virtuel cloud uniquement

Un réseau virtuel cloud uniquement n’est pas connecté à un réseau local. Si vous voulez simplement créer rapidement une batterie de serveurs SharePoint de base ou à haute disponibilité, consultez [Création de batteries de serveurs SharePoint](virtual-machines-sharepoint-farm-azure-preview.md). L'exemple suivant montre la configuration d'une batterie de serveurs SharePoint de base.

![](./media/virtual-machines-workload-intranet-sharepoint-farm/Non-HAFarm.png)

### Réseau virtuel entre sites locaux

Un réseau virtuel entre sites locaux est connecté à un réseau local avec une connexion VPN de site à site ou ExpressRoute. Si vous voulez créer un environnement de développement/test qui reproduit la configuration finale, et expérimenter l’accès au serveur SharePoint et l’administration à distance via une connexion VPN, consultez [Configurer une batterie de serveurs SharePoint intranet dans un cloud hybride pour des tests](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md).

![](./media/virtual-machines-workload-intranet-sharepoint-farm/CreateSPFarmHybridCloud.png)

L’étape suivante consiste à créer une batterie de serveurs SharePoint intranet à haute disponibilité dans Azure.

## Déployer une batterie de serveurs SharePoint intranet hébergée dans Azure

La configuration de base représentative pour une batterie de serveurs SharePoint intranet fonctionnelle à haute disponibilité se présente comme suit :

![](./media/virtual-machines-workload-intranet-sharepoint-farm/workload-spsqlao.png)

Elle est constituée de :

- Une batterie SharePoint intranet avec deux serveurs aux niveaux web, application et base de données.
- Une configuration de groupes de disponibilité SQL Server AlwaysOn avec deux serveurs SQL et un ordinateur de nœud majoritaire dans un cluster.
- Deux contrôleurs de domaine de réplica d’un domaine Active Directory local.

Pour voir cette configuration sous forme d’infographie, consultez [SharePoint avec SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788).

Pour déployer cette configuration, procédez comme suit :

- Phase 1 : configuration d'Azure.

	Utilisez Azure PowerShell pour créer un compte de stockage, des groupes à haute disponibilité et un réseau virtuel intersite. Pour les étapes de configuration détaillées, consultez [Phase 1](virtual-machines-workload-intranet-sharepoint-phase1.md).

- Phase 2 : configuration de contrôleurs de domaine.

	Configurez deux contrôleurs de domaine répliqués Active Directory et les paramètres DNS pour le réseau virtuel. Pour les étapes de configuration détaillées, consultez [Phase 2](virtual-machines-workload-intranet-sharepoint-phase2.md).

- Phase 3 : configurer l’infrastructure SQL Server

	Préparez les machines virtuelles SQL Server pour une utilisation avec SharePoint, puis créez le cluster SQL Server. Pour les étapes de configuration détaillées, consultez [Phase 3](virtual-machines-workload-intranet-sharepoint-phase3.md).

- Phase 4 : configurer les serveurs SharePoint

	Configurez les quatre machines virtuelles SharePoint pour une nouvelle batterie de serveurs SharePoint. Pour les étapes de configuration détaillées, consultez [Phase 4](virtual-machines-workload-intranet-sharepoint-phase4.md).

- Phase 5 : créer un groupe de disponibilité AlwaysOn

	Préparez les bases de données SharePoint, créez un groupe de disponibilité AlwaysOn, puis ajoutez-y les bases de données SharePoint. Pour les étapes de configuration détaillées, consultez [Phase 5](virtual-machines-workload-intranet-sharepoint-phase5.md).

Une fois la configuration terminée, vous pouvez développer cette batterie de serveurs SharePoint selon les directives données dans [Architectures Microsoft Azure pour SharePoint 2013](http://technet.microsoft.com/library/dn635309.aspx).

## Étape suivante

- Découvrez un [aperçu](virtual-machines-workload-intranet-sharepoint-overview.md) de la charge de travail de production avant de vous lancer dans la configuration.

<!---HONumber=AcomDC_1223_2015-->