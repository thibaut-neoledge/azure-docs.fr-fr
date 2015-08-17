<properties 
	pageTitle="Charge de travail des services d’infrastructure Azure : batterie de serveurs SharePoint intranet" 
	description="Découvrez la valeur d’une batterie de serveurs SharePoint intranet déployée dans Azure, comment configurer un environnement de développement/test et comment déployer une configuration de production à haute disponibilité." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows-sharepoint" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/21/2015" 
	ms.author="josephd"/>

# Charge de travail des services d’infrastructure Azure : batterie de serveurs SharePoint intranet

Configurez votre première ou votre prochaine batterie de serveurs SharePoint dans Microsoft Azure, et bénéficiez de la facilité de configuration et de la possibilité d’étendre rapidement la batterie de serveurs pour y ajouter de nouvelles capacités ou y optimiser des fonctionnalités importantes. Beaucoup de batteries de serveurs SharePoint évoluent d’une configuration standard à haute disponibilité et à trois niveaux vers une batterie de serveurs comprenant une dizaine de serveurs ou davantage, optimisés pour les performances ou pour des rôles distincts, comme la mise en cache distribuée ou la recherche.
 
Avec les machines virtuelles et les fonctionnalités des réseaux virtuels des services d’infrastructure Azure, vous pouvez déployer et faire fonctionner rapidement une batterie de serveurs SharePoint connectée de façon transparente à votre réseau local. Par exemple, vous pouvez configurer ceci.

![](./media/virtual-machines-workload-intranet-sharepoint-farm/workload-spsqlao.png)
 
Comme Azure Virtual Network est une extension de votre réseau local avec toutes les appellations correctes et le routage de trafic en place, vos utilisateurs y accèdent de la même façon que s’ils se trouvaient dans un centre de données local.

Cette configuration vous permet d’étendre facilement la batterie de serveurs SharePoint en ajoutant de nouvelles machines virtuelles Azure. Les coûts de cette batterie quant au matériel et à la maintenance sont inférieurs à ceux d’une batterie de serveurs équivalente dans votre centre de données.

L’hébergement d’une batterie de serveurs SharePoint intranet dans les services d’infrastructure Azure est un exemple d’application métier. Pour une vue d’ensemble, consultez le [plan de l’architecture des applications métier](http://msdn.microsoft.com/dn630664).

L’étape suivante consiste à configurer une batterie de serveurs SharePoint intranet de développement/test hébergée dans Azure.

## Créer une batterie de serveurs SharePoint intranet de développement/test hébergée dans Azure

Deux choix seulement s’offrent à vous pour créer un environnement de développement/test pour une batterie de serveurs SharePoint hébergée dans Azure :

- Réseau virtuel cloud uniquement
- Réseau virtuel entre sites locaux

Vous pouvez créer ces environnements de développement/test gratuitement avec votre [abonnement MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/) ou avec un [abonnement d’évaluation Azure](http://azure.microsoft.com/pricing/free-trial/).

### Réseau virtuel cloud uniquement

Un réseau virtuel cloud uniquement n’est pas connecté à un réseau local. Si vous voulez simplement créer rapidement une batterie de serveurs SharePoint de base ou à haute disponibilité, consultez [Batterie de serveurs SharePoint](virtual-machines-sharepoint-farm-azure-preview.md). Voici la configuration d’une batterie de serveurs SharePoint de base.

![](./media/virtual-machines-workload-intranet-sharepoint-farm/SPFarm_Basic.png)
 
### Réseau virtuel entre sites locaux

Un réseau virtuel entre sites locaux est connecté à un réseau local avec une connexion VPN de site à site ou ExpressRoute. Si vous voulez créer un environnement de développement/test qui reproduit la configuration finale, et expérimenter l’accès au serveur SharePoint et l’administration à distance via une connexion VPN, consultez [Configurer une batterie de serveurs SharePoint intranet dans un cloud hybride pour des tests](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md).

![](./media/virtual-machines-workload-intranet-sharepoint-farm/CreateSPFarmHybridCloud.png)
 
L’étape suivante consiste à créer une batterie de serveurs SharePoint intranet à haute disponibilité dans Azure.

## Déployer une batterie de serveurs SharePoint intranet hébergée dans Azure

La configuration de référence représentative pour une batterie de serveurs SharePoint intranet fonctionnelle à haute disponibilité dans Azure se présente comme suit.

![](./media/virtual-machines-workload-intranet-sharepoint-farm/workload-spsqlao.png)
 
Elle est constituée de :

- Une batterie SharePoint intranet avec deux serveurs aux niveaux web, application et base de données.
- Une configuration de groupes de disponibilité SQL Server AlwaysOn avec deux serveurs SQL Server et un ordinateur de nœud majoritaire dans un cluster.
- Active Directory dans le réseau virtuel avec deux contrôleurs de domaine répliqués.

Pour voir cette configuration sous forme d’infographie, consultez [SharePoint avec SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788).

### Nomenclature

Cette configuration de référence requiert l’ensemble suivant de services et de composants Azure :

- Neuf machines virtuelles Azure
- Quatre disques de données supplémentaires pour les contrôleurs de domaine et les serveurs SQL Server
- Trois services cloud
- Quatre groupes à haute disponibilité
- Un réseau virtuel entre sites locaux
- Un compte de stockage
- Un abonnement Azure

### Phases de déploiement

Pour déployer cette configuration, procédez comme suit :

- Phase 1 : configurer Azure 

	Utilisez le portail de gestion Azure et Azure PowerShell pour créer un compte de stockage, les services cloud et un réseau virtuel entre les sites locaux. Pour les étapes de configuration détaillées, consultez [Phase 1](virtual-machines-workload-intranet-sharepoint-phase1.md).

- Phase 2 : configurer les contrôleurs de domaine

	Configurez deux contrôleurs de domaine répliqués Active Directory et les paramètres DNS pour le réseau virtuel. Pour les étapes de configuration détaillées, consultez [Phase 2](virtual-machines-workload-intranet-sharepoint-phase2.md).

- Phase 3 : configurer l’infrastructure SQL Server

	Préparez les machines virtuelles SQL Server pour une utilisation avec SharePoint, puis créez le cluster SQL Server. Pour les étapes de configuration détaillées, consultez [Phase 3](virtual-machines-workload-intranet-sharepoint-phase3.md).

- Phase 4 : configurer les serveurs SharePoint

	Configurez les quatre machines virtuelles SharePoint pour une nouvelle batterie de serveurs SharePoint. Pour les étapes de configuration détaillées, consultez [Phase 4](virtual-machines-workload-intranet-sharepoint-phase4.md).

- Phase 5 : créer un groupe de disponibilité AlwaysOn

	Préparez les bases de données SharePoint, créez un groupe de disponibilité AlwaysOn, puis ajoutez-y les bases de données SharePoint. Pour les étapes de configuration détaillées, consultez [Phase 5](virtual-machines-workload-intranet-sharepoint-phase5.md).

Une fois la configuration terminée, vous pouvez développer cette batterie de serveurs SharePoint selon les directives données dans [Architectures Microsoft Azure pour SharePoint 2013](http://technet.microsoft.com/library/dn635309.aspx).

## Ressources supplémentaires

[Déploiement de SharePoint avec des groupes de disponibilité SQL Server AlwaysOn dans Azure](../virtual-machines-workload-deploy-spsqlao-overview.md)

[Configuration d’une batterie de serveurs SharePoint intranet dans un cloud hybride à des fins de test](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[Architectures Microsoft Azure pour SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Infographie SharePoint avec SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[Batteries de serveurs SharePoint hébergés dans des services d’infrastructure Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Instructions pour l’implémentation des services d’infrastructure Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

<!---HONumber=August15_HO6-->