<properties
   	pageTitle="Publier des applications HDInsight | Microsoft Azure"
   	description="Découvrez comment créer et publier des applications HDInsight."
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="06/29/2016"
   	ms.author="jgao"/>

# Publier des applications HDInsight dans Azure Marketplace

Une application HDInsight est une application que les utilisateurs peuvent installer sur un cluster HDInsight sous Linux. Ces applications peuvent être développées par Microsoft, par des éditeurs de logiciels indépendants (ISV) ou par vous-même. Dans cet article, vous découvrirez comment publier une application HDInsight dans Azure Marketplace. Pour obtenir des informations générales sur la publication dans Azure Marketplace, consultez la page [Publier une offre dans Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).

Les applications HDInsight utilisent le modèle *BYOL (apportez votre propre licence)*, où le fournisseur de l’application doit fournir aux utilisateurs finaux la licence de l’application. Azure facture uniquement les ressources que ces utilisateurs créent, telles que le cluster HDInsight et ses machines virtuelles/nœuds. À ce stade, la facturation de l’application elle-même n’est pas effectuée via Azure.

Autre article concernant les applications HDInsight :

- [Install HDInsight applications](hdinsight-apps-install-applications.md) (Installer des applications HDInsight) : découvrez comment installer une application HDInsight sur vos clusters.
- [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md) (Installer des applications HDInsight personnalisées) : découvrez comment installer et tester des applications HDInsight personnalisées.

 
## Configuration requise

Pour envoyer votre application personnalisée au Marketplace, vous devez l’avoir créée et testée. Consultez les articles suivants :

- [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md) (Installer des applications HDInsight personnalisées) : découvrez comment installer et tester des applications HDInsight personnalisées.

Vous devez également avoir enregistré votre compte développeur. Consultez les pages [Publier une offre dans Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) et [Créer un compte de développeur Microsoft](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).

## Définir l’application

La publication d’applications dans Azure Marketplace se fait en deux étapes. D’abord, vous définissez un fichier **createUiDef.json** pour indiquer les clusters compatibles avec votre application ; ensuite, vous publiez le modèle à partir du portail Azure. Voici un exemple de fichier createUiDef.json.

	{
		"handler": "Microsoft.HDInsight",
		"version": "0.0.1-preview",
		"clusterFilters": {
			"types": ["Hadoop", "HBase", "Storm", "Spark"],
			"tiers": ["Standard", "Premium"],
			"versions": ["3.4"]
		}
	}


|Champ | Description | Valeurs possibles|
|-------|---------------|----------------|
|types |Les types de cluster compatibles avec l’application. |Hadoop, HBase, Storm, Spark (ou toute combinaison de ceux-ci)|
|tiers |Les niveaux de cluster compatibles avec l’application. |Standard, Premium, (ou les deux)|
|versions|	Les types de cluster HDInsight compatibles avec l’application. |3\.4|

## Empaqueter une application

Créez un fichier zip qui contient tous les fichiers requis pour l’installation de vos applications HDInsight. Vous aurez besoin du fichier zip à l’étape [Publication de l’application](#publish-application).

- [createUiDefinition.json](#define-application).
- mainTemplate.json. Voir un exemple dans l’article [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md) (Installer des applications HDInsight personnalisées).

	>[AZURE.IMPORTANT] L’appellation des noms des scripts d’installation d’application doit être unique pour un cluster particulier, au format suivant. En outre, toutes les actions de script d’installation et de désinstallation doivent être idempotentes, ce qui signifie que les scripts peuvent être appelés de manière répétitive tout en produisant le même résultat.
	
	>	name": "[concat('hue-install-v0','-' ,uniquestring(‘applicationName’)]"
		
	>Notez que le nom du script se compose de trois parties :
		
	>	1. Un préfixe, devant inclure soit le nom de l’application soit un nom en lien avec cette dernière.
	>	2. Un tiret, pour une meilleure lisibilité.
	>	3. Une fonction de chaîne unique utilisant le nom de l’application comme paramètre.

	>	Dans l’exemple ci-dessus, la liste d’actions de script persistantes contient le nom suivant : hue-install-v0-4wkahss55hlas. Pour obtenir un exemple de charge JSON, voir [https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).

- Tous les scripts nécessaires.

> [AZURE.NOTE] Les fichiers d’application (y compris les fichiers d’application web, le cas échéant) peuvent se trouver sur n’importe quel point de terminaison accessible publiquement.

## Publication de l’application

Suivez ces étapes pour publier une application HDInsight :

1. Connectez-vous au [portail de publication Azure](https://publish.windowsazure.com/).
2. Cliquez sur **Modèles de solution** pour créer un modèle de solution.
3. Cliquez sur **Create Dev Center account and join the Azure program** (Créer un compte du Centre de développement et participer au programme Azure) pour enregistrer votre société, si ce n’est pas encore fait. Voir [Créer un compte de développeur Microsoft](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).
4. Cliquez sur **Définir des Topologies pour démarrer**. Un modèle de solution est « parent » de toutes ses topologies. Vous pouvez définir plusieurs topologies dans une offre/un modèle de solution. Quand une offre est envoyée dans l’environnement intermédiaire, toutes ses topologies l’accompagnent.
5. Ajouter une nouvelle version.
6. Charger le fichier zip préparé à l’étape [Empaqueter une application](#package-application).
7. Cliquez sur **Request Certification** (Demander la certification). L’équipe de certification Microsoft examine les fichiers et certifie la topologie.

## Étapes suivantes

- [Install HDInsight applications](hdinsight-apps-install-applications.md) (Installer des applications HDInsight) : découvrez comment installer une application HDInsight sur vos clusters.
- [Installer des applications HDInsight personnalisées](hdinsight-apps-install-custom-applications.md) : découvrez comment déployer des applications HDInsight inédites vers HDInsight.
- [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md) : apprenez à utiliser l’action de script pour installer des applications supplémentaires.
- [Créer des clusters Hadoop sous Linux dans HDInsight à l’aide de modèles Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md) : apprenez à appeler des modèles Resource Manager pour la création de clusters HDInsight.

<!---HONumber=AcomDC_0706_2016-->