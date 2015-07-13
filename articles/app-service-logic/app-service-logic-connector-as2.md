<properties 
   pageTitle="Connecteur AS2" 
   description="Connecteur AS2" 
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

#Connecteur AS2
Le connecteur Microsoft Azure AS2 permet de recevoir et d'envoyer des messages selon le protocole de transport AS2 dans les communications interentreprises. AS2 signifie « Applicability Statement 2 ». Les données sont transportées de manière fiable et sécurisée sur Internet. Le chiffrement et les certificats numériques garantissent la sécurité.

##Conditions préalables
- Application API TPM : avant de créer un connecteur AS2, vous devez créer un [connecteur de gestion des partenaires commerciaux BizTalk][1].
- Base de données SQL Azure : chacune des applications API B2B requiert sa propre base de données SQL Azure.
- Conteneur de stockage d'objets Blob Azure : stocke les propriétés de message quand l'archivage AS2 est activé. Si vous n'avez pas besoin de l'archivage des messages AS2, un conteneur de stockage n'est pas utile. 

##Utilisation du connecteur AS2
Pour utiliser le connecteur AS2, vous devez d'abord créer une instance de l'application API du connecteur AS2. Cela est possible inline, lors de la création d'une application logique ou en sélectionnant l'application API du connecteur AS2 à partir d'Azure Marketplace.

##Configuration du connecteur AS2
Les partenaires commerciaux sont des entités impliquées dans des communications B2B (entreprise à entreprise). Lorsque deux partenaires établissent une relation, il est question d'un « accord ». L’accord défini est basé sur le type de communication dont les deux partenaires souhaitent bénéficier. Il est propre au protocole ou au transport.

Les étapes de création d'un accord de partenariat commercial sont documentées [ici][2].

##Utilisation du connecteur AS2 dans l'aire du concepteur d'applications logiques
Vous pouvez utiliser le connecteur AS2 comme un déclencheur ou une action.

###Déclencheur
- Lancez le concepteur de flux des applications logiques Azure
- Cliquez sur le connecteur AS2 dans le panneau de droite

	![Paramètres du déclencheur][3]
- Cliquez sur ->

	![Options du déclencheur][4]
- Le connecteur AS2 expose un seul déclencheur. Sélectionnez *Recevoir et décoder*

	![Entrée Recevoir et décoder][5]
- Ce déclencheur ne possède aucune entrée. Cliquez sur ->

	![Recevoir et décoder configuré][6]
- Dans le cadre de la sortie, le connecteur renvoie la charge AS2, ainsi que les métadonnées spécifiques AS2.

###Action
- Cliquez sur le connecteur AS2 dans le panneau de droite

	![Paramètres d'action][7]
- Cliquez sur ->

	![Liste d'actions][8]
- Le connecteur AS2 ne prend en charge qu'une seule action. Sélectionnez *Encoder et envoyer*

	![Entrée Encoder et envoyer][9]
- Indiquez les entrées de l'action et configurez celle-ci

	![Encoder et envoyer configuré][10]

<table>
	<tr>
		<th>Paramètre</th>
		<th>Type</th>
		<th>Description du paramètre</th>
	</tr>
	<tr>
		<td>Payload</td>
		<td>objet</td>
		<td>Payload</td>
	</tr>
	<tr>
		<td>AS2 à partir de</td>
		<td>string</td>
		<td>AS2 à partir de</td>
	</tr>
	<tr>
		<td>AS2 vers</td>
		<td>string</td>
		<td>AS2 vers</td>
	</tr>
	<tr>
		<td>URL du partenaire</td>
		<td>string</td>
		<td>URL du partenaire</td>
	</tr>
	<tr>
		<td>Activer l'archivage</td>
		<td>booléenne</td>
		<td>Activer l'archivage</td>
	</tr>
</table>

L'action retourne un code de réponse HTTP 200 en cas de réussite.

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Voir [Gérer et surveiller les applications API et le connecteur](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->
[1]: app-service-logic-connector-tpm.md
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-as2/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-as2/TriggerOptions.PNG
[5]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeInput.PNG
[6]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeConfigured.PNG
[7]: ./media/app-service-logic-connector-as2/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-as2/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-as2/EncodeAndSendInput.PNG
[10]: ./media/app-service-logic-connector-as2/EncodeAndSendConfigured.PNG

<!---HONumber=62-->