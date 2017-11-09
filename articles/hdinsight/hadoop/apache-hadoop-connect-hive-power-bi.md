---
title: "Visualiser des Big Data à l’aide de Power BI dans Azure HDInsight | Microsoft Docs"
description: "Découvrez comment utiliser Microsoft Power BI pour visualiser des données Hive traitées par Azure HDInsight."
keywords: hdinsight,hadoop,hive,interactive query,interactive hive,LLAP,odbc
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: jgao
ms.openlocfilehash: 390342eb08ae970fa760b414674b1a6783404d80
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2017
---
# <a name="visualize-hive-data-with-microsoft-power-bi-in-azure-hdinsight"></a>Visualiser des données Hive à l’aide de Microsoft Power BI dans Azure HDInsight

Découvrez comment connecter Microsoft Power BI à Azure HDInsight et comment visualiser des données Hive. Power BI prend uniquement en charge les connexions ODBC à HDInsight. Dans ce didacticiel, vous allez charger les données d’une table Hive hivesampletable dans Power BI. La table Hive contient des données sur l’utilisation des téléphones mobiles. Vous allez ensuite tracer ces données d’utilisation sur une carte du monde :

![rapport cartographique Power BI HDInsight](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Les informations s’appliquent également au nouveau type de cluster [Interactive Query](../interactive-query/apache-interactive-query-get-started.md).

## <a name="prerequisites"></a>Composants requis
Avant de poursuivre cet article, vérifiez que vous avez les éléments suivants :

* Un **cluster HDInsight**. Cela peut être un cluster HDInsight avec Hive ou un cluster du nouveau type Interactive Query. Pour plus d’informations sur la création de clusters, consultez [Créer un cluster](apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Vous pouvez télécharger une copie à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Création d’une source de données ODBC Hive

Consultez [Création d’une source de données ODBC Hive](apache-hadoop-connect-excel-hive-odbc-driver.md#create-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Chargement des données à partir de HDInsight

La table Hive hivesampletable est fournie avec tous les clusters HDInsight.

1. Connectez-vous à Power BI Desktop.
2. Cliquez sur l’onglet **Accueil**, cliquez sur **Obtenir des données** dans le ruban **Données externes**, puis sélectionnez **Plus**.

    ![données affichées Power BI HDInsight](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)
3. Dans le volet **Obtenir des données**, cliquez sur **Autre** à gauche, cliquez sur **ODBC** à droite, puis cliquez sur **Se connecter** en bas.
4. Dans le volet **À partir d’ODBC**, sélectionnez le nom de la source de données que vous avez créée dans la dernière section, puis cliquez sur **OK**.
5. Dans le volet **Navigateur**, développez **ODBC->HIVE->par défaut**, sélectionnez **hivesampletable**, puis cliquez sur **Charger**.

## <a name="visualize-date"></a>Visualisation des données

Poursuivez la procédure précédente.

1. Dans le volet Visualisations, sélectionnez **Carte**  (icône représentant un globe).

    ![personnalisation de rapport Power BI HDInsight](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. Dans le volet Champs, sélectionnez **country** et **devicemake**. Vous pouvez voir les données tracées sur la carte.
3. Développez la carte.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à visualiser des données de HDInsight à l’aide de Power BI.  Pour en savoir plus, consultez les articles suivants :

* [Utiliser Zeppelin pour exécuter des requêtes Hive dans Azure HDInsight](./../hdinsight-connect-hive-zeppelin.md).
* [Connecter Excel à HDInsight avec le pilote ODBC Microsoft Hive](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Connexion d’Excel à Hadoop à l’aide de Power Query](apache-hadoop-connect-excel-power-query.md).
* [Se connecter à Azure HDInsight et exécuter des requêtes Hive avec Data Lake Tools pour Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Utiliser Azure HDInsight Tools pour Visual Studio Code](../hdinsight-for-vscode.md).
* [Charger des données dans HDInsight](./../hdinsight-upload-data.md).
