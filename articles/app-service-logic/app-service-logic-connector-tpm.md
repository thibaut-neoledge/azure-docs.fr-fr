<properties 
   pageTitle="Gestion des partenaires commerciaux BizTalk"
	description="Gestion des partenaires commerciaux BizTalk"
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

#Gestion des partenaires commerciaux BizTalk
Le service de gestion des partenaires commerciaux (GPC) de Microsoft Azure permet de définir et de conserver les relations interentreprises, comme les partenaires et les accords, ainsi que les artefacts associés tels que les schémas et les certificats. Ces relations peuvent alors être appliquées par les services API associés tels que AS2, EDIFACT et X12.

L'application API TPM (Gestion des partenaires commerciaux) est la configuration de base requise du connecteur AS2 et des applications API X12 or EDIFACT.

##Conditions préalables
- Base de données SQL Azure vide : vous devez créer une base de données SQL Azure vide avant de créer une application d'API TPM.

##Compréhension des partenaires, accords et profils
Pour en savoir plus sur l'accord de partenariat commercial, cliquez [ici][1]

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Consultez la page [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md).

<!--References-->
[1]: app-service-logic-create-a-trading-partner-agreement.md

<!---HONumber=August15_HO8-->