<properties linkid="manage-services-hdinsight-connect-excel-with-hive-ODBC" urlDisplayName="Connect Excel to HDInsight" pageTitle="Connect Excel to HDInsight with the Hive ODBC Driver | Azure" metaKeywords="" description="Learn how to set up and use the Microsoft Hive ODBC driver for Excel to query data in an HDInsight cluster." metaCanonical="" services="hdinsight" documentationCenter="" title="Connect Excel to HDInsight with the Microsoft Hive ODBC Driver" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

Connexion d'Excel à HDInsight à l'aide du pilote ODBC Microsoft Hive
====================================================================

Une fonctionnalité clé de la solution de données volumineuses de Microsoft est l'intégration d'outils décisionnels (BI) Microsoft à des clusters Apache Hadoop qui ont été déployés par Azure HDInsight. Un exemple de cette intégration est la possibilité de connecter Excel à l'entrepôt de données Hive d'un cluster HDInsight Hadoop au moyen du pilote Microsoft Hive Open Database Connectivity (ODBC).

Il est également possible de connecter les données associées à un cluster HDInsight et d'autres sources de données, y compris d'autres clusters Hadoop (non HDInsight), à partir d'Excel au moyen du complément Microsoft Power Query pour Excel. Pour plus d'informations sur l'installation et l'utilisation de Power Query, consultez la page [Connexion d'Excel à HDInsight à l'aide de Power Query](/fr-fr/documentation/articles/hdinsight-connect-excel-power-query/).

**Conditions préalables** :

Avant de commencer la lecture de cet article, vous devez disposer des éléments suivants :

-   Un cluster HDInsight. Pour en configurer un, consultez la page [Prise en main d'Azure HDInsight](/fr-fr/documentation/articles/hdinsight-get-started/).
-   Un ordinateur exécutant Windows 8, Windows 7, Windows Server 2012 ou Windows Server 2008 R2.
-   Office Professionnel Plus 2013, Office 365 ProPlus, l'édition autonome d'Excel 2013 ou Office Professionnel Plus 2010.

Dans cet article
----------------

1.  [Installation du pilote ODBC Microsoft Hive](#InstallHiveODBCDriver)
2.  [Création d'une source de données ODBC Hive](#CreateHiveODBCDataSource)
3.  [Importation de données dans Excel à partir d'un cluster HDInsight](#ImportData)
4.  [Étapes suivantes](#nextsteps)

Installation du pilote ODBC Microsoft Hive
------------------------------------------

Téléchargez et installez le pilote ODBC Microsoft Hive à partir du [Centre de téléchargement](http://go.microsoft.com/fwlink/?LinkID=286698).

Ce pilote peut être installé sur les versions 32 bits ou 64 bits de Windows 7, Windows 8, Windows Server 2008 R2 et Windows Server 2012, et permet des connexions à Azure HDInsight (version 1.6 et versions ultérieures) et Azure HDInsight Emulator (v.1.0.0.0 et versions ultérieures). Veillez à installer la version qui correspond à celle de l'application où vous utiliserez le pilote ODBC. Pour les besoins de ce didacticiel, le pilote utilisé provient d'Office Excel.

Création d'une source de données ODBC Hive
------------------------------------------

La procédure suivante explique comment créer une source de données ODBC Hive.

1.  Sur Windows 8, appuyez sur la touche Windows pour ouvrir l'écran d'accueil, puis tapez **sources de données**.
2.  Cliquez sur **Configurer les sources de données ODBC (32 bits)** ou **Configurer les sources de données ODBC (64 bits)** selon votre version d'Office. Si vous utilisez Windows 7, choisissez **Sources de données ODBC (32 bits)** ou **Sources de données ODBC (64 bits)** dans **Outils d'administration**. Cette action lance la boîte de dialogue **Administrateur de sources de données ODBC**.

    ![Administrateur de sources de données ODBC](./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png)

3.  Dans User DNS, cliquez sur **Ajouter** pour ouvrir l'Assistant **Créer une nouvelle source de données**.
4.  Sélectionnez **Microsoft Hive ODBC Driver**, puis cliquez sur **Terminer**. Cette action lance la boîte de dialogue **Microsoft Hive ODBC Driver DNS Setup**.

5.  Tapez ou sélectionnez les valeurs suivantes :

	<table data-morhtml="true" border="1">
	<tr data-morhtml="true"><td data-morhtml="true"><strong data-morhtml="true">Propri&eacute;t&eacute;</strong></td><td data-morhtml="true"><strong data-morhtml="true">Description</strong></td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Data Source Name</td><td data-morhtml="true">Donnez un nom &agrave; votre source de donn&eacute;es</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Host</td><td data-morhtml="true">Entrez <hdinsightclustername data-morhtml="true">.azurehdinsight.net. Par exemple, myHDICluster.azurehdinsight.net
	<tr data-morhtml="true"><td data-morhtml="true">Port</td><td data-morhtml="true">Utilisez <strong data-morhtml="true">443</strong> (ce port est pass&eacute; de 563 &agrave; 443).</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Database</td><td data-morhtml="true">Utilisez <strong data-morhtml="true">Default</strong>.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Hive Server Type</td><td data-morhtml="true">S&eacute;lectionnez <strong data-morhtml="true">Hive Server 2</strong></td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Mechanism</td><td data-morhtml="true">S&eacute;lectionnez <strong data-morhtml="true">Azure HDInsight Service</strong></td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">HTTP Path</td><td data-morhtml="true">Laissez cette valeur vide.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">User Name</td><td data-morhtml="true">Entrez le nom d'utilisateur du cluster HDInsight. Il s'agit du nom d'utilisateur cr&eacute;&eacute; au cours du processus d'approvisionnement de cluster. Si vous avez utilis&eacute; l'option de cr&eacute;ation rapide, le nom d'utilisateur par d&eacute;faut est <strong data-morhtml="true">admin</strong>.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Password</td><td data-morhtml="true">Entrez le mot de passe du cluster HDInsight.</td></tr>
	</table> 

	Certains param&egrave;tres importants sont &agrave; prendre en compte lorsque vous cliquez sur **Options avanc&eacute;es**&nbsp;:

	<table data-morhtml="true" border="1">
	<tr data-morhtml="true"><td data-morhtml="true">Use Native Query</td><td data-morhtml="true">Si cette option est s&eacute;lectionn&eacute;e, le pilote ODBC n'essaiera PAS de convertir TSQL en HiveQL. &Agrave; utiliser uniquement si vous &ecirc;tes s&ucirc;r &agrave; 100&nbsp;% que vous envoyez des instructions HiveQL pures. Si vous effectuez une connexion &agrave; SQL&nbsp;Server ou Base de donn&eacute;es SQL Azure, ne s&eacute;lectionnez pas cette option.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Rows fetched per block</td><td data-morhtml="true">Lors de la r&eacute;cup&eacute;ration d'une grande quantit&eacute; d'enregistrements, la d&eacute;finition de ce param&egrave;tre peut &ecirc;tre n&eacute;cessaire pour garantir des performances optimales.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Default string column length, <br data-morhtml="true" /> Binary column length,  <br data-morhtml="true" /> Decimal column scale</td><td data-morhtml="true">Les pr&eacute;cisions et longueurs des types de donn&eacute;es peuvent affecter la fa&ccedil;on dont les donn&eacute;es sont renvoy&eacute;es. Elles peuvent entra&icirc;ner le renvoi d'informations incorrectes en raison d'une perte de pr&eacute;cision et/ou de troncations.</td></tr>
	</table>

	![Advanced options][img-HiveOdbc-DataSource-AdvancedOptions]

6.  Cliquez sur **Tester** pour tester la source de données. Une fois que la source de données est configurée correctement, le message suivant apparaît *TESTS COMPLETED SUCCESSFULLY!*.
7.  Cliquez sur **OK** pour fermer la boîte de dialogue de test. La nouvelle source de données doit à présent figurer dans la boîte de dialogue **Administrateur de sources de données ODBC**.
8.  Cliquez sur **OK** pour quitter l'Assistant.

Importation de données dans Excel à partir d'un cluster HDInsight
-----------------------------------------------------------------

La procédure ci-dessous décrit la façon d'importer les données d'une table hive dans un classeur Excel au moyen de la source de données ODBC que vous avez créée dans la procédure ci-dessus.

1.  Ouvrez un nouveau classeur ou un classeur existant dans Excel.
2.  À partir de l'onglet **Données**, cliquez sur la vignette **Données externes**, cliquez sur **À partir d'autres sources de données**, puis sur **Provenance : Assistant Connexion de données** pour lancer **Assistant Connexion de données**.

    ![Assistant Ouvrir la connexion de données](./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png)

3.  Sélectionnez **DSN ODBC** comme source de données, puis cliquez sur **Suivant**.
4.  Dans Sources de données ODBC, sélectionnez le nom de la source de données que vous avez créée à l'étape précédente, puis cliquez sur **Suivant**.
5.  Entrez à nouveau le mot de passe pour le cluster dans l'Assistant, puis cliquez sur **Tester** pour vérifier la configuration.
6.  Cliquez sur **OK** pour fermer la boîte de dialogue de test.
7.  Cliquez sur **OK**. Attendez l'ouverture de la boîte de dialogue **Sélection d'une base de données et d'une table**. Cette opération peut prendre quelques secondes.
8.  Sélectionnez la table que vous voulez importer, puis cliquez sur **Suivant**. *hivesampletable* est un exemple de table hive fournie avec les clusters HDInsight. Vous pouvez la choisir si vous n'en avez pas créé une. Pour plus d'informations sur l'exécution de requêtes Hive et la création de tables Hive, consultez la rubrique [Utilisation de Hive avec HDInsight](/fr-fr/documentation/articles/hdinsight-use-hive/).
9.  Cliquez sur **Terminer**.
10. Dans la boîte de dialogue **Importation de données**, vous pouvez modifier ou spécifier la requête. Pour cela, cliquez sur **Propriétés**. Cette opération peut prendre quelques secondes.
11. Cliquez sur l'onglet **Définition**, puis ajoutez **LIMIT 200** à l'instruction Select Hive dans la zone de texte **Texte de la commande**. La modification va limiter le jeu d'enregistrements renvoyé à 200.

    ![Propriétés de connexion](./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png)

12. Cliquez sur **OK** pour fermer la boîte de dialogue Propriétés de connexion.
13. Cliquez sur **OK** pour fermer la boîte de dialogue **Importation de données**.
14. Entrez à nouveau le mot de passe, puis cliquez sur **OK**. Patientez quelques secondes pour que les données soient importées dans Excel.

Étapes suivantes
----------------

Dans cet article, vous avez appris à utiliser le pilote Microsoft Hive ODBC pour extraire les données du service HDInsight dans Excel. De la même façon, vous pouvez extraire les données du service HDInsight dans la base de données SQL. Il est également possible de télécharger des données dans un service HDInsight. Pour plus d'informations, consultez les rubriques :

-   [Analyse des données sur les retards de vol avec HDInsight](/fr-fr/documentation/articles/hdinsight-analyze-flight-delay-data/)
-   [Téléchargement de données vers HDInsight](/fr-fr/documentation/articles/hdinsight-upload-data/)
-   [Utilisation de Sqoop avec HDInsight](../hdinsight-use-sqoop/)

[hdinsight-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-analyze-flight-delay-data]: /fr-fr/documentation/articles/hdinsight-analyze-flight-delay-data/
[hdinsight-hive]: /fr-fr/documentation/articles/hdinsight-use-hive/
[hdinsight-upload-data]: /fr-fr/documentation/articles/hdinsight-upload-data/
[hdinsight-power-query]: /fr-fr/documentation/articles/hdinsight-connect-excel-power-query/
[hdinsight-get-started]: /fr-fr/documentation/articles/hdinsight-get-started/

[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698

[img-hdi-simbahiveodbc-datasource-admin]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png 
[img-HiveOdbc-DataSource-AdvancedOptions]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png 
[img-hdi-simbahiveodbc-excel-connectionproperties]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png 
[img-hdi-simbahiveodbc.excel.dataconnection]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png 
