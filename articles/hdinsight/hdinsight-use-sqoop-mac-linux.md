---
title: Apache Sqoop avec Hadoop - Azure HDInsight | Documents Microsoft
description: "Découvrez comment utiliser Apache Sqoop pour échanger des données entre Hadoop sur HDInsight et Azure SQL Database."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
author: Blackmist
tags: azure-portal
keywords: hadoop sqoop, sqoop
ms.assetid: 303649a5-4be5-4933-bf1d-4b232083c354
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: a1e46f8cdcbb4dbb0f3bd64d677cd06a2e31d884
ms.contentlocale: fr-fr
ms.lasthandoff: 05/16/2017


---
# <a name="use-apache-sqoop-to-import-and-export-data-between-hadoop-in-hdinsight-and-sql-database"></a>Utiliser Apache Sqoop pour échanger des données entre Hadoop sur HDInsight et SQL Database

[!INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Découvrez comment utiliser Apache Sqoop pour échanger des données entre un cluster Hadoop dans Azure HDInsight et une base de données Azure SQL Database ou Microsoft SQL Server. Les étapes décrites dans ce document utilisent la commande `sqoop` directement à partir du nœud principal du cluster Hadoop. Vous utilisez SSH pour vous connecter au nœud principal et exécutez les commandes décrites dans ce document.

> [!IMPORTANT]
> Les étapes décrites dans ce document fonctionnent uniquement avec les clusters HDInsight sur Linux. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Contrôle de version des composants HDInsight](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

## <a name="install-freetds"></a>Installer FreeTDS

1. Utilisez SSH pour vous connecter au cluster HDInsight. Par exemple, la commande suivante établit une connexion au nœud principal d’un cluster nommé `mycluster` :

    ```bash
    ssh CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilisez la commande suivante pour installer FreeTDS :

    ```bash
    sudo apt install --assume-yes install freetds-dev freetds-bin
    ```

    FreeTDS est utilisé dans plusieurs étapes pour se connecter à SQL Database.

## <a name="create-the-table-in-sql-database"></a>Créer la table dans la base de données SQL

> [!IMPORTANT]
> Si vous utilisez le cluster HDInsight et la base de données SQL Database créés à l’étape [Créer un cluster et une base de données SQL](hdinsight-use-sqoop.md), ignorez les étapes décrites dans cette section. La base de données et la table ont été créées dans le cadre de la procédure décrite dans le document [Créer un cluster et une base de données SQL](hdinsight-use-sqoop.md).

1. À partir de la session SSH, utilisez la commande suivante pour vous connecter au serveur SQL Database.

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Le résultat ressemble au texte suivant :

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

2. À l’invite de commandes `1>` , entrez la requête suivante :

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [clientid] [nvarchar](50),
    [querytime] [nvarchar](50),
    [market] [nvarchar](50),
    [deviceplatform] [nvarchar](50),
    [devicemake] [nvarchar](50),
    [devicemodel] [nvarchar](50),
    [state] [nvarchar](50),
    [country] [nvarchar](50),
    [querydwelltime] [float],
    [sessionid] [bigint],
    [sessionpagevieworder] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
    GO
    ```

    Une fois l’instruction `GO` entrée, les instructions précédentes sont évaluées. Tout d’abord, la table **mobiledata** est créée, puis un index cluster est ajouté (nécessaire à la base de données SQL).

    Utilisez la requête suivante pour vérifier que la table a été créée :

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Le résultat est similaire au texte suivant :

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE

3. Pour quitter l’utilitaire psql, entrez `exit` at the `1>` .

## <a name="sqoop-export"></a>Exportation de Sqoop

1. À partir de la connexion SSH au cluster, utilisez la commande suivante pour vérifier que Sqoop peut voir votre base de données SQL Database :

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>
    ```

    Cette commande renvoie une liste des bases de données, y compris la base de données **sqooptest** que vous avez créée précédemment.

2. Pour exporter des données à partir de la table **hivesampletable** dans la table **mobiledata**, utilisez la commande suivante :

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasbs:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1
    ```

    Cette commande demande à Sqoop de se connecter à la base de données **sqooptest**. Sqoop exporte alors les données depuis **wasbs:///hive/warehouse/hivesampletable** vers la table **mobiledata**.

3. Une fois la commande terminée, utilisez la commande suivante pour vous connecter à la base de données à l’aide de TSQL :

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest
    ```

    Une fois que vous êtes connecté, utilisez les instructions suivantes pour vérifier que les données ont été exportées dans la table **mobiledata** :

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    Vous devez voir une liste des données dans la table. Tapez `exit` pour quitter l’utilitaire tsql.

## <a name="sqoop-import"></a>Importation de Sqoop

1. Utilisez la commande suivante pour importer des données à partir de la table **mobiledata** dans SQL Database vers le répertoire **wasbs:///tutorials/usesqoop/importeddata** sur HDInsight :

    ```bash
    sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

    Les champs dans les données sont séparés par un caractère de tabulation et les lignes se terminent par un caractère de nouvelle ligne.

2. Une fois l’importation terminée, utilisez la commande suivante pour répertorier les données dans le nouveau répertoire :

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="using-sql-server"></a>Utilisation de SQL Server

Vous pouvez également utiliser Sqoop pour importer et exporter des données à partir de SQL Server, dans votre centre de données ou sur une machine virtuelle hébergée dans Azure. Les différences entre l’utilisation de la base de données SQL et SQL Server sont les suivantes :

* HDInsight et SQL Server doivent figurer sur le même réseau Azure Virtual Network.

    Lorsque vous utilisez SQL Server dans votre centre de données, vous devez configurer le réseau virtuel comme étant *de site à site* ou *de point à site*.

  > [!NOTE]
  > Lorsque vous utilisez un réseau virtuel **de point à site**, SQL Server doit exécuter l’application de configuration du client VPN. Le client VPN est disponible à partir du **tableau de bord** de votre configuration de réseau virtuel Azure.

    Pour plus d’informations sur l’utilisation de HDInsight avec Azure Virtual Network, voir le document [Étendre HDInsight avec Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md). Pour plus d’informations sur Azure Virtual Network, voir [Présentation du réseau virtuel](../virtual-network/virtual-networks-overview.md).

* Le serveur SQL Server doit également être configuré pour autoriser l’authentification SQL. Pour plus d’informations, consultez le document [Choisir un mode d’authentification](https://msdn.microsoft.com/ms144284.aspx).

* Vous devez configurer SQL Server pour qu’il accepte les connexions à distance. Pour plus d’informations, consultez le document [Résolution des problèmes de connexion au moteur de base de données SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

* Créez la base de données **sqooptest** dans SQL Server à l’aide d’un utilitaire tel que **SQL Server Management Studio** ou **tsql**. Les étapes d’utilisation de l’interface Azure CLI fonctionnent uniquement pour Azure SQL Database.

    Utilisez les instructions Transact-SQL suivantes pour créer la table **mobiledata**:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [clientid] [nvarchar](50),
    [querytime] [nvarchar](50),
    [market] [nvarchar](50),
    [deviceplatform] [nvarchar](50),
    [devicemake] [nvarchar](50),
    [devicemodel] [nvarchar](50),
    [state] [nvarchar](50),
    [country] [nvarchar](50),
    [querydwelltime] [float],
    [sessionid] [bigint],
    [sessionpagevieworder] [bigint])
    ```

* Lors de la connexion à SQL Server à partir de HDInsight, vous devrez peut-être utiliser l’adresse IP du serveur SQL. Par exemple :

    ```bash
    sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

## <a name="limitations"></a>Limitations

* Exportation en bloc : avec HDInsight sous Linux, le connecteur Sqoop utilisé pour exporter des données vers Microsoft SQL Server ou la base de données SQL Azure ne prend pas en charge les insertions en bloc.

* Traitement par lots : avec HDInsight sous Linux, lorsque vous utilisez le commutateur `-batch` pour effectuer des insertions, Sqoop effectue plusieurs insertions plutôt qu’un traitement par lots des opérations d’insertion.

## <a name="next-steps"></a>Étapes suivantes

Vous maîtrisez à présent l'utilisation de Sqoop. Pour plus d'informations, consultez les rubriques suivantes :

* [Utilisation d’Oozie avec HDInsight][hdinsight-use-oozie] : utilisez l’action Sqoop dans un workflow Oozie.
* [Analyse des données sur les retards de vol avec HDInsight][hdinsight-analyze-flight-data] : utilisez Hive pour analyser des données sur les retards de vol, puis utilisez Sqoop pour exporter ces données vers une base de données SQL Azure.
* [Chargement de données vers HDInsight][hdinsight-upload-data] : découvrez d’autres méthodes pour charger des données vers HDInsight ou Stockage Blob Azure.

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

