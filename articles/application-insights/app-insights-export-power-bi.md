<properties 
	pageTitle="Exportation vers Power BI à partir d’Application Insights" 
	description="Articles" 
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
	ms.date="04/05/2016" 
	ms.author="awills"/>

# Alimentation de Power BI à partir d’Application Insights

[Power BI](http://www.powerbi.com/) est une suite d’outils d’analyse métier pour analyser les données et partager les informations. Chaque périphérique bénéficie de tableaux de bord riches. Vous pouvez combiner des données provenant de nombreuses sources, notamment [Visual Studio Application Insights](app-insights-overview.md).

Pour commencer, consultez [Affichage de données Application Insights dans Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-application-insights/).

Vous obtenez un tableau de bord initial que vous pouvez personnaliser. Il associe des graphiques Application Insights avec ceux d’autres sources. Il existe une galerie de visualisation où vous pouvez obtenir plus de graphiques. Chaque graphique comporte des paramètres que vous pouvez définir.

![](./media/app-insights-export-power-bi/010.png)


Après l’importation initiale, le tableau de bord et les rapports sont mis à jour quotidiennement. Vous pouvez contrôler la planification de l’actualisation du jeu de données.


**Échantillonnage.** Si votre application envoie des données en grand nombre et si vous utilisez le kit de développement logiciel Application Insights pour ASP.NET version 2.0.0-beta3 ou ultérieure, la fonctionnalité d’échantillonnage adaptatif peut fonctionner et transmettre uniquement un pourcentage de vos données de télémétrie. Il en est de même si vous avez défini manuellement l’échantillonnage dans le Kit de développement logiciel ou sur ingestion. [En savoir plus sur l'échantillonnage.](app-insights-sampling.md)

## Autres manières d’afficher des données Application Insights

* Les [tableaux de bord Azure contenant des graphiques Application Insights](app-insights-dashboards.md) peuvent être plus appropriés si vous n’avez pas besoin d’afficher des données autres que les données Azure. Par exemple, si vous souhaitez configurer un tableau de bord de graphiques Application Insights surveillant les différents composants d’un système, peut-être avec certains analyseurs de service Azure, alors un tableau de bord Azure est la solution idéale. Il se met à jour plus fréquemment par défaut. 
* L’[exportation continue](app-insights-export-telemetry.md) copie vos données entrantes dans le stockage Azure. De là, vous pouvez les déplacer et les traiter comme vous le souhaitez.
* L’[analyse](app-insights-analytics.md) vous permet d’effectuer des requêtes complexes sur les données brutes conservées par Application Insights.


## Création de votre propre adaptateur Power BI à l’aide de Stream Analytics

Le pack de contenu Power BI pour Application Insights affiche un sous-ensemble utile des données de télémétrie de votre application qui sera probablement suffisant pour vos besoins. Mais si vous souhaitez obtenir une gamme plus étendue de données de télémétrie que ce qui est fourni ou si vous souhaitez calculer des données à partir de la télémétrie brute, vous pouvez créer votre propre adaptateur à l’aide du service Azure Stream Analytics.

Dans ce schéma, nous allons exporter des données d’Application Insights vers le stockage Azure. [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) se chargera d’extraire les données à partir de là, de renommer et de traiter certains champs, puis de les diriger vers Power BI. Stream Analytics est un service qui peut filtrer, regrouper et effectuer des calculs sur un flux continu de données.

![Diagramme de blocs pour l’exportation via SA vers PBI](./media/app-insights-export-power-bi/020.png)


>[AZURE.TIP] **Vous n’avez pas besoin de suivre la procédure décrite dans le reste de cet article** (à l’aide de Stream Analytics) pour afficher des données Application Insights dans Power BI. Il existe un moyen beaucoup plus facile ! [Utilisez l’adaptateur gratuit](https://powerbi.microsoft.com/documentation/powerbi-content-pack-application-insights/) à la place. Suivez le reste de cet article uniquement si cet adaptateur ne fournit pas toutes les données que vous voulez obtenir ou si vous souhaitez définir vos propres agrégations ou fonctions sur vos données.

### Création d’un stockage dans Azure

Comme l’exportation continue génère toujours des données vers un compte de stockage Azure, vous devez commencer par créer ce stockage.

1. Avez-vous essayé le [powerpack Power BI pour Application Insights](https://powerbi.microsoft.com/documentation/powerbi-content-pack-application-insights/) ? S’il répond à vos besoins, alors vous n’avez pas besoin de lire le reste de cet article.

2.  Créez un compte de stockage « classique » dans votre abonnement sur le [portail Azure](https://portal.azure.com).

    ![Sur le portail Azure, choisissez Nouveau, Données, Stockage.](./media/app-insights-export-power-bi/030.png)

2. Créez un conteneur.

    ![Dans le nouvel emplacement de stockage, sélectionnez Conteneurs, cliquez sur la mosaïque Conteneurs puis sur Ajouter.](./media/app-insights-export-power-bi/040.png)

3. Copiez la clé d’accès au stockage.

    Vous en aurez bientôt besoin pour configurer l’entrée dans le service Stream analytics.

    ![Dans le stockage, ouvrez Paramètres, Clés et copiez la clé d’accès principale.](./media/app-insights-export-power-bi/045.png)

### Démarrer l’exportation continue vers le stockage Azure

L'[exportation continue](app-insights-export-telemetry.md) déplace des données d'Application Insights vers le stockage Azure.

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

4. Inspectez les données exportées. Dans Visual Studio, sélectionnez **Afficher / Cloud Explorer**, puis ouvrez Azure / Stockage. (Si vous n'avez pas cette option, vous devez installer le SDK Azure : Ouvrez la boîte de dialogue Nouveau projet et ouvrez Visual C# / Cloud / Obtenir Microsoft Azure SDK pour .NET.)

    ![](./media/app-insights-export-power-bi/04-data.png)

    Prenez note de la partie commune du nom du chemin d'accès, qui est dérivée du nom de l'application et de la clé d'instrumentation.

Les événements sont écrits dans des fichiers blob au format JSON. Chaque fichier peut contenir un ou plusieurs événements. Donc, nous devons lire les données d’événement et filtrer les champs voulus. Nous pourrions effectuer toutes sortes d'opérations avec les données, mais notre objectif aujourd'hui est d'utiliser Stream Analytics pour transmettre les données vers Power BI.

### Création d’une instance Azure Stream Analytics

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


**Veillez à définir le format de date sur AAAA-MM-JJ (avec des tirets).**

La séquence d'octets préfixe du chemin d'accès spécifie où Stream Analytics recherche les fichiers d'entrée dans le stockage. Vous devez la configurer pour correspondre au mode de stockage des données de l'exportation continue. Définissez-la comme suit :

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

Dans cet exemple :

* `webapplication27` est le nom de la ressource Application Insights, **tout en minuscules**.
* `1234...` est la clé d'instrumentation de la ressource Application Insights, **sans les tirets**. 
* `PageViews` est le type de données que vous souhaitez analyser. Les types disponibles varient selon le filtre que vous définissez dans l’exportation continue. Examinez les données exportées pour voir les autres types disponibles et consultez le [modèle d’exportation de données](app-insights-export-data-model.md).
* `/{date}/{time}` est une séquence écrite de manière littérale.

> [AZURE.NOTE] Vérifiez le stockage pour être sûr d'avoir le chemin d'accès approprié.

#### Fin de l’installation initiale

Confirmez le format de sérialisation :

![Confirmez et fermez l’assistant.](./media/app-insights-export-power-bi/150.png)

Fermez l’assistant et attendez la fin de l’installation.

> [AZURE.TIP] Utilisez l'exemple de commande pour télécharger des données. Gardez-le comme exemple de test pour déboguer votre requête.

### Définir la sortie

Sélectionnez maintenant votre tâche et définissez la sortie.

![Sélectionnez le nouveau canal, cliquez sur Sorties, Ajouter, Power BI](./media/app-insights-export-power-bi/160.png)

Indiquez votre **compte professionnel ou scolaire** pour autoriser Stream Analytics à accéder à votre ressource Power BI. Inventez ensuite un nom pour la sortie et le jeu de données et la table Power BI cibles.

![Inventer trois noms](./media/app-insights-export-power-bi/170.png)

### Définir la requête

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
* Nous utilisons [OUTER APPLY GetElements](https://msdn.microsoft.com/library/azure/dn706229.aspx), car le nom de l'événement se trouve dans un tableau JSON imbriqué. Ensuite, l’instruction Select récupère le nom de l’événement, ainsi que le nombre d’instances portant ce nom dans la période donnée. La clause [Regrouper par](https://msdn.microsoft.com/library/azure/dn835023.aspx) regroupe les éléments en intervalles d'1 minute.


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

#### Requête d’inclusion de valeurs de propriétés de dimension

```SQL

    WITH flat AS (
    SELECT
      MySource.context.data.eventTime as eventTime,
      InstanceId = MyDimension.ArrayValue.InstanceId.value,
      BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
    FROM MySource
    OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
    )
    SELECT
     eventTime,
     InstanceId,
     BusinessUnitId
    INTO AIOutput
    FROM flat

```

* Cette requête inclut les valeurs des propriétés de dimension, indépendamment d’une dimension particulière se trouvant à un index fixe dans le tableau de dimension.

### Exécution de la tâche

Vous pouvez sélectionner une date dans le passé à partir de laquelle démarrer la tâche.

![Sélectionnez la tâche et cliquez sur Interroger. Collez l’exemple ci-dessous.](./media/app-insights-export-power-bi/190.png)

Attendez que le travail s’exécute.

### Afficher les résultats dans Power BI

Ouvrez Power BI avec votre compte professionnel ou scolaire, puis sélectionnez le jeu de données et la table que vous avez définis comme sortie de la tâche Stream Analytics.

![Dans Power BI, sélectionnez votre dataset et vos champs.](./media/app-insights-export-power-bi/200.png)

Vous pouvez maintenant utiliser ce jeu de données dans des rapports et des tableaux de bord dans [Power BI](https://powerbi.microsoft.com).


![Dans Power BI, sélectionnez votre dataset et vos champs.](./media/app-insights-export-power-bi/210.png)


### Pas de données ?

* Vérifiez que vous avez [défini le format de date](#set-path-prefix-pattern) correctement sur AAAA-MM-JJ (avec des tirets).


### Vidéo

Noam Ben Zeev montre comment exporter vers Power BI.

> [AZURE.VIDEO export-to-power-bi-from-application-insights]

## Rubriques connexes

* [Exportation continue](app-insights-export-telemetry.md)
* [Référence de modèle de données détaillé pour les valeurs et types de propriétés.](app-insights-export-data-model.md)
* [Application Insights](app-insights-overview.md)
* [Plus d'exemples et de procédures pas à pas](app-insights-code-samples.md)
 

<!---HONumber=AcomDC_0420_2016-->