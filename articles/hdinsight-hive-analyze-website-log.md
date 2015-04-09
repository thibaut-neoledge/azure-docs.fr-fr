<properties 
	pageTitle="Utilisation de Hive dans HDInsight Hadoop pour l'analyse des journaux de site web | Azure" 
	description="Découvrez comment utiliser Hive avec HDInsight pour analyser les journaux de site web. Vous allez utiliser un fichier journal en tant qu'entrée dans une table HDInsight, puis faire appel à HiveQL pour interroger les données." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/06/2015" 
	ms.author="nitinme"/>

# Utilisation de Hive avec HDInsight pour analyser les journaux de site web

Découvrez comment utiliser HiveQL avec HDInsight pour analyser les journaux d'un site web. L'analyse des journaux de site web permet de segmenter votre public en fonction d'activités similaires, de classer les visiteurs d'un site sur la base de données démographiques, d'identifier le contenu qu'ils affichent, les sites web qu'ils ont visités avant, etc.

Dans cet exemple, vous allez utiliser un cluster HDInsight pour analyser des fichiers journaux de site web afin d'obtenir des informations sur la fréquence des accès au site web en une journée à partir de sites web externes, ainsi qu'un résumé des erreurs de site web rencontrées par les utilisateurs. Vous apprendrez à :

- Vous connecter à un stockage d'objets blob Azure contenant des fichiers journaux de site web
- Créer des tables HIVE pour interroger ces journaux
- Créer des requêtes HIVE pour analyser les données
- Utiliser Microsoft Excel pour vous connecter à HDInsight (au moyen d'une connexion ODBC) pour extraire les données analysées

![HDI.Samples.Website.Log.Analysis][img-hdi-weblogs-sample]

##Conditions préalables

- Vous devez avoir approvisionné un **cluster HDInsight**. Pour plus d'informations, consultez la rubrique [Approvisionnement de clusters HDInsight][hdinsight-provision]. 
- Microsoft Excel 2010 ou Microsoft Excel 2013 doivent être installés.
- Vous devez disposer d'un [pilote ODBC Microsoft Hive](http://www.microsoft.com/download/details.aspx?id=40886) pour importer des données à partir de Hive dans Excel.


##Exécution de l'exemple

1. À partir du portail de gestion Azure, cliquez sur le cluster sur lequel vous voulez exécuter l'exemple, puis cliquez sur **Console de requête** dans la partie inférieure. Vous pouvez également ouvrir directement la console de requête au moyen de l'URL suivante :

	 	https://<clustername>.azurehdinsight.net
	
	À l'invite, authentifiez-vous au moyen du nom d'utilisateur et du mot de passe d'administrateur que vous avez utilisés lors de l'approvisionnement du cluster.
  
2. Dans la page web qui s'ouvre, cliquez sur l'onglet **Galerie de mise en route**, puis sous la catégorie **Exemples**, cliquez sur l'exemple **Analyse du journal du site web**.
3. Suivez les instructions fournies dans la page web pour terminer l'exemple.

##Étapes suivantes
Essayez l'exemple sur la façon d'analyser les données de capteur au moyen d'Azure HDInsight. Consultez la rubrique [Analyse des données de capteur au moyen de Hive avec HDInsight][hdinsight-sensor-data-sample].


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-sensor-data-sample]: hdinsight-use-hive-sensor-data-analysis.md

[img-hdi-weblogs-sample]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png

<!--HONumber=49-->