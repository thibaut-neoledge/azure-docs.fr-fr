<properties 
	pageTitle="Connexion d’Excel à Hadoop à l’aide de Power Query | Microsoft Azure" 
	description="Découvrez comment tirer profit des outils décisionnels et utiliser Power Query pour Excel afin d’accéder aux données stockées dans Hadoop sur HDInsight." 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="bradsev"/>


#Connexion d’Excel à Hadoop à l’aide de Power Query

Une fonctionnalité clé de la solution de données volumineuses (« big data ») de Microsoft est l’intégration d’outils décisionnels (BI) Microsoft à des clusters Hadoop dans Azure HDInsight. Un exemple important de cette intégration est la possibilité de connecter Excel au compte Azure Storage contenant les données associées à votre cluster Hadoop en utilisant le complément Microsoft Power Query pour Excel. Cet article vous explique comment configurer et utiliser Power Query pour interroger des données associées à un cluster Hadoop géré avec HDInsight.

## Configuration requise

Avant de commencer cet article, vous devez disposer des éléments suivants :

- Un cluster HDInsight. Pour en configurer un, consultez la page [Prise en main d'Azure HDInsight][hdinsight-get-started].
- Un ordinateur fonctionnant sous Windows 7, Windows Server 2008 R2 ou une version ultérieure.
- Office Professionnel Plus 2013, Office 365 ProPlus, l’édition autonome d’Excel 2013 ou Office Professionnel Plus 2010.


## <a id="InstallPowerQuery"></a>Installation de Microsoft Power Query pour Excel

Power Query permet d’importer des données provenant de différentes sources dans Microsoft Excel, où il peut alimenter des outils décisionnels (BI) tels que PowerPivot et Power View. En particulier, Power Query permet d'importer des données produites ou générées par une tâche Hadoop s'exécutant sur un cluster HDInsight.

Téléchargez Microsoft Power Query pour Excel depuis le [Centre de téléchargement Microsoft][powerquery-download] et installez-le.

## <a id="ImportData"></a>Importation de données HDInsight dans Excel

Le complément Power Query pour Excel facilite l’importation de données depuis votre cluster HDInsight dans Excel, où des outils décisionnels tels que PowerPivot et Power Map peuvent être utilisés pour inspecter, analyser et présenter les données.

**Pour importer des données à partir d’un cluster HDInsight**

1. Ouvrez Excel.

2. Créez un classeur vide.

3. Cliquez dans le menu **Power Query**, cliquez sur **À partir d'autres sources**, puis sur **Depuis Microsoft Windows Azure HDInsight**.

	![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]

	Remarque : si vous ne voyez pas le menu **Power Query**, cliquez sur **Fichier** > **Options** > **Compléments** et sélectionnez **Compléments COM** dans la zone déroulante **Gestionnaire** en bas de la page. Sélectionnez le bouton **OK** et vérifiez que la case correspondant au complément Power Query pour Excel a été cochée.

3. Pour le champ **Nom du compte**, saisissez le nom du compte de stockage d’objets blob Azure associé à votre cluster, puis cliquez sur **OK**.

4. Pour le champ **Clé du compte**, saisissez la clé du compte de stockage d’objets blob, puis cliquez sur **Enregistrer** (cette opération ne doit être effectuée que la première fois où vous accédez à cet entrepôt).

5. Dans le volet **Navigateur** situé à gauche de l’Éditeur de requête, double-cliquez sur le nom du conteneur de stockage d’objets Blob. Par défaut, le nom du conteneur est identique à celui du cluster.

6. Localisez **HiveSampleData.txt** dans la colonne **Nom** (le chemin du dossier est **../hive/warehouse/hivesampletable/**), puis cliquez sur **Binaire** à gauche de HiveSampleData.txt.

	![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]

7. Le cas échéant, vous pouvez renommer le nom des colonnes. Une fois que vous êtes prêt, cliquez sur **Appliquer et fermer**.

	![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a id="NextSteps"></a>Étapes suivantes

Dans cet article, vous avez appris à utiliser Power Query pour extraire des données de HDInsight et les importer dans Excel. De la même façon, vous pouvez extraire des données de HDInsight et les importer dans une base de données SQL Azure. Il est également possible de télécharger des données dans HDInsight. Pour en savoir plus, consultez les articles suivants :

* [Connexion d’Excel à HDInsight à l’aide du pilote ODBC Microsoft Hive][hdinsight-ODBC]
* [Téléchargement de données vers HDInsight][hdinsight-upload-data]

[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689

<!--HONumber=54--> 