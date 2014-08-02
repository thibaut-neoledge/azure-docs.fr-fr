<properties linkid="manage-services-hdinsight-sample-sqoop-import-export" urlDisplayName="HDInsight Samples" pageTitle="Sqoop Import-Export sample | Windows Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to run Sqoop import and export on HDInsight." umbracoNaviHide="0" disqusComments="1" writer="bradsev" editor="cgronlun" manager="paulettm" title="Sqoop Import-Export sample" />

Exemple d'importation/exportation Sqoop
=======================================

Cette rubrique d'exemple montre comment utiliser Apache Sqoop pour importer des données d'une base de données SQL sur Azure vers une infrastructure Hadoop sur le cluster HDFS Azure.

Alors qu'il semble naturel de choisir Hadoop pour traiter des données non structurées et semi-structurées, telles que des journaux et des fichiers, il peut également s'avérer nécessaire de traiter les données structurées stockées dans des bases de données relationnelles.

Sqoop est un outil conçu pour transférer des données entre les clusters Hadoop et des bases de données relationnelles. Il permet d'importer des données depuis un système de gestion de base de données relationnelle (ou SGBDR) tel que SQL, MySQL ou Oracle vers HDFS (Hadoop Distributed File System), de transformer les données dans Hadoop avec MapReduce ou Hive, puis de les réexporter dans un SGBDR. Dans ce didacticiel, vous allez utiliser une base de données SQL comme base de données relationnelle.

Sqoop est un produit logiciel open source de Cloudera, Inc. Le développement logiciel pour Sqoop a été transféré en 2011 de gitHub sur le site [Apache Sqoop](http://sqoop.apache.org/).

Dans Azure HDInsight, Sqoop est déployé depuis l'interface de commande Hadoop sur le nœud principal du cluster HDFS. Vous pouvez utiliser la fonctionnalité Bureau à distance disponible dans Hadoop sur le portail Azure pour accéder au nœud principal du cluster pour ce déploiement.

**Vous apprendrez à effectuer les opérations suivantes :**

-   utiliser Azure PowerShell pour exécuter un programme MapReduce sur Azure HDInsight qui analyse les données contenues dans un fichier.

**Conditions préalables** : vous disposez d'un compte Azure et avez activé HDInsight Service dans le cadre de votre abonnement. Vous devez avoir installé Azure PowerShell et les outils Powershell pour Azure HDInsight et les avoir configurés afin de les utiliser avec votre compte. Pour obtenir des instructions sur la procédure à suivre, consultez la page [Prise en main d'Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/).

Vous allez également avoir besoin d'une adresse IP vers l'extérieur pour votre emplacement actuel lorsque vous configurez votre pare-feu sur la base de données SQL. Pour l'obtenir, accédez au site [WhatIsMyIP](http://www.whatismyip.com/) et notez-la. Dans la suite de la procédure, vous allez également avoir besoin de l'adresse IP vers l'extérieur pour le nœud principal du cluster Hadoop. Vous pouvez procéder de la même façon pour obtenir cette adresse IP.

**Résumé** Cette rubrique vous montre comment exécuter l'exemple, présente le code Java pour le programme MapReduce, récapitule ce que vous avez appris, et indique les étapes suivantes. Elle se compose des sections suivantes :

1.  [Configuration d'une base de données SQL](#set-up-sql)
2.  [Utilisation de Sqoop depuis HDInsight pour importer des données dans un cluster](#java-code)
3.  [Résumé](#summary)
4.  [Étapes suivantes](#next-steps)

Configuration d'une base de données SQL
---------------------------------------

TBD: captures d'écran pour cette section : il s'agit encore de l'approche RDP.

**Pour créer une base de données SQL**

1.  Connectez-vous au portail de gestion Azure.
2.  Cliquez sur NOUVEAU dans le coin inférieur gauche, cliquez sur SERVICES DE DONNÉES, puis sur BASE DE DONNÉES SQL. Connectez-vous au compte Azure. Pour créer un serveur de base de données, cliquez sur l'icône **Base de données** dans le coin inférieur gauche de la page.

3.  Sur la page **Démarrage**, cliquez sur l'option **Créer un serveur de base de données SQL**.

4.  Sélectionnez le type d'abonnement (tel que **Paiement à l'utilisation**) associé au compte figurant dans la fenêtre **Créer un serveur** et appuyez sur **Suivant**.

5.  Sélectionnez la **Région** appropriée dans la fenêtre **Créer un serveur**, puis cliquez sur **Suivant**.

6.  Spécifiez l'identifiant et le mot de passe de la connexion principale de niveau serveur de votre serveur de base de données SQL et appuyez sur **Suivant**.

7.  Appuyez sur **Ajouter** pour déterminer une règle de pare-feu qui permet l'accès de votre emplacement actuel à la base de données SQL afin de télécharger la base de données AdventureWorks. Le pare-feu octroie l'accès en fonction de l'adresse IP d'origine de chaque demande. Utilisez l'adresse IP trouvée lors des étapes préliminaires de configuration de ce didacticiel pour renseigner les valeurs à ajouter. Indiquez un nom de règle, comme illustré, mais souvenez-vous d'utiliser votre adresse IP, et pas celle utilisée à des fins d'exemple ci-dessous. (Vous devez également ajouter l'adresse IP vers l'extérieur du nœud principal de votre cluster Hadoop. Si vous la connaissez déjà, ajoutez-la à présent.) Appuyez ensuite sur le bouton **Terminer**.


**Pour importer AdventureWorks2012**

1.  Téléchargez la base de données AdventureWorks2012 sur votre ordinateur local à partir du lien Recommended Download sur le site Adventure Works for SQL Database.

2.  Décompressez le fichier, ouvrez l'invite de commandes en tant qu'administrateur, puis accédez au répertoire AdventureWorks dans le dossier AdventureWorks2012ForSQLAzure.

3.  Exécutez CreateAdventureWorksForSQLAzure.cmd en tapant la commande suivante :

   CreateAdventureWorksForSQLAzure.cmd servername username password

   Par exemple, si le serveur de base de données SQL attribué se nomme b1gl33p, le nom d'utilisateur de l'administrateur « Fred » et le mot de passe « Secret », vous tapez la commande suivante :

    CreateAdventureWorksForSQLAzure.cmd b1gl33p.database.windows.net Fred@b1gl33p Secret

    Le script crée la base de données, installe le schéma et remplit la base de données avec des exemples de données.

1.  Revenez à la page du portail **WindowsAzurePlatform**, cliquez sur votre abonnement à gauche (**Paiement à l'utilisation** dans l'exemple ci-dessous), puis sélectionnez votre base de données (nommée ici wq6xlbyoq0). AventureWorks2012 doit s'afficher dans la colonne **Nom de la base de données**. Sélectionnez-la, puis cliquez sur **Gérer** en haut de la page.

2.  Lorsque vous y êtes invité, entrez les informations d'identification de la base de données SQL et appuyez sur **Se connecter**.

3.  Vous ouvrez ainsi l'interface Web de la base de données Adventure Works pour la base de données SQL. Appuyez sur l'icône **Nouvelle requête** située en haut pour ouvrir l'éditeur de requête.

4.  Sqoop ajoute actuellement des crochets au nom de table. Nous devons donc ajouter un synonyme pour prendre en charge l'attribution de noms à deux parties pour les tables SQL Server. Pour ce faire, exécutez la requête suivante :

   `CREATE SYNONYM [Sales.SalesOrderDetail] FOR Sales.SalesOrderDetail`

1.  Exécutez la requête suivante et examinez son résultat.

   `select top 200 * from [Sales.SalesOrderDetail] `

Utilisation de Sqoop depuis HDInsight pour importer des données dans un cluster
-------------------------------------------------------------------------------

1.  Sur votre page Compte, faites défiler jusqu'à l'icône Ports ouverts dans la section relative à votre cluster, puis cliquez sur l'icône pour ouvrir le port du serveur ODBC sur le nœud principal de votre cluster.

2.  Revenez à votre page Compte, faites défiler jusqu'à la section relative à votre cluster, puis cliquez cette fois sur l'icône **Bureau à distance** afin d'ouvrir le nœud principal de votre cluster. TBD: mettre à jour pour utiliser avec PS.

3.  Sélectionnez **Ouvrir** lorsque vous êtes invité à ouvrir le fichier .rdp.

4.  Sélectionnez Connecter dans la fenêtre Connexion Bureau à distance.

5.  Entrez vos informations d'identification pour le cluster Hadoop (pas votre cluster Hadoop sur le compte Azure) dans la fenêtre **Sécurité Windows**, puis sélectionnez **OK**.

6.  Ouvrez Internet Explorer et accédez au site WhatIsMyIP afin d'obtenir l'adresse IP vers l'extérieur du nœud principal du cluster. Revenez à la page de gestion de base de données SQL, puis ajoutez une règle de pare-feu qui permet à votre cluster Hadoop d'accéder à la base de données SQL. Le pare-feu octroie l'accès en fonction de l'adresse IP d'origine de chaque demande.

7.  Double-cliquez sur l'icône d'interface de commande Hadoop dans la partie supérieure gauche du Bureau afin de l'ouvrir. Accédez au répertoire « c:\\Apps\\dist\\sqoop\\bin », puis exécutez la commande suivante :

    `sqoop import --connect "jdbc:sqlserver://[serverName].database.windows.net;username=[userName]@[serverName];password=[password];database=AdventureWorks2012" --table Sales.SalesOrderDetail --target-dir /data/lineitemData -m 1`

    Ainsi, par exemple, avec les valeurs suivantes :
    * server name: wq6xlbyoq0 
    * username: HadoopOnAzureSqoopAdmin
    * password: Pa\$\$w0rd

    La commande sqoop est la suivante :

    `sqoop import --connect "jdbc:sqlserver://wq6xlbyoq0.database.windows.net;username=HadoopOnAzureSqoopAdmin@wq6xlbyoq0;password=Pa$$w0rd;;database=AdventureWorks2012" --table Sales.SalesOrderDetail --target-dir /data/lineitemData -m 1`

8.  Revenez à la page **Comptes** de Hadoop sur le portail Azure, puis ouvrez cette fois la console interactive. Exécutez la commande \#lsr à partir de la console JavaScript pour énumérer les fichiers et les répertoires de votre cluster HDFS. TBD Mettre à jour pour GA.

9.  Exécutez la commande \#tail pour afficher les résultats sélectionnés à partir du fichier part-m-0000. `tail /user/RAdmin/data/SalesOrderDetail/part-m-00000`

Résumé
------

Dans ce didacticiel, vous avez appris à transférer des données entre un cluster Hadoop géré par Azure HDInsight et une base de données relationnelle à l'aide d'Apache Sqoop.

Étapes suivantes
----------------

Pour suivre des didacticiels exécutant d'autres exemples et fournissant des instructions sur l'utilisation des tâches Pig, Hive et MapReduce sur Azure HDInsight avec Azure PowerShell, consultez les rubriques suivantes :

-   [Exemple : Estimateur Pi](/en-us/manage/services/hdinsight/howto-run-samples/sample-pi-estimator/)
-   [Exemple : WordCount](/en-us/manage/services/hdinsight/howto-run-samples/sample-wordcount/)
-   [Exemple : Diffusion en continu en C\#](/en-us/manage/services/hdinsight/howto-run-samples/sample-csharp-streaming/)
-   [Exemple : GraySort 10 Go](/en-us/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/)
-   [Didacticiel : utilisation de Pig](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Didacticiel : utilisation de Hive](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Didacticiel : utilisation de MapReduce](/en-us/manage/services/hdinsight/using-mapreduce-with-hdinsight/)
-   [Documentation du Kit de développement logiciel (SDK) Azure HDInsight](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)

