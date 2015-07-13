<properties 
   pageTitle="Encodeur JSON BizTalk" 
   description="Encodeur JSON BizTalk" 
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
   ms.date="06/14/2015"
   ms.author="rajram"/>

#Encodeur JSON BizTalk
Le connecteur Encodeur Décodeur JSON BizTalk facilite l'interopérabilité de votre application entre les données JSON et XML. Il peut convertir une instance JSON donnée au format XML et vice versa.

##Utilisation de l'encodeur JSON BizTalk
Pour utiliser l'encodeur JSON BizTalk, vous devez d'abord créer une instance de l'application API d'encodeur JSON BizTalk. Cela est possible inline, lors de la création d'une application logique, ou en sélectionnant l'application API d'encodeur JSON BizTalk dans Azure Marketplace.

##Utilisation de l'encodeur JSON BizTalk dans l'aire du concepteur d'applications logiques
L'encodeur JSON BizTalk peut être utilisé en tant qu'action. Il ne possède aucun déclencheur.

###Action
- Cliquez sur l'encodeur JSON BizTalk dans le volet de droite

	![Paramètres d'action][3]
- Cliquez sur ->

	![Liste d'actions][4]
- L'encodeur JSON BizTalk ne prend en charge qu'une seule action. Sélectionnez *Xml à JSON*

	![Entrée XML à JSON][5]
- Indiquez les entrées de l'action et configurez celle-ci

	![Encoder et envoyer configuré][6]

<table>
	<tr>
		<th>Paramètre</th>
		<th>Type</th>
		<th>Description du paramètre</th>
	</tr>
	<tr>
		<td>XML d'entrée</td>
		<td>objet</td>
		<td>Contenu du XML d’entrée</td>
	</tr>
	<tr>
		<td>Supprimer l'enveloppe extérieure</td>
		<td>string</td>
		<td>Indicateur pour supprimer le nœud racine du contenu Xml</td>
	</tr>
</table>

L'action retourne une représentation JSON du contenu d'entrée.

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Voir [Gérer et surveiller les applications API et le connecteur](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->
[1]: app-service-logic-connector-tpm
[2]: app-service-logic-create-a-trading-partner-agreement
[3]: ./media/app-service-logic-json-encoder/ActionSettings.PNG
[4]: ./media/app-service-logic-json-encoder/ListOfActions.PNG
[5]: ./media/app-service-logic-json-encoder/EncodeInput.PNG
[6]: ./media/app-service-logic-json-encoder/EncodeConfigured.PNG

<!---HONumber=62-->