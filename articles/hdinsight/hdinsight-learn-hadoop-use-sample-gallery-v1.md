<properties
   pageTitle="Découverte de Hadoop dans HDInsight à l’aide de la galerie d’exemples | Microsoft Azure"
   description="Apprenez rapidement à utiliser Hadoop en exécutant des exemples d’applications dans la galerie de prise en main de HDInsight. Utilisez les exemples de données ou vos propres données."
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.workload="big-data"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.date="08/11/2015"
   ms.author="jgao"/>

# Découverte de Hadoop à l’aide de la galerie de prise en main de HDInsight

La galerie de prise en main de HDInsight fournit un moyen facile et rapide de découvrir Hadoop en exécutant des exemples d’applications dans HDInsight. Certains exemples sont fournis avec des exemples de données. Vous pouvez fournir vos propres données pour le reste des exemples. Actuellement, les six exemples (d’autres sont en préparation) suivants sont disponibles :

- Solutions avec vos données Azure
	- Analyse des journaux du site Web Microsoft Azure
	- Analyse du stockage Microsoft Azure
- Solutions avec les exemples de données
	- Analyse des données de capteur
	- Analyse de tendances Twitter
	- Analyse du journal de site web
	- Recommandation de films Mahout

[AZURE.INCLUDE [portail-azure-hdinsight](../../includes/hdinsight-azure-portal.md)]

* [Découverte de Hadoop à l’aide de la galerie de prise en main de HDInsight](hdinsight-learn-hadoop-use-sample-gallery.md)


![Solutions de la galerie de prise en main HDInsight, Hadoop, Storm et HBase, y compris des exemples de données.][hdinsight.sample.gallery]

Le clip vidéo suivant montre comment exécuter l’exemple d’analyse de tendances Twitter :

<center><a href="https://www.youtube.com/embed/7ePbHot1SN4">https://www.youtube.com/embed/7ePbHot1SN4></a></center>


**Exécution d’un exemple depuis la galerie de prise en main**

1.	Connectez-vous au [portail Azure][azure.portal].
2.	Cliquez sur **HDInsight** dans le menu de gauche. Vous verrez une liste de clusters HDInsight existants, y compris les clusters Hadoop, Storm et HBase.
3.	Cliquez sur le cluster où vous voulez exécuter l’exemple.
4.	En bas de la page, sélectionnez **CONSOLE DE REQUÊTES**.
5.	Entrez le nom d’utilisateur Hadoop et le mot de passe pour le cluster.
6.	Cliquez sur **Galerie de prise en main** en haut de la page.
7.	Cliquez sur un des exemples. Chaque exemple fournit des instructions détaillées pour son exécution. L’image suivante montre l’exemple d’analyse de tendances Twitter :

	![Exemple d'analyse de tendance Twitter HDInsight][hdinsight.twitter.sample]

## Étapes suivantes
D’autres façons d’apprendre à utiliser HDInsight comprennent :

- [Plan de formation pour HDInsight][hdinsight.learn.map]
- [Infographie HDInsight][hdinsight.infographic]

<!--Image references-->
[hdinsight.sample.gallery]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Getting-Started-Gallery.png
[hdinsight.twitter.sample]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Twitter-Trend-Analysis-sample.png

<!--Link references-->
[hdinsight.learn.map]: hdinsight-learn-map.md
[hdinsight.infographic]: http://go.microsoft.com/fwlink/?linkid=523960
[azure.portal]: https://manage.windowsazure.com

<!---HONumber=Oct15_HO3-->