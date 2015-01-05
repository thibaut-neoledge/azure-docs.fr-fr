<properties urlDisplayName="Connect Excel to HDInsight" pageTitle="Connexion d'Excel à Hadoop à l'aide du pilote ODBC Hive | Azure" metaKeywords="" description="Learn how to set up and use the Microsoft Hive ODBC driver for Excel to query data in an HDInsight cluster." metaCanonical="" services="hdinsight" documentationCenter="" title="Connect Excel to Hadoop with the Microsoft Hive ODBC Driver" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />



#Connexion d'Excel à Hadoop à l'aide du pilote ODBC Microsoft Hive


Une fonctionnalité clé de la solution de données volumineuses de Microsoft est l'intégration d'outils décisionnels (BI) Microsoft à des clusters Apache Hadoop qui ont été déployés par Azure HDInsight. Un exemple de cette intégration est la possibilité de connecter Excel à l'entrepôt de données Hive d'un cluster Hadoop dans HDInsight au moyen du pilote Microsoft Hive Open Database Connectivity (ODBC). 

Il est également possible de connecter les données associées à un cluster HDInsight et d'autres sources de données, y compris d'autres clusters Hadoop (non HDInsight), à partir d'Excel au moyen du complément Microsoft Power Query pour Excel. Pour plus d'informations sur l'installation et l'utilisation de Power Query, consultez la page [Connexion d'Excel à HDInsight à l'aide de Power Query][hdinsight-power-query].

**Configuration requise** :

Avant de commencer cet article, vous devez disposer des éléments suivants :

- Un cluster HDInsight. Pour en configurer un, consultez la page [Prise en main d'Azure HDInsight][hdinsight-get-started].
- Un ordinateur exécutant Windows 8, Windows 7, Windows Server 2012 ou Windows Server 2008 R2.
- Office Professionnel Plus 2013, Office 365 ProPlus, l'édition autonome d'Excel 2013 ou Office Professionnel Plus 2010.

##Dans cet article

1. [Installation du pilote ODBC Microsoft Hive](#InstallHiveODBCDriver)
2. [Création d'une source de données ODBC Hive](#CreateHiveODBCDataSource)
3. [Importation de données dans Excel à partir d'un cluster HDInsight](#ImportData)
4. [Étapes suivantes](#nextsteps)

##<a id="InstallHiveODBCDriver"></a>Installation du pilote ODBC Microsoft Hive

Téléchargez et installez le pilote ODBC Microsoft Hive à partir du [Centre de téléchargement][hive-odbc-driver-download]. 

Ce pilote peut être installé sur les versions 32 bits ou 64 bits de Windows 7, Windows 8, Windows Server 2008 R2 et Windows Server 2012, et permet des connexions à Azure HDInsight (version 1.6 et versions ultérieures) et Azure HDInsight Emulator (v.1.0.0.0 et versions ultérieures). Veillez à installer la version qui correspond à celle de l'application où vous utiliserez le pilote ODBC. Pour les besoins de ce didacticiel, le pilote utilisé provient d'Office Excel. 

##<a id="CreateHiveODBCDataSource"></a>Création d'une source de données ODBC Hive

La procédure suivante explique comment créer une source de données ODBC Hive.

1. Sur Windows 8, appuyez sur la touche Windows pour ouvrir l'écran d'accueil, puis tapez **sources de données**.
2. Cliquez sur **Configurer les sources de données ODBC (32 bits)** ou **Configurer les sources de données ODBC (64 bits)** selon votre version d'Office. Si vous utilisez Windows 7, choisissez **Sources de données ODBC (32 bits)** ou **Sources de données ODBC (64 bits)** dans **Outils d'administration**. Cette action lance la boîte de dialogue **Administrateur de sources de données ODBC**. 
 
	![OBDC data source administrator][img-hdi-simbahiveodbc-datasource-admin]

3. Dans DNS utilisateur, cliquez sur **Ajouter** pour ouvrir l'Assistant **Créer une nouvelle source de données**. 
4. Sélectionnez **Pilote ODBC Microsoft Hive**, puis cliquez sur **Terminer**. Cette action lance la boîte de dialogue **Configuration DNS du pilote ODBC Microsoft Hive**. 

5. Tapez ou sélectionnez les valeurs suivantes :

	<table border="1">
	<tr><td><strong>Propriété</strong></td><td><strong>Description</strong></td></tr>
	<tr><td>Data Source Name</td><td>Donnez un nom à votre source de données</td></tr>
	<tr><td>Host</td><td>Entrez <HDInsightClusterName>.azurehdinsight.net. Par exemple, myHDICluster.azurehdinsight.net</td></tr>
	<tr><td>Port</td><td>Utilisez <strong>443</strong>(ce port est passé de 563 à 443).</td></tr>
	<tr><td>Database</td><td>Utilisez <strong>Default</strong>.</td></tr>
	<tr><td>Hive Server Type</td><td>Sélectionnez <strong>Hive Server 2</strong></td></tr>
	<tr><td>Mechanism</td><td>Sélectionnez <strong>Service Azure HDInsight</strong></td></tr>
	<tr><td>HTTP Path</td><td>Laissez cette valeur vide.</td></tr>
	<tr><td>User Name</td><td>Entrez le nom d'utilisateur du cluster HDInsight. Il s'agit du nom d'utilisateur créé au cours du processus d'approvisionnement de cluster. Si vous avez utilisé l'option de création rapide, le nom d'utilisateur par défaut est <strong>admin</strong>.</td></tr>
	<tr><td>Password</td><td>Entrez le mot de passe du cluster HDInsight.</td></tr>
	</table>

	Certains paramètres importants sont à prendre en compte lorsque vous cliquez sur **Options avancées** :

	<table border="1">
	<tr><td>Use Native Query</td><td>Si cette option est sélectionnée, le pilote ODBC n'essaiera PAS de convertir TSQL en HiveQL. À utiliser uniquement si vous êtes sûr à 100 % que vous envoyez des instructions HiveQL pures. Si vous effectuez une connexion à SQL Server ou Base de données SQL Azure, ne sélectionnez pas cette option.</td></tr>
	<tr><td>Rows fetched per block</td><td>Lors de la récupération d'une grande quantité d'enregistrements, la définition de ce paramètre peut être nécessaire pour garantir des performances optimales.</td></tr>
	<tr><td>Default string column length, <br/>
			Binary column length,  <br/>
			Decimal column scale</td><td>Les précisions et longueurs des types de données peuvent affecter la façon dont les données sont renvoyées. Elles peuvent entraîner le renvoi d'informations incorrectes en raison d'une perte de précision et/ou de troncations.</td></tr>
	</table>

	![Advanced options][img-HiveOdbc-DataSource-AdvancedOptions]

6. Cliquez sur **Tester** pour tester la source de données. Une fois que la source de données est configurée correctement, le message suivant apparaît *TESTS EFFECTUÉS AVEC SUCCÈS*.
7. Cliquez sur **OK** pour fermer la boîte de dialogue de test. La nouvelle source de données doit à présent figurer dans la boîte de dialogue **Administrateur de sources de données ODBC**. 
8. Cliquez sur **OK**  pour quitter l'Assistant.
	
##<a id="ImportData"></a>Importation de données dans Excel à partir d'un cluster HDInsight

La procédure ci-dessous décrit la façon d'importer les données d'une table hive dans un classeur Excel au moyen de la source de données ODBC que vous avez créée dans la procédure ci-dessus.

1. Ouvrez un nouveau classeur ou un classeur existant dans Excel.
2. Dans l'onglet **Données**, cliquez sur la vignette **Données externes**, cliquez sur **À partir d'autres sources de données**, puis cliquez sur **Provenance : Assistant Connexion de données** pour lancer l'**Assistant Connexion de données**.

	![Open data connection wizard][img-hdi-simbahiveodbc.excel.dataconnection]

3. Sélectionnez **DSN ODBC** comme source de données, puis cliquez sur **Suivant**.
4. Dans Sources de données ODBC, sélectionnez le nom de la source de données que vous avez créée à l'étape précédente, puis cliquez sur **Suivant**.
5. Entrez à nouveau le mot de passe pour le cluster dans l'Assistant, puis cliquez sur **Tester** pour vérifier la configuration.
6. Cliquez sur **OK** pour fermer la boîte de dialogue de test.
7. Cliquez sur **OK**. Attendez l'ouverture de la boîte de dialogue **Sélection d'une base de données et d'une table**. Cette opération peut prendre quelques secondes.
8. Sélectionnez la table que vous voulez importer, puis cliquez sur **Suivant**. *hivesampletable* est un exemple de table hive fournie avec les clusters HDInsight.  Vous pouvez la choisir si vous n'en avez pas créé une. Pour plus d'informations sur l'exécution de requêtes Hive et la création de tables Hive, consultez la rubrique [Utilisation de Hive avec HDInsight][hdinsight-use-hive].
8. Cliquez sur **Terminer**.
9. Dans la boîte de dialogue **Importation de données**, vous pouvez modifier ou spécifier la requête. Pour cela, cliquez sur **Propriétés**. Cette opération peut prendre quelques secondes.
10. Cliquez sur l'onglet **Définition**, puis ajoutez **LIMIT 200** à l'instruction Select Hive dans la zone de texte **Texte de la commande**. La modification va limiter le jeu d'enregistrements renvoyé à 200.

	![Connection Properties][img-hdi-simbahiveodbc-excel-connectionproperties]

11. Cliquez sur **OK** pour fermer la boîte de dialogue Propriétés de connexion.
12. Cliquez sur **OK** pour fermer la boîte de dialogue **Importation de données**.  
13. Entrez à nouveau le mot de passe, puis cliquez sur **OK**. Patientez quelques secondes pour que les données soient importées dans Excel.

##<a id="nextsteps"></a>Étapes suivantes

Dans cet article, vous avez appris à utiliser le pilote Microsoft Hive ODBC pour extraire les données du service HDInsight dans Excel. De la même façon, vous pouvez extraire les données du service HDInsight dans la base de données SQL. Il est également possible de télécharger des données dans un service HDInsight. Pour plus d'informations, consultez les rubriques suivantes :

- [Analyse des données sur les retards de vol avec HDInsight][hdinsight-analyze-flight-data]
- [Téléchargement de données vers HDInsight][hdinsight-upload-data]
- [Utilisation de Sqoop avec HDInsight][hdinsight-use-sqoop]


[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/
[hdinsight-get-started]: ../hdinsight-get-started/

[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698

[img-hdi-simbahiveodbc-datasource-admin]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png 
[img-HiveOdbc-DataSource-AdvancedOptions]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png 
[img-hdi-simbahiveodbc-excel-connectionproperties]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png 
[img-hdi-simbahiveodbc.excel.dataconnection]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png 

<!--HONumber=35.1-->
