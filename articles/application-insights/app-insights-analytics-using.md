<properties 
	pageTitle="Utilisation d’Analytics, le puissant outil de recherche d’Application Insights | Microsoft Azure" 
	description="Utilisation d’Analytics, le puissant outil de recherche de diagnostic d’Application Insights. " 
	services="application-insights" 
    documentationCenter=""
	authors="danhadari" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/15/2016" 
	ms.author="danha"/>


# Utilisation d’Analytics dans Application Insights


[Analytics](app-insights-analytics.md) est la puissante fonctionnalité de recherche d’[Application Insights](app-insights-overview.md). Ces pages décrivent le langage de requête Analytics.

* **[Regardez la vidéo d’introduction](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Testez la version d’évaluation d’Analytics sur nos données simulées](https://analytics.applicationinsights.io/demo)** si votre application n’envoie pas encore de données à Application Insights.

## Ouverture de la fonctionnalité Analytics

À partir de la ressource de base de votre application dans Application Insights, cliquez sur Analytics. ![Ouvrez portal.azure.com, ouvrez votre ressource Application Insights, puis cliquez sur Analyse.](./media/app-insights-analytics-using/001.png)

Le didacticiel en ligne vous donne quelques idées sur les possibilités qui vous sont offertes.

Vous pouvez cependant consulter [ici une présentation approfondie](app-insights-analytics-tour.md).

## Interrogation de votre télémétrie

### Écrivez votre requête.

![](./media/app-insights-analytics-using/150.png)

Commencez par les noms des tables répertoriées sur la gauche (ou les opérateurs [range](app-insights-analytics-reference.md#range-operator) ou [union](app-insights-analytics-reference.md#union-operator)). Utilisez `|` pour créer un pipeline [d’opérateurs](app-insights-analytics-reference.md#queries-and-operators). IntelliSense vous indique les opérateurs et certains éléments d’expression que vous pouvez utiliser.

Consultez la [vue d’ensemble du langage d’Analytics](app-insights-analytics-tour.md) et les [informations de référence sur le langage](app-insights-analytics-reference.md).

### Exécution d’une requête

![Exécution d’une requête](./media/app-insights-analytics-using/130.png)

1. Vous pouvez utiliser des sauts de ligne uniques dans une requête.
2. Placez le curseur à l’intérieur ou à la fin de la requête à exécuter.
3. Cliquez sur Accéder pour exécuter la requête.
4. N’insérez pas de lignes vides dans votre requête. Vous pouvez conserver plusieurs requêtes séparées dans un onglet de requête en les séparant par des lignes vides. Seule la requête pointée par le curseur est exécutée.

### Enregistrement d’une requête

![Enregistrement d’une requête](./media/app-insights-analytics-using/140.png)

1. Enregistrez le fichier de requête actuel.
2. Ouvrez un fichier de requête enregistré.
3. Créez un fichier de requête.


## Voir des informations détaillées

Développez des lignes dans les résultats pour afficher la liste complète de leurs propriétés. Vous pouvez développer davantage n’importe quelle propriété qui est une valeur structurée. Par exemple, des dimensions personnalisées ou la liste d’empilement dans une exception.

![Extension d’une ligne](./media/app-insights-analytics-using/070.png)

 

## Réorganisation des résultats

Vous pouvez trier, filtrer, paginer et regrouper les résultats retournés par votre requête.

> [AZURE.NOTE] Le tri, le regroupement et le filtrage dans le navigateur ne réexécute pas votre requête. Ils réorganisent uniquement les résultats retournés par votre dernière requête.
> 
> Pour exécuter ces tâches sur le serveur avant que les résultats ne soient retournés, écrivez votre requête avec les opérateurs [sort](app-insights-analytics-reference.md#sort-operator), [summarize](app-insights-analytics-reference.md#summarize-operator) et [where](app-insights-analytics-reference.md#where-operator).

Choisissez les colonnes que vous souhaitez voir, faites glisser les en-têtes de colonne pour les réorganiser et redimensionnez les colonnes en faisant glisser ses bordures.

![Réorganisation de colonnes](./media/app-insights-analytics-using/030.png)

### Tri et filtrage d’éléments

Triez les résultats en cliquant sur l’en-tête d’une colonne. Cliquez à nouveau pour trier dans l’ordre inverse, puis cliquez sur une troisième fois pour rétablir l’ordre d’origine retourné par la requête.

Utilisez l’icône de filtre pour affiner votre recherche.

![Tri et filtrage de colonnes](./media/app-insights-analytics-using/040.png)



### Regroupement d’éléments

Pour trier par plusieurs colonnes, utilisez le regroupement. Tout d’abord, activez le regroupement, puis faites glisser les en-têtes de colonne dans l’espace au-dessus de la table.

![Groupe](./media/app-insights-analytics-using/060.png)



### Certains résultats manquent ?

Il existe une limite d’environ 10 000 lignes pour les résultats retournés à partir du portail. Un avertissement s’affiche si vous dépassez la limite. Dans ce cas, le tri des résultats de la table ne vous indique pas toujours tous les premiers ou derniers résultats réels.

Il est recommandé d’éviter d’atteindre la limite. Utilisez des opérateurs tels que :

* [where timestamp > ago(3d)](app-insights-analytics-reference.md#where-operator)
* [top 100 by timestamp](app-insights-analytics-reference.md#top-operator)
* [take 100](app-insights-analytics-reference.md#take-operator)
* [summarize](app-insights-analytics-reference.md#summarize-operator)



## Diagrammes

Sélectionnez le type de diagramme de votre choix :

![](./media/app-insights-analytics-using/230.png)

Si vous disposez de plusieurs colonnes de types appropriés, vous pouvez choisir les axes x et y et spécifier une colonne de dimensions avec laquelle fractionner les résultats.

Par défaut, les résultats sont initialement affichés sous forme de table. Vous pouvez ensuite sélectionner le diagramme manuellement. Mais vous pouvez également utiliser la [directive render](app-insights-analytics-reference.md#render-directive) à la fin d’une requête pour sélectionner un diagramme.

Vous pouvez épingler un diagramme à l’un de vos [tableaux de bord partagés](app-insights-dashboards.md) : cliquez simplement sur l’épingle. (Disponible uniquement pour les applications d’un niveau tarifaire payant).

## Exporter vers Excel

Une fois votre requête exécutée, vous pouvez télécharger un fichier .csv. Cliquez sur **Exporter vers Excel**.

## Exporter vers Power BI

1. Placez le curseur dans une requête et choisissez **Exporter vers Power BI**.

    ![](./media/app-insights-analytics-using/240.png)

    Cette option vous permet de télécharger un fichier de script M.

3. Copiez le script de langage M dans l’éditeur de requêtes avancé de Power BI Desktop.
 * Ouvrez le fichier exporté.
 * Dans Power BI Desktop, sélectionnez **Obtenir des données, Requête vide, Éditeur avancé** et collez le script de langage M.

    ![](./media/app-insights-analytics-using/250.png)

4. Modifiez si besoin les informations d’identification avant de générer votre rapport.

    ![](./media/app-insights-analytics-using/260.png)




[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0727_2016-->