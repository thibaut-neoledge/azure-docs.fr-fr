<properties 
   pageTitle="Utilisation du connecteur BizTalk Edifact dans des applications logiques | Microsoft Azure App Service" 
   description="Comment créer et configurer le connecteur BizTalk Edifact ou une application API et l'utiliser dans une application logique d’Azure App Service" 
   services="logic-apps" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/20/2016"
   ms.author="rajram"/>

# Utilisation et ajout du connecteur BizTalk Edifact dans votre application logique  

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Utilisez le service Edifact pour recevoir et envoyer des messages par le biais du protocole Edifact dans les communications interentreprises. Le protocole Edifact est également communément appelé ASC Edifact ou Accredited Standards Committee Edifact et son utilisation est très répandue dans le monde de l'industrie.

Vous pouvez ajouter le connecteur BizTalk Edifact à votre flux d’entreprise et traiter les données dans le cadre de ce flux de travail dans une application logique.

## Composants requis
- Application API TPM : avant de créer un connecteur Edifact, vous devez créer un [connecteur de gestion des partenaires commerciaux BizTalk][1].
- Base de données SQL Azure : chacune des applications API B2B requiert sa propre base de données SQL Azure.
- Azure Service Bus : facultatif et utilisé uniquement dans le cas de traitement par lot.

## Utilisation du connecteur Edifact
Pour utiliser le connecteur Edifact, vous devez d'abord créer une instance de l'application API du connecteur AS2. Cela est possible inline, lors de la création d'une application logique ou en sélectionnant l'application API du connecteur AS2 à partir d'Azure Marketplace.

## Configuration du connecteur Edifact
Les partenaires commerciaux sont des entités impliquées dans des communications B2B (entreprise à entreprise). Lorsque deux partenaires établissent une relation, il est question d'un « accord ». L’accord défini est basé sur le type de communication dont les deux partenaires souhaitent bénéficier. Il est propre au protocole ou au transport.

Consultez les étapes à suivre pour [créer un accord de partenariat commercial][2].

## Utilisation du connecteur Edifact dans l'aire du concepteur d'applications logiques
Vous pouvez utiliser le connecteur Edifact comme un déclencheur ou une action.

### Déclencheur
- Lancez le concepteur de flux des applications logiques Azure
- Cliquez sur le connecteur Edifact dans le panneau de droite :  
![Paramètres du déclencheur][3]
- Cliquez sur -> :  
![Options du déclencheur][4]
- Le connecteur EDIFACT expose un seul déclencheur. Sélectionnez *Publier lot* :  
![Entrée Publier lot][5]
- Ce déclencheur ne possède aucune entrée. Cliquez sur -> :  
![Option Publier lot configurée][6]
- Dans le cadre de la sortie, le connecteur renvoie la charge utile Edifact, l'ID de l'accord, ainsi que des informations indiquant si le message est traité par lot ou non.

### Action
- Cliquez sur le connecteur Edifact dans le panneau de droite :  
![Paramètres d'action][7]
- Cliquez sur -> :  
![Liste d'actions][8]
- Le connecteur Edifact prend en charge de nombreuses actions. Sélectionnez *Encoder* :  
![Codage d'entrée][9]
- Indiquez les entrées de l'action et configurez celle-ci :  
![Codage configuré][10]

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

>[AZURE.NOTE] Si vous voulez vous familiariser avec Azure Logic Apps avant d'ouvrir un compte Azure, accédez à la page [Essayer Logic App](https://tryappservice.azure.com/?appservice=logic), où vous pourrez créer immédiatement une application logique temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

 


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

<!---HONumber=AcomDC_0803_2016-->