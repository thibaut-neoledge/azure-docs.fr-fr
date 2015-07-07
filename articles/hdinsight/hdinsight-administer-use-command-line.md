<properties
	pageTitle="Gestion des clusters Hadoop à l’aide de l’interface de ligne de commande Azure | Microsoft Azure"
	description="Utiliser l’interface de ligne de commande Azure pour gérer les clusters Hadoop dans HDInsight"
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="mumian"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/08/2015"
	ms.author="jgao"/>

# Gestion des clusters Hadoop dans HDInsight à l’aide de l’interface de ligne de commande Azure (CLI Azure)

Utiliser l’interface de ligne de commande Azure pour gérer des clusters Hadoop dans Azure HDInsight. L’interface de ligne de commande Azure est implémentée dans Node.js. Elle peut être utilisée sur toute plateforme prenant en charge Node.js, y compris Windows, Mac et Linux.

L’interface de ligne de commande Azure est open source. Le code source est géré dans GitHub sur <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>.

Cet article aborde uniquement l’utilisation de l’interface de ligne de commande Azure dans HDInsight. Pour une aide générale sur l’utilisation de l’interface de ligne de commande Azure, consultez la rubrique [Utiliser l’interface de ligne de commande Azure][azure-command-line-tools].


##Composants requis

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la rubrique [Obtenir une version d'évaluation gratuite d'Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Azure CLI** - Consultez la rubrique [Installer et configurer l’interface de ligne de commande Azure](../xplat-cli.md) pour obtenir des informations sur l’installation et la configuration.

##Installation

Si ce n’est pas déjà fait, utilisez le document [Installer et configurer l’interface de ligne de commande Azure](../xplat-cli.md) pour installer et configurer l’interface de ligne de commande Azure.

##Approvisionnement d'un cluster HDInsight

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]


HDInsight utilise le conteneur de stockage d'objets blob Azure comme système de fichiers par défaut. Un compte Azure Storage est nécessaire avant de pouvoir créer un cluster HDInsight.

Après avoir importé le fichier de paramètres de publication, vous pouvez utiliser la commande suivante pour créer un compte de stockage :

	azure account storage create [options] <StorageAccountName>


> [AZURE.NOTE]Il doit se trouver dans le même centre de données que le cluster HDInsight.


Pour plus d’informations sur la création d’un compte Azure Storage au moyen du portail Azure, consultez la rubrique [Création, gestion ou suppression d’un compte de stockage][azure-create-storageaccount].

Si vous disposez déjà d’un compte de stockage, mais que vous ne connaissez ni le nom ni la clé du compte, vous pouvez utiliser les commandes suivantes pour récupérer les informations :

	-- Lists Storage accounts
	azure account storage list
	-- Shows a Storage account
	azure account storage show <StorageAccountName>
	-- Lists the keys for a Storage account
	azure account storage keys list <StorageAccountName>

Pour plus d’informations sur l’obtention d’informations au moyen du portail Azure, consultez la section « Affichage, copie et régénération des clés d’accès de stockage » de la rubrique [Création, gestion ou suppression d’un compte de stockage][azure-create-storageaccount].


La commande **azure hdinsight cluster create** crée le conteneur s'il n'existe pas. Si vous avez choisi de créer le conteneur au préalable, vous pouvez utiliser la commande suivante :

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Une fois que le compte de stockage et le conteneur d’objets blob sont prêts, vous êtes prêt à créer un cluster :

	azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

![HDI.CLIClusterCreation][image-cli-clustercreation]

















##Approvisionnement d’un cluster HDInsight au moyen d’un fichier de configuration
Généralement, vous approvisionnez un cluster HDInsight, y exécutez des tâches, puis le supprimez, afin de réduire les coûts. L'interface de ligne de commande vous donne la possibilité d'enregistrer les configurations dans un fichier, de sorte que vous pouvez les réutiliser chaque fois que vous mettez en service un cluster.

	azure hdinsight cluster config create <file>

	azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"

	azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
	       --storageAccountKey "<StorageAccountKey>"

	azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
	       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

	azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
	       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

	azure hdinsight cluster create --config <file>



![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


##Énumération et affichage des détails de cluster
Utilisez les commandes suivantes pour énumérer et afficher les détails de cluster :

	azure hdinsight cluster list
	azure hdinsight cluster show <ClusterName>

![HDI.CLIListCluster][image-cli-clusterlisting]


##Suppression d'un cluster
Utilisez les commandes suivantes pour supprimer un cluster :

	azure hdinsight cluster delete <ClusterName>




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
 

<!---HONumber=62-->