<properties 
   pageTitle="Utilisation du connecteur de gestion des partenaires commerciaux BizTalk dans des applications logiques | Microsoft Azure App Service" 
   description="Comment créer et configurer le connecteur de gestion des partenaires commerciaux BizTalk ou une application API et l'utiliser dans une application logique d’Azure App Service" 
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
   ms.date="11/30/2015"
   ms.author="rajram"/>

# Prise en main du service de gestion des partenaires commerciaux BizTalk et ajout de celui-ci à votre application logique
Le service de gestion des partenaires commerciaux (TPM) BizTalk vous permet de définir et de conserver des relations interentreprises, comme les partenaires et les accords, ainsi que les artefacts associés tels que les schémas et les certificats. Ces relations peuvent alors être appliquées par les services API associés tels que AS2, EDIFACT et X12.

L'application API TPM (Gestion des partenaires commerciaux) est la configuration de base requise du connecteur AS2 et des applications API X12 et EDIFACT. Vous pouvez ajouter le service de gestion des partenaires commerciaux à votre flux d’entreprise et traiter les données dans le cadre d'un flux de travail interentreprises dans une application logique.

## Configuration requise
- Base de données SQL Azure vide : vous devez créer une base de données SQL Azure vide avant de créer une application d'API TPM.

## Compréhension des partenaires, accords et profils
Pour en savoir plus sur l'accord de partenariat commercial, cliquez [ici][1]

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure Logic Apps avant d'ouvrir un compte Azure, accédez à la page [Essayer Logic App](https://tryappservice.azure.com/?appservice=logic), où vous pourrez créer immédiatement une application logique temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

Affichez la référence d'API REST Swagger sur [Référence de connecteurs et d'applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Consultez la page [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md).

<!--References-->
[1]: app-service-logic-create-a-trading-partner-agreement.md

<!---HONumber=AcomDC_1203_2015-->