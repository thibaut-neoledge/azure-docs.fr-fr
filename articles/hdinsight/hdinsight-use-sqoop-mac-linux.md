<properties
	pageTitle="Utilisation de Hadoop Sqoop dans HDInsight basé sur Linux | Microsoft Azure"
	description="Découvrez comment utiliser l’importation et l’exportation Sqoop entre un cluster Hadoop sur HDInsight sous Linux et une base de données SQL Azure."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/09/2016"
	ms.author="larryfr"/>

#Utilisation de Sqoop avec Hadoop dans HDInsight (SSH)

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Découvrez comment utiliser Sqoop pour importer et exporter entre un cluster HDInsight sous Linux et la base de données SQL Azure ou SQL Server.

> [AZURE.NOTE] Les étapes décrites dans cet article utilisent SSH pour se connecter à un cluster HDInsight sous Linux. Les clients Windows utilisent également Azure PowerShell et le Kit de développement logiciel (SDK) HDInsight .NET pour travailler avec Sqoop sur les clusters Linux. Utilisez le sélecteur d’onglet pour ouvrir ces articles.

##Composants requis

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :


- **Cluster Hadoop dans HDInsight**. Consultez [Création du cluster et de la base de données SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database).
- **Station de travail** : ordinateur avec un client SSH.
- **Interface de ligne de commande Azure** : pour plus d’informations, consultez la rubrique [Installation et configuration de l’interface de ligne de commande Azure](../xplat-cli-install.md).

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##Exportation de Sqoop

2. Utilisez la commande suivante pour créer un lien vers le pilote JDBC SQL Server à partir du répertoire de la bibliothèque Sqoop. Sqoop peut ainsi utiliser ce pilote pour communiquer avec la base de données SQL :

        sudo ln /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /usr/hdp/current/sqoop-client/lib/sqljdbc41.jar

3. Utilisez la commande suivante pour vérifier que Sqoop peut voir votre base de données SQL :

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    Cette commande doit renvoyer une liste des bases de données, y compris la base de données **sqooptest** que vous avez créée précédemment.

4. Utilisez la commande suivante pour exporter des données à partir de **hivesampletable** dans la table **mobiledata** :

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasb:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1

    Sqoop doit se connecter à la base de données SQL, à la base de données **sqooptest**, puis exporter des données à partir de ****wasb:///hive/warehouse/hivesampletable** (fichiers physiques de *hivesampletable*,) vers la table **mobiledata**.

5. Une fois la commande terminée, utilisez les éléments suivants pour vous connecter à la base de données à l’aide de TSQL :

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Une fois que vous êtes connecté, utilisez les instructions suivantes pour vérifier que les données ont été exportées dans la table **mobiledata** :

        SELECT * FROM mobiledata
        GO

    Vous devez voir une liste des données dans la table. Tapez `exit` pour quitter l’utilitaire tsql.

##Importation de Sqoop

1. Utilisez la commande suivante pour importer des données à partir de la table **mobiledata** de la base de données SQL dans le répertoire ****wasb:///tutorials/usesqoop/importeddata** sur HDInsight :

        sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

    Les champs des données importées sont séparés par un caractère de tabulation et les lignes se terminent par un caractère de nouvelle ligne.

2. Une fois l’importation terminée, utilisez la commande suivante pour répertorier les données dans le nouveau répertoire :

        hadoop fs -text wasb:///tutorials/usesqoop/importeddata/part-m-00000

##Utilisation de SQL Server

Vous pouvez également utiliser Sqoop pour importer et exporter des données à partir de SQL Server, dans votre centre de données ou sur une machine virtuelle hébergée dans Azure. Les différences entre l’utilisation de la base de données SQL et SQL Server sont les suivantes :

* HDInsight et le serveur SQL doivent être sur le même réseau virtuel Azure

    > [AZURE.NOTE] HDInsight prend en charge uniquement les réseaux virtuels basés sur l'emplacement et ne fonctionne pas pour le moment avec des réseaux virtuels basés sur des groupes d'affinités.

    Lorsque vous utilisez SQL Server dans votre centre de données, vous devez configurer le réseau virtuel comme étant *de site à site* ou *de point à site*.

    > [AZURE.NOTE] Pour les réseaux virtuels de **point à site**, SQL Server doit exécuter l'application de configuration du client VPN, qui est disponible depuis le **tableau de bord** de la configuration de votre réseau virtuel Azure.

    Pour plus d’informations sur la création et la configuration d’un réseau virtuel, consultez la rubrique [Tâches de configuration d’un réseau virtuel](../services/virtual-machines/).

* Le serveur SQL Server doit également être configuré pour autoriser l’authentification SQL. Pour plus d’informations, consultez la rubrique [Choisir un mode d’authentification](https://msdn.microsoft.com/ms144284.aspx).

* Vous devez configurer SQL Server pour qu’il accepte les connexions à distance. Pour plus d’informations, consultez la page [Résolution des problèmes de connexion au moteur de base de données SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

* Vous devez créer la base de données **sqooptest** dans SQL Server à l’aide d’un utilitaire tel que **SQL Server Management Studio** ou **tsql**. Les étapes d’utilisation de l’interface de ligne de commande Azure fonctionnent uniquement pour Base de données SQL Azure.

    Les instructions TSQL permettant de créer la table **mobiledata** sont similaires à celles utilisées pour la base de données SQL, à l’exception de la création d’un index cluster. Cela n’est pas obligatoire pour SQL Server :

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

* En vous connectant à SQL Server à partir de HDInsight, vous devez utiliser l’adresse IP du serveur SQL, sauf si vous avez configuré un DNS (Domain Name System) pour résoudre les noms sur le réseau virtuel Azure. Par exemple :

        sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

##Étapes suivantes

Vous maîtrisez à présent l'utilisation de Sqoop. Pour plus d'informations, consultez les rubriques suivantes :

- [Utilisation d'Oozie avec HDInsight][hdinsight-use-oozie] : utilisez l’action Sqoop dans un flux de travail Oozie.
- [Analyse des données sur les retards de vol avec HDInsight][hdinsight-analyze-flight-data] : utilisez Hive pour analyser des données sur les retards de vol, puis utilisez Sqoop pour exporter ces données vers une base de données SQL Azure.
- [Téléchargement de données vers HDInsight][hdinsight-upload-data] : découvrez d'autres méthodes pour télécharger des données vers HDInsight ou le stockage d'objets blob Azure.



[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

<!---HONumber=AcomDC_0420_2016-->