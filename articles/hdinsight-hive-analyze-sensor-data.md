<properties title="Analyse des donn&eacute;es de capteur au moyen de Hive avec HDInsight" pageTitle="Analyse des donn&eacute;es de capteur au moyen de Hive et de Microsoft Azure HDInsight (Hadoop)" description="D&eacute;couvrez comment utiliser Hive et Excel pour analyser et visualiser des donn&eacute;es de capteur avec HDInsight (Hadoop)" metaKeywords="Azure hdinsight hive, Azure hdinsight hive sensor, azure hadoop hive, azure hadoop sensor, azure hadoop excel, azure hdinsight excel" services="hdinsight" solutions="" documentationCenter="big-data" authors="larryfr" videoId="" scriptId="" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/30/2014" ms.author="larryfr" />

# Analyse des données de capteur au moyen de Hive avec HDInsight

Découvrez comment analyser des données de capteur au moyen de Hive avec HDInsight (Hadoop), puis visualiser les données dans Microsoft Excel à l'aide de Power View.

Dans cet exemple, vous allez utiliser Hive pour traiter les données d'historique produites par les systèmes de chauffage, de ventilation et de climatisation, et ainsi identifier les systèmes qui ne sont pas en mesure de maintenir de façon fiable une température donnée. Vous apprendrez à :

-   Créer des tables HIVE pour interroger des données stockées dans des fichiers au format CSV
-   Créer des requêtes HIVE pour analyser les données
-   Utiliser Microsoft Excel pour vous connecter à HDInsight (au moyen d'une connexion ODBC) pour extraire les données analysées
-   Utiliser Power View pour visualiser les données

![A diagram of the solution architecture][A diagram of the solution architecture]

## Configuration requise :

-   Un cluster HDInsight (Hadoop) - consultez la rubrique [Approvisionnement de clusters Hadoop dans HDInsight][Approvisionnement de clusters Hadoop dans HDInsight] pour des informations sur la création d'un cluster

-   Microsoft Excel 2013

    > [WACOM.NOTE] Microsoft Excel est utilisé pour la visualisation des données avec [Power View][Power View], qui est actuellement disponible uniquement dans Windows.

-   [Pilote ODBC Microsoft Hive][Pilote ODBC Microsoft Hive]

## Exécution de l'exemple

1.  À partir du portail de gestion Azure, cliquez sur le cluster sur lequel vous voulez exécuter l'exemple, puis cliquez sur **Console de requête** dans la partie inférieure. Vous pouvez également ouvrir directement la console de requête au moyen de l'URL suivante :

        https://<clustername>.azurehdinsight.net

    À l'invite, authentifiez-vous au moyen du nom d'utilisateur et du mot de passe d'administrateur que vous avez utilisés lors de l'approvisionnement de ce cluster.

2.  Dans la page web qui s'ouvre, cliquez sur l'onglet **Galerie de mise en route**, puis sous la catégorie **Exemples**, cliquez sur l'exemple **Analyse du journal du site web**.

3.  Suivez les instructions fournies dans la page web pour terminer l'exemple.

  [A diagram of the solution architecture]: ./media/hdinsight-use-hive-sensor-data-analysis/hvac-architecture.png
  [Approvisionnement de clusters Hadoop dans HDInsight]: /fr-fr/documentation/articles/hdinsight-provision-clusters/
  [Power View]: https://support.office.com/fr-fr/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=fr-fr&rs=fr-fr&ad=US
  [Pilote ODBC Microsoft Hive]: http://www.microsoft.com/fr-fr/download/details.aspx?id=40886
