<properties
	pageTitle="Utilisation de Hadoop Sqoop dans HDInsight basé sur Linux | Microsoft Azure"
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

> [AZURE.NOTE] Les étapes décrites dans cet article utilisent SSH pour se connecter à un cluster HDInsight sous Linux. Les clients Windows utilisent également Azure PowerShell pour travailler avec Sqoop sur les clusters Linux comme documenté dans [Utilisation de Sqoop avec Hadoop dans HDInsight (PowerShell)](hdinsight-use-sqoop.md).

##Qu'est-ce que Sqoop ?

Bien que Hadoop soit préférable pour traiter des données non structurées et semi-structurées, telles que des journaux et des fichiers, il peut être également nécessaire de traiter les données structurées stockées dans des bases de données relationnelles.

[Sqoop][sqoop-user-guide-1.4.4] est un outil conçu pour transférer des données entre les clusters Hadoop et les bases de données relationnelles. Vous pouvez l’utiliser pour importer des données depuis un système de gestion de base de données relationnelle (SGBDR) tel que SQL Server, MySQL ou Oracle dans un système de fichiers distribués Hadoop (HDFS), transformer des données dans Hadoop avec MapReduce ou Hive et exporter à nouveau les données dans un SGBDR. Dans ce didacticiel, vous allez utiliser une base de données SQL Server comme base de données relationnelle.

Pour obtenir la liste des versions Sqoop prises en charge par les clusters HDInsight, consultez la rubrique [Nouveautés sur les versions de cluster fournies par HDInsight][hdinsight-versions].


##Configuration requise

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Station de travail** : ordinateur avec un client SSH.

- **Interface de ligne de commande Azure** : pour plus d’informations, consultez la rubrique [Installation et configuration de l’interface de ligne de commande Azure](../xplat-cli-install.md).

##Présentation du scénario

Le cluster HDInsight inclut des exemples de données. Vous utilisez une table Hive appelée **hivesampletable**, qui référence le fichier de données situé sous **wasb:///hive/warehouse/hivesampletable**. Cette table contient des données relatives aux appareils mobiles. Le schéma de la table Hive est le suivant :

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


## Création du cluster et de la base de données SQL

1. Cliquez sur l’image suivante pour ouvrir un modèle ARM dans le portail Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fusesqoop%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-and-sql-database.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Le modèle ARM est situé dans le conteneur d’objets blob public **https://hditutorialdata.blob.core.windows.net/usesqoop/create-linux-based-hadoop-cluster-in-hdinsight-and-sql-database.json*.
    
    Le modèle ARM appelle un package bacpac pour déployer les schémas de table vers la base de données SQL. Le package bacpac est lui aussi situé dans le conteneur d’objets blob public https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Si vous souhaitez utiliser un conteneur privé pour stocker les fichiers bacpac, appliquez les valeurs suivantes au modèle :
    
        "storageKeyType": "Primary",
        "storageKey": "<TheAzureStorageAccountKey>",
    
2. À partir du panneau Paramètres, saisissez les informations suivantes :

    - **ClusterName** : entrez le nom du cluster Hadoop que vous allez créer.
    - **Nom d’utilisateur et mot de passe de cluster** : le nom de connexion par défaut est admin.
    - **Nom d’utilisateur et mot de passe SSH**.
    - **Nom et mot de passe de connexion au serveur de base de données SQL**.

    Les valeurs suivantes sont codées en dur dans la section des variables :
    
    |Nom du compte de stockage par défaut|<CluterName>store|
    |----------------------------|-----------------|
    |Nom du serveur de base de données SQL Azure.|<ClusterName>dbserver|
    |Nom de la base de données SQL Azure|<ClusterName>db|
    
    Veuillez noter ces valeurs. Vous en aurez besoin plus loin dans le didacticiel.
    
3\. Cliquez sur **OK** pour enregistrer les paramètres.

4\. Dans le panneau **Déploiement personnalisé**, cliquez sur la zone de liste déroulante **Groupe de ressources**, puis cliquez sur **Nouveau** pour créer un groupe de ressources. Le groupe de ressources est un conteneur qui regroupe le cluster, le compte de stockage dépendant et une autre ressource liée.

5\. Cliquez sur **Conditions juridiques**, puis sur **Créer**.

6\. Cliquez sur **Créer**. Vous verrez une nouvelle vignette intitulée Envoi du déploiement pour Déploiement de modèle. La création du cluster et de la base de données SQL prend environ 20 minutes.

Si vous choisissez d’utiliser une base de données SQL Azure ou Microsoft SQL Server existante

- **Base de données SQL Azure** : vous devez configurer une règle de pare-feu pour le serveur de base de données SQL Azure afin d’autoriser l'accès depuis votre station de travail. Pour des instructions sur la création d'une base de données SQL Azure et la configuration d'un pare-feu, consultez la rubrique [Prise en main de la base de données SQL Azure][sqldatabase-get-started]. 

    > [AZURE.NOTE] Par défaut, une base de données SQL Azure autorise des connexions aux services Azure tels qu’Azure HDinsight. Si ce paramètre de pare-feu est désactivé, vous devez l'activer depuis le portail de gestion Azure. Pour obtenir des instructions sur la création d'une base de données SQL Azure et la configuration des règles de pare-feu, consultez la rubrique [Création et configuration d'une base de données SQL][sqldatabase-create-configue].

- **SQL Server** : si votre cluster HDInsight se trouve sur le même réseau virtuel que SQL Server dans Azure, vous pouvez utiliser les étapes décrites dans cet article pour importer et exporter des données vers une base de données SQL Server.

    > [AZURE.NOTE] HDInsight prend en charge uniquement les réseaux virtuels basés sur l'emplacement et ne fonctionne pas pour le moment avec des réseaux virtuels basés sur des groupes d'affinités.

    * Pour créer et configurer un réseau virtuel, consultez la rubrique [Tâches de configuration du réseau virtuel](../services/virtual-machines/).

        * Lorsque vous utilisez SQL Server dans votre centre de données, vous devez configurer le réseau virtuel comme étant *de site à site* ou *de point à site*.

            > [AZURE.NOTE] Pour les réseaux virtuels de **point à site**, SQL Server doit exécuter l'application de configuration du client VPN, qui est disponible depuis le **tableau de bord** de la configuration de votre réseau virtuel Azure.

        * Lorsque vous utilisez SQL Server sur une machine virtuelle Azure, toute configuration du réseau virtuel peut être utilisée si la machine virtuelle qui héberge SQL Server est membre du même réseau virtuel que HDInsight.

    * Pour créer un cluster HDInsight sur un réseau virtuel, consultez la rubrique [Création de clusters Hadoop basés sur Windows dans HDInsight](hdinsight-provision-clusters.md)

    > [AZURE.NOTE] SQL Server doit également autoriser l'authentification. Vous devez utiliser une connexion SQL Server pour compléter les étapes décrites dans cet article.
	

##Exportation de Sqoop

2. Utilisez la commande suivante pour créer un lien vers le pilote JDBC SQL Server à partir du répertoire de la bibliothèque Sqoop. Sqoop peut ainsi utiliser ce pilote pour communiquer avec la base de données SQL :

        sudo ln /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /usr/hdp/current/sqoop-client/lib/sqljdbc41.jar

3. Utilisez la commande suivante pour vérifier que Sqoop peut voir votre base de données SQL :

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    Cette commande doit renvoyer une liste des bases de données, y compris la base de données **sqooptest** que vous avez créée précédemment.

4. Utilisez la commande suivante pour exporter des données à partir de **hivesampletable** dans la table **mobiledata** :

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasb:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1

    Sqoop doit se connecter à la base de données SQL, à la base de données **sqooptest**, puis exporter des données à partir de ****wasb:///hive/warehouse/hivesampletable** (fichiers physiques de *hivesampletable*,) vers la table **mobiledata**.

5. Une fois la commande terminée, utilisez les éléments suivants pour vous connecter à la base de données à l’aide de TSQL :

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Une fois que vous êtes connecté, utilisez les instructions suivantes pour vérifier que les données ont été exportées dans la table **mobiledata** :

        SELECT * FROM mobiledata
        GO

    Vous devez voir une liste des données dans la table. Tapez `exit` pour quitter l’utilitaire tsql.

##Importation de Sqoop

1. Utilisez la commande suivante pour importer des données à partir de la table **mobiledata** de la base de données SQL dans le répertoire ****wasb:///tutorials/usesqoop/importeddata** sur HDInsight :

        sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

    Les champs des données importées sont séparés par un caractère de tabulation et les lignes se terminent par un caractère de nouvelle ligne.

2. Une fois l’importation terminée, utilisez la commande suivante pour répertorier les données dans le nouveau répertoire :

        hadoop fs -text wasb:///tutorials/usesqoop/importeddata/part-m-00000

##Utilisation de SQL Server

Vous pouvez également utiliser Sqoop pour importer et exporter des données à partir de SQL Server, dans votre centre de données ou sur une machine virtuelle hébergée dans Azure. Les différences entre l’utilisation de la base de données SQL et SQL Server sont les suivantes :

* HDInsight et le serveur SQL doivent être sur le même réseau virtuel Azure

    > [AZURE.NOTE] HDInsight prend en charge uniquement les réseaux virtuels basés sur l'emplacement et ne fonctionne pas pour le moment avec des réseaux virtuels basés sur des groupes d'affinités.

    Lorsque vous utilisez SQL Server dans votre centre de données, vous devez configurer le réseau virtuel comme étant *de site à site* ou *de point à site*.

    > [AZURE.NOTE] Pour les réseaux virtuels de **point à site**, SQL Server doit exécuter l'application de configuration du client VPN, qui est disponible depuis le **tableau de bord** de la configuration de votre réseau virtuel Azure.

    Pour plus d’informations sur la création et la configuration d’un réseau virtuel, consultez la rubrique [Tâches de configuration d’un réseau virtuel](../services/virtual-machines/).

* Le serveur SQL Server doit également être configuré pour autoriser l’authentification SQL. Pour plus d’informations, consultez la rubrique [Choisir un mode d’authentification](https://msdn.microsoft.com/ms144284.aspx).

* Vous devez configurer SQL Server pour qu’il accepte les connexions à distance. Pour plus d’informations, consultez la page [Résolution des problèmes de connexion au moteur de base de données SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

* Vous devez créer la base de données **sqooptest** dans SQL Server à l’aide d’un utilitaire tel que **SQL Server Management Studio** ou **tsql**. Les étapes d’utilisation de l’interface de ligne de commande Azure fonctionnent uniquement pour Base de données SQL Azure.

    Les instructions TSQL permettant de créer la table **mobiledata** sont similaires à celles utilisées pour la base de données SQL, à l’exception de la création d’un index cluster. Cela n’est pas obligatoire pour SQL Server :

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

* En vous connectant à SQL Server à partir de HDInsight, vous devez utiliser l’adresse IP du serveur SQL, sauf si vous avez configuré un DNS (Domain Name System) pour résoudre les noms sur le réseau virtuel Azure. Par exemple :

        sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

##Étapes suivantes

Vous maîtrisez à présent l'utilisation de Sqoop. Pour plus d'informations, consultez les rubriques suivantes :

- [Utilisation d'Oozie avec HDInsight][hdinsight-use-oozie] : utilisez l’action Sqoop dans un flux de travail Oozie.
- [Analyse des données sur les retards de vol avec HDInsight][hdinsight-analyze-flight-data] : utilisez Hive pour analyser des données sur les retards de vol, puis utilisez Sqoop pour exporter ces données vers une base de données SQL Azure.
- [Téléchargement de données vers HDInsight][hdinsight-upload-data] : découvrez d'autres méthodes pour télécharger des données vers HDInsight ou le stockage d'objets blob Azure.



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

<!----HONumber=AcomDC_0323_2016-->