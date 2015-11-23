<properties 
	pageTitle="Déploiement d'une application métier | Microsoft Azure" 
	description="Déployez une application métier à haute disponibilité basée sur le web avec des groupes de haute disponibilité SQL Server AlwaysOn dans Azure en cinq phases." 
	documentationCenter=""
	services="virtual-machines" 
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
	ms.date="11/09/2015" 
	ms.author="josephd"/>

# Déployer une application métier à haute disponibilité dans Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique

Cette rubrique contient des liens vers des instructions détaillées pour le déploiement d'une application métier basée sur le Web spécialement conçue pour l’Intranet, avec des groupes de haute disponibilité SQL Server AlwaysOn dans des services d'infrastructure Azure. L'application est hébergée sur ces ordinateurs :

- Deux serveurs Web
- Deux serveurs de base de données
- Un serveur de nœud majoritaire du cluster
- Deux contrôleurs de domaine

Il s'agit de la configuration avec des noms d'espace réservé pour chaque serveur.

![](./media/virtual-machines-workload-high-availability-LOB-application-overview/workload-lobapp-phase4.png)
 
Au moins deux ordinateurs pour chaque rôle assurent une haute disponibilité. Toutes les machines virtuelles sont dans un seul emplacement Azure (également appelé région). Chaque groupe de machines virtuelles pour un rôle spécifique dans leur propre groupe à haute disponibilité.

Vous déployez cette configuration dans les phases suivantes :

- [Phase 1 : configuration d'Azure](virtual-machines-workload-high-availability-LOB-application-phase1.md). Créez des comptes de stockage, des groupes à haute disponibilité et un réseau virtuel intersite.
- [Phase 2 : configurer les contrôleurs de domaine](virtual-machines-workload-high-availability-LOB-application-phase2.md). Créez et configurez des réplicas de contrôleurs de domaine des services de domaine Active Directory (AD DS).
- [Phase 3 : configurer l’infrastructure SQL Server](virtual-machines-workload-high-availability-LOB-application-phase3.md). Créez et configurez les ordinateurs virtuels exécutant SQL Server, créez le cluster et activez des groupes de haute disponibilité SQL Server AlwaysOn.
- [Phase 4 : configurer les serveurs web](virtual-machines-workload-high-availability-LOB-application-phase4.md). Créez et configurez les deux machines virtuelles du serveur Web.
- [Phase 5 : ajouter les bases de données de l’application à un groupe de disponibilité SQL Server AlwaysOn](virtual-machines-workload-high-availability-LOB-application-phase5.md). Préparez les bases de données de l’application métier et ajoutez-les à un groupe de haute disponibilité SQL Server AlwaysOn.

Ce déploiement est conçu pour accompagner le [Plan de l’architecture des applications métier](http://msdn.microsoft.com/dn630664) et intégrer les dernières recommandations.

Il s'agit d'une architecture normative prédéfinie. N'oubliez pas les éléments suivants :

- Si vous êtes un responsable expérimenté de l’implémentation d’applications métier basées sur le Web, n'hésitez pas à adapter les instructions données dans les étapes 3 à 5 et créez l’infrastructure d’application qui correspond le mieux à vos besoins. 
- Si vous avez déjà une implémentation de cloud hybride Azure, n'hésitez pas à adapter ou ignorer les instructions des étapes 1 et 2 pour héberger les machines virtuelles de la nouvelle application sur le sous-réseau approprié.
- Tous les serveurs se situent sur un seul sous-réseau dans le réseau virtuel Azure. Si vous souhaitez une sécurité supplémentaire équivalente à l'isolation de sous-réseaux, vous pouvez utiliser des [Groupes de sécurité réseau](../virtual-networks/virtual-networks-nsg.md).

Pour créer un environnement de développement/test ou une preuve de concept de cette configuration, consultez [Configurer une application métier basée sur le Web dans un cloud hybride à des fins de test](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md).

Pour plus d’informations sur la conception des charges de travail informatiques pour Azure, consultez [Instructions d’implémentation des services d’infrastructure Azure](virtual-machines-infrastructure-services-implementation-guidelines.md).

## Étape suivante

Pour démarrer la configuration de cette charge de travail, passez à la [Phase 1 : configuration d'Azure](virtual-machines-workload-high-availability-LOB-application-phase1.md).

## Ressources supplémentaires

[Plan de l’architecture des applications métier](http://msdn.microsoft.com/dn630664)

[Configuration d’une application métier web dans un cloud hybride à des fins de test](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Instructions d’implémentation des services d’infrastructure Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Charge de travail des services d’infrastructure Azure : batterie de serveurs SharePoint Server 2013](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=Nov15_HO3-->