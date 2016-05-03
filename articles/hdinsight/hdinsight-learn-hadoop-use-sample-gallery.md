<properties
   pageTitle="Découverte de Hadoop dans HDInsight à l’aide de la galerie d’exemples | Microsoft Azure"
   description="Apprenez rapidement à utiliser Hadoop en exécutant des exemples d’applications dans la galerie de prise en main de HDInsight. Utilisez les exemples de données ou vos propres données."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.workload="big-data"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="02/04/2016"
   ms.author="jgao"/>

# Découverte de Hadoop à l’aide de la galerie de prise en main d’Azure HDInsight

La galerie de prise en main de HDInsight fournit un moyen facile et rapide de découvrir Hadoop en exécutant des exemples d’applications dans HDInsight. Certains exemples sont fournis avec des exemples de données. Vous pouvez fournir vos propres données pour le reste des exemples. Actuellement, les six exemples (d’autres sont en préparation) suivants sont disponibles :

- Solutions avec vos données Azure
	- Analyse des journaux du site Web Microsoft Azure
	- Analyse du stockage Microsoft Azure
- Solutions avec les exemples de données
	- Analyse des données de capteur
	- Analyse de tendances Twitter
	- Analyse du journal de site web
	- Recommandation de films Mahout

![Solutions de la galerie de prise en main HDInsight, Hadoop, Storm et HBase, y compris des exemples de données.][hdinsight.sample.gallery]

La vidéo suivante montre comment exécuter l’exemple d’analyse de tendances Twitter :

<center><a href="https://www.youtube.com/embed/7ePbHot1SN4">https://www.youtube.com/embed/7ePbHot1SN4></a></center>

Le tableau de bord est accessible via l’adresse http://<YourHDInsightClusterName>.azurehdinsight.net/ ou depuis le portail Azure.

**Exécution d’un exemple depuis la galerie de prise en main**

1. Connectez-vous au [portail Azure][azure.portal].
2. Cliquez sur **Parcourir** dans le menu de gauche, sur **Clusters HDInsight**, puis sur votre nom de cluster.
3. Cliquez sur **Tableau de bord** dans le menu du haut.
4. Saisissez le nom d'utilisateur et le mot de passe HTTP (également appelé utilisateur de cluster).
6. Cliquez sur **Galerie de prise en main** en haut de la page.
7. Cliquez sur un des exemples. Chaque exemple fournit des instructions détaillées pour son exécution. L’image suivante montre l’exemple d’analyse de tendances Twitter :

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
[azure.portal]: https://portal.azure.com

<!---HONumber=AcomDC_0420_2016-->