<properties
	pageTitle="Batterie de serveurs SharePoint | Microsoft Azure"
	description="Créez rapidement une batterie de serveurs SharePoint Server 2013 simple ou à haute disponibilité à l'aide de la fonctionnalité Batterie de serveurs SharePoint du portail Azure en version préliminaire."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/05/2015"
	ms.author="josephd"/>

# Batterie de serveurs SharePoint

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Cet article traite de la création de ressources avec le modèle de déploiement classique.

Avec la batterie de serveurs SharePoint, la version préliminaire du portail Azure crée automatiquement une batterie SharePoint Server 2013 préconfigurée. Cela peut vous faire gagner un temps précieux lorsque vous avez besoin d'une batterie de serveurs SharePoint de base ou à haute disponibilité pour un environnement de test et de développement ou si vous envisagez l'adoption de SharePoint Server 2013 comme solution de collaboration pour votre entreprise.

La batterie de serveurs SharePoint de base se compose de trois machines virtuelles dans cette configuration.

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_Basic.png)

Vous pouvez utiliser cette configuration de batterie comme configuration simplifiée pour le développement d'applications SharePoint ou pour votre évaluation initiale de SharePoint 2013.

La batterie de serveurs SharePoint à haute disponibilité se compose de neuf machines virtuelles dans cette configuration.

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_HighAvail.png)

Vous pouvez utiliser cette configuration de batterie pour tester des charges de clients plus élevées, la haute disponibilité du site SharePoint externe et SQL Server AlwaysOn pour une batterie SharePoint. Vous pouvez également l'utiliser pour le développement d'applications SharePoint dans un environnement à haute disponibilité.

Pour obtenir des informations de configuration détaillées concernant ces deux types de batteries de serveurs, consultez la page [Informations de configuration de la batterie de serveurs SharePoint](virtual-machines-sharepoint-farm-config-azure-preview.md).

> [AZURE.NOTE]Microsoft a publié SharePoint Server 2016 IT Preview. Pour faciliter l’installation et le test de cette version d’évaluation, vous pouvez utiliser une image de la galerie de machines virtuelles Azure avec SharePoint Server 2016 IT Preview et ses composants requis préinstallés. Pour plus d’informations, consultez le billet de blog [Test the SharePoint Server 2016 IT Preview in Azure](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/).

## Parcours de la configuration

Pour créer votre batterie SharePoint avec le modèle Batterie de serveurs SharePoint, procédez comme suit :

1. Dans le [portail Microsoft Azure en version préliminaire](https://portal.azure.com/), cliquez sur **Nouveau** > **Calcul** > **Batterie de serveurs SharePoint**. Si **Batterie de serveurs SharePoint** n’apparaît pas, cliquez sur **Nouveau** > **Calcul** > **Marketplace**, tapez **SharePoint** dans **Rechercher calcul**, puis cliquez sur **Batterie de serveurs SharePoint**. Dans le volet **Batterie de serveurs SharePoint**, cliquez sur **Créer**.
2. Dans le volet **Créer une batterie de serveurs SharePoint**, tapez le nom d'un groupe de ressources.
3. Tapez un nom d'utilisateur et un mot de passe pour le compte administrateur local sur chaque machine virtuelle de votre batterie. Choisissez un nom et un mot de passe difficiles à deviner, enregistrez-les et stockez-les dans un emplacement sécurisé.
4. Si vous souhaitez une batterie à haute disponibilité, cliquez sur **Activer la haute disponibilité**.
5. Pour configurer vos contrôleurs de domaine, cliquez sur la flèche. Vous pouvez indiquer un préfixe de nom d'hôte (par défaut, il s'agit du nom du groupe de ressources), le nom de domaine racine de forêt (contoso.com, par défaut) et la taille de vos contrôleurs de domaine (A1, par défaut).
6. Pour configurer vos serveurs SQL, cliquez sur la flèche. Vous pouvez indiquer un préfixe de nom d'hôte (par défaut, il s'agit du nom du groupe de ressources), la taille de vos serveurs SQL (A5, par défaut), un nom et un mot de passe de compte d'accès à la base de données (par défaut, il s'agit du compte administrateur), ainsi qu'un nom de compte de service de serveur SQL (sqlservice) et un mot de passe (par défaut, le même mot de passe que le compte administrateur est utilisé).
7. Pour configurer vos serveurs SharePoint, cliquez sur la flèche. Vous pouvez indiquer un préfixe de nom d’hôte (par défaut, il s’agit du nom du groupe de ressources), la taille de vos serveurs SharePoint (A2 par défaut), un compte d’utilisateur SharePoint (par défaut, il s’agit de sp\_setup) et un mot de passe, un nom de compte de batterie de serveurs SharePoint (par défaut, il s’agit de sp\_farm) et un mot de passe, ainsi qu’une phrase secrète de batterie de serveurs SharePoint. Par défaut, vous utiliserez le mot de passe administrateur du compte d'utilisateur SharePoint, le compte de la batterie de serveurs et une phrase secrète.
8. Pour configurer les paramètres de configuration facultatifs pour le réseau virtuel, le compte de stockage ou les diagnostics, cliquez sur la flèche correspondante.
9. Pour indiquer l'abonnement, cliquez sur la flèche.
10. Pour indiquer l’emplacement Azure, cliquez sur la flèche.
11. Une fois ces opérations effectuées, cliquez sur **Créer**.

> [AZURE.NOTE]Le contrôleur de domaine ne dispose pas des outils de gestion Active Directory installés par défaut. Pour installer ces derniers, exécutez la commande **Install-WindowsFeature AD-Domain-Services -IncludeManagementTools** à partir d’une invite de commandes Windows PowerShell de niveau administrateur sur la machine virtuelle du contrôleur de domaine.

## Accès aux batteries de serveurs SharePoint et gestion

Les batteries SharePoint disposent d’un point de terminaison préconfiguré afin d’autoriser le trafic web non authentifié (port TCP 80) vers le serveur web SharePoint pour un ordinateur client connecté à Internet. Ce point de terminaison pointe vers un site d'équipe préconfiguré. Pour accéder à ce site d’équipe :

1.	Dans la version préliminaire du portail Azure, cliquez sur **Parcourir**, puis sur **Groupes de ressources**.
2.	Dans la liste des groupes de ressources, cliquez sur le nom du groupe de ressources de votre batterie de serveurs SharePoint.
3.	Dans le volet du groupe de ressources de votre batterie de serveurs SharePoint, cliquez sur **Historique du déploiement**.
4.	Dans le volet **Historique du déploiement**, cliquez sur **Microsoft.SharePointFarm**.
5.	Dans le volet **Microsoft.SharePointFarm**, sélectionnez l’URL dans le champ **SHAREPOINTSITEURL** et copiez-la.
6.	Collez cette URL dans le champ d'adresse de votre navigateur Internet.
7.	Lorsque vous y êtes invité, entrez les informations d'identification du compte d'utilisateur que vous avez indiquées lors de la création de la batterie de serveurs.

Sur le site d'administration centrale de SharePoint, vous pouvez configurer Mes sites, des applications SharePoint, ainsi que d'autres fonctionnalités. Pour plus d'informations, consultez la page [Configurer SharePoint 2013](http://technet.microsoft.com/library/ee836142.aspx). Pour accéder au site d’administration centrale de SharePoint :

1.	Dans la version préliminaire du portail Azure, cliquez sur **Parcourir**, puis sur **Groupes de ressources**.
2.	Dans la liste des groupes de ressources, cliquez sur le nom du groupe de ressources de votre batterie de serveurs SharePoint.
3.	Dans le volet du groupe de ressources de votre batterie de serveurs SharePoint, cliquez sur **Historique du déploiement**.
4.	Dans le volet **Historique du déploiement**, cliquez sur **Microsoft.SharePointFarm**.
5.	Dans le volet **Microsoft.SharePointFarm**, sélectionnez l’URL dans le champ **SHAREPOINTCENTRALADMINURL** et copiez-la.
6.	Collez cette URL dans le champ d'adresse de votre navigateur Internet.
7.	Lorsque vous y êtes invité, entrez les informations d'identification du compte d'utilisateur que vous avez indiquées lors de la création de la batterie de serveurs.


Remarques :

- La version préliminaire du portail Azure crée ces deux batteries de serveurs dans un réseau virtuel sur le cloud uniquement avec une présence web accessible sur Internet. Il n’existe pas de connexion VPN de site à site ou ExpressRoute en amont vers le réseau de votre organisation.
- Vous pouvez administrer ces serveurs par le biais de connexions Bureau à distance. Pour plus d’informations, consultez [Connexion à la machine virtuelle](virtual-machines-windows-tutorial.md#log-on-to-the-virtual-machine).

## Azure Resource Manager

La fonctionnalité Batterie de serveurs SharePoint du portail Azure en version préliminaire crée des machines virtuelles avec le modèle de déploiement classique. Pour créer des batteries de serveurs SharePoint Server 2013 avec le modèle de déploiement Resource Manager, consultez [Déployer des batteries de serveurs SharePoint avec des modèles Azure Resource Manager](virtual-machines-workload-template-sharepoint.md).

## Ressources supplémentaires

[Informations de configuration de la batterie de serveurs SharePoint](virtual-machines-sharepoint-farm-config-azure-preview.md)

[Batteries de serveurs SharePoint hébergés dans des services d’infrastructure Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Configuration d’une batterie de serveurs SharePoint intranet dans un cloud hybride à des fins de test](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

<!---HONumber=Oct15_HO2-->