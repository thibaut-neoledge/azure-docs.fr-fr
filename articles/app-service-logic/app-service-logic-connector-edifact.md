<properties 
   pageTitle="Connecteur BizTalk Edifact"
	description="Connecteur BizTalk Edifact"
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
	ms.date="08/19/2015"
	ms.author="rajram"/>

# Connecteur BizTalk Edifact
Le service Microsoft Azure Edifact permet de recevoir et d'envoyer des messages selon le protocole Edifact dans les communications interentreprises. Edifact est également communément appelé ASC Edifact ou Accredited Standards Committee Edifact. Il est largement utilisé dans les entreprises.

## Conditions préalables
- Application API TPM : avant de créer un connecteur Edifact, vous devez créer un [connecteur de gestion des partenaires commerciaux BizTalk][1].
- Base de données SQL Azure : chacune des applications API B2B requiert sa propre base de données SQL Azure.
- Azure Service Bus : facultatif et utilisé uniquement dans le cas de traitement par lot.

## Utilisation du connecteur Edifact
Pour utiliser le connecteur Edifact, vous devez d'abord créer une instance de l'application API du connecteur AS2. Cela est possible inline, lors de la création d'une application logique ou en sélectionnant l'application API du connecteur AS2 à partir d'Azure Marketplace.

## Configuration du connecteur Edifact
Les partenaires commerciaux sont des entités impliquées dans des communications B2B (entreprise à entreprise). Lorsque deux partenaires établissent une relation, il est question d'un « accord ». L’accord défini est basé sur le type de communication dont les deux partenaires souhaitent bénéficier. Il est propre au protocole ou au transport.

Les étapes de création d'un accord de partenariat commercial sont documentées [ici][2].

## Utilisation du connecteur Edifact dans l'aire du concepteur d'applications logiques
Vous pouvez utiliser le connecteur Edifact comme un déclencheur ou une action.

### Déclencheur
- Lancez le concepteur de flux des applications logiques Azure
- Cliquez sur le connecteur Edifact dans le panneau de droite : ![Paramètres du déclencheur][3]
- Cliquez sur -> : ![Options du déclencheur][4]
- Le connecteur EDIFACT expose un seul déclencheur. Sélectionnez *Publier lot* : ![Entrée Publier lot][5]
- Ce déclencheur ne possède aucune entrée. Cliquez sur -> : ![Option Publier lot configurée][6]
- Dans le cadre de la sortie, le connecteur renvoie la charge utile Edifact, l'ID de l'accord, ainsi que des informations indiquant si le message est traité par lot ou non.

### Action
- Cliquez sur le connecteur Edifact dans le panneau de droite : ![Paramètres d'action][7]
- Cliquez sur -> : ![Liste d'actions][8]
- Le connecteur Edifact prend en charge de nombreuses actions. Sélectionnez *Encoder* : ![Codage d'entrée][9]
- Indiquez les entrées de l'action et configurez celle-ci : ![Codage configuré][10]

	Paramètre|Type|Description du paramètre
---|---|---
Contenu|string|Message XML
ID de l'accord|integer|ID de l'accord
Est un message par lot|booléenne|Est un message par lot
Séparateur d'éléments de données|string|Séparateur d'éléments de données
Séparateur de composants|string|Séparateur de composants
Terminateur de segment|string|Terminateur de segment
Indicateur de la virgule décimale|string|Indicateur de la virgule décimale
Séparateur de répétition|string|Séparateur de répétition
Caractère d'échappement|string|Caractère d'échappement
Caractère de remplacement|string|Caractère de remplacement
Suffixe du terminateur de segment|string|Suffixe du terminateur de segment

L'action retourne un objet contenant la charge utile EDIFACT en cas de traitement réussi.

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Voir [Gérer et surveiller les applications API et le connecteur](../app-service-api/app-service-api-manage-in-portal.md).


<!--References -->
[1]: app-service-logic-connector-tpm.md
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-edifact/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-edifact/ListOfTriggers.PNG
[5]: ./media/app-service-logic-connector-edifact/ReleaseBatchTriggerInput.PNG
[6]: ./media/app-service-logic-connector-edifact/ReleaseBatchTriggerConfigured.PNG
[7]: ./media/app-service-logic-connector-edifact/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-edifact/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-edifact/EncodeInput.PNG
[10]: ./media/app-service-logic-connector-edifact/EncodeConfigured.PNG

<!---HONumber=August15_HO8-->