<properties 
	pageTitle="Utilisation d’Analytics, le puissant outil de recherche d’Application Insights | Microsoft Azure" 
	description="Utilisation d’Analytics, le puissant outil de recherche de diagnostic d’Application Insights." 
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
	ms.date="03/30/2016" 
	ms.author="awills"/>




# Utilisation d’Analytics dans Application Insights


[Analytics](app-insights-analytics.md) est la puissante fonctionnalité de recherche d’[Application Insights](app-insights-overview.md). Ces pages décrivent le langage de requête Analytics.


## Ouverture de la fonctionnalité Analytics

À partir de la ressource de base de votre application dans Application Insights, cliquez sur Analytics.

![Ouvrez portal.azure.com, ouvrez votre ressource Application Insights, puis cliquez sur Analyse.](./media/app-insights-analytics-using/001.png)

Le didacticiel en ligne vous donne quelques idées sur les possibilités qui vous sont offertes.

Vous pouvez cependant consulter [ici une présentation approfondie](app-insights-analytics-tour.md).

## Écriture de requêtes

Écrivez une requête commençant par le nom de n’importe quelle table répertoriée sur la gauche. Utilisez `|` pour créer un pipeline d[’opérateurs](app-insights-analytics-reference.md#queries-and-operators).


![](./media/app-insights-analytics-using/150.png)

* N’insérez pas de lignes vides dans votre requête.
* Vous pouvez utiliser des sauts de ligne uniques dans une requête.
* Vous pouvez conserver plusieurs requêtes dans la fenêtre, en les séparant par des lignes vides.
* Pour exécuter une requête, **placez le curseur à l’intérieur ou à la fin de la requête**, puis cliquez sur OK.


![](./media/app-insights-analytics-using/130.png)

* Vous pouvez enregistrer et rappeler le contenu de la fenêtre de requête.

![](./media/app-insights-analytics-using/140.png)

## Réorganisation des résultats

Vous pouvez choisir les colonnes que vous souhaitez consulter. Développez un élément pour afficher toutes les valeurs de colonne renvoyées.

![](./media/app-insights-analytics-using/030.png)

> [AZURE.NOTE] Cliquez sur l’en-tête d’une colonne pour réorganiser rapidement les résultats disponibles dans le navigateur Web. Sachez toutefois que, pour un jeu de résultats volumineux, le système limite le nombre de lignes téléchargées vers le navigateur. L’utilisation de cette méthode de tri ne vous permet donc pas toujours d’obtenir effectivement les éléments dans l’ordre croissant ou décroissant. Pour cela, vous devez utiliser l’opérateur [top](app-insights-analytics-reference.md#top-operator) ou [sort](app-insights-analytics-reference.md#sort-operator).

Il est cependant recommandé d’utiliser les opérateurs [take](app-insights-analytics-reference.md#take-operator), [top](app-insights-analytics-reference.md#top-operator) ou [summarize](app-insights-analytics-reference.md#summarize-operator) pour éviter le téléchargement de tables volumineuses à partir du serveur. Quoi qu’il en soit, le nombre de lignes par requête est automatiquement limité à 10 000.


## Diagrammes

Sélectionnez le type de diagramme de votre choix :

![](./media/app-insights-analytics-using/230.png)

Si vous disposez de plusieurs colonnes de types appropriés, vous pouvez choisir les axes x et y et spécifier une colonne de dimensions pour fractionner les résultats par :

![](./media/app-insights-analytics-using/100.png)

Par défaut, les résultats sont initialement affichés sous forme de table. Vous pouvez ensuite sélectionner le diagramme manuellement. Mais vous pouvez également utiliser la [directive render](app-insights-analytics-reference.md#render-directive) à la fin d’une requête pour sélectionner un diagramme.

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

<!---HONumber=AcomDC_0615_2016-->