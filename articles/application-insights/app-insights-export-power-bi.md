---
title: "Exportation vers Power BI à partir d’Application Insights | Microsoft Docs"
description: "Les requêtes Analytics peuvent être affichées dans Power BI."
services: application-insights
documentationcenter: 
author: noamben
manager: douge
ms.assetid: 7f13ea66-09dc-450f-b8f9-f40fdad239f2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 7a9c40081f52b2ffe918f4612f790f7fd08acc5a
ms.openlocfilehash: dba7f1ac2257246f14acf79de6e9fc714689b276


---
# <a name="feed-power-bi-from-application-insights"></a>Alimentation de Power BI à partir d’Application Insights
[Power BI](http://www.powerbi.com/) est une suite d’outils d’analyse métiers permettant d’analyser les données et de partager les informations. Chaque périphérique bénéficie de tableaux de bord riches. Vous pouvez combiner des données provenant de nombreuses sources, notamment des requêtes Analytics d’[Azure Application Insights](app-insights-overview.md).

Pour exporter des données d’Application Insights vers Power BI, nous vous recommandons trois méthodes. Vous pouvez les utiliser séparément ou ensemble.

* [**Adaptateur Power BI**](#power-pi-adapter) : configurez un tableau de bord complet des données de télémétrie à partir de votre application. L’ensemble de graphiques est prédéfini, mais vous pouvez ajouter vos propres requêtes à partir d’autres sources.
* [**Exporter des requêtes Analytics**](#export-analytics-queries) : écrivez une requête souhaitée à l’aide d’Analytics et exportez-la vers Power BI. Vous pouvez placer cette requête sur un tableau de bord, avec d’autres données.
* [**Exportation continue et Stream Analytics**](app-insights-export-stream-analytics.md) : implique un travail supplémentaire de configuration. Cela est utile si vous souhaitez conserver vos données pendant de longues périodes. Sinon, les autres méthodes sont recommandées.

## <a name="power-bi-adapter"></a>Adaptateur Power BI
Cette méthode crée un tableau de bord complet des données de télémétrie. Le jeu de données initial est prédéfini, mais vous pouvez y ajouter plus de données.

### <a name="get-the-adapter"></a>Obtenir l’adaptateur
1. Connectez-vous à [Power BI](https://app.powerbi.com/).
2. Ouvrez **Obtenir des données**, **Services**, **Application Insights**
   
    ![Obtenir à partir d’une source de données Application Insights](./media/app-insights-export-power-bi/power-bi-adapter.png)
3. Fournissez les détails de votre ressource Application Insights.
   
    ![Obtenir à partir d’une source de données Application Insights](./media/app-insights-export-power-bi/azure-subscription-resource-group-name.png)
4. Patientez une minute à deux minutes avant l’importation des données.
   
    ![Adaptateur Power BI](./media/app-insights-export-power-bi/010.png)

Vous pouvez modifier le tableau de bord en associant des graphiques Application Insights avec ceux d’autres sources et avec des requêtes Analytics. Il existe une galerie de visualisation où vous pouvez obtenir plus de graphiques. Chaque graphique comporte des paramètres que vous pouvez définir.

Après l’importation initiale, le tableau de bord et les rapports sont mis à jour quotidiennement. Vous pouvez contrôler la planification de l’actualisation du jeu de données.

## <a name="export-analytics-queries"></a>Exporter des requêtes Analytics
Cet itinéraire permet d’écrire la requête Analytics souhaitée, puis de l’exporter vers un tableau de bord Power BI. (Vous pouvez ajouter au tableau de bord créé par l’adaptateur).

### <a name="one-time-install-power-bi-desktop"></a>Une fois : installez Power BI Desktop
Pour importer votre requête Application Insights, vous utilisez la version pour ordinateur de bureau de Power BI. Ensuite vous pouvez la publier sur le web ou vers votre espace de travail cloud Power BI. 

Installez [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Exporter une requête Analytics
1. [Ouvrez Analytics et écrivez votre requête](app-insights-analytics-tour.md).
2. Testez et affinez la requête jusqu'à ce que vous soyez satisfait des résultats.

   **Vérifiez que la requête s’exécute correctement dans Analytics avant de l’exporter.**
3. Dans le menu **Exporter**, choisissez **Power BI (M)**. Enregistrez le fichier texte.
   
    ![Exporter une requête Power BI](./media/app-insights-export-power-bi/analytics-export-power-bi.png)
4. Dans Power BI Desktop, sélectionnez **Obtenir des données, Requête vide** puis, dans l’éditeur de requête, sous **Affichage**, sélectionnez **Éditeur de requêtes avancé**.

    Collez le script de langage M exporté dans l’Éditeur de requêtes avancé.

    ![Éditeur de requêtes avancé](./media/app-insights-export-power-bi/power-bi-import-analytics-query.png)

1. Vous devrez peut-être fournir des informations d’identification pour autoriser Power BI à accéder à Azure. Utilisez « Compte professionnel » pour vous connecter avec votre compte Microsoft.
   
    ![Fournissez les informations d’identification Azure pour permettre à Power BI d’exécuter votre requête Application Insights.](./media/app-insights-export-power-bi/power-bi-import-sign-in.png)

    (Si vous devez vérifier les informations d’identification, utilisez la commande de menu Paramètres de la source de données dans l’Éditeur de requête. Veillez à spécifier les informations d’identification que vous utilisez pour Azure, qui peuvent-être différentes de vos informations d’identification pour Power BI.)
2. Choisissez une visualisation de votre requête et sélectionnez les champs pour l’axe x, l’axe y et la dimension de segmentation.
   
    ![Sélectionner une visualisation](./media/app-insights-export-power-bi/power-bi-analytics-visualize.png)
3. Publiez votre rapport sur votre espace de travail cloud Power BI. À partir de là, vous pouvez incorporer une version synchronisée dans d’autres pages web.
   
    ![Sélectionner une visualisation](./media/app-insights-export-power-bi/publish-power-bi.png)
4. Actualisez le rapport manuellement selon des intervalles ou configurez une actualisation planifiée dans la page des options.

## <a name="about-sampling"></a>À propos de l’échantillonnage
Si votre application envoie beaucoup de données, la fonctionnalité d’échantillonnage adaptatif peut fonctionner et envoyer seulement un pourcentage de vos données de télémétrie. Il en est de même si vous avez défini manuellement l’échantillonnage dans le Kit SDK ou sur ingestion. [En savoir plus sur l’échantillonnage.](app-insights-sampling.md)

## <a name="next-steps"></a>Étapes suivantes
* [Power BI - En savoir plus](http://www.powerbi.com/learning/)
* [Didacticiel Analytics](app-insights-analytics-tour.md)




<!--HONumber=Nov16_HO3-->


