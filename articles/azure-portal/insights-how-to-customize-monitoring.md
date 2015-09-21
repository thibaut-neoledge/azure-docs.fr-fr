<properties 
	pageTitle="Surveillance des mesures de service" 
	description="Découvrez comment personnaliser les graphiques d'analyse dans Azure." 
	authors="stepsic-microsoft-com" 
	manager="ronmart" 
	editor="" 
	services="azure-portal"
documentationCenter=""/>

<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/08/2015" 
	ms.author="stepsic"/>

# Surveillance des mesures de service

Tous les services Azure assurent le suivi des mesures clés qui vous permettent de surveiller l’intégrité, les performances, la disponibilité et l'utilisation de vos services. Vous pouvez afficher ces mesures dans le portail Azure, et utiliser l’[API REST](https://msdn.microsoft.com/library/azure/dn931930.aspx) ou le [Kit de développement logiciel (SDK) .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) pour accéder à l'ensemble des mesures par programmation.

Pour certains services, vous devrez peut-être activer les diagnostics pour afficher les mesures. Pour d'autres, tels que des machines virtuelles, un ensemble de mesures de base vous sera proposé, mais vous devrez activer l’ensemble des mesures à fréquence élevée. Consultez la rubrique [Activation de la surveillance et des diagnostics](insights-how-to-use-diagnostics.md) pour en savoir plus.

## Utilisation des graphiques de surveillance 

Vous pouvez représenter l’une des mesures sur une période que vous choisissez.

1. Dans le [Portail Azure](https://portal.azure.com/), cliquez sur **Parcourir**, puis sur une ressource que vous voulez surveiller.

2. La rubrique **Surveillance** contient les mesures les plus importantes pour chaque ressource Azure. Une application Web dispose, par exemple, de l’option **Demandes et erreurs**, alors qu’une machine virtuelle posséderait **Pourcentage UC** et **Lecture et écriture sur le disque** : ![Filtre Monitoring](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)

3. Cliquez sur n'importe quel graphique pour afficher le panneau des **mesures**. Sur le panneau se trouve, en plus du graphique, un tableau qui affiche l'agrégation de ces mesures (comme la moyenne, le minimum et le maximum, la période que vous avez choisie). Retrouvez ci-dessous les règles d'alerte de la ressource. ![Volet Metric](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)

4. Pour personnaliser les lignes qui s'affichent, cliquez sur le bouton **Modifier** du graphique, ou la commande **Modifier le graphique** du panneau des mesures.

5. À partir du panneau Modifier la requête, vous pouvez accomplir trois actions :
    - Indiquer la période
    - Basculer entre le diagramme à bâtons et le diagramme linéaire
    - Choisir d’autres mesures ![Edit Query](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)

6. Pour modifier l'intervalle de temps, il suffit de sélectionner une autre plage (par exemple **Past Hour**) et de cliquer sur **Save** en bas du volet. Vous pouvez également sélectionner l’option **Personnalisée**, qui vous permet de choisir une période sur les deux dernières semaines. Vous pouvez, par exemple, afficher l'ensemble des deux dernières semaines ou simplement une heure la veille. Pour entrer une autre heure, tapez-la dans la zone de texte. ![Intervalle de temps personnalisé](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)

7. Sous l'intervalle de temps, vous pouvez choisir le nombre de mesures à afficher sur le graphique.

8. Dès lors que vous cliquerez sur Enregistrer, vos modifications seront enregistrées pour cette ressource. Si vous possédez, par exemple, deux machines virtuelles et que vous modifiez un graphique sur l’une d’entre elles, cette opération n’aura aucune incidence sur l'autre.

## Création de graphiques côte à côte

Grâce au niveau élevé de personnalisation du portail, vous pouvez ajouter autant de graphiques que vous le souhaitez.

1. Dans le menu **...**, situé en haut du panneau, cliquez sur **Ajouter des vignettes** : ![Ajouter un menu](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. Vous pouvez ensuite sélectionner un graphique à partir de la **Galerie**, située sur le côté droit de l'écran : ![Galerie](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. Si vous ne voyez pas les mesures souhaitées, vous pouvez toujours ajouter une des mesures prédéfinies et **Modifier** le graphique pour afficher les mesures dont vous avez besoin. 

## Surveillance des quotas d'utilisation

La plupart des mesures vous indiquent les tendances au fil du temps, mais certaines données, telles que les quotas d'utilisation, sont des informations limitées dans le temps et disposant d’un seuil.

Vous pouvez également découvrir les quotas d'utilisation sur le panneau des ressources qui disposent de quotas :

![Usage](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

Comme avec les mesures, vous pouvez utiliser l’[API REST](https://msdn.microsoft.com/library/azure/dn931963.aspx) ou le [Kit de développement logiciel (SDK) .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) pour accéder à l'ensemble des quotas d'utilisation par programmation.

## Étapes suivantes

* [Réception de notifications d'alerte](insights-receive-alert-notifications.md) lorsqu'une mesure dépasse un seuil.
* [Activation de la surveillance et des diagnostics](insights-how-to-use-diagnostics.md) pour collecter des mesures détaillées à fréquence élevée sur votre service.
* [Mise à l’échelle automatique du nombre d’instances](insights-how-to-scale.md) pour vous assurer que votre service est disponible et réactif.
* [Surveillance des performances d'une application](insights-perf-analytics.md) si vous voulez comprendre exactement comment votre code s'exécute dans le cloud.
* Utilisez [Application Insights pour les pages Web et les applications JavaScript](../app-insights-web-track-usage.md) pour obtenir une analyse client des navigateurs qui consultent une page Web.
* [Surveillance de la disponibilité et de la réactivité des pages Web](../app-insights-monitor-web-app-availability.md) avec Application Insights pour déterminer si vos pages sont inactives.
 

<!---HONumber=Sept15_HO2-->