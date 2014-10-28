<properties title="How to debug with events" pageTitle="How to debug with events" description="Learn how to see events in Azure." authors="hanikn"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="hanikn"></tags>

# Surveillance des événements ayant un impact sur vos ressources ou vos groupes de ressources Azure

1.  Connectez-vous à la [version préliminaire du portail Azure][version préliminaire du portail Azure].
2.  Cliquez sur le bouton **Parcourir** de la barre de navigation de gauche (aussi appelée **barre d'index**).  
    ![Hub Parcourir][Hub Parcourir]
3.  Ensutie, choisissez une ressource quelconque (selon les événements qui vous intéressent). Pour illustrer cette rubrique, les captures d'écran de ce document comprennent le groupe de ressources Event.
4.  Sur le volet **Resource groups**, cliquez sur le nom du groupe de ressources. Ceci ouvre le volet du groupe de ressources.  
    ![Groupe de ressources][Groupe de ressources]
5.  Le volet du groupe de ressources comprend une partie **Events in the past week**. Chacune des barres de cette partie représente le nombre d'événements qui se sont produits chaque jour de la semaine écoulée. Les barres peuvent être de deux couleurs : bleu et rose. Le rose représente les événements **Failed** de la journée et le bleu représente tous les autres événements.  
    ![Groupes de ressources][Groupes de ressources]
6.  Maintenant, cliquez sur la partie **Events in the past week**. Un nouveau volet s'affiche, appelé **Events**. Il comprend tous les événements de la semaine écoulée qui ont eu un impact sur votre groupe de ressources.
    ![Groupes de ressources][1]
7.  Cliquez sur l'un des événements.  
    ![Groupes de ressources][2]  
    Un nouveau volet s'affiche, avec des détails sur l'événement. Pour les événements **Failed**, cette page comprend normalement une section **Substatus** et une section **Properties**, qui présentent des détails utiles pour le débogage.

  [version préliminaire du portail Azure]: https://portal.azure.com/
  [Hub Parcourir]: ./media/insights-debugging-with-events/Insights_Browse.png
  [Groupe de ressources]: ./media/insights-debugging-with-events/Insights_SelectRG.png
  [Groupes de ressources]: ./media/insights-debugging-with-events/Insights_RGBlade.png
  [1]: ./media/insights-debugging-with-events/Insights_AllEvents.png
  [2]: ./media/insights-debugging-with-events/Insights_EventDetails.png
