<properties 
	pageTitle="Procédure pas à pas : analyser Microsoft CRM avec Application Insights" 
	description="Obtenez des données de télémétrie à partir de Microsoft CRM Online à l’aide d’Application Insights. Procédure pas à pas de configuration, obtention de données, visualisation et exportation." 
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
 
# Procédure pas à pas : activation de télémétrie pour Microsoft CRM Online à l’aide d’Application Insights

Cet article montre comment obtenir des données de télémétrie à partir de [Microsoft CRM Online](https://www.dynamics.com/) à l’aide de [Visual Studio Application Insights](https://azure.microsoft.com/services/application-insights/). Nous explorerons le processus complet d’ajout de script Application Insights à votre application, de capture de données et de visualisation des données.

## Ajouter Application Insights à une instance de CRM Online nouvelle ou existante 

Pour analyser votre application, vous devez y ajouter un Kit de développement logiciel (SDK) Application Insights. Le Kit SDK envoie les données de télémétrie au [portail Application Insights](https://portal.azure.com), où vous pouvez utiliser nos puissants outils de diagnostic et d’analyse ou exporter les données vers un emplacement de stockage.

### Créer une ressource Application Insights dans Azure

1. Obtenez un [compte dans Microsoft Azure](http://azure.com/pricing). 
2. Connectez-vous au [portail Azure](https://portal.azure.com) et ajoutez une nouvelle ressource Application Insights. C’est là où vos données seront traitées et affichées.

    ![Cliquez sur +, Services de développement, Application Insights.](./media/app-insights-sample-mscrm/01.png)

    Choisissez le type d’application ASP.NET.

3. Ouvrez l’onglet Démarrage rapide et ouvrez le script de code.

    ![](./media/app-insights-sample-mscrm/03.png)

**Laissez la page de code ouverte** pendant que vous effectuez l’étape suivante dans une autre fenêtre de navigateur. Vous aurez bientôt besoin du code.

### Créer une ressource web JavaScript dans Microsoft CRM

1. Ouvrez votre instance de CRM Online et connectez-vous avec des privilèges d’administrateur.
2. Ouvrez Microsoft Dynamics CRM, Paramètres, Personnalisations, Système.

    ![](./media/app-insights-sample-mscrm/04.png)
    
    ![](./media/app-insights-sample-mscrm/05.png)


    ![](./media/app-insights-sample-mscrm/06.png)

3. Créez une ressource JavaScript.

    ![](./media/app-insights-sample-mscrm/07.png)

    Donnez-lui un nom, sélectionnez **Script (JScript)** et ouvrez l’éditeur de texte.

    ![](./media/app-insights-sample-mscrm/08.png)
    
4. Copiez le code à partir d’Application Insights.

    ![](./media/app-insights-sample-mscrm/09.png)

    Le code contient la clé d’instrumentation qui identifie votre ressource Application Insights.

5. Enregistrez et publiez.

    ![](./media/app-insights-sample-mscrm/10.png)

### Instrumenter les formulaires

1. Dans Microsoft CRM Online, ouvrez le formulaire Compte.

    ![](./media/app-insights-sample-mscrm/11.png)

2. Ouvrez les propriétés de formulaire.

    ![](./media/app-insights-sample-mscrm/12.png)

3. Ajoutez la ressource web JavaScript que vous avez créée.

    ![](./media/app-insights-sample-mscrm/13.png)

    ![](./media/app-insights-sample-mscrm/14.png)

4. Enregistrez et publiez vos personnalisations de formulaire.


## Mesures capturées

Vous avez maintenant configuré la capture de télémétrie pour le formulaire. Chaque fois qu’il sera utilisé, des données seront envoyées à votre ressource Application Insights.

Voici des exemples de données qui seront affichées.

#### Intégrité d’application

![](./media/app-insights-sample-mscrm/15.png)

![](./media/app-insights-sample-mscrm/16.png)

Exceptions du navigateur :

![](./media/app-insights-sample-mscrm/17.png)

Cliquez sur le graphique pour obtenir plus de détails :

![](./media/app-insights-sample-mscrm/18.png)

#### Utilisation

![](./media/app-insights-sample-mscrm/19.png)

![](./media/app-insights-sample-mscrm/20.png)

![](./media/app-insights-sample-mscrm/21.png)

#### Navigateurs

![](./media/app-insights-sample-mscrm/22.png)

![](./media/app-insights-sample-mscrm/23.png)

#### Géolocalisation

![](./media/app-insights-sample-mscrm/24.png)

![](./media/app-insights-sample-mscrm/25.png)

#### Demande d’affichage de page

![](./media/app-insights-sample-mscrm/26.png)

![](./media/app-insights-sample-mscrm/27.png)

![](./media/app-insights-sample-mscrm/28.png)

![](./media/app-insights-sample-mscrm/29.png)

![](./media/app-insights-sample-mscrm/30.png)

## Power BI

Vous pouvez effectuer une analyse encore plus approfondie si vous [exportez les données vers Microsoft Power BI](app-insights-export-power-bi.md).

## En savoir plus

* [Présentation d’Application Insights](app-insights-overview.md)
* [Application Insights pour les pages web](app-insights-javascript.md)

 

<!---HONumber=July15_HO5-->