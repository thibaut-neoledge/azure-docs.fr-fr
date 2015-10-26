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
	ms.date="10/09/2015"
	ms.author="larryfr"/>

#Utilisation de Sqoop avec Hadoop dans HDInsight (SSH)

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Découvrez comment utiliser Sqoop pour importer et exporter entre un cluster HDInsight sous Linux et la base de données SQL Azure ou SQL Server.

> [AZURE.NOTE]Les étapes décrites dans cet article utilisent SSH pour se connecter à un cluster HDInsight sous Linux. Les clients Windows utilisent également Azure PowerShell pour travailler avec Sqoop sur les clusters Linux comme documenté dans [Utilisation de Sqoop avec Hadoop dans HDInsight (PowerShell)](hdinsight-use-sqoop.md).

##Qu'est-ce que Sqoop ?

Bien que Hadoop soit préférable pour traiter des données non structurées et semi-structurées, telles que des journaux et des fichiers, il peut être également nécessaire de traiter les données structurées stockées dans des bases de données relationnelles.

[Sqoop][sqoop-user-guide-1.4.4] est un outil conçu pour transférer des données entre les clusters Hadoop et les bases de données relationnelles. Vous pouvez l’utiliser pour importer des données depuis un système de gestion de base de données relationnelle (SGBDR) tel que SQL Server, MySQL ou Oracle dans un système de fichiers distribués Hadoop (HDFS), transformer des données dans Hadoop avec MapReduce ou Hive et exporter à nouveau les données dans un SGBDR. Dans ce didacticiel, vous allez utiliser une base de données SQL Server comme base de données relationnelle.

Pour obtenir la liste des versions Sqoop prises en charge par les clusters HDInsight, consultez la rubrique [Nouveautés sur les versions de cluster fournies par HDInsight][hdinsight-versions].


##Configuration requise

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Station de travail** : ordinateur avec un client SSH.

- **Interface de ligne de commande Azure** : pour plus d’informations, consultez la rubrique [Installation et configuration de l’interface de ligne de commande Azure](../xplat-cli-install.md).

- **Cluster HDInsight sous Linux** : pour obtenir des instructions sur l’approvisionnement des clusters, consultez les rubriques [Prise en main de HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) ou [Approvisionnement de clusters HDInsight][hdinsight-provision].

- **Base de données SQL Azure** : ce document fournit des instructions pour la création d’un exemple de base de données SQL. Pour plus d’informations sur la base de données SQL, consultez la rubrique [Prise en main de Base de données SQL Azure][sqldatabase-get-started].

* **SQL Server** : les étapes décrites dans ce document peuvent également être utilisées, avec quelques modifications, avec SQL Server ; toutefois, le cluster HDInsight et SQL Server doivent être sur le même réseau virtuel Azure. Pour plus d’informations sur la configuration requise spécifique de l’utilisation de cet article avec SQL Server, consultez la section [Utilisation de SQL Server](#using-sql-server).

##Présentation du scénario

Le cluster HDInsight inclut des exemples de données. Vous utilisez une table Hive appelée **hivesampletable**, qui référence le fichier de données situé sous ****wasb:///hive/warehouse/hivesampletable**. Cette table contient des données relatives aux appareils mobiles. Le schéma de la table Hive est le suivant :

| Champ | Type de données |
| ----- | --------- |
| clientid | string |
| querytime | string |
| market | string |
| deviceplatform | string |
| devicemake | string |
| devicemodel | string |
| state | string |
| country | string |
| querydwelltime | double |
| sessionid | bigint |
| sessionpagevieworder | bigint |

Vous allez tout d’abord exporter **hivesampletable**, dans une table nommée **mobiledata**, dans la base de données SQL Azure ou SQL Server, puis réimporter la table dans HDInsight à l’adresse ****wasb:///tutorials/usesqoop/importeddata**.

##Créer une base de données

1. Ouvrez un terminal ou une invite de commandes et utilisez la commande suivante pour créer un serveur de base de données SQL Azure :

        azure sql server create <adminLogin> <adminPassword> <region>

    Par exemple, `azure sql server create admin password "West US"`.

    Quand la commande est terminée, vous recevez une réponse similaire à ce qui suit :

        info:    Executing command sql server create
        + Creating SQL Server
        data:    Server Name i1qwc540ts
        info:    sql server create command OK

    > [AZURE.IMPORTANT]Notez le nom de serveur renvoyé par cette commande. Voici le nom court du serveur de base de données SQL qui a été créé. Le nom de domaine complet (FQDN) est **&lt;nomcourt&gt;.database.windows.net**.

2. Utilisez la commande suivante pour créer une base de données nommée **sqooptest** sur le serveur de base de données SQL :

        sql db create [options] <serverName> sqooptest <adminLogin> <adminPassword>

    Un message « OK » s’affiche à la fin de l’opération.

	> [AZURE.NOTE]Si une erreur indiquant un problème d’accès s’affiche, vous devez ajouter l’adresse IP de votre station de travail cliente au pare-feu de la base de données SQL à l’aide de la commande suivante :
	>
	> `sql firewallrule create [options] <serverName> <ruleName> <startIPAddress> <endIPAddress>`

##Création d'une table

> [AZURE.NOTE]Il existe de nombreuses façons de se connecter à la base de données SQL pour créer une table. Les étapes suivantes utilisent [FreeTDS](http://www.freetds.org/) à partir du cluster HDInsight.

1. Utilisez SSH pour vous connecter à un cluster HDInsight sous Linux. L’adresse à utiliser pendant la connexion est `CLUSTERNAME-ssh.azurehdinsight.net` et le port est `22`.

	Pour plus d’informations sur l’utilisation de SSH pour se connecter à HDInsight, consultez les documents suivants :

    * **Clients Linux, Unix ou OS X** : consultez la rubrique [Connexion à un cluster HDInsight sous Linux à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)

    * **Clients Windows** : consultez la rubrique [Connexion à un cluster HDInsight sous Linux à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)

3. Utilisez la commande suivante pour installer FreeTDS :

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. Une fois FreeTDS installé, utilisez la commande suivante pour vous connecter au serveur de base de données SQL créé précédemment :

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Le résultat ressemble à ce qui suit :

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. À l’invite de commandes `1>`, entrez les lignes suivantes :

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

    Une fois entrée l’instruction `GO`, les instructions précédentes sont évaluées. Tout d’abord, la table **mobiledata** est créée, puis un index cluster est ajouté (nécessaire à la base de données SQL).

    Vérifiez que la table a été créée à l’aide des éléments suivants :

        SELECT * FROM information_schema.tables
        GO

    Le résultat ressemble à ce qui suit :

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE

8. Pour quitter l’utilitaire psql, entrez `exit` à l’invite de commandes `1>`.

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

    > [AZURE.NOTE]HDInsight prend en charge uniquement les réseaux virtuels basés sur l'emplacement et ne fonctionne pas pour le moment avec des réseaux virtuels basés sur des groupes d'affinités.

    Lorsque vous utilisez SQL Server dans votre centre de données, vous devez configurer le réseau virtuel comme étant *de site à site* ou *de point à site*.

    > [AZURE.NOTE]Pour les réseaux virtuels de **point à site**, SQL Server doit exécuter l'application de configuration du client VPN, qui est disponible depuis le **tableau de bord** de la configuration de votre réseau virtuel Azure.

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
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

<!---HONumber=Oct15_HO3-->