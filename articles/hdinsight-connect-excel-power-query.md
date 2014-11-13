<properties urlDisplayName="HDInsight and Excel" pageTitle="Connexion d'Excel &agrave; Hadoop &agrave; l'aide de Power Query | Azure" metaKeywords="hdinsight, excel, data explorer, hive excel, hdinsight excel, power query" description="D&eacute;couvrez comment tirer profit des outils d&eacute;cisionnels et utiliser Excel pour acc&eacute;der aux donn&eacute;es stock&eacute;es dans Azure HDInsight au moyen de Power Query." metaCanonical="" services="hdinsight" documentationCenter="" title="Connexion d'Excel &agrave; Hadoop &agrave; l'aide de Power Query" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# Connexion d'Excel à Hadoop à l'aide de Power Query

Une fonctionnalité clé de la solution de données volumineuses de Microsoft est l'intégration d'outils décisionnels (BI) Microsoft à des clusters Hadoop dans HDInsight. Un exemple important de cette intégration est la possibilité de connecter Excel au compte Azure Storage contenant les données associées à votre cluster Hadoop en utilisant Microsoft Power Query pour Excel. Cet article vous explique comment configurer et utiliser Power Query à partir d'Excel pour interroger des données associées à un cluster Hadoop géré avec HDInsight.

**Configuration requise**

Avant de commencer cet article, vous devez disposer des éléments suivants :

-   Un cluster HDInsight. Pour en configurer un, consultez la page [Prise en main d'Azure HDInsight][Prise en main d'Azure HDInsight].
-   Un ordinateur exécutant Windows 8, Windows 7, Windows Server 2012 ou Windows Server 2008 R2.
-   Office Professionnel Plus 2013, Office 365 ProPlus, l'édition autonome d'Excel 2013 ou Office Professionnel Plus 2010.

## Dans cet article

-   [Installation de Microsoft Power Query pour Excel][Installation de Microsoft Power Query pour Excel]
-   [Importation de données dans Excel][Importation de données dans Excel]
-   [Étapes suivantes][Étapes suivantes]

## <span id="InstallPowerQuery"></span></a>Installation de Microsoft Power Query pour Excel

Power Query permet d'importer des données provenant de différentes sources dans Microsoft Excel, où il peut alimenter des outils décisionnels (BI) tels que PowerPivot et Power View. En particulier, Power Query permet d'importer des données produites ou générées par une tâche Hadoop s'exécutant sur un cluster HDInsight.

Téléchargez Microsoft Power Query pour Excel depuis le [Centre de téléchargement Microsoft][Centre de téléchargement Microsoft] et installez-le.

## <span id="ImportData"></span></a>Importation de données HDInsight dans Excel

Le complément Power Query pour Excel facilite l'importation de données depuis votre cluster HDInsight dans Excel, où des outils décisionnels tels que PowerPivot et Power Map peuvent être utilisés pour inspecter, analyser et présenter les données.

**Pour importer des données à partir d'un cluster HDInsight**

1.  Ouvrez Excel.

2.  Créez un classeur vide.

3.  Cliquez dans le menu **Power Query**, cliquez sur **À partir d'autres sources**, puis sur **Depuis Microsoft Windows Azure HDInsight**.

    ![HDI.PowerQuery.SelectHdiSource][HDI.PowerQuery.SelectHdiSource]

    Remarque : si vous ne voyez pas le menu **Power Query**, cliquez sur **Fichier** \> **Options** \> **Compléments** et sélectionnez **Compléments COM** dans la zone déroulante **Gestionnaire** en bas de la page. Sélectionnez le bouton **OK** et vérifiez que la zone correspondant au complément Microsoft Office Power Query pour Excel a été activée.

4.  Entrez le **nom de compte** du compte de stockage d'objets blob Azure associé à votre cluster, puis cliquez sur **OK**.

5.  Entrez la **clé de compte** du compte de stockage d'objets blob, puis cliquez sur **Enregistrer** (cette opération ne doit être effectuée que la première fois où vous accédez à cet entrepôt).

6.  Dans le volet **Navigateur** à gauche de l'**Éditeur de requête**, double-cliquez sur le nom du conteneur de stockage d'objets Blob. Par défaut, le nom du conteneur est identique à celui du cluster.

7.  Localisez **HiveSampleData.txt** dans la colonne **Nom** (le chemin du dossier est **../hive/warehouse/hivesampletable/**), puis cliquez sur **Binaire** à gauche de HiveSampleData.txt.

    ![HDI.PowerQuery.ImportData][HDI.PowerQuery.ImportData]

8.  Le cas échéant, vous pouvez renommer le nom des colonnes. Une fois que vous êtes prêt, cliquez sur **Appliquer et fermer**.

    ![HDI.PowerQuery.ImportedTable][HDI.PowerQuery.ImportedTable]

## <span id="NextSteps"></span></a>Étapes suivantes

Dans cet article, vous avez appris à utiliser Power Query pour extraire des données de HDInsight et les importer dans Excel. De la même façon, vous pouvez extraire des données de HDInsight et les importer dans SQL Azure. Il est également possible de télécharger des données dans HDInsight. Pour en savoir plus, consultez les articles suivants :

-   [Connexion d'Excel à HDInsight à l'aide du pilote ODBC Microsoft Hive][Connexion d'Excel à HDInsight à l'aide du pilote ODBC Microsoft Hive]
-   [Téléchargement de données vers HDInsight][Téléchargement de données vers HDInsight].

  [Prise en main d'Azure HDInsight]: ../hdinsight-get-started/
  [Installation de Microsoft Power Query pour Excel]: #InstallPowerQuery
  [Importation de données dans Excel]: #ImportData
  [Étapes suivantes]: #NextSteps
  [Centre de téléchargement Microsoft]: http://go.microsoft.com/fwlink/?LinkID=286689
  [HDI.PowerQuery.SelectHdiSource]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
  [HDI.PowerQuery.ImportData]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png
  [HDI.PowerQuery.ImportedTable]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG
  [Connexion d'Excel à HDInsight à l'aide du pilote ODBC Microsoft Hive]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [Téléchargement de données vers HDInsight]: ../hdinsight-upload-data/
