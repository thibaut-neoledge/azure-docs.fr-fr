---
title: Utilisation du connecteur Encodeur JSON Biztalk dans des applications logiques | Microsoft Docs
description: Comment créer et configurer le connecteur Encodeur JSON BizTalk ou une application API et l'utiliser dans une application logique d’Azure App Service
services: logic-apps
documentationcenter: .net,nodejs,java
author: rajeshramabathiran
manager: erikre
editor: ''

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 04/20/2016
ms.author: rajram

---
# Utilisation et ajout de l’encodeur JSON BizTalk dans votre application logique
[!INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Le connecteur Encodeur Décodeur JSON BizTalk facilite l'interopérabilité de votre application entre les données JSON et XML. Il peut convertir une instance JSON donnée au format XML et vice versa.

Vous pouvez ajouter l’encodeur JSON BizTalk à votre flux d’entreprise et traiter les données dans le cadre de ce flux de travail dans une application logique.

## Utilisation de l'encodeur JSON BizTalk
Pour utiliser l'encodeur JSON BizTalk, vous devez d'abord créer une instance de l'application API d'encodeur JSON BizTalk. Cela est possible inline, lors de la création d'une application logique, ou en sélectionnant l'application API d'encodeur JSON BizTalk dans Azure Marketplace.

## Utilisation de l'encodeur JSON BizTalk dans l'aire du concepteur d'applications logiques
Suivez les étapes de [Création d’une application logique]. L'encodeur JSON BizTalk peut être utilisé en tant qu'action. Il ne possède aucun déclencheur.

### Action
* Cliquez sur l'encodeur JSON BizTalk dans le volet de droite
  
    ![Paramètres d'action][3]
* Cliquez sur ->
  
    ![Liste d'actions][4]
* L’encodeur JSON BizTalk prend en charge deux actions. Sélectionnez *Xml à JSON*
  
    ![Entrée XML à JSON][5]
* Indiquez les entrées de l'action et configurez celle-ci
  
    ![Encoder et envoyer configuré][6]

| Paramètre | Type | Description du paramètre |
| --- | --- | --- |
| XML d'entrée |objet |Contenu du XML d’entrée |
| Supprimer l'enveloppe extérieure |string |Indicateur pour supprimer le nœud racine du contenu Xml |

L'action retourne une représentation JSON du contenu d'entrée.

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

> [!NOTE]
> Si vous voulez vous familiariser avec Azure Logic Apps avant d'ouvrir un compte Azure, accédez à la page [Essayer Logic App](https://tryappservice.azure.com/?appservice=logic), où vous pourrez créer immédiatement une application logique temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.
> 
> 

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

<!--References -->
[1]: app-service-logic-connector-tpm
[2]: app-service-logic-create-a-trading-partner-agreement
[3]: ./media/app-service-logic-json-encoder/ActionSettings.PNG
[4]: ./media/app-service-logic-json-encoder/ListOfActions.PNG
[5]: ./media/app-service-logic-json-encoder/EncodeInput.PNG
[6]: ./media/app-service-logic-json-encoder/EncodeConfigured.PNG

<!--Links -->
[Création d’une application logique]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0803_2016-->