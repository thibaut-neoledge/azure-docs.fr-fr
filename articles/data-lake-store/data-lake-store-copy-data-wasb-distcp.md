<properties
   pageTitle="Copier des données vers et depuis WASB dans Data Lake Store à l’aide de Distcp| Microsoft Azure"
   description="Utilisez l’outil Distcp pour copier des données entre des objets blob Azure Storage et Data Lake Store"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/02/2016"
   ms.author="nitinme"/>

# Utilisation de l’utilitaire Distcp pour copier des données entre des objets blob Azure Storage et le Data Lake Store

> [AZURE.SELECTOR]
- [Utilisation de DistCp](data-lake-store-copy-data-wasb-distcp.md)
- [Utilisation d’AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)


Une fois que vous avez créé un cluster HDInsight ayant accès à un compte Data Lake Store, vous pouvez utiliser des outils de l’écosystème Hadoop, comme Distcp, pour copier des données **vers et depuis** un stockage de cluster HDInsight (WASB) dans un compte Data Lake Store. Cet article fournit des instructions sur la procédure à suivre.

##Configuration requise

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Activez votre abonnement Azure** pour la version d'évaluation publique de Data Lake Store. Consultez les [instructions](data-lake-store-get-started-portal.md#signup).
- **Cluster Azure HDInsight** ayant accès à un compte Data Lake Store. Voir [Créer un cluster HDInsight avec Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Veillez à activer le Bureau à distance pour le cluster.

## Les vidéos vous permettent-elles d’apprendre plus rapidement ?

[Regardez cette vidéo](https://mix.office.com/watch/1liuojvdx6sie) pour savoir comment copier des données entre des objets blob Azure Storage et Data Lake Store à l’aide de DistCp.

## Utilisez Distcp à partir d’un bureau à distance (cluster Windows) ou d’un serveur SSH (cluster Linux)

Un cluster HDInsight est fourni avec l’utilitaire Distcp, que vous pouvez utiliser pour copier dans un cluster des données provenant de différentes sources. Si vous avez configuré le cluster HDInsight pour utiliser Data Lake Store comme espace de stockage supplémentaire, l’utilitaire Distcp peut également être utilisé tel quel pour copier des données vers et depuis un compte Data Lake Store. Dans cette section, nous allons voir comment utiliser l’utilitaire Distcp.

1. Si vous possédez un cluster Windows, connectez-vous à distance à un cluster ayant accès à un compte Data Lake Store. Pour la marche à suivre, consultez [Connexion à des clusters à l’aide du bureau à distance](../hdinsight/hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp). À partir du Bureau du cluster, ouvrez la ligne de commande Hadoop.

	Si vous disposez d’un cluster Linux, utilisez SSH pour vous connecter au cluster. Consultez [Connexion à un cluster HDInsight sous Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster). Exécutez les commandes de l’invite SSH.

3. Vérifiez si vous pouvez accéder aux objets blob d’Azure Storage (WASB). Exécutez la commande suivante :

		hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

	Vous devriez obtenir la liste du contenu de l’objet blob de stockage.

4. Là encore, vérifiez si vous pouvez accéder au compte Data Lake Store à partir du cluster. Exécutez la commande suivante :

		hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

	Vous devriez accéder à une liste des fichiers/dossiers contenus dans le compte Data Lake Store.

5. Utilisez Distcp pour copier des données de WASB vers un compte Data Lake Store.

		hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

	Cette commande copie le contenu du dossier **/example/data/gutenberg/** de WASB vers le répertoire **/myfolder** du compte Data Lake Store.

6. De la même manière, utilisez Distcp pour copier des données d’un compte Data Lake Store vers WASB.

		hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

	Cette commande copie le contenu du répertoire **/myfolder** du compte Data Lake Store vers le dossier **/example/data/gutenberg/** de WASB.

## Voir aussi

- [Copier des données d’objets blob Azure Storage vers Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
- [Sécuriser les données dans Data Lake Store](data-lake-store-secure-data.md)
- [Utiliser Azure Data Lake Analytics avec Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utiliser Azure HDInsight avec Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0803_2016-->