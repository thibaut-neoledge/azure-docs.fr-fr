<properties 
	pageTitle="Application métier dans Azure | Microsoft Azure" 
	description="Découvrez la valeur d'une application métier dans Azure, configurez un environnement de test et déployez une configuration à haute disponibilité." 
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
	ms.date="08/11/2015" 
	ms.author="josephd"/>

# Charge de travail des services d’infrastructure Azure : applications métier à haute disponibilité

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique


Configurez votre première ou prochaine application métier basée sur le Web et exclusivement conçue pour l’Intranet dans Microsoft Azure et bénéficiez de la facilité de configuration et de la possibilité d’étendre rapidement l’application pour y ajouter de nouvelles capacités.
 
Avec les machines virtuelles et les fonctionnalités des réseaux virtuels des services d’infrastructure Azure, vous pouvez déployer et faire fonctionner rapidement une application métier accessible aux utilisateurs de votre organisation. Par exemple, vous pouvez configurer ceci.

![](./media/virtual-machines-workload-high-availability-LOB-application/workload-lobapp-phase4.png)
 
Comme Azure Virtual Network est une extension de votre réseau local avec toutes les appellations correctes et le routage de trafic en place, vos utilisateurs accèdent à l’application métier comme s’ils se trouvaient dans un centre de données local.

Cette configuration vous permet d’étendre facilement les capacités de l’application en ajoutant de nouvelles machines virtuelles Azure grâce auxquelles les coûts récurrents générés par le matériel et la maintenance sont inférieurs à ceux d’équipements équivalents dans votre centre de données.

L'étape suivante consiste pour vous à définir une ligne de développement/test de l'application métier hébergée dans Azure.

## Création d’une ligne de développement/test de l'application métier hébergée dans Azure

Un réseau virtuel entre sites locaux est connecté à un réseau local avec une connexion VPN de site à site ou ExpressRoute. Si vous voulez créer un environnement de développement/test qui reproduit la configuration finale et expérimenter l’accès à l’application et l’administration à distance via une connexion VPN, consultez [Configurer une application métier basée sur le Web dans un cloud hybride pour des tests](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md).

![](./media/virtual-machines-workload-high-availability-LOB-application/CreateLOBAppHybridCloud_3.png)
 
Vous pouvez créer cet environnement de développement/test gratuitement avec votre [abonnement MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/) ou avec un [abonnement d’évaluation Azure](http://azure.microsoft.com/pricing/free-trial/).

La prochaine étape est la création d’une application métier à haute disponibilité dans Azure.

## Déploiement d’une application métier hébergée dans Azure

La configuration de référence représentative pour une application métier à haute disponibilité dans Azure se présente comme suit.

![](./media/virtual-machines-workload-high-availability-LOB-application/workload-lobapp-phase4.png)
 
Elle est constituée de :

- une application métier spécialement conçue pour l’Intranet avec deux serveurs aux niveaux Web et base de données ;
- une configuration SQL Server AlwaysOn avec deux machines virtuelles exécutant SQL Server et un ordinateur de nœud majoritaire dans un cluster.
- Services de domaine Active Directory dans le réseau virtuel avec deux contrôleurs de domaine répliqués.

Pour obtenir une vue d’ensemble des applications métier, consultez le [Plan de l’architecture des applications métier](http://msdn.microsoft.com/dn630664).

### Nomenclature

Cette configuration de référence requiert l’ensemble suivant de services et de composants Azure :

- Sept machines virtuelles
- Quatre disques de données supplémentaires pour les contrôleurs de domaine et les machines virtuelles exécutant SQL Server
- Trois groupes à haute disponibilité
- Un réseau virtuel entre sites locaux
- deux comptes de stockage ;

### Phases de déploiement

Pour déployer cette configuration, procédez comme suit :

- Phase 1 : configurer Azure 

	Utilisez Azure PowerShell pour créer les comptes de stockage, les groupes à haute disponibilité et un réseau virtuel intersite. Pour les étapes de configuration détaillées, consultez [Phase 1](virtual-machines-workload-high-availability-LOB-application-phase1.md).

- Phase 2 : configurer les contrôleurs de domaine

	Configurez deux contrôleurs de domaine répliqués Active Directory et les paramètres DNS pour le réseau virtuel. Pour les étapes de configuration détaillées, consultez [Phase 2](virtual-machines-workload-high-availability-LOB-application-phase2.md).

- Phase 3 : configurer l’infrastructure SQL Server

	Création des machines virtuelles exécutant SQL Server et le cluster. Pour les étapes de configuration détaillées, consultez [Phase 3](virtual-machines-workload-high-availability-LOB-application-phase3.md).

- Phase 4 : configurer les serveurs Web

	Déployer les machines virtuelles du serveur Web et y ajouter votre application métier. Pour les étapes de configuration détaillées, consultez [Phase 4](virtual-machines-workload-high-availability-LOB-application-phase4.md).

- Phase 5 : configurer un groupe à haute disponibilité SQL Server AlwaysOn

	Préparez les bases de données de l’application, créez un groupe à haute disponibilité SQL Server AlwaysOn, puis ajoutez-y les bases de données de l’application. Pour les étapes de configuration détaillées, consultez [Phase 5](virtual-machines-workload-high-availability-LOB-application-phase5.md).

Une fois la configuration effectuée, vous pouvez étendre cette application métier en ajoutant des serveurs Web ou des machines virtuelles exécutant des serveurs SQL vers le cluster.

## Ressources supplémentaires

[Déployer une application métier à haute disponibilité dans Azure](virtual-machines-workload-high-availability-LOB-application-overview.md)

[Plan de l’architecture des applications métier](http://msdn.microsoft.com/dn630664)

[Configuration d’une application métier web dans un cloud hybride à des fins de test](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Instructions d’implémentation des services d’infrastructure Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Charge de travail des services d’infrastructure Azure : batterie de serveurs SharePoint Server 2013](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=Oct15_HO3-->