<properties
	pageTitle="Gestion des clusters Hadoop à l’aide de l’interface de ligne de commande Azure | Microsoft Azure"
	description="Utiliser l’interface de ligne de commande Azure pour gérer les clusters Hadoop dans HDInsight"
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="mumian"
	tags="azure-portal"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/04/2016"
	ms.author="jgao"/>

# Gestion des clusters Hadoop dans HDInsight à l'aide du portail Azure

[AZURE.INCLUDE [sélecteur](../../includes/hdinsight-portal-management-selector.md)]

Apprenez à utiliser [l'interface de ligne de commande Azure](xplat-cli-install.md) afin de gérer des clusters Hadoop dans Azure HDInsight. L’interface de ligne de commande Azure est implémentée dans Node.js. Elle peut être utilisée sur toute plateforme prenant en charge Node.js, y compris Windows, Mac et Linux.

Cet article aborde uniquement l’utilisation de l’interface de ligne de commande Azure dans HDInsight. Pour une aide générale sur l’utilisation de l’interface Azure CLI, consultez la rubrique [Installer et configurer l’interface Azure CLI][azure-command-line-tools].

##Configuration requise

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la rubrique [Obtenir une version d'évaluation gratuite d'Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Azure CLI** - Consultez la rubrique [Installer et configurer l’interface de ligne de commande Azure](../xplat-cli-install.md) pour obtenir des informations sur l’installation et la configuration.
- **Connectez-vous à Azure** en utilisant la commande suivante :

		azure login

	Pour plus d'informations sur l'authentification à l'aide d'un compte professionnel ou scolaire, consultez la rubrique [Se connecter à un abonnement Azure à partir de l'interface de ligne de commande Azure](xplat-cli-connect.md).
	
- **Passez en mode Azure Resource Manager** en exécutant la commande suivante :

		azure config mode arm

Pour obtenir de l’aide, utilisez le commutateur **-h**. Par exemple :

	azure hdinsight cluster create -h
	
##Créer des clusters

Voir [Création de clusters Hadoop basés sur Windows dans HDInsight à l'aide de l’interface de ligne de commande Azure](hdinsight-hadoop-create-windows-clusters-cli.md).

##Énumération et affichage des détails de cluster
Utilisez les commandes suivantes pour énumérer et afficher les détails de cluster :

	azure hdinsight cluster list
	azure hdinsight cluster show <Cluster Name>

![HDI.CLIListCluster][image-cli-clusterlisting]


##Suppression des clusters
Utilisez les commandes suivantes pour supprimer un cluster :

	azure hdinsight cluster delete <Cluster Name>

##Mise à l’échelle des clusters

Pour modifier la taille du cluster Hadoop :

	azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>


## Activer / désactiver l’accès HTTP pour un cluster

	azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
	azure hdinsight cluster disable-http-access [options] <Cluster Name>

## Activer / désactiver l’accès RDP pour un cluster

  	azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
  	azure hdinsight cluster disable-rdp-access [options] <Cluster Name>


##Étapes suivantes
Dans cet article, vous avez appris comment effectuer différentes tâches d'administration sur un cluster HDInsight. Pour en savoir plus, consultez les articles suivants :

* [Administration de HDInsight à l’aide du portail Azure][hdinsight-admin-portal]
* [Administration de HDInsight à l’aide d’Azure PowerShell][hdinsight-admin-powershell]
* [Prise en main d'Azure HDInsight][hdinsight-get-started]
* [Utilisation de l’interface de ligne de commande Azure][azure-command-line-tools]


[azure-command-line-tools]: ../xplat-cli.md
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: ../hdinsight-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "Énumération et affichage des clusters"

<!---HONumber=AcomDC_0107_2016-->