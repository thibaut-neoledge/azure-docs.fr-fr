<properties 
   pageTitle="Connecteur Wait" 
   description="Connecteur Wait" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="06/29/2015"
   ms.author="rajram"/>

#Connecteur Wait
Le connecteur Wait permet à une application de retarder son exécution pour une durée spécifiée ou jusqu’à une heure spécifiée. Lorsqu’il est utilisé dans un flux, il permet de différer l’exécution.

##Utilisation du connecteur Wait
Pour utiliser le connecteur Wait, vous devez d’abord créer une instance de l’application API de ce connecteur. Cela est possible inline, lors de la création d’une application logique ou en sélectionnant l’application API du connecteur Wait à partir d’Azure Marketplace.

##Utilisation du connecteur Wait dans l’aire du concepteur Logic Apps
Le connecteur Wait peut être utilisé en tant qu’action. Il ne possède aucun déclencheur.

###Action
- Cliquez sur le connecteur Wait dans le volet de droite.

	![Liste d'actions][1]
- Le connecteur Wait prend en charge deux actions : 
	- Retarder
	- Retarder jusqu’à
	 
- Sélectionnez *Retarder*.

	![Valeur du retard][2]
- Indiquez la valeur de l’action et configurez-la.

	![Action configurée][3]

<table>
	<tr>
		<th>Paramètre</th>
		<th>Type</th>
		<th>Description du paramètre</th>
	</tr>
	<tr>
		<td>Durée en minutes</td>
		<td>integer</td>
		<td>Durée du retard en minutes</td>
	</tr>
</table>


## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Voir [Gérer et surveiller les applications API et le connecteur](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->
[1]: ./media/app-service-logic-wait/ListOfActions.PNG
[2]: ./media/app-service-logic-wait/DelayInput.PNG
[3]: ./media/app-service-logic-wait/ActionConfigured.PNG

<!---HONumber=July15_HO5-->