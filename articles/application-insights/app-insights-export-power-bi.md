<properties 
	pageTitle="Afficher des données Application Insights dans Power BI" 
	description="Utilisez Power BI pour analyser les performances et l’utilisation de votre application." 
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
	ms.date="09/23/2015" 
	ms.author="awills"/>
 
# Vues Power BI des données Application Insights

[Microsoft Power BI](https://powerbi.microsoft.com/) présente vos données dans des graphiques riches et variés, avec la possibilité de rassembler des informations provenant de plusieurs sources. Vous pouvez diffuser des données télémétriques sur les performances et l’utilisation de vos applications web ou pour appareil à partir d’Application Insights vers Power BI.

![Exemple de vue Power BI de données d’utilisation Application Insights](./media/app-insights-export-power-bi/010.png)

Dans cet article, nous allons vous montrer comment exporter des données à partir d’Application Insights et utiliser Stream Analytics pour déplacer les données dans Power BI. [Stream Analytics](http://azure.microsoft.com/services/stream-analytics/) est un service Azure que nous utiliserons comme adaptateur.

![Exemple de vue Power BI de données d’utilisation Application Insights](./media/app-insights-export-power-bi/020.png)


> [AZURE.NOTE]Vous avez besoin d'un compte professionnel ou scolaire (compte professionnel MSDN) pour envoyer des données de Stream Analytics vers Power BI.

## Vidéo

Noam Ben Zeev montre ce que nous décrivons dans cet article.

> [AZURE.VIDEO export-to-power-bi-from-application-insights]

## Analyser votre application avec Application Insights

Si vous n’avez pas encore essayé, c’est le moment ou jamais. Application Insights peut analyser n’importe quelle application web ou pour appareil sur un large éventail de plateformes, y compris Windows, iOS, Android et J2EE. [Mise en route](app-insights-get-started.md)

## Créer un stockage dans Azure

Comme l’exportation continue génère toujours des données vers un compte de stockage Azure, vous devez commencer par créer ce stockage.

1. Créez un compte de stockage classique dans votre abonnement sur le [portail Azure](https://portal.azure.com).

    ![Sur le portail Azure, choisissez Nouveau, Données, Stockage.](./media/app-insights-export-power-bi/030.png)

2. Créez un conteneur.

    ![Dans le nouvel emplacement de stockage, sélectionnez Conteneurs, cliquez sur la mosaïque Conteneurs puis sur Ajouter.](./media/app-insights-export-power-bi/040.png)

3. Copiez la clé d’accès au stockage.

    Vous en aurez bientôt besoin pour configurer l’entrée dans le service Stream analytics.

    ![Dans le stockage, ouvrez Paramètres, Clés et copiez la clé d’accès principale.](./media/app-insights-export-power-bi/045.png)

## Démarrer l’exportation continue vers le stockage Azure

L'[exportation continue](app-insights-export-telemetry.md) déplace des données d'Application Insights vers le stockage Azure.

1. Sur le portail Azure, accédez à la ressource Application Insights que vous avez créée pour votre application.

    ![Sélectionnez Parcourir, Application Insights, puis votre application.](./media/app-insights-export-power-bi/050.png)

2. Créez une exportation continue.

    ![Choisissez Paramètres, Exportation continue, Ajouter.](./media/app-insights-export-power-bi/060.png)


    Sélectionnez le compte de stockage que vous avez préalablement créé.

    ![Définissez la destination de l’exportation.](./media/app-insights-export-power-bi/070.png)
    
    Définissez les types d’événements que vous souhaitez afficher :

    ![Choisissez les types d’événements.](./media/app-insights-export-power-bi/080.png)

3. Laissez les données s'accumuler. Installez-vous confortablement et laissez les utilisateurs utiliser votre application pendant un certain temps. Les données de télémétrie vont vous être transmises et vous permettre d’afficher des graphiques statistiques dans [Metrics explorer](app-insights-metrics-explorer.md) et des événements dans [Recherche de diagnostic](app-insights-diagnostic-search.md).

    Les données seront également exportées vers votre stockage.

4. Inspectez les données exportées. Dans Visual Studio, sélectionnez **Afficher / Cloud Explorer**, puis ouvrez Azure / Stockage. (Si vous n'avez pas cette option, vous devez installer le SDK Azure : Ouvrez la boîte de dialogue Nouveau projet et ouvrez Visual C# / Cloud / Obtenir Microsoft Azure SDK pour .NET.)

    ![](./media/app-insights-export-power-bi/04-data.png)

    Prenez note de la partie commune du nom du chemin d'accès, qui est dérivée du nom de l'application et de la clé d'instrumentation.

Les événements sont écrits dans des fichiers blob au format JSON. Chaque fichier peut contenir un ou plusieurs événements. Donc, nous devons lire les données d’événement et filtrer les champs voulus. Nous pourrions effectuer toutes sortes d'opérations avec les données, mais notre objectif aujourd'hui est d'utiliser Stream Analytics pour transmettre les données vers Power BI.

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

La séquence d'octets préfixe du chemin d'accès spécifie où Stream Analytics recherche les fichiers d'entrée dans le stockage. Vous devez la configurer pour correspondre au mode de stockage des données de l'exportation continue. Définissez-la comme suit :

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

Dans cet exemple :

* `webapplication27` est le nom de la ressource Application Insights, **tout en minuscules**.
* `1234...` est la clé d'instrumentation de la ressource Application Insights, **sans les tirets**. 
* `PageViews` est le type de données à analyser. Les types disponibles varient selon le filtre que vous définissez dans l’exportation continue. Examinez les données exportées pour voir les autres types disponibles et consultez le [modèle d'exportation de données](app-insights-export-data-model.md).
* `/{date}/{time}` est une séquence écrite de manière littérale.

> [AZURE.NOTE]Vérifiez le stockage pour être sûr d'avoir le chemin d'accès approprié.

#### Fin de l’installation initiale

Confirmez le format de sérialisation :

![Confirmez et fermez l’assistant.](./media/app-insights-export-power-bi/150.png)

Fermez l’assistant et attendez la fin de l’installation.

> [AZURE.TIP]Utilisez l'exemple de commande pour télécharger des données. Gardez-le comme exemple de test pour déboguer votre requête.

## Définir la sortie

Sélectionnez maintenant votre tâche et définissez la sortie.

![Sélectionnez le nouveau canal, cliquez sur Sorties, Ajouter, Power BI](./media/app-insights-export-power-bi/160.png)

Indiquez votre **compte professionnel ou scolaire** pour autoriser Stream Analytics à accéder à votre ressource Power BI. Inventez ensuite un nom pour la sortie et le jeu de données et la table Power BI cibles.

![Inventer trois noms](./media/app-insights-export-power-bi/170.png)

## Définir la requête

La requête régit la traduction de l’entrée vers la sortie.

![Sélectionnez la tâche et cliquez sur Interroger. Collez l’exemple ci-dessous.](./media/app-insights-export-power-bi/180.png)


Utilisez la fonction de Test pour vérifier que vous obtenez le résultat approprié. Indiquez les exemples de données que vous avez copiés de la page d'entrées.

#### Requête d'affichage du nombre d'événements

Collez cette requête :

```SQL

    SELECT
      flat.ArrayValue.name,
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
* Nous utilisons [OUTER APPLY GetElements](https://msdn.microsoft.com/library/azure/dn706229.aspx) car le nom de l'événement se trouve dans un tableau JSON imbriqué. Ensuite, l’instruction Select récupère le nom de l’événement, ainsi que le nombre d’instances portant ce nom dans la période donnée. La clause [Regrouper par](https://msdn.microsoft.com/library/azure/dn835023.aspx) regroupe les éléments en périodes d'1 minute.


#### Requête d'affichage des valeurs de mesure


```SQL

    SELECT
      A.context.data.eventtime,
      avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.context.custom.metrics) as flat
    GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime

``` 

* Cette requête explore la télémétrie des mesures pour obtenir l'heure de l'événement et la valeur de mesure. Les valeurs de mesure se trouvent dans un tableau, donc nous utilisons le modèle OUTER APPLY GetElements pour extraire les lignes. Dans ce cas, « myMetric » est le nom de la mesure. 



## Exécution de la tâche

Vous pouvez sélectionner une date dans le passé à partir de laquelle démarrer la tâche.

![Sélectionnez la tâche et cliquez sur Interroger. Collez l’exemple ci-dessous.](./media/app-insights-export-power-bi/190.png)

Attendez que le travail s’exécute.

## Afficher les résultats dans Power BI

Ouvrez Power BI avec votre compte professionnel ou scolaire, puis sélectionnez le jeu de données et la table que vous avez définis comme sortie de la tâche Stream Analytics.

![Dans Power BI, sélectionnez votre dataset et vos champs.](./media/app-insights-export-power-bi/200.png)

Vous pouvez maintenant utiliser ce jeu de données dans des rapports et des tableaux de bord dans [Power BI](https://powerbi.microsoft.com).


![Dans Power BI, sélectionnez votre dataset et vos champs.](./media/app-insights-export-power-bi/210.png)

## Vidéo

Noam Ben Zeev montre comment exporter vers Power BI.

> [AZURE.VIDEO export-to-power-bi-from-application-insights]

## Rubriques connexes

* [Exportation continue](app-insights-export-telemetry.md)
* [Référence de modèle de données détaillé pour les valeurs et types de propriétés.](app-insights-export-data-model.md)
* [Application Insights](app-insights-overview.md)
* [Plus d'exemples et de procédures pas à pas](app-insights-code-samples.md)

<!---HONumber=Sept15_HO4-->