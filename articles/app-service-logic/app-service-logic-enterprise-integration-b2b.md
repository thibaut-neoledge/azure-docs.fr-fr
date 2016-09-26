<properties 
	pageTitle="Création de solutions B2B avec Enterprise Integration Pack | Microsoft Azure App Service | Microsoft Azure" 
	description="En savoir plus sur la réception de données à l’aide des fonctionnalités B2B d'Enterprise Integration Pack" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# En savoir plus sur la réception de données à l’aide des fonctionnalités B2B d'Enterprise Integration Pack#

## Vue d'ensemble ##

Ce document fait partie du Logic Apps Enterprise Integration Pack. Consultez la vue d'ensemble pour en savoir plus sur les [fonctionnalités d'Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md).

## Composants requis ##

Pour utiliser les actions AS2 et X12, vous aurez besoin d'un compte Enterprise Integration

[Comment créer un compte Enterprise Integration](./app-service-logic-enterprise-integration-accounts.md)

## Comment utiliser les connecteurs B2B Logic Apps ##

Une fois que vous avez créé un compte d’intégration et que vous y avez ajouté des partenaires et des contrats, vous êtes prêt à créer une application logique qui implémente un flux de travail Business to Business (B2B).

Dans cette procédure, vous verrez comment utiliser les actions AS2 et X12 pour créer une application logique B2B qui reçoit les données d’un partenaire commercial.

1. Créez une nouvelle application logique et [liez-la à votre compte d’intégration](./app-service-logic-enterprise-integration-accounts.md).
2. Ajoutez un déclencheur **Demande - Quand une demande HTTP est reçue** à votre application logique ![](./media/app-service-logic-enterprise-integration-b2b/flatfile-1.png)
3. Ajouter l’action **Decode AS2** en sélectionnant d’abord **Ajouter une action** ![](./media/app-service-logic-enterprise-integration-b2b/transform-2.png)
4. Entrez le mot **as2** dans la zone de recherche afin de filtrer toutes les actions et d’obtenir celle que vous souhaitez utiliser ![](./media/app-service-logic-enterprise-integration-b2b/b2b-5.png)
6. Sélectionnez l'action **AS2 - Decode AS2 message** ![](./media/app-service-logic-enterprise-integration-b2b/b2b-6.png)
7. Comme indiqué, ajoutez l'élément **Body** (Corps) comme entrée. Dans cet exemple, sélectionnez le corps de la demande HTTP qui a déclenché l’application logique. Vous pouvez également entrer une expression comme entrée dans les en-têtes du champ **HEADERS** :

    @triggerOutputs()['headers']

8. Ajoutez les **en-têtes** requis pour AS2. Ils apparaitront dans les en-têtes de requête HTTP. Dans cet exemple, sélectionnez les en-têtes de la demande HTTP qui a déclenché l’application logique.
9. Ajoutez maintenant à nouveau l’action Decode X12 message en sélectionnant **Ajouter une action** ![](./media/app-service-logic-enterprise-integration-b2b/b2b-9.png)
10. Entrez le mot **x12** dans la zone de recherche afin de filtrer toutes les actions et d’obtenir celle que vous souhaitez utiliser ![](./media/app-service-logic-enterprise-integration-b2b/b2b-10.png)
11. Sélectionnez l'action **X12 - Decode X12 message** pour l’ajouter à l’application logique ![](./media/app-service-logic-enterprise-integration-b2b/b2b-as2message.png)
12. Vous devez maintenant spécifier l’entrée de cette action qui correspondra à la sortie de l’action AS2 ci-dessus. Le contenu réel du message figure dans un objet JSON, au format base64 encodé. Vous devez donc spécifier une expression comme entrée : entrez l’expression suivante dans le champ d'entrée **X12 FLAT FILE MESSAGE TO DECODE**

    @base64ToString(body('Decode\_AS2\_message')?['AS2Message']?['Content'])

13. Cette étape décodera les données X12 provenant d’un partenaire commercial et générera plusieurs éléments dans un objet JSON. Pour informer le partenaire de la réception des données, vous pouvez renvoyer une réponse contenant l'élément AS2 Message Disposition Notification (MDN) dans une action Response HTTP
14. Ajoutez l'action **Response** en sélectionnant **Ajouter une action** ![](./media/app-service-logic-enterprise-integration-b2b/b2b-14.png)
15. Entrez le mot **réponse** dans la zone de recherche afin de filtrer toutes les actions et d’obtenir celle que vous souhaitez utiliser ![](./media/app-service-logic-enterprise-integration-b2b/b2b-15.png)
16. Sélectionnez l'action **Response** à ajouter à ![](./media/app-service-logic-enterprise-integration-b2b/b2b-16.png)
17. Définissez le champ de réponse **BODY** (corps) à l’aide de l’expression suivante pour accéder à l'élément MDN à partir de la sortie de l'action **Decode X12 message**

    @base64ToString(body('Decode\_AS2\_message')?['OutgoingMdn']?['Content'])

![](./media/app-service-logic-enterprise-integration-b2b/b2b-17.png)
18. Enregistrez votre travail ![](./media/app-service-logic-enterprise-integration-b2b/transform-5.png)

À ce stade, vous avez terminé de configurer votre application logique B2B. Dans une application réelle, vous souhaiterez peut-être stocker les données X12 décodées dans une application métier ou un magasin de données. Vous pouvez facilement ajouter des actions supplémentaires pour effectuer cette opération, ou écrire des API personnalisées pour vous connecter à vos propres applications métier puis utiliser ces API dans votre application logique.

## Fonctionnalités et cas d’usage ##

- Les actions de décodage et d'encodage AS2 et X12 vous permettent de recevoir des données et d’en envoyer à des partenaires commerciaux à l'aide de protocoles standard en utilisant des applications logiques
- Vous pouvez utiliser AS2 et X12 ensemble ou de façon isolée pour échanger des données avec des partenaires commerciaux en fonction des besoins
- Les actions B2B facilitent la création de partenaires et de contrats dans le compte d’intégration et leur utilisation dans une application logique
- En étendant votre application logique avec d’autres actions, vous pouvez envoyer et recevoir des données vers et depuis d’autres applications et services tels que SalesForce

## En savoir plus ##

[En savoir plus sur Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md)

<!---HONumber=AcomDC_0914_2016-->