<properties
   pageTitle="Analyser les journaux des sites web à l'aide d'Azure Data Lake Analytics | Azure"
   description="Apprendre à analyser les journaux des sites web à l'aide de Data Lake Analytics."
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/26/2016"
   ms.author="edmaca"/>

# Didacticiel : Analyser les journaux des sites web à l'aide d'Azure Data Lake Analytics

Découvrez comment analyser les journaux des sites web à l'aide de Data Lake Analytics, en particulier pour découvrir quels points d'accès ont rencontré des erreurs lorsqu'ils ont essayé de visiter le site web.

>[AZURE.NOTE] Si vous souhaitez simplement voir fonctionner l'application, vous gagnerez du temps en consultant [Utiliser les didacticiels interactifs Azure Data Lake Analytics](data-lake-analytics-use-interactive-tutorials.md). Ce didacticiel est basé sur le même scénario et le même code. Ce didacticiel vise à permettre aux développeurs de créer et d'exécuter une application de Data Lake Analytics de bout en bout.

## Configuration requise :

- **Visual Studio 2015, Visual Studio 2013 mise à jour 4 ou Visual Studio 2012 avec Visual C++ installé**.
- **Kit de développement logiciel (SDK) Microsoft Azure pour .NET version 2.5 ou ultérieure**. Installez-le avec [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx).
- **[Data Lake Tools pour Visual Studio](http://aka.ms/adltoolsvs)**.

	Une fois les Data Lake Tools pour Visual Studio installés, vous verrez un menu **Data Lake** dans Visual Studio :

	![Menu U-SQL Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)

- **Connaissances de base de Data Lake Analytics et des Data Lake Tools pour Visual Studio**. Pour commencer, consultez :

	- [Prise en main d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-get-started-portal.md).
	- [Développer des scripts U-SQL à l'aide des Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

- **Un compte Data Lake Analytics.** Consultez [Créer un compte Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md#create_adl_analytics_account).

	Data Lake Tools ne prend pas en charge la création de comptes Data Lake Analytics. Par conséquent, vous devez le créer avec le portail Azure, Azure PowerShell, le Kit de développement logiciel (SDK) .NET ou Azure CLI.
- **Télécharger les exemples de données sur le compte Data Lake Analytics.** Consultez [Télécharger SearchLog.tsv sur le compte Data Lake Storage par défaut](data-lake-analytics-get-started-portal.md#update-data-to-the-default-adl-storage-account).

    Pour lancer une tâche Data Lake Analytics, vous aurez besoin de données. Bien que Data Lake Tools prenne en charge le téléchargement de données, vous allez utiliser le portail pour télécharger les exemples de données, ce qui facilitera la progression dans ce didacticiel.

## Connexion à Azure

Avant de pouvoir générer et tester des scripts U-SQL, vous devez d'abord vous connecter à Azure.

**Se connecter à Data Lake Analytics**

1. Ouvrez Visual Studio.
2. Dans le menu **Data Lake**, cliquez sur **Options et paramètres**.
4. Cliquez sur **Connexion** ou sur **Changer d'utilisateur** si un autre utilisateur est connecté et suivez les instructions.
5. Cliquez sur **OK** pour fermer la boîte de dialogue Options et paramètres.

**Pour parcourir vos comptes Data Lake Analytics**

1. Dans Visual Studio, ouvrez l'**Explorateur de serveurs** en appuyant sur **CTRL+ALT+S**.
2. Dans l'**Explorateur de serveurs**, développez **Azure**, puis **Data Lake Analytics**. Le cas échéant, la liste de vos comptes Data Lake Analytics s'affiche. Vous ne pouvez pas créer de comptes Data Lake Analytics à partir du studio. Pour créer un compte, consultez [Prise en main d'Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-get-started-portal.md) ou [Prise en main d'Azure Data Lake Analytics avec Azure PowerShell](data-lake-analytics-get-started-powershell.md).

## Développement d'application U-SQL

Une application U-SQL est essentiellement un script U-SQL. Pour en savoir plus sur U-SQL, consultez [Prise en main de U-SQL](data-lake-analytics-u-sql-get-started.md).

Vous pouvez ajouter des opérateurs définis par l'utilisateur à l'application. Pour plus d'informations, consultez [Développer des opérateurs définis par l'utilisateur U-SQL pour des travaux Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md).

**Pour créer et soumettre une tâche Data Lake Analytics**

1. Dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.
2. Sélectionnez le type de projet U-SQL.

	![nouveau projet U-SQL Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Cliquez sur **OK**. Visual Studio crée une solution avec un fichier Script.usql.
4. Insérez le script suivant dans le fichier Script.usql :

        // Create a database for easy reuse, so you don't need to read from a file every time.
        CREATE DATABASE IF NOT EXISTS SampleDBTutorials;

        // Create a Table valued function. TVF ensures that your jobs fetch data from the weblog file with the correct schema.
        DROP FUNCTION IF EXISTS SampleDBTutorials.dbo.WeblogsView;
        CREATE FUNCTION SampleDBTutorials.dbo.WeblogsView()
        RETURNS @result TABLE
        (
            s_date DateTime,
            s_time string,
            s_sitename string,
            cs_method string,
            cs_uristem string,
            cs_uriquery string,
            s_port int,
            cs_username string,
            c_ip string,
            cs_useragent string,
            cs_cookie string,
            cs_referer string,
            cs_host string,
            sc_status int,
            sc_substatus int,
            sc_win32status int,
            sc_bytes int,
            cs_bytes int,
            s_timetaken int
        )
        AS
        BEGIN

            @result = EXTRACT
                s_date DateTime,
                s_time string,
                s_sitename string,
                cs_method string,
                cs_uristem string,
                cs_uriquery string,
                s_port int,
                cs_username string,
                c_ip string,
                cs_useragent string,
                cs_cookie string,
                cs_referer string,
                cs_host string,
                sc_status int,
                sc_substatus int,
                sc_win32status int,
                sc_bytes int,
                cs_bytes int,
                s_timetaken int
            FROM @"/Samples/Data/WebLog.log"
            USING Extractors.Text(delimiter:' ');
		    RETURN;
		END;

        // Create a table for storing referrers and status
        DROP TABLE IF EXISTS SampleDBTutorials.dbo.ReferrersPerDay;
        @weblog = SampleDBTutorials.dbo.WeblogsView();
        CREATE TABLE SampleDBTutorials.dbo.ReferrersPerDay
        (
            INDEX idx1
            CLUSTERED(Year ASC)
            PARTITIONED BY HASH(Year)
        ) AS

        SELECT s_date.Year AS Year,
            s_date.Month AS Month,
            s_date.Day AS Day,
            cs_referer,
            sc_status,
            COUNT(DISTINCT c_ip) AS cnt
        FROM @weblog
        GROUP BY s_date,
                cs_referer,
                sc_status;

    Pour comprendre U-SQL, voir [Prise en main du langage U-SQL Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).

5. Ajoutez un nouveau script U-SQL dans votre projet et entrez ce qui suit :

        // Query the referrers that ran into errors
        @content =
            SELECT *
            FROM SampleDBTutorials.dbo.ReferrersPerDay
            WHERE sc_status >=400 AND sc_status < 500;

        OUTPUT @content
        TO @"/Samples/Outputs/UnsuccessfulResponses.log"
        USING Outputters.Tsv();

6. Revenez au premier script U-SQL, puis, à côté du bouton **Soumettre**, spécifiez votre compte Analytics.
7. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **Script.usql**, puis cliquez sur **Générer le script**. Vérifiez les résultats dans le volet Sortie.
8. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **Script.usql**, puis cliquez sur **Soumettre le script**.
9. Vérifiez que le **compte Analytics** est celui où vous souhaitez exécuter la tâche, puis cliquez sur **Soumettre**. Les résultats de la soumission et le lien vers la tâche sont disponibles dans la fenêtre Résultats de Data Lake Tools pour Visual Studio à l'issue de la soumission.
10. Attendez que la tâche soit terminée avec succès. Si la tâche a échoué, le fichier source est probablement manquant. Consultez la section Configuration requise de ce didacticiel. Pour plus d'informations de dépannage, consultez [Analyser et résoudre les problèmes des tâches Azure Data Lake Analytics](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).

    Une fois la tâche terminée, l'écran suivant s'affiche :

    ![data lake analytics analyser les journaux des sites web](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)

11. Répétez les étapes 7 à 10 pour **Script1.usql**.

>[AZURE.NOTE]Vous ne pouvez pas lire ou écrire dans une table U-SQL qui a été créée ou modifiée dans le même script. C'est pourquoi nous utilisons deux scripts pour cet exemple.

**Voir le résultat de la tâche**

1. Dans l'**Explorateur de serveurs**, développez **Azure**, **Analytique Data Lake**, puis votre compte Analytique Data Lake et **Comptes de stockage**. Cliquez avec le bouton droit sur le compte de stockage Data Lake par défaut, puis sur **Explorateur**.
2.  Double-cliquez sur **Exemples** pour ouvrir le dossier, puis double-cliquez sur **Sorties**.
3.  Double-cliquez sur **UnsuccessfulResponsees.log**.
4.  Vous pouvez également double-cliquer sur le fichier de sortie dans l'affichage graphique de la tâche afin d'accéder directement à la sortie.

## Voir aussi

Pour commencer à utiliser Data Lake Analytics à l'aide de différents outils, consultez :

- [Prendre en main Data Lake Analytics à l'aide du portail Azure](data-lake-analytics-get-started-portal.md)
- [Prise en main de Data Lake Analytics à l'aide d'Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Prise en main de Data Lake Analytics à l'aide du Kit de développement logiciel (SDK) .NET](data-lake-analytics-get-started-net-sdk.md)

Pour afficher les autres rubriques sur le développement :

- [Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Prise en main du langage U-SQL Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md)
- [Développer des opérateurs définis par l'utilisateur U-SQL pour des travaux Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)

<!---HONumber=AcomDC_0504_2016-->