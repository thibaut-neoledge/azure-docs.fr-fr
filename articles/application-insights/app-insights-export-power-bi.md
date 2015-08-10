<properties 
	pageTitle="Afficher des données Application Insights dans Power BI" 
	description="Utilisez Power BI pour analyser les performances et l’utilisation de votre application." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="awills"/>
 
# Vues Power BI des données Application Insights

[Microsoft Power BI](https://powerbi.microsoft.com/) présente vos données dans des graphiques riches et variés, avec la possibilité de rassembler des informations provenant de plusieurs sources. Vous pouvez diffuser des données télémétriques sur les performances et l’utilisation de vos applications web ou pour appareil à partir d’Application Insights vers Power BI.

![Exemple de vue Power BI de données d’utilisation Application Insights](./media/app-insights-export-power-bi/010.png)

Dans cet article, nous allons vous montrer comment exporter des données à partir d’Application Insights et utiliser Stream Analytics pour déplacer les données dans Power BI. [Stream Analytics](http://azure.microsoft.com/services/stream-analytics/) est un service Azure que nous utiliserons comme adaptateur.

![Exemple de vue Power BI de données d’utilisation Application Insights](./media/app-insights-export-power-bi/020.png)

## Vidéo

Noam Ben Zeev montre ce que nous décrivons dans cet article.

> [AZURE.VIDEO export-to-power-bi-from-application-insights]

## Analyser votre application avec Application Insights

Si vous n’avez pas encore essayé, c’est le moment ou jamais. Application Insights peut analyser n’importe quelle application web ou pour appareil sur un large éventail de plateformes, y compris Windows, iOS, Android et J2EE. [Prise en main](app-insights-get-started.md).

## Créer un stockage dans Azure

Comme l’exportation continue génère toujours des données vers un compte de stockage Azure, vous devez commencer par créer ce stockage.

1. Créez un compte de stockage dans votre abonnement sur le [portail Azure](https://portal.azure.com).

    ![Sur le portail Azure, choisissez Nouveau, Données, Stockage.](./media/app-insights-export-power-bi/030.png)

2. Créez un conteneur.

    ![Dans le nouvel emplacement de stockage, sélectionnez Conteneurs, puis Ajouter.](./media/app-insights-export-power-bi/040.png)

3. Copiez la clé d’accès au stockage.

    Vous en aurez bientôt besoin pour configurer l’entrée dans le service Stream analytics.

    ![Dans le stockage, ouvrez Paramètres, Clés et copiez la clé d’accès principale.](./media/app-insights-export-power-bi/045.png)

## Démarrer l’exportation continue vers le stockage Azure

L’[exportation continue](app-insights-export-telemetry.md) déplace les données d’Application Insights vers le stockage Azure.

1. Sur le portail Azure, accédez à la ressource Application Insights que vous avez créée pour votre application.

    ![Sélectionnez Parcourir, Application Insights, puis votre application.](./media/app-insights-export-power-bi/050.png)

2. Créez une exportation continue.

    ![Choisissez Paramètres, Exportation continue, Ajouter.](./media/app-insights-export-power-bi/060.png)


    Sélectionnez le compte de stockage que vous avez préalablement créé.

    ![Définissez la destination de l’exportation.](./media/app-insights-export-power-bi/070.png)
    
    Définissez les types d’événements que vous souhaitez afficher :

    ![Choisissez les types d’événements.](./media/app-insights-export-power-bi/080.png)

À présent, installez-vous confortablement et laissez les utilisateurs utiliser votre application pendant un certain temps. Les données de télémétrie vont vous être transmises et vous permettre d’afficher des graphiques statistiques dans [Metrics explorer](app-insights-metrics-explorer.md) et des événements dans [Recherche de diagnostic](app-insights-diagnostic-search.md).

Les données seront également exportées vers votre stockage.

## Création d’une instance Azure Stream Analytics

À partir du [portail classique Azure](https://manage.windowsazure.com/), sélectionnez le service Azure Stream Analytics et créez une nouvelle tâche Stream Analytics :


![](./media/app-insights-export-power-bi/090.png)



![](./media/app-insights-export-power-bi/100.png)

Une fois la tâche créée, développez les informations qui s’y rapportent :

![](./media/app-insights-export-power-bi/110.png)


#### Définition de l’emplacement des objets blob

Définissez-le pour qu’il tienne compte des données de votre objet blob d’exportation continue :

![](./media/app-insights-export-power-bi/120.png)

Vous devez maintenant disposer de la clé d’accès principale issue de votre compte de stockage, que vous avez notée précédemment. Définissez-la comme clé de compte de stockage.

![](./media/app-insights-export-power-bi/130.png)

#### Définition de la séquence d’octets préfixe du chemin d’accès 

![](./media/app-insights-export-power-bi/140.png)

Veillez à définir le format de date sur AAAA-MM-JJ (avec des tirets).

La séquence d’octets préfixe du chemin d’accès spécifie la manière dont Stream Analytics recherche les fichiers d’entrée dans le stockage. Vous devez la configurer pour qu’il corresponde au mode de stockage des données de l’exportation continue. Définissez-la comme suit :

    webapplication27_100000000-0000-0000-0000-000000000000/PageViews/{date}/{time}

Dans cet exemple :

* `webapplication27` est le nom de la ressource Application Insights. 
* `1000...` est la clé d’instrumentation de la ressource Application Insights. 
* `PageViews` est le type de données que nous souhaitons analyser. Les types disponibles varient selon le filtre que vous définissez dans l’exportation continue. Examinez les données exportées pour voir les autres types disponibles.
* `/{date}/{time}` est une séquence écrite de manière littérale.

Pour obtenir le nom et l’iKey de votre ressource Application Insights, ouvrez Essentials sur sa page de présentation ou ouvrez Paramètres.

#### Fin de l’installation initiale

Confirmez le format de sérialisation :

![Confirmez et fermez l’assistant.](./media/app-insights-export-power-bi/150.png)

Fermez l’assistant et attendez la fin de l’installation.

## Définir la sortie

Sélectionnez maintenant votre tâche et définissez la sortie.

![Sélectionnez le nouveau canal, cliquez sur Sorties, Ajouter, Power BI](./media/app-insights-export-power-bi/160.png)

Autorisez Stream Analytics à accéder à votre ressource Power BI, puis créez un nom pour la sortie et pour le dataset et la table Power BI cibles.

![Inventer trois noms](./media/app-insights-export-power-bi/170.png)

## Définir la requête

La requête régit la traduction de l’entrée vers la sortie.

![Sélectionnez la tâche et cliquez sur Interroger. Collez l’exemple ci-dessous.](./media/app-insights-export-power-bi/180.png)

Collez cette requête :

```SQL

    SELECT
      flat.ArrayValue.name
      count(*)
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.[event]) as flat
    GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* export-input est l’alias que nous avons donné au flux d’entrée
* pbi-output est l’alias de sortie que nous avons défini
* Nous utilisons GetElements car le nom de l’événement est dans un tableau JSON imbriqué. Ensuite, l’instruction Select récupère le nom de l’événement, ainsi que le nombre d’instances portant ce nom dans la période donnée. La clause Group By regroupe les éléments en périodes de 1 minute.

## Exécution de la tâche

Vous pouvez sélectionner une date dans le passé à partir de laquelle démarrer la tâche.

![Sélectionnez la tâche et cliquez sur Interroger. Collez l’exemple ci-dessous.](./media/app-insights-export-power-bi/190.png)

Attendez que le travail s’exécute.

## Afficher les résultats dans Power BI

Ouvrez Power BI et sélectionnez le dataset et la table que vous avez définis comme sortie de la tâche Stream Analytics.

![Dans Power BI, sélectionnez votre dataset et vos champs.](./media/app-insights-export-power-bi/200.png)

Vous pouvez maintenant utiliser ce dataset dans des rapports et des tableaux de bord dans [Power BI](https://powerbi.microsoft.com).


![Dans Power BI, sélectionnez votre dataset et vos champs.](./media/app-insights-export-power-bi/210.png)

## Vidéo

Noam Ben Zeev montre comment exporter vers Power BI.

> [AZURE.VIDEO export-to-power-bi-from-application-insights]

## Rubriques connexes

* [Exportation continue](app-insights-export-telemetry.md)
* [Application Insights](app-insights-overview.md)

<!---HONumber=July15_HO5-->