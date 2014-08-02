<properties title="How to customize monitoring" pageTitle="How to customize monitoring" description="Learn how to customize monitoring charts in Azure." authors="stepsic" />

Personnalisation de la surveillance
===================================

La version préliminaire du portail Azure propose plus d'options d'affichage de vos données de surveillance, dont la personnalisation des intervalles de temps et un plus grand choix de mesures.

1.  Dans la [version préliminaire du portail Azure](https://portal.azure.com/), cliquez sur **Browse**, puis sur **Web Sites** et cliquez sur le nom d'un site Web pour ouvrir le volet correspondant.

2.  Sous l'objectif **Monitoring**, vous disposez des options liées aux demandes, aux erreurs, aux [tests Web](http://go.microsoft.com/fwlink/?LinkID=394528&clcid=0x409) et aux [analyses](http://go.microsoft.com/fwlink/?LinkID=394529&clcid=0x409). Cliquez sur **Requests and errors today** pour afficher le volet **Metric**.

    ![Objectif Monitoring](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)

3.  Le volet **Metric** affiche les détails des mesures que vous sélectionnez. En haut du volet se trouve un graphe et en dessous un tableau qui affiche l'agrégation de ces mesures, comme la moyenne, le minimum et le maximum. En dessous apparaît la liste des alertes que vous avez définies, filtrées en fonction des mesures affichées sur le volet. Cela permet, si vous avez un grand nombre d'alertes, de n'afficher ici que celles dont vous avez besoin. Vous pouvez revenir à la vue générale des alertes pour votre site Web en cliquant sur la zone **Alert rules** sur le volet **Web site**.

    ![Volet Metric](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)

4.  Pour personnaliser les mesures affichées, cliquez avec le bouton droit sur le graphique et sélectionnez **Edit Query** :

    ![Edit Query](./media/insights-how-to-customize-monitoring/Insights_MetricMenu.png)

5.  À partir du volet Edit Query vous pouvez accomplir deux actions : modifier l'intervalle de temps et choisir d'autres mesures.

    ![Edit Query](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)

6.  Pour modifier l'intervalle de temps, il suffit de sélectionner une autre plage (par exemple **Past Hour**) et de cliquer sur **Save** en bas du volet. Nouveauté de la version préliminaire du portail, vous pouvez choisir **Custom** :

	![Intervalle de temps personnalisé](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)

1.  Custom permet de choisir une période sur les deux dernières semaines. Vous pouvez, par exemple, afficher l'ensemble des deux dernières semaines ou simplement une heure la veille. Pour entrer une autre heure, tapez-la dans la zone de texte.

2.  Sous l'intervalle de temps, vous pouvez choisir le nombre de mesures à afficher sur le graphique. De nouvelles mesures sont disponibles : **Memory working set** et **Average memory working set**.

3.  Lorsque vous avez cliqué sur Save, vos modifications sont conservées jusqu'à la fermeture du volet du site Web. À votre prochain retour, la mesure et l'intervalle de temps d'origine sont de nouveau affichées.

Surveillance des plans d'hébergement Web
----------------------------------------

Autre nouveauté de la version préliminaire du portail Azure : la possibilité de surveiller les mesures de performances des instances sur lesquelles vos sites Web s'exécutent. Pour accéder à ces mesures, cliquez sur l'icône Web Hosting Plan de l'objectif Summary.

![Plan d'hébergement Web](./media/insights-how-to-customize-monitoring/Insights_WHPSelect.png)

Le graphique de l'objectif **Monitoring**, présenté ci-dessus, fonctionne comme celui du volet du site Web, mais comporte les nouvelles mesures suivantes : - CPU Percentage - Memory Percentage - HTTP Queue Depth - Disk Queue Depth

Création de graphiques côte à côte
----------------------------------

Le niveau élevé de personnalisation par l'utilisateur de la version préliminaire du portail Azure vous permet de créer des graphiques côte à côte.

1.  Cliquez avec le bouton droit sur le graphique de départ et sélectionnez **Customize**.

    ![Personnaliser un graphique](./media/insights-how-to-customize-monitoring/Insights_Customize.png)

2.  Cliquez sur **Clone** dans le menu **...** pour copier la partie concernée.

    ![Cloner la partie](./media/insights-how-to-customize-monitoring/Insights_ClonePart.png)

3.  Pour terminer, cliquez sur **Done** sur la barre d'outils en haut de l'écran. Vous pouvez désormais traiter cette partie comme une partie de mesures classique. Si vous cliquez avec le bouton droit et que vous modifiez la mesure affichée, vous pouvez voir deux mesures différentes affichées en même temps côte à côte :

    ![Deux mesures côte à côte](./media/insights-how-to-customize-monitoring/Insights_SideBySide.png)

Notez que l'intervalle de temps et les mesures choisies pour le graphique sont réinitialisées lorsque vous quittez le portail.

