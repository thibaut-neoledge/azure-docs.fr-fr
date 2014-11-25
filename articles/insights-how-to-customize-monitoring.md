<properties title="How to customize monitoring" pageTitle="How to customize monitoring" description="Learn how to customize monitoring charts in Azure." authors="stepsic"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="stepsic" />

# Personnalisation de la surveillance

La version préliminaire du portail Azure propose plus d'options d'affichage de vos données de surveillance, dont la personnalisation des intervalles de temps et un plus grand choix de mesures.

1.  Dans la [version préliminaire du portail Azure][version préliminaire du portail Azure], cliquez sur **Browse**, puis sur une ressource de surveillance qui vous intéresse.
2.  Le filtre **Monitoring** contient les mesures les plus importantes pour chaque ressource Azure. Par exemple, les sites web proposent des options pour les demandes, les erreurs, les [tests web][tests web] et les [analyses][analyses]. Cliquez sur **Requests and errors today** pour afficher le volet **Metric**.  
    ![Filtre Monitoring][Filtre Monitoring]
3.  Le volet **Metric** affiche les détails des mesures que vous sélectionnez. En haut du volet se trouve un graphe et en dessous un tableau qui affiche l'agrégation de ces mesures, comme la moyenne, le minimum et le maximum. En dessous apparaît la liste des alertes que vous avez définies, filtrées en fonction des mesures affichées dans le volet. Cela permet, si vous avez un grand nombre d'alertes, de n'afficher ici que celles dont vous avez besoin. Vous pouvez revenir à la vue générale des alertes pour votre site web en cliquant sur la zone **Alert rules** dans le volet **Web site**.  
    ![Volet Metric][Volet Metric]
4.  Pour personnaliser les mesures affichées, cliquez avec le bouton droit sur le graphique et sélectionnez **Edit Query** :  
    ![Edit Query][Edit Query]
5.  À partir du volet Edit Query vous pouvez accomplir deux actions : modifier l'intervalle de temps et choisir d'autres mesures.  
    ![Edit Query][1]
6.  Pour modifier l'intervalle de temps, il suffit de sélectionner une autre plage (par exemple **Past Hour**) et de cliquer sur **Save** en bas du volet. Nouveauté de la version préliminaire du portail, vous pouvez choisir **Custom** :  
    ![Intervalle de temps personnalisé][Intervalle de temps personnalisé]
7.  Custom permet de choisir une période sur les deux dernières semaines. Vous pouvez, par exemple, afficher l'ensemble des deux dernières semaines ou simplement une heure la veille. Pour entrer une autre heure, tapez-la dans la zone de texte.
8.  Sous l'intervalle de temps, vous pouvez choisir le nombre de mesures à afficher sur le graphique. De nouvelles mesures sont disponibles : **Memory working set** et **Average memory working set**.

9.  Lorsque vous avez cliqué sur Save, vos modifications sont conservées jusqu'à la fermeture du volet du site web. À votre prochain retour, la mesure et l'intervalle de temps d'origine sont de nouveau affichées.

## Surveillance de nouvelles ressources

La version préliminaire du portail Azure offre désormais la possibilité de surveiller les mesures de performance de différentes nouvelles ressources, y compris :

-   Les plans d'hébergement web
-   Le cache Redis
-   Le compte DocumentDB

Les plans d'hébergement web sont un peu plus compliqués que les autres ressources, car ils représentent les performances des instances sur lesquelles s'exécutent vos **sites web**. Pour accéder aux mesures de plan d'hébergement web, cliquez sur l'icône Web Hosting Plan du filtre Summary de votre site web.

![Plan d'hébergement web][Plan d'hébergement web]

Le graphique de l'objectif **Monitoring**, présenté ci-dessus, fonctionne comme celui du volet du site web, mais comporte les nouvelles mesures suivantes :

-   Pourcentage UC
-   Pourcentage de mémoire
-   Profondeur de file d'attente HTTP
-   Profondeur de file d'attente de disque

## Création de graphiques côte à côte

Le niveau élevé de personnalisation par l'utilisateur de la version préliminaire du portail Azure vous permet de créer des graphiques côte à côte.

1.  Tout d'abord, cliquez avec le bouton droit sur le graphique de départ et sélectionnez **Customize**  
    ![.Personnaliser un graphique][.Personnaliser un graphique]
2.  Cliquez sur **Clone** dans le menu **...** pour copier la partie concernée.  
    ![Cloner la partie][Cloner la partie]
3.  Pour terminer, cliquez sur **Done** sur la barre d'outils en haut de l'écran. Vous pouvez désormais traiter cette partie comme une partie de mesures classique. Si vous cliquez avec le bouton droit et que vous modifiez la mesure affichée, vous pouvez voir deux mesures différentes affichées en même temps côte à côte :  
    ![Deux mesures côte à côte][Deux mesures côte à côte]

Notez que l'intervalle de temps et les mesures choisies pour le graphique sont réinitialisées lorsque vous quittez le portail.

  [version préliminaire du portail Azure]: https://portal.azure.com/
  [tests web]: http://go.microsoft.com/fwlink/?LinkID=394528&clcid=0x409
  [analyses]: http://go.microsoft.com/fwlink/?LinkID=394529&clcid=0x409
  [Filtre Monitoring]: ./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png
  [Volet Metric]: ./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png
  [Edit Query]: ./media/insights-how-to-customize-monitoring/Insights_MetricMenu.png
  [1]: ./media/insights-how-to-customize-monitoring/Insights_EditQuery.png
  [Intervalle de temps personnalisé]: ./media/insights-how-to-customize-monitoring/Insights_CustomTime.png
  [Plan d'hébergement web]: ./media/insights-how-to-customize-monitoring/Insights_WHPSelect.png
  [.Personnaliser un graphique]: ./media/insights-how-to-customize-monitoring/Insights_Customize.png
  [Cloner la partie]: ./media/insights-how-to-customize-monitoring/Insights_ClonePart.png
  [Deux mesures côte à côte]: ./media/insights-how-to-customize-monitoring/Insights_SideBySide.png
