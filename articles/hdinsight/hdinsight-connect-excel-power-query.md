---
title: "Connexion d’Excel à Hadoop à l’aide de Power Query | Microsoft Docs"
description: "Découvrez comment tirer profit des outils décisionnels et utiliser Power Query pour Excel afin d’accéder aux données stockées dans Hadoop sur HDInsight."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 01ad2f90-7520-44d9-8c16-4d936faaff9b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 6407c371bc51461a05429fabaf38d3f9bc80d32c
ms.openlocfilehash: 26383db7a3fe6736fb739121dd545518784c098a
ms.lasthandoff: 02/07/2017


---
# <a name="connect-excel-to-hadoop-by-using-power-query"></a>Connexion d’Excel à Hadoop à l’aide de Power Query
Une fonctionnalité clé de la solution de données volumineuses (« big data ») de Microsoft est l’intégration d’outils décisionnels (BI) Microsoft à des clusters Hadoop dans Azure HDInsight. Un exemple important de cette intégration est la possibilité de connecter Excel au compte Azure Storage contenant les données associées à votre cluster Hadoop en utilisant le complément Microsoft Power Query pour Excel. Cet article vous explique comment configurer et utiliser Power Query pour interroger des données associées à un cluster Hadoop géré avec HDInsight.

> [!NOTE]
> Même si les étapes décrites dans cet article peuvent être utilisées avec un cluster HDInsight basé sur Linux ou Windows, Windows est requis pour le poste de travail client.
> 
> 

### <a name="prerequisites"></a>Conditions préalables
Avant de commencer cet article, vous devez disposer des éléments suivants :

* **Un cluster HDInsight**. Pour en configurer un, consultez la page [Prise en main d’Azure HDInsight][hdinsight-get-started].
* **Une station de travail** fonctionnant sous Windows 7, Windows Server 2008 R2 ou une version ultérieure.
* **Office Professionnel Plus 2013, Office 365 ProPlus, l’édition autonome d’Excel 2013 ou Office Professionnel Plus 2010.**

## <a name="install-power-query"></a>Installation de Power Query
Power Query permet d’importer des données provenant de différentes sources dans Microsoft Excel, où il peut alimenter des outils décisionnels (BI) tels que PowerPivot et Power View. En particulier, Power Query permet d'importer des données produites ou générées par une tâche Hadoop s'exécutant sur un cluster HDInsight.

Téléchargez Microsoft Power Query pour Excel depuis le [Centre de téléchargement Microsoft][powerquery-download] et installez-le.

## <a name="import-hdinsight-data-into-excel"></a>Importation de données HDInsight dans Excel
Le complément Power Query pour Excel facilite l’importation de données depuis votre cluster HDInsight dans Excel, où des outils décisionnels tels que PowerPivot et Power Map peuvent être utilisés pour inspecter, analyser et présenter les données.

**Pour importer des données à partir d’un cluster HDInsight**

1. Ouvrez Excel.
2. Créez un classeur vide.
3. Cliquez dans le menu **Power Query**, cliquez sur **À partir d'Azure**, puis sur **Depuis Microsoft Azure HDInsight**.
   
    ![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]
   
    **Remarque :** si vous ne voyez pas le menu **Power Query**, cliquez sur **Fichier** > **Options** > **Compléments** et sélectionnez **Compléments COM** dans la zone déroulante **Gestionnaire** en bas de la page. Sélectionnez le bouton **OK** et vérifiez que la case correspondant au complément Power Query pour Excel a été cochée.
   
    **Remarque :** Power Query vous permet également d'importer des données à partir de HDFS en cliquant sur **À partir d'autres sources**.
4. Pour le champ **Nom du compte**, saisissez le nom du compte de stockage d’objets blob Azure associé à votre cluster, puis cliquez sur **OK**. Il peut s’agir du [compte de stockage par défaut](hdinsight-administer-use-management-portal.md#find-the-default-storage-account) ou d’un compte de stockage lié.  Le format est *https://<StorageAccountName>.blob.core.windows.net/*.
5. Pour le champ **Clé du compte**, saisissez la clé du compte de stockage d’objets blob, puis cliquez sur **Enregistrer**. (cette opération ne doit être effectuée que la première fois où vous accédez à cet entrepôt).
6. Dans le volet **Navigateur** situé à gauche de l’Éditeur de requête, double-cliquez sur le nom du conteneur de stockage d’objets Blob. Par défaut, le nom du conteneur est identique à celui du cluster.
7. Localisez **HiveSampleData.txt** dans la colonne **Nom** (le chemin du dossier est **../hive/warehouse/hivesampletable/**), puis cliquez sur **Binaire** à gauche de HiveSampleData.txt. HiveSampleData.txt est fourni avec tout le cluster. Si vous le souhaitez, vous pouvez utiliser votre propre fichier.
   
    ![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]
8. Le cas échéant, vous pouvez renommer le nom des colonnes. Lorsque vous êtes prêt, cliquez sur **Fermer et charger**.  Les données ont été chargées dans votre classeur :
   
    ![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à utiliser Power Query pour extraire des données de HDInsight et les importer dans Excel. De la même façon, vous pouvez extraire des données de HDInsight et les importer dans une base de données SQL Azure. Il est également possible de télécharger des données dans HDInsight. Pour en savoir plus, consultez les articles suivants :

* [Connexion d’Excel à HDInsight à l’aide du pilote ODBC Microsoft Hive][hdinsight-ODBC]
* [Téléchargement de données vers HDInsight][hdinsight-upload-data]

[hdinsight-ODBC]: hdinsight-connect-excel-hive-odbc-driver.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689

