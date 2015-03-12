<properties 
	pageTitle="Débogage avec des événements" 
	description="Découvrez comment afficher les événements dans Azure." 
	authors="HaniKN-MSFT" 
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
	ms.date="11/24/2014" 
	ms.author="hanikn"/>

# Surveillance des événements ayant un impact sur vos ressources ou vos groupes de ressources Azure

1. Connectez-vous à la [version préliminaire du portail Azure](https://portal.azure.com/).
2. Cliquez sur le bouton **Parcourir** dans la barre de navigation de gauche (aussi appelée **barre d'index**).  
    ![Browse Hub](./media/insights-debugging-with-events/Insights_Browse.png)
3. Ensuite, choisissez une ressource (selon les événements qui vous intéressent). Pour illustrer cette rubrique, les captures d'écran de ce document comprennent les événements des groupes de ressources.
4. Dans le panneau **Groupes de ressources**, cliquez sur le nom du groupe de ressources. Cela permet d'ouvrir le panneau du groupe de ressources.  
    ![Resource groups](./media/insights-debugging-with-events/Insights_SelectRG.png)
5. Le panneau du groupe de ressources comprend une partie **Événements au cours de la dernière semaine**. Chacune des barres de cette partie représente le nombre d'événements qui se sont produits chaque jour de la semaine écoulée. Les barres peuvent être de deux couleurs : bleu et rose. Le rose représente les événements **non réussis** de la journée et le bleu représente tous les autres événements.  
    ![Resource groups](./media/insights-debugging-with-events/Insights_RGBlade.png)
6. Maintenant, cliquez sur la partie **Événements au cours de la dernière semaine**. Un nouveau panneau s'affiche, appelé **Événements**. Il comprend tous les événements de la semaine écoulée qui ont eu un impact sur votre groupe de ressources.
    ![Resource groups](./media/insights-debugging-with-events/Insights_AllEvents.png)
7. Cliquez sur l'un des événements.  
    ![Resource groups](./media/insights-debugging-with-events/Insights_EventDetails.png)  
    Un nouveau panneau s'affiche, avec des détails concernant l'événement. Pour les événements **non réussis**, cette page comprend normalement une section **Sous-statut** et une section **Propriétés**, qui présentent des détails utiles pour le débogage.


<!--HONumber=46--> 
