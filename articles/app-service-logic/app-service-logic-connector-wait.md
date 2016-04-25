<properties 
   pageTitle="Utilisation du connecteur Wait dans des applications logiques | Microsoft Azure App Service" 
   description="Comment créer et configurer le connecteur Wait ou une application API et l'utiliser dans une application logique d’Azure App Service" 
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

# Prise en main du connecteur Wait et ajout de celui-ci à votre application logique
>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma 2014-12-01-preview des applications logiques.

Le connecteur Wait permet à une application de retarder son exécution pour une durée spécifiée ou jusqu’à une heure spécifiée. Vous pouvez ajouter le connecteur Wait à votre flux de travail professionnel et traiter les données dans le cadre de ce flux de travail dans une application logique. Lorsqu’il est utilisé dans une application logique, il permet de différer l’exécution.

## Utilisation du connecteur Wait
Pour utiliser le connecteur Wait, vous devez d’abord créer une instance de l’application API de ce connecteur. Cela est possible inline, lors de la création d’une application logique ou en sélectionnant l’application API du connecteur Wait à partir d’Azure Marketplace.

## Utilisation du connecteur Wait dans l’aire du concepteur Logic Apps
Le connecteur Wait peut être utilisé en tant qu’action. Il ne possède aucun déclencheur.

### Action
- Cliquez sur le connecteur Wait dans le volet de droite : ![Liste d'actions][1]
- Le connecteur Wait prend en charge deux actions : 
	- Retarder
	- Retarder jusqu’à
	 
- Sélectionnez *Retarder* : ![Valeur du retard][2]
- Indiquez les entrées de l'action et configurez celle-ci : ![Action configurée][3]

Paramètre|Type|Description du paramètre
---|---|---
Durée en minutes|integer|Durée du retard en minutes


## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Si vous voulez vous familiariser avec Azure Logic Apps avant d'ouvrir un compte Azure, accédez à la page [Essayer Logic App](https://tryappservice.azure.com/?appservice=logic), où vous pourrez créer immédiatement une application logique temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

 

<!--References -->
[1]: ./media/app-service-logic-wait/ListOfActions.PNG
[2]: ./media/app-service-logic-wait/DelayInput.PNG
[3]: ./media/app-service-logic-wait/ActionConfigured.PNG

<!---HONumber=AcomDC_0413_2016-->