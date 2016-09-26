<properties
   pageTitle="Connexion d’Excel à Hadoop à l’aide du pilote ODBC Hive | Microsoft Azure"
   description="Découvrez comment configurer et utiliser le pilote ODBC Microsoft Hive pour Excel afin d'interroger des données dans un cluster HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="jhubbard"
   tags="azure-portal"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/25/2016"
   ms.author="jgao"/>

#Connexion d'Excel à Hadoop à l'aide du pilote ODBC Microsoft Hive

[AZURE.INCLUDE [sélecteur-JDBC-ODBC](../../includes/hdinsight-selector-odbc-jdbc.md)]

La solution de données volumineuses de Microsoft intègre des composants BI (Business Intelligence) à des clusters Apache Hadoop qui ont été déployés par Azure HDInsight. Un exemple de cette intégration est la possibilité de connecter Excel à l'entrepôt de données Hive d'un cluster Hadoop dans HDInsight au moyen du pilote Microsoft Hive Open Database Connectivity (ODBC).

Il est également possible de connecter les données associées à un cluster HDInsight et d'autres sources de données, y compris d'autres clusters Hadoop (non HDInsight), à partir d'Excel au moyen du complément Microsoft Power Query pour Excel. Pour plus d'informations sur l'installation et l'utilisation de Power Query, consultez la page [Connexion d'Excel à HDInsight à l'aide de Power Query][hdinsight-power-query].

> [AZURE.NOTE] Même si les étapes décrites dans cet article peuvent être utilisées avec un cluster HDInsight basé sur Linux ou Windows, Windows est requis pour le poste de travail client.

**Conditions préalables** :

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Un cluster HDInsight**. Pour en créer un, consultez la page [Prise en main d’Azure HDInsight][hdinsight-get-started].
- **Une station de travail** avec Office Professionnel Plus 2013, Office 365 ProPlus, l'édition autonome d'Excel 2013 ou Office Professionnel Plus 2010.


##Installation du pilote ODBC Microsoft Hive

Téléchargez et installez le pilote ODBC Microsoft Hive à partir du [Centre de téléchargement][hive-odbc-driver-download].

Ce pilote peut être installé sur les versions 32 bits ou 64 bits de Windows 7, Windows 8, Windows 10, Windows Server 2008 R2 et Windows Server 2012, et permet des connexions à Azure HDInsight (versions 1.6 et ultérieures) et Azure HDInsight Emulator (versions 1.0.0.0 et ultérieures). Veillez à installer la version qui correspond à celle de l'application où vous utiliserez le pilote ODBC. Pour les besoins de ce didacticiel, le pilote utilisé provient d'Office Excel.

##Création d’une source de données ODBC Hive

La procédure suivante explique comment créer une source de données ODBC Hive.

1. Sur Windows 8 ou Windows 10, appuyez sur la touche Windows pour ouvrir l’écran d’accueil, puis tapez **sources de données**.
2. Cliquez sur **Configurer les sources de données ODBC (32 bits)** ou **Configurer les sources de données ODBC (64 bits)** selon votre version d'Office. Si vous utilisez Windows 7, choisissez **Sources de données ODBC (32 bits)** ou **Sources de données ODBC (64 bits)** dans **Outils d'administration**. Cette action lance la boîte de dialogue **Administrateur de sources de données ODBC**.

	![Administrateur de sources de données ODBC][img-hdi-simbahiveodbc-datasource-admin]

3. Dans User DNS, cliquez sur **Ajouter** pour ouvrir l'Assistant **Créer une nouvelle source de données**.
4. Sélectionnez **Microsoft Hive ODBC Driver**, puis cliquez sur **Terminer**. Cette action lance la boîte de dialogue **Microsoft Hive ODBC Driver DNS Setup**.

5. Tapez ou sélectionnez les valeurs suivantes :

    Propriété|Description
    ---|---
    Data Source Name|Donnez un nom à votre source de données
    Host|Entrez &lt;HDInsightClusterName>.azurehdinsight.net. Par exemple, myHDICluster.azurehdinsight.net
    Port|Utilisez <strong>443</strong>. (ce port est passé de 563 à 443).
    Base de données|Utilisez <strong>Default</strong>.
    Hive Server Type|Sélectionnez <strong>Hive Server 2</strong>.
    Mechanism|Sélectionnez <strong>Azure HDInsight Service</strong>.
    HTTP Path|Laissez cette valeur vide.
    User Name|Entrez le nom d'utilisateur du cluster HDInsight. Il s'agit du nom d'utilisateur créé au cours du processus d'approvisionnement de cluster. Si vous avez utilisé l’option de création rapide, le nom d’utilisateur par défaut est <strong>admin</strong>.
    Mot de passe|Entrez le mot de passe du cluster HDInsight.
    </table>

    Certains paramètres importants sont à prendre en compte lorsque vous cliquez sur **Options avancées** :

    Paramètre|Description
    ---|---
    Use Native Query|Si cette option est sélectionnée, le pilote ODBC n'essaiera PAS de convertir TSQL en HiveQL. À utiliser uniquement si vous êtes sûr à 100 % que vous envoyez des instructions HiveQL pures. Si vous effectuez une connexion à SQL Server ou Base de données SQL Azure, ne sélectionnez pas cette option.
    Rows fetched per block|Lors de la récupération d'une grande quantité d'enregistrements, la définition de ce paramètre peut être nécessaire pour garantir des performances optimales.
    Default string column length, Binary column length, Decimal column scale|Les précisions et longueurs des types de données peuvent affecter la façon dont les données sont renvoyées. Elles peuvent entraîner le renvoi d'informations incorrectes en raison d'une perte de précision et/ou de troncations.


	![Options avancées][img-HiveOdbc-DataSource-AdvancedOptions]

6. Cliquez sur **Tester** pour tester la source de données. Une fois que la source de données est configurée correctement, le message suivant apparaît *TESTS COMPLETED SUCCESSFULLY!*.
7. Cliquez sur **OK** pour fermer la boîte de dialogue de test. La nouvelle source de données doit à présent figurer dans la boîte de dialogue **Administrateur de sources de données ODBC**.
8. Cliquez sur **OK** pour quitter l'Assistant.

##Importation de données dans Microsoft Excel à partir de HDInsight

La procédure ci-dessous décrit la façon d'importer les données d'une table hive dans un classeur Excel au moyen de la source de données ODBC que vous avez créée dans la procédure ci-dessus.

1. Ouvrez un nouveau classeur ou un classeur existant dans Excel.
2. À partir de l’onglet **Données**, cliquez sur **À partir d’autres sources de données**, puis sur **Provenance : Assistant Connexion de données** pour lancer l’**Assistant Connexion de données**.

	![Assistant Ouvrir la connexion de données][img-hdi-simbahiveodbc.excel.dataconnection]

3. Sélectionnez **DSN ODBC** comme source de données, puis cliquez sur **Suivant**.
4. Dans Sources de données ODBC, sélectionnez le nom de la source de données que vous avez créée à l'étape précédente, puis cliquez sur **Suivant**.
5. Entrez à nouveau le mot de passe du cluster dans l’Assistant, puis cliquez sur **Tester** pour vérifier une nouvelle fois la configuration, le cas échéant.
6. Cliquez sur **OK** pour fermer la boîte de dialogue de test.
7. Cliquez sur **OK**. Attendez l'ouverture de la boîte de dialogue **Sélection d'une base de données et d'une table**. Cette opération peut prendre quelques secondes.
8. Sélectionnez la table que vous voulez importer, puis cliquez sur **Suivant**. *hivesampletable* est un exemple de table hive fournie avec les clusters HDInsight. Vous pouvez la choisir si vous n'en avez pas créé une. Pour plus d'informations sur l'exécution de requêtes Hive et la création de tables Hive, consultez la rubrique [Utilisation de Hive avec HDInsight][hdinsight-use-hive].
8. Cliquez sur **Terminer**.
9. Dans la boîte de dialogue **Importation de données**, vous pouvez modifier ou spécifier la requête. Pour cela, cliquez sur **Propriétés**. Cette opération peut prendre quelques secondes.
10. Cliquez sur l'onglet **Définition**, puis ajoutez **LIMIT 200** à l'instruction Select Hive dans la zone de texte **Texte de la commande**. La modification va limiter le jeu d'enregistrements renvoyé à 200.

	![Propriétés de connexion][img-hdi-simbahiveodbc-excel-connectionproperties]

11. Cliquez sur **OK** pour fermer la boîte de dialogue Propriétés de connexion.
12. Cliquez sur **OK** pour fermer la boîte de dialogue **Importation de données**.
13. Entrez à nouveau le mot de passe, puis cliquez sur **OK**. Patientez quelques secondes pour que les données soient importées dans Excel.

##Étapes suivantes

Dans cet article, vous avez appris à utiliser le pilote Microsoft Hive ODBC pour extraire les données du service HDInsight dans Excel. De la même façon, vous pouvez extraire les données du service HDInsight dans la base de données SQL. Il est également possible de télécharger des données dans un service HDInsight. Pour plus d'informations, consultez les rubriques suivantes :

- [Analyse des données sur les retards de vol avec HDInsight][hdinsight-analyze-flight-data]
- [Téléchargement de données vers HDInsight][hdinsight-upload-data]
- [Utilisation de Sqoop avec HDInsight][hdinsight-use-sqoop]


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md

[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698

[img-hdi-simbahiveodbc-datasource-admin]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png
[img-HiveOdbc-DataSource-AdvancedOptions]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png
[img-hdi-simbahiveodbc-excel-connectionproperties]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png
[img-hdi-simbahiveodbc.excel.dataconnection]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png

<!---HONumber=AcomDC_0914_2016-->