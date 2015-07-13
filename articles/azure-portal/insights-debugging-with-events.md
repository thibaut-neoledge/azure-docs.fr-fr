<properties 
	pageTitle="Affichage des événements et des journaux d'audit" 
	description="Découvrez comment afficher tous les événements qui se produisent dans votre abonnement Azure." 
	authors="HaniKN-MSFT" 
	manager="kamrani" 
	editor="" 
	services="azure-portal" 
	documentationCenter="na"/>

<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/28/2015" 
	ms.author="hanikn"/>

# Affichage des événements et des journaux d'audit

Toutes les opérations effectuées sur les ressources Azure sont entièrement auditées par Azure Resource Manager, de leur création jusqu’à leur suppression, pour accorder ou révoquer l'accès. Vous pouvez parcourir ces journaux dans le portail Azure, et également utiliser l’[API REST](https://msdn.microsoft.com/library/azure/dn931927.aspx) ou le [Kit de développement logiciel (SDK) .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) pour accéder à l'ensemble des événements par programmation.

## Recherchez les événements ayant un impact sur votre abonnement Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **Parcourir** et sélectionnez **Journaux d'Audit**. ![Parcourir le Hub](./media/insights-debugging-with-events/Insights_Browse.png)
3. Vous ouvrirez ainsi un panneau vous indiquant tous les événements qui ont eu un impact sur l’un de vos abonnements au cours des 7 derniers jours. Vous trouverez en haut du panneau un graphique vous indiquant les données par niveau et ci-dessous la liste complète des journaux : ![Tous les événements](./media/insights-debugging-with-events/Insights_AllEvents.png)

>[AZURE.NOTE]Vous ne pouvez afficher que les 500 derniers événements d’un abonnement donné dans le portail Azure.

4. Vous pouvez cliquer sur une entrée de journal pour afficher les événements qui la composent. Par exemple, lorsque vous déployez un élément vers un groupe de ressources, différentes ressources sont créées ou modifiées. Pour chaque entrée, vous pouvez découvrir :
    * Le **niveau** de l'événement : il peut par exemple s’agir d’un élément à suivre (**information**), ou d’un incident sur lequel vous souhaiteriez être informé (**erreur**). 
    * L’**état** : l'état final sera généralement **Succeeded** ou **Failed**, voire également **Accepted** pour les opérations de longue durée.
    * Le*moment* où l’événement s’est produit.
    * La *personne* qui a effectué l'opération, le cas échéant. Les opérations ne sont pas toutes effectuées par des utilisateurs. Certaines d’entre elles sont effectuées par des services principaux pour éviter d’avoir un **appelant**.
    * L’**ID de corrélation** de l'événement : c’est-à-dire l'identificateur unique de cet ensemble d'opérations.

5. Vous pouvez vous rendre ainsi dans le panneau des détails pour découvrir les détails de l'événement.
   
    ![Groupes de ressources](./media/insights-debugging-with-events/Insights_EventDetails.png)

    Pour les événements **Failed**, cette page comprend normalement une section **Substatus** et une section **Properties**, qui présentent des détails utiles pour le débogage.

## Filtrage des journaux spécifiques

Pour afficher les événements qui s'appliquent à une entité ou à un type spécifique, vous pouvez filtrer le panneau des journaux d’audit en cliquant sur la commande **Filtrer**. Vous pouvez également utiliser le panneau Filtrer pour modifier la **période** du panneau des journaux d’audit.

Dès que vous aurez cliqué sur cette commande, un nouveau panneau s'ouvrira :

![Filtre](./media/insights-debugging-with-events/Insights_EventFilter.png)

Il existe quatre types de filtres :

1. Par abonnement
2. Par **groupe de ressources**
3. Par **type de ressources**
4. Par **ressource** spécifique : vous devez pour cela coller l’*ID de ressource* de la ressource qui vous intéresse

Vous pouvez également filtrer les événements en fonction de la personne qui a effectué l'événement ou du niveau de l'événement.

Après avoir choisi ce que vous souhaitez afficher, cliquez sur le bouton **Mise à jour**, situé en bas du panneau.

## Surveillance des événements ayant un impact sur des ressources spécifiques

1. Cliquez sur **Parcourir** pour rechercher la ressource qui vous intéresse. Vous pouvez également afficher tous les journaux d’un **groupe de ressources**.
2. Sur le panneau des ressources, faites défiler l’écran vers le bas jusqu'à ce que vous trouviez la vignette des **événements**. ![Vignette d'événements](./media/insights-debugging-with-events/Insights_EventsTile.png)
3. Cliquez sur cette vignette pour n’afficher que les événements de la ressource que vous avez sélectionnée. Vous pouvez utiliser la commande **Filtrer** pour modifier la période ou appliquer des filtres plus spécifiques.

## Étapes suivantes

* [Réception de notifications d'alerte](insights-receive-alert-notifications.md) lorsqu’un événement se produit.
* [Surveillance des mesures de service](insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et réactif.
* [Suivi de l'intégrité du service](insights-service-health.md) pour déterminer à quel moment Azure a rencontré des interruptions de service ou une dégradation des performances.  

<!---HONumber=62-->