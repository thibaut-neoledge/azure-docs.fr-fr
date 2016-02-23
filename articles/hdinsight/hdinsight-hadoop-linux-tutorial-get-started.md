<properties
   	pageTitle="Didacticiel Linux : prise en main de Hadoop et Hive | Microsoft Azure"
   	description="Suivez ce didacticiel Linux pour apprendre à utiliser Hadoop dans HDInsight. Découvrez comment approvisionner des clusters Linux et interroger des données avec Hive."
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
   	ms.date="02/11/2016"
   	ms.author="jgao"/>

# Didacticiel Hadoop : prise en main de Hadoop sous Linux dans HDInsight

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-tutorial-get-started-windows.md)
- [Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

Apprenez à créer des clusters Hadoop sous Linux dans HDInsight et à utiliser l’affichage Hive Ambari pour exécuter des tâches Hive.

Si vous ne connaissez pas Hadoop et le Big Data, vous pouvez en savoir plus sur les termes [Apache Hadoop](http://go.microsoft.com/fwlink/?LinkId=510084), [MapReduce](http://go.microsoft.com/fwlink/?LinkId=510086), [HDFS (Hadoop Distributed File System)](http://go.microsoft.com/fwlink/?LinkId=510087) et [Hive](http://go.microsoft.com/fwlink/?LinkId=510085). Pour comprendre la manière dont HDInsight prend en charge Hadoop dans Azure, consultez la rubrique [Présentation de Hadoop dans HDInsight](hdinsight-hadoop-introduction.md).

### Configuration requise

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un abonnement Azure** : consultez la page [Obtention d’un essai gratuit Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## Créer un cluster

La plupart des tâches Hadoop sont des tâches de traitements par lots. Vous pouvez créer un cluster et exécuter certaines tâches. Dans cette section, vous allez créer un cluster Hadoop sous Linux dans HDInsight avec le [modèle ARM Azure](../resource-group-template-deploy.md). Aucune expérience du modèle ARM Azure n’est requise pour ce didacticiel. Pour obtenir d’autres méthodes de création de cluster et comprendre les paramètres, consultez [Création de clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Pour plus d’informations sur l’utilisation du modèle ARM pour créer des clusters Hadoop dans HDInsight, consultez [Création de clusters Hadoop basés sur Windows dans HDInsight à l’aide de modèles ARM](hdinsight-hadoop-create-windows-clusters-arm-templates.md)

1. Cliquez sur l’image suivante pour ouvrir un modèle ARM dans le portail Azure. Le modèle ARM est situé dans un conteneur blob public. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/fr-FR/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. À partir du panneau **Paramètres**, saisissez les informations suivantes :

    - **ClusterName** : entrez un nom pour le cluster Hadoop que vous allez créer.
    - **ClusterStorageAccountName** : chaque cluster possède une dépendance de compte de stockage d’objets blob Azure. Après avoir supprimé un cluster, les données sont conservées dans le compte de stockage.
    - **Nom d’utilisateur et mot de passe de cluster** : le nom de connexion par défaut est **admin**.
    - **Nom d’utilisateur SSH et mot de passe** : le nom d’utilisateur par défaut est **sshuser**. Vous pouvez le renommer. Tous les autres paramètres sont facultatifs. Vous pouvez les laisser tels quels.  
3. Cliquez sur **OK** pour enregistrer les paramètres.
4. Dans le panneau **Déploiement personnalisé**, cliquez sur la zone de liste déroulante **Groupe de ressources**, puis cliquez sur **Nouveau** pour créer un groupe de ressources. Le groupe de ressources est un conteneur qui regroupe le cluster, le compte de stockage dépendant et une autre ressource liée.
5. Cliquez sur **Conditions juridiques**, puis cliquez sur **Créer**.
6. Cliquez sur **Create**. Vous verrez une nouvelle vignette intitulée **Envoi de déploiement pour le déploiement du modèle**. La création d’un cluster prend environ 20 minutes. Une fois le cluster créé, vous pouvez cliquer sur le panneau du cluster dans le portail pour l’ouvrir.

Après avoir terminé ce didacticiel, vous souhaiterez peut-être supprimer le cluster. Avec HDInsight, vos données sont stockées Azure Storage, pour que vous puissiez supprimer un cluster en toute sécurité s’il n’est pas en cours d’utilisation. Vous devez également payer pour un cluster HDInsight, même lorsque vous ne l’utilisez pas. Étant donné que les frais pour le cluster sont bien plus élevés que les frais de stockage, économique, mieux vaut supprimer les clusters lorsqu’ils ne sont pas utilisés. Pour obtenir des instructions sur la suppression d’un cluster, consultez la page [Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure](hdinsight-administer-use-management-portal.md#delete-clusters).


##Exécuter des requêtes Hive

Les affichages [Ambari](hdinsight-hadoop-manage-ambari.md) mettent à votre disposition plusieurs utilitaires via une page web. L’affichage Hive figure parmi les utilitaires disponibles. Dans cette section, vous utiliserez l’affichage Hive pour exécuter des requêtes Hive sur votre cluster HDInsight. Pour d’autres méthodes d’exécution des requêtes Hive, consultez la page [Utilisation de Hive et HiveQL avec Hadoop dans HDInsight pour l’analyse d’un exemple de fichier Apache log4j](hdinsight-use-hive.md).

1. Accédez à **https://&lt;ClusterName>.azurehdinsight.net**, où <ClusterName> désigne le cluster que vous avez créé dans la section précédente pour ouvrir Ambari.
2. Entrez le nom d’utilisateur Hadoop et le mot de passe que vous avez spécifiés dans la section précédente. Le nom d’utilisateur par défaut est **admin**.
3. Ouvrez l’**affichage Hive** comme illustré dans la capture d’écran suivante.

    ![Sélection des vues ambari](./media/hdinsight-hadoop-linux-tutorial-get-started/selecthiveview.png).
4. Dans la section __Éditeur de requête__ de la page, collez les instructions HiveQL suivantes dans la feuille de calcul :

		SHOW tables;
5. Cliquez sur __Exécuter__. Une section __Résultats du processus de requête__ doit apparaître en dessous de l’éditeur de requête et afficher des informations sur la tâche. 

    Une fois la requête terminée, la section __Résultats du processus de requête__ affiche les résultats de l’opération. Vous devriez voir une table appelée **hivesampletable**. Cet exemple de table Hive est fourni avec les clusters HDInsight.
    
    > [AZURE.TIP] Notez la présence du menu déroulant __Enregistrer les résultats__ en haut à gauche de la section __Résultats du processus de requête__ ; vous pouvez utiliser ce menu pour télécharger les résultats ou pour les enregistrer dans un stockage HDInsight sous la forme d’un fichier CSV.
6. Répétez les étapes 4 et 5 pour exécuter la requête suivante :

        SELECT * FROM hivesampletable;

Une fois que vous avez terminé une tâche Hive, vous pouvez [exporter les résultats dans une base de données SQL Azure ou SQL Server](hdinsight-use-sqoop-mac-linux.md). Vous pouvez également [visualiser les résultats à l’aide d’Excel](hdinsight-connect-excel-power-query.md). Pour plus d’informations sur l’utilisation de Hive dans HDInsight, consultez [Utilisation de Hive et HiveQL avec Hadoop dans HDInsight pour l’analyse d’un exemple de fichier Apache log4j](hdinsight-use-hive.md).

## Étapes suivantes

Dans ce didacticiel, vous avez appris à créer un cluster HDInsight Linux à l’aide d’un modèle ARM et à effectuer des requêtes Hive de base.

Pour en savoir plus sur l’analyse des données avec HDInsight, consultez les articles suivants :

- Pour en savoir plus sur l’utilisation de Hive avec HDInsight, y compris comment exécuter des requêtes Hive à partir de Visual Studio, consultez la page [Utilisation de Hive avec HDInsight][hdinsight-use-hive].

- Pour en savoir plus sur Pig, un langage utilisé pour transformer les données, consultez la page [Utilisation de Pig avec HDInsight][hdinsight-use-pig].

- Pour en savoir plus sur MapReduce, un moyen d’écrire des programmes pour traiter les données sur Hadoop, consultez la page [Utilisation de MapReduce avec HDInsight][hdinsight-use-mapreduce].

- Pour en savoir plus sur l’utilisation des outils HDInsight pour Visual Studio pour analyser les données sur HDInsight, consultez la page [Prise en main des outils Hadoop de Visual Studio pour HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md).

Si vous êtes prêt à commencer à utiliser vos propres données et que vous avez besoin d’en savoir plus sur la façon dont HDInsight stocke les données ou sur l’ajout de données dans HDInsight, consultez les articles suivants :

- Pour plus d’informations sur la façon dont HDInsight utilise le stockage d’objets blob Azure, consultez la page [Utilisation du stockage d’objets blob Azure avec HDInsight](hdinsight-hadoop-use-blob-storage.md).

- Pour plus d’informations sur le téléchargement de données dans HDInsight, consultez la page [Téléchargement de données dans HDInsight][hdinsight-upload-data].

Si vous voulez en savoir plus sur la création ou la gestion d’un cluster HDInsight, consultez les rubriques suivantes :

- Pour en savoir plus sur la gestion de votre cluster HDInsight Linux, consultez la page [Gestion des clusters HDInsight à l’aide d’Ambari](hdinsight-hadoop-manage-ambari.md).

- Pour en savoir plus sur les options que vous pouvez sélectionner pendant la création d’un cluster HDInsight, consultez la page [Création de HDInsight sur Linux à l’aide des options personnalisées](hdinsight-hadoop-provision-linux-clusters.md).

- Si vous maîtrisez Linux et Hadoop, mais que vous souhaitez connaître les spécificités de Hadoop sur HDInsight, consultez la page [Utilisation de HDInsight sur Linux](hdinsight-hadoop-linux-information.md). Cette rubrique vous fournit des informations telles que :

	* les URL correspondant aux services hébergés sur le cluster, tels qu'Ambari et WebHCat
	* l'emplacement des fichiers Hadoop et des exemples sur le système de fichiers local
	* l'utilisation de stockage d'Azure Storage (WASB) au lieu de HDFS, en tant que stockage de données par défaut


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: powershell-install-configure.md
[powershell-open]: powershell-install-configure.md#Install

[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png

<!---HONumber=AcomDC_0218_2016-->