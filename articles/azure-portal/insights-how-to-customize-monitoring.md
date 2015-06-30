<properties 
	pageTitle="Personnalisation de la surveillance" 
	description="Découvrez comment personnaliser les graphiques d'analyse dans Azure." 
	authors="alancameronwills" 
	manager="kamrani" 
	editor="" 
	services="application-insights" 
	documentationCenter=""/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2014-11-04" 
	ms.author="awills"/>

# Personnalisation de la surveillance

Votre application Azure possède plusieurs mesures que vous pouvez surveiller et représenter sur une période que vous choisissez.

1. Dans la [version préliminaire du portail Azure](https://portal.azure.com/), cliquez sur **Parcourir**, puis sur une ressource que vous souhaitez surveiller.
2. Le filtre **Surveillance** contient les mesures les plus importantes pour chaque ressource Azure. Par exemple, les sites web proposent des options pour les demandes, les erreurs, les [tests web](http://go.microsoft.com/fwlink/?LinkID=394528&clcid=0x409) et les [analyses](http://go.microsoft.com/fwlink/?LinkID=394529&clcid=0x409). Cliquez sur **Demandes et erreurs aujourd'hui** pour afficher le volet **Métrique**.  
    ![Monitoring lens](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)
3. Le volet **Métrique** affiche les détails des mesures que vous sélectionnez. En haut du volet se trouve un graphe et en dessous un tableau qui affiche l'agrégation de ces mesures, comme la moyenne, le minimum et le maximum. En dessous apparaît la liste des alertes que vous avez définies, filtrées en fonction des mesures affichées dans le volet. Cela permet, si vous avez un grand nombre d'alertes, de n'afficher ici que celles dont vous avez besoin. Vous pouvez revenir à la vue générale des alertes pour votre site web en cliquant sur la zone **Règles d'alerte** dans le volet **Site web**.  
    ![Metric blade](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)
4. Pour personnaliser les mesures affichées, cliquez avec le bouton droit sur le graphique et sélectionnez **Modifier la requête** :  
    ![Edit Query](./media/insights-how-to-customize-monitoring/Insights_MetricMenu.png)
5. À partir du volet Modifier la requête, vous pouvez accomplir deux actions : modifier l'intervalle de temps et choisir d'autres mesures.  
    ![Edit Query](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)
6. Pour modifier l'intervalle de temps, il suffit de sélectionner une autre plage (par exemple **Dernière heure**) et de cliquer sur **Enregistrer** en bas du volet. Nouveauté de la version préliminaire du portail, vous pouvez désormais choisir l'option **Personnalisée** :  
    ![Custom time range](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)
7. L'option Personnalisée permet de choisir une période sur les deux dernières semaines. Vous pouvez, par exemple, afficher l'ensemble des deux dernières semaines ou simplement une heure la veille. Pour entrer une autre heure, tapez-la dans la zone de texte.
8. Sous l'intervalle de temps, vous pouvez choisir le nombre de mesures à afficher sur le graphique. De nouvelles mesures sont disponibles : **Kit de traitement de la mémoire** et **Kit de traitement de la mémoire moyenne**.

9. Une fois que vous avez cliqué sur Enregistrer, vos modifications sont conservées jusqu'à la fermeture du panneau du site web. La mesure et l'intervalle de temps d'origine s'affichent de nouveau lorsque vous rouvrez le panneau.

## Surveillance de nouvelles ressources

La version préliminaire du portail Azure offre désormais la possibilité de surveiller les mesures de performance de différentes nouvelles ressources, y compris :
- Les plans d'hébergement web
- Le cache Redis
- Le compte DocumentDB

Les plans d'hébergement web sont un peu plus compliqués que les autres ressources, car ils représentent les performances des instances sur lesquelles s'exécutent vos **sites web**. Pour accéder aux mesures du plan d'hébergement web, cliquez sur l'icône Plan d'hébergement web du filtre Synthèse de votre site web.

![Web hosting plan](./media/insights-how-to-customize-monitoring/Insights_WHPSelect.png)

Le graphique du filtre **Surveillance** présenté ci-dessus fonctionne comme celui du panneau du site web, mais comporte les nouvelles mesures suivantes :

- Pourcentage UC
- Pourcentage de mémoire
- Profondeur de file d'attente HTTP
- Profondeur de file d'attente de disque

## Création de graphiques côte à côte

Le niveau élevé de personnalisation par l'utilisateur de la version préliminaire du portail Azure vous permet de créer des graphiques côte à côte.

1. Tout d'abord, cliquez avec le bouton droit sur le graphique de départ et sélectionnez **Personnaliser.**  
    ![Customize chart](./media/insights-how-to-customize-monitoring/Insights_Customize.png)
2. Cliquez ensuite sur **Cloner** dans le menu **...** pour copier la section.  
    ![Clone part](./media/insights-how-to-customize-monitoring/Insights_ClonePart.png)
3. Pour terminer, cliquez sur **Terminé** sur la barre d'outils en haut de l'écran. Vous pouvez désormais traiter cette partie comme une partie de mesures classique. Si vous cliquez avec le bouton droit et que vous modifiez la mesure affichée, vous pouvez voir deux mesures différentes affichées en même temps côte à côte :  
    ![Two metrics Side by Side](./media/insights-how-to-customize-monitoring/Insights_SideBySide.png)

Notez que l'intervalle de temps et les mesures choisies pour le graphique sont réinitialisées lorsque vous quittez le portail.


<!--HONumber=46--> 
 