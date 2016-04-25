<properties 
   pageTitle="Utilisation du connecteur BizTalk X12 dans des applications logiques | Microsoft Azure App Service" 
   description="Comment créer et configurer le connecteur BizTalk X12 ou une application API et l'utiliser dans une application logique d’Azure App Service" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="02/10/2016"
   ms.author="rajram"/>

# Prise en main de l’application API BizTalk X12 et ajout de celle-ci à votre application logique
>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma 2014-12-01-preview des applications logiques.

Utilisez le service BizTalk X12 pour recevoir et envoyer des messages par le biais du protocole X12 dans les communications interentreprises. Le protocole X12 est également communément appelé ASC X12 (Accredited Standards Committee X12) et son utilisation est très répandue dans le monde de l'industrie.

Vous pouvez ajouter l’application API BizTalk X12 à votre flux d’entreprise et traiter les données dans le cadre d'un flux de travail interentreprises dans une application de logique.


## Configuration requise
- Application API TPM : avant de créer un connecteur X12, vous devez créer un [connecteur de gestion des partenaires commerciaux BizTalk][1].
- Base de données SQL Azure : chacune des applications API B2B requiert sa propre base de données SQL Azure.
- Azure Service Bus : facultatif et utilisé uniquement dans le cas de traitement par lot.

## Utilisation du connecteur BizTalk X12
Pour utiliser le Connecteur BizTalk X12, vous devez d'abord créer une instance de l'application API du Connecteur BizTalk X12. Cela est possible inline, lors de la création d'une application logique ou en sélectionnant l'application API du Connecteur BizTalk X12 à partir d'Azure Marketplace.

## Configuration du connecteur BizTalk X12
Les partenaires commerciaux sont des entités impliquées dans des communications B2B (entreprise à entreprise). Lorsque deux partenaires établissent une relation, il est question d'un « accord ». L’accord défini est basé sur le type de communication dont les deux partenaires souhaitent bénéficier. Il est propre au protocole ou au transport.

Consultez les étapes à suivre pour [créer un accord de partenariat commercial][2].

## Utilisation du connecteur X12 dans l'aire du concepteur d'applications logiques
Vous pouvez utiliser le connecteur X12 comme un déclencheur ou une action.

### Déclencheur
- Lancez le concepteur de flux des applications logiques Azure
- Cliquez sur le connecteur X12 dans le panneau de droite :
![Paramètres du déclencheur][3]
- Cliquez sur -> :
![Options du déclencheur][4]
- Le connecteur BizTalk X12 expose un seul déclencheur. Sélectionnez *Publier lot* :
![Entrée Publier lot][5]
- Ce déclencheur ne possède aucune entrée. Cliquez sur -> :
![Option Publier lot configurée][6]
- Dans le cadre de la sortie, le connecteur renvoie la charge utile X12, l'ID de l'accord, ainsi que des informations indiquant si le message est traité par lot ou non.

### Action
- Cliquez sur le connecteur X12 dans le panneau de droite :
![Paramètres d'action][7]
- Cliquez sur -> :
![Liste d'actions][8]
- Le connecteur X12 prend en charge de nombreuses actions. Sélectionnez *Encoder* :
![Codage d'entrée][9]
- Indiquez les entrées de l'action et configurez celle-ci :
![Codage configuré][10]

Paramètre|Type|Description du paramètre
---|---|---
Contenu|string|Message XML
ID de l'accord|string|ID de l'accord
Est un message par lot|string|Est un message par lot

L'action retourne un objet contenant la charge utile X12.

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Si vous voulez vous familiariser avec Azure Logic Apps avant d'ouvrir un compte Azure, accédez à la page [Essayer Logic App](https://tryappservice.azure.com/?appservice=logic), où vous pourrez créer immédiatement une application logique temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

 


<!--References -->
[1]: app-service-logic-connector-tpm.md
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-x12/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-x12/ListOfTriggers.PNG
[5]: ./media/app-service-logic-connector-x12/ReleaseBatchTriggerInput.PNG
[6]: ./media/app-service-logic-connector-x12/ReleaseBatchTriggerConfigured.PNG
[7]: ./media/app-service-logic-connector-x12/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-x12/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-x12/EncodeInput.PNG
[10]: ./media/app-service-logic-connector-x12/EncodeConfigured.PNG
[11]: ./media/app-service-logic-connector-x12/TriggerSettings.PNG

<!---HONumber=AcomDC_0413_2016-->