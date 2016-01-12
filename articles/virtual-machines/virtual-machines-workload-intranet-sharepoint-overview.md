<properties
	pageTitle="Déploiement d'une batterie de serveurs SharePoint Server 2013 | Microsoft Azure"
	description="Déployez une batterie de serveurs SharePoint Server 2013 avec des groupes de disponibilité SQL Server AlwaysOn dans Azure en cinq phases."
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
	ms.date="12/17/2015"
	ms.author="josephd"/>

# Déploiement de SharePoint avec des groupe de disponibilité SQL Server AlwaysOn dans Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique

Cette rubrique contient des liens vers des instructions pas à pas pour le déploiement d’une batterie de serveurs SharePoint 2013 intranet uniquement avec des groupes de disponibilité SQL Server AlwaysOn. La batterie de serveurs comprend les ordinateurs suivants :

- Deux serveurs Web SharePoint
- Deux serveurs d'applications SharePoint
- Deux serveurs de base de données
- Un serveur de nœud majoritaire du cluster
- Deux contrôleurs de domaine

Il s'agit de la configuration avec des noms d'espace réservé pour chaque serveur.

![](./media/virtual-machines-workload-intranet-sharepoint-overview/workload-spsqlao_05.png)

Deux ordinateurs pour chaque rôle assurent une haute disponibilité. Toutes les machines virtuelles se trouvent dans une seule région. Chaque groupe de machines virtuelles pour un rôle spécifique dans son propre groupe à haute disponibilité.

## Nomenclature

Cette configuration de référence requiert l’ensemble suivant de services et de composants Azure :

- Neuf machines virtuelles.
- Quatre disques de données supplémentaires pour les contrôleurs de domaine et les serveurs SQL.
- Quatre groupes à haute disponibilité.
- Un réseau virtuel entre sites.
- Un compte de stockage.
- Un abonnement Azure.

Voici les machines virtuelles et leur taille par défaut pour cette configuration.

Élément | Description de la machine virtuelle | Image de galerie | Taille par défaut
--- | --- | --- | ---
1\. | Premier contrôleur de domaine | Windows Server 2012 R2 Datacenter | A2
2\. | Deuxième contrôleur de domaine | Windows Server 2012 R2 Datacenter | A2
3\. | Premier serveur de base de données | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | A5
4\. | Deuxième serveur de base de données | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | A5
5\. | Nœud majoritaire du cluster | Windows Server 2012 R2 Datacenter | A1
6\. | Premier serveur d’applications SharePoint | Microsoft SharePoint Server 2013, version d'évaluation – Windows Server 2012 R2 | A4
7\. | Deuxième serveur d’applications SharePoint | Microsoft SharePoint Server 2013, version d'évaluation – Windows Server 2012 R2 | A4
8\. | Premier serveur web SharePoint | Microsoft SharePoint Server 2013, version d'évaluation – Windows Server 2012 R2 | A4
9\. | Deuxième serveur web SharePoint | Microsoft SharePoint Server 2013, version d'évaluation – Windows Server 2012 R2 | A4

Pour calculer le coût estimé de cette configuration, consultez la [Calculatrice de tarification Azure](https://azure.microsoft.com/pricing/calculator/).

1. Dans **Modules**, cliquez sur **Calculer**, puis sur **Machines virtuelles**, suffisamment de fois pour créer une liste de neuf machines virtuelles.
2. Pour chaque machine virtuelle, sélectionnez :
	- votre région prévue
	- le type **Windows**
	- le niveau tarifaire **Standard**
	- la taille par défaut de la table précédente ou la **taille prévue des instances**

> [AZURE.NOTE]La calculatrice de prix Azure n’inclut pas les coûts supplémentaires de la licence SQL Server pour les deux machines virtuelles exécutant SQL Server 2014 Enterprise. Pour plus d’informations, consultez [Tarification Virtual Machines-SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

## Phases du déploiement

Vous déployez cette configuration dans les phases suivantes :

- [Phase 1 : configuration d'Azure](virtual-machines-workload-intranet-sharepoint-phase1.md). Créez un compte de stockage, des groupes à haute disponibilité et un réseau virtuel intersite.
- [Phase 2 : configurer les contrôleurs de domaine](virtual-machines-workload-intranet-sharepoint-phase2.md). Créez et configurez des réplicas de contrôleurs de domaine des services de domaine Active Directory (AD DS).
- [Phase 3 : configurer l’infrastructure SQL Server](virtual-machines-workload-intranet-sharepoint-phase3.md). Créez et configurez des machines virtuelles SQL Server, préparez-les pour une utilisation avec SharePoint, puis créez le cluster.
- [Phase 4 : configurer les serveurs SharePoint](virtual-machines-workload-intranet-sharepoint-phase4.md). Créez et configurez les quatre machines virtuelles SharePoint.
- [Phase 5 : créer le groupe de disponibilité et ajouter les bases de données SharePoint](virtual-machines-workload-intranet-sharepoint-phase5.md). Préparez les bases de données et créez un groupe de disponibilité SQL Server AlwaysOn.

Ce déploiement de SharePoint avec SQL Server AlwaysOn est conçu pour accompagner l’[Infographie SharePoint avec SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788) et intégrer les dernières recommandations.

Cette configuration est un guide normatif par phase pour une architecture prédéfinie permettant de créer une batterie de serveurs SharePoint intranet fonctionnelle hautement disponible dans des services d'infrastructure Azure. Pour obtenir des conseils architecturaux supplémentaires sur l’implémentation de SharePoint 2013 dans Azure, consultez [Architectures Microsoft Azure pour SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx).

N'oubliez pas les éléments suivants :

- Si vous êtes un responsable d'implémentation expérimenté de SharePoint, n'hésitez pas à adapter les instructions dans les étapes 3 à 5 et créez la batterie de serveurs qui correspond le mieux à vos besoins.
- Si un déploiement de cloud hybride Azure existe déjà, n’hésitez pas à adapter ou ignorer les instructions des étapes 1 et 2 pour héberger la nouvelle batterie de serveurs SharePoint sur le sous-réseau approprié.
- Tous les serveurs se situent sur un seul sous-réseau dans le réseau virtuel Azure. Si vous souhaitez proposer une sécurité supplémentaire équivalente à l’isolation de sous-réseaux, vous pouvez utiliser des [groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md).

Pour créer un environnement de développement/test ou une preuve de concept de cette configuration, consultez [Configuration d’une batterie de serveurs SharePoint intranet dans un cloud hybride à des fins de test](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md).

Pour plus d’informations sur SharePoint avec les groupes de disponibilité SQL Server AlwaysOn, consultez [Configurer des groupes de disponibilité AlwaysOn SQL Server 2012 pour SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).

> [AZURE.NOTE]Microsoft a publié SharePoint Server 2016 IT Preview. Pour faciliter l’installation et le test de cette version d’évaluation, vous pouvez utiliser une image de la galerie de machines virtuelles Azure avec SharePoint Server 2016 IT Preview et ses composants requis préinstallés. Pour plus d’informations, consultez [Tester la version préliminaire SharePoint Server 2016 IT dans Azure](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/).

## Étape suivante

- Commencez la configuration de cette charge de travail avec la [Phase 1](virtual-machines-workload-intranet-sharepoint-phase1.md).

<!---HONumber=AcomDC_1223_2015-->