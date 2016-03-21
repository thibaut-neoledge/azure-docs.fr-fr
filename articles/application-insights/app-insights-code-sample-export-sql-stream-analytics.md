<properties 
	pageTitle="Procédure : exporter des données de télémétrie depuis Application Insights vers la base de données SQL" 
	description="Exportez de façon continue les données Application Insights vers SQL à l’aide de Stream Analytics." 
	services="application-insights" 
    documentationCenter=""
	authors="noamben" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/06/2015" 
	ms.author="awills"/>
 
# Procédure pas à pas : exporter vers SQL à partir d’Application Insights à l’aide de Stream Analytics

Cet article explique comment déplacer vos données de télémétrie à partir de [Visual Studio Application Insights][start] vers une base de données SQL Azure à l’aide de l’[Exportation continue][export] et d’[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/).

L’exportation continue déplace vos données de télémétrie vers le stockage Azure au format JSON. Nous analyserons les objets JSON à l’aide d’Azure Stream Analytics et créerons des lignes dans une table de base de données.

(Plus généralement, l’exportation continue est la méthode qui vous permet de réaliser votre propre analyse des données de télémétrie que vos applications transmettent à Application Insights. Vous pourriez adapter cet exemple de code pour effectuer d’autres actions à l’aide des données de télémétrie exportées, comme agréger les données.)

Nous allons partir du principe que vous disposez déjà de l’application que vous voulez analyser.


Dans cet exemple, nous allons utiliser les données d’affichage de page. Toutefois, le même modèle peut facilement être étendu à d’autres types de données, tels que des événements et des exceptions personnalisés.


## Ajouter Application Insights à votre application


Pour commencer :

1. [Configurer Application Insights pour vos pages web](app-insights-javascript.md). 

    (Dans cet exemple, nous allons nous concentrer sur le traitement des données d’affichage de page dans les navigateurs clients, mais vous pouvez également configurer Application Insights pour le côté serveur de votre application [Java](app-insights-java-get-started.md) ou [ASP.NET](app-insights-asp-net.md) et traiter la demande, les dépendances et autres données de télémétrie du serveur.)


5. Publiez votre application et surveillez les données de télémétrie apparaissant dans votre ressource Application Insights.


## Création d’un stockage dans Azure

Comme l’exportation continue génère toujours des données vers un compte de stockage Azure, vous devez commencer par créer ce stockage.

1. Créez un compte de stockage dans votre abonnement sur le [portail Azure][portal].

    ![Sur le portail Azure, choisissez Nouveau, Données, Stockage. Sélectionnez Classique, cliquez sur Créer. Fournissez un nom de stockage.](./media/app-insights-code-sample-export-sql-stream-analytics/040-store.png)

2. Créez un conteneur.

    ![Dans le nouvel emplacement de stockage, sélectionnez Conteneurs, cliquez sur la mosaïque Conteneurs puis sur Ajouter.](./media/app-insights-code-sample-export-sql-stream-analytics/050-container.png)

3. Copiez la clé d’accès au stockage.

    Vous en aurez bientôt besoin pour configurer l’entrée dans le service Stream analytics.

    ![Dans le stockage, ouvrez Paramètres, Clés et copiez la clé d’accès principale.](./media/app-insights-code-sample-export-sql-stream-analytics/21-storage-key.png)

## Démarrer l’exportation continue vers le stockage Azure

1. Sur le portail Azure, accédez à la ressource Application Insights que vous avez créée pour votre application.

    ![Sélectionnez Parcourir, Application Insights, puis votre application.](./media/app-insights-code-sample-export-sql-stream-analytics/060-browse.png)

2. Créez une exportation continue.

    ![Choisissez Paramètres, Exportation continue, Ajouter.](./media/app-insights-code-sample-export-sql-stream-analytics/070-export.png)


    Sélectionnez le compte de stockage que vous avez préalablement créé.

    ![Définissez la destination de l’exportation.](./media/app-insights-code-sample-export-sql-stream-analytics/080-add.png)
    
    Définissez les types d’événements que vous souhaitez afficher :

    ![Choisissez les types d’événements.](./media/app-insights-code-sample-export-sql-stream-analytics/085-types.png)


3. Laissez les données s'accumuler. Installez-vous confortablement et laissez les utilisateurs utiliser votre application pendant un certain temps. Les données de télémétrie vont vous être transmises et vous permettre d’afficher des graphiques statistiques dans [Metrics explorer](app-insights-metrics-explorer.md) et des événements dans [Recherche de diagnostic](app-insights-diagnostic-search.md).

    Les données seront également exportées vers votre stockage.

4. Inspectez les données exportées, soit dans le portail (choisissez **Parcourir**, sélectionnez votre compte de stockage, puis **Conteneurs**), soit dans Visual Studio. Dans Visual Studio, sélectionnez **Afficher / Cloud Explorer** et ouvrez Azure / Stockage. (Si vous n'avez pas cette option, vous devez installer le SDK Azure : Ouvrez la boîte de dialogue Nouveau projet et ouvrez Visual C# / Cloud / Obtenir Microsoft Azure SDK pour .NET.)

    ![Dans Visual Studio, ouvrez Explorateur de serveurs, Azure, Stockage](./media/app-insights-code-sample-export-sql-stream-analytics/087-explorer.png)

    Prenez note de la partie commune du nom du chemin d'accès, qui est dérivée du nom de l'application et de la clé d'instrumentation.

Les événements sont écrits dans des fichiers blob au format JSON. Chaque fichier peut contenir un ou plusieurs événements. Donc, nous devons lire les données d’événement et filtrer les champs voulus. Nous pourrions effectuer toutes sortes d’opérations avec les données, mais notre objectif aujourd’hui est d’utiliser Stream Analytics pour déplacer les données vers une base de données SQL. Cette action va simplifier l’exécution d’un grand nombre de requêtes intéressantes.

## Création d’une base de données SQL Azure

De nouveau, à partir de votre abonnement sur le [portail Azure][portal], créez la base de données (et un nouveau serveur, sauf si vous en avez déjà un) dans laquelle vous allez écrire les données.

![Nouveau, Données, SQL](./media/app-insights-code-sample-export-sql-stream-analytics/090-sql.png)


Assurez-vous que le serveur de base de données permet d’accéder aux services Azure :


![Parcourir, Serveurs, votre serveur, Paramètres, Pare-feu, Autoriser l’accès à Azure](./media/app-insights-code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## Créer une table dans la base de données SQL Azure

Connectez-vous à la base de données créée dans la section précédente à l’aide de votre outil de gestion préféré. Dans cette procédure pas à pas, nous utiliserons [Outils d’administration SQL Server](https://msdn.microsoft.com/ms174173.aspx) (SSMS).

![](./media/app-insights-code-sample-export-sql-stream-analytics/31-sql-table.png)

Créez une nouvelle requête et exécutez le T-SQL suivant :

```SQL

CREATE TABLE [dbo].[PageViewsTable](
	[pageName] [nvarchar](max) NOT NULL,
	[viewCount] [int] NOT NULL,
	[url] [nvarchar](max) NULL,
	[urlDataPort] [int] NULL,
	[urlDataprotocol] [nvarchar](50) NULL,
	[urlDataHost] [nvarchar](50) NULL,
	[urlDataBase] [nvarchar](50) NULL,
	[urlDataHashTag] [nvarchar](max) NULL,
	[eventTime] [datetime] NOT NULL,
	[isSynthetic] [nvarchar](50) NULL,
	[deviceId] [nvarchar](50) NULL,
	[deviceType] [nvarchar](50) NULL,
	[os] [nvarchar](50) NULL,
	[osVersion] [nvarchar](50) NULL,
	[locale] [nvarchar](50) NULL,
	[userAgent] [nvarchar](max) NULL,
	[browser] [nvarchar](50) NULL,
	[browserVersion] [nvarchar](50) NULL,
	[screenResolution] [nvarchar](50) NULL,
	[sessionId] [nvarchar](max) NULL,
	[sessionIsFirst] [nvarchar](50) NULL,
	[clientIp] [nvarchar](50) NULL,
	[continent] [nvarchar](50) NULL,
	[country] [nvarchar](50) NULL,
	[province] [nvarchar](50) NULL,
	[city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
	[eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![](./media/app-insights-code-sample-export-sql-stream-analytics/34-create-table.png)

Dans cet exemple, nous utilisons les données issues des affichages de pages. Pour voir les autres données disponibles, examinez la sortie JSON et consultez le [modèle d’exportation de données](app-insights-export-data-model.md).

## Création d’une instance Azure Stream Analytics

À partir du [portail classique Azure](https://manage.windowsazure.com/), sélectionnez le service Azure Stream Analytics et créez une nouvelle tâche Stream Analytics :


![](./media/app-insights-code-sample-export-sql-stream-analytics/37-create-stream-analytics.png)



![](./media/app-insights-code-sample-export-sql-stream-analytics/38-create-stream-analytics-form.png)

Une fois la tâche créée, développez les informations qui s’y rapportent :

![](./media/app-insights-code-sample-export-sql-stream-analytics/41-sa-job.png)

#### Définition de l’emplacement des objets blob

Définissez-le pour qu’il tienne compte des données de votre objet blob d’exportation continue :

![](./media/app-insights-code-sample-export-sql-stream-analytics/42-sa-wizard1.png)

Vous devez maintenant disposer de la clé d’accès principale issue de votre compte de stockage, que vous avez notée précédemment. Définissez-la comme clé de compte de stockage.

![](./media/app-insights-code-sample-export-sql-stream-analytics/46-sa-wizard2.png)

#### Définition de la séquence d’octets préfixe du chemin d’accès 

![](./media/app-insights-code-sample-export-sql-stream-analytics/47-sa-wizard3.png)

Veillez à définir le format de date sur **AAAA-MM-JJ** (avec des **tirets**).

La séquence d’octets préfixe du chemin d’accès spécifie la manière dont Stream Analytics recherche les fichiers d’entrée dans le stockage. Vous devez la configurer pour correspondre au mode de stockage des données de l'exportation continue. Définissez-la comme suit :

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

Dans cet exemple :

* `webapplication27` est le nom de la ressource Application Insights, **tout en minuscules**. 
* `1234...` est la clé d’instrumentation de la ressource Application Insights **avec les tirets supprimés**. 
* `PageViews` est le type de données que nous souhaitons analyser. Les types disponibles varient selon le filtre que vous définissez dans l’exportation continue. Examinez les données exportées pour voir les autres types disponibles et consultez le [modèle d’exportation de données](app-insights-export-data-model.md).
* `/{date}/{time}` est une séquence écrite de manière littérale.

Pour obtenir le nom et l’iKey de votre ressource Application Insights, ouvrez Essentials sur sa page de présentation ou ouvrez Paramètres.

#### Fin de l’installation initiale

Confirmez le format de sérialisation :

![Confirmez et fermez l’assistant.](./media/app-insights-code-sample-export-sql-stream-analytics/48-sa-wizard4.png)

Fermez l’assistant et attendez la fin de l’installation.

>[AZURE.TIP] Utilisez l’exemple de fonction pour vérifier que vous avez correctement défini le chemin d’accès d’entrée. En cas d’échec, vérifiez qu’il y a des données dans le stockage pour l’exemple de période que vous avez choisi. Modifiez la définition de l’entrée et vérifiez que vous avez correctement défini le compte de stockage, le préfixe de chemin d’accès et le format de date.

## Définition d’une requête

Ouvrez la section de la requête :

![Dans Stream analytics, sélectionnez Requête.](./media/app-insights-code-sample-export-sql-stream-analytics/51-query.png)

Remplacez la requête par défaut par :

```SQL

    SELECT flat.ArrayValue.name as pageName
	, flat.ArrayValue.count as viewCount
	, flat.ArrayValue.url as url
	, flat.ArrayValue.urlData.port as urlDataPort
	, flat.ArrayValue.urlData.protocol as urlDataprotocol
	, flat.ArrayValue.urlData.host as urlDataHost
	, flat.ArrayValue.urlData.base as urlDataBase
	, flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOutput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

Notez que les premières propriétés sont spécifiques aux données d’affichage de page. Les exportations d’autres types de télémétrie disposent de propriétés différentes. Consultez la [référence de modèle de données détaillé pour les valeurs et types de propriétés.](app-insights-export-data-model.md)

## Configuration de la sortie vers la base de données

Sélectionnez SQL comme sortie.

![Dans Stream analytics, sélectionnez Sorties.](./media/app-insights-code-sample-export-sql-stream-analytics/53-store.png)

Spécifiez la base de données SQL.


![Remplissez les détails de votre base de données.](./media/app-insights-code-sample-export-sql-stream-analytics/55-output.png)

Fermez l’assistant et attendez une notification indiquant que la sortie a été configurée.

## Démarrage du traitement

Démarrez la tâche à partir de la barre d’action :

![Dans Stream analytics, cliquez sur Démarrer.](./media/app-insights-code-sample-export-sql-stream-analytics/61-start.png)

Vous pouvez choisir de démarrer le traitement à partir de données actuelles ou de données antérieures. La dernière possibilité est utile si l’exportation continue fonctionne déjà depuis un certain temps.


![Dans Stream analytics, cliquez sur Démarrer.](./media/app-insights-code-sample-export-sql-stream-analytics/63-start.png)

Après quelques minutes, revenez aux Outils d’administration SQL Server et observez les données qui y circulent. Utilisez, par exemple, le type de requête suivant :

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## Articles connexes

* [Exportation vers SQL à l’aide d’un rôle de travail](app-insights-code-sample-export-telemetry-sql-database.md)
* [Exporter vers PowerBI à l’aide de Stream Analytics](app-insights-export-power-bi.md)
* [Référence de modèle de données détaillé pour les valeurs et types de propriétés.](app-insights-export-data-model.md)
* [Exportation continue dans Application Insights](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-overview.md

 

<!---HONumber=AcomDC_0309_2016-->