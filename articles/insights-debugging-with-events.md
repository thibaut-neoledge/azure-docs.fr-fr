<properties title="How to debug with events" pageTitle="How to debug with events" description="Learn how to see events in Azure." authors="hanikn" />

Surveillance des événements ayant un impact sur vos sites Web ou vos groupes de ressources Azure
================================================================================================

1.  Connectez-vous à la [version préliminaire du portail Azure](https://portal.azure.com/).

2.  Cliquez sur le bouton **Browse** de la barre de navigation de gauche (aussi appelée **barre d'index**).

	![Parcourir le Hub](./media/insights-debugging-with-events/Insights_Browse.png)

1.  Choisissez ensuite **Resource groups** ou **Websites** (selon les événements qui vous intéressent). Pour illustrer cette rubrique, les captures d'écran de ce document comprennent le groupe de ressources Event.

2.  Sur le volet **Resource groups**, cliquez sur le nom du groupe de ressources. Ceci ouvre le volet du groupe de ressources.

    ![Groupes de ressources](./media/insights-debugging-with-events/Insights_SelectRG.png)

3.  Le volet du groupe de ressources comprend une partie **Events in the past week**. Chacune des barres de cette partie représente le nombre d'événements qui se sont produits chaque jour de la semaine écoulée. Les barres peuvent être de deux couleurs : bleu et rose. Le rose représente les événements **Failed** de la journée et le bleu représente tous les autres événements.

	![Groupes de ressources](./media/insights-debugging-with-events/Insights_RGBlade.png)

1.  Maintenant, cliquez sur la partie **Events in the past week**. Un nouveau volet s'affiche, appelé **Events**. Il comprend tous les événements de la semaine écoulée qui ont eu un impact sur votre groupe de ressources.

	![Groupes de ressources](./media/insights-debugging-with-events/Insights_AllEvents.png)

1.  Cliquez sur l'un des événements.

	![Groupes de ressources](./media/insights-debugging-with-events/Insights_EventDetails.png)

Un nouveau volet s'affiche, avec des détails sur l'événement. Pour les événements **Failed**, cette page comprend normalement une section **Substatus** et une section **Properties**, qui présentent des détails utiles pour le débogage.

