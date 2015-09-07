<properties
   pageTitle="Utilisation du connecteur Azure Service Bus dans des applications logiques | Microsoft Azure App Service"
	description="Comment créer et configurer le connecteur Azure Service Bus ou une application API et l'utiliser dans une application logique d’Azure App Service"
	services="app-service\logic"
	documentationCenter=".net,nodejs,java"
	authors="anuragdalmia"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="app-service-logic"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="integration"
	ms.date="08/23/2015"
	ms.author="andalmia"/>


# Utilisation et ajout du connecteur Azure Service Bus dans votre application logique 
Connectez-vous à Azure Service Bus pour envoyer des messages aux files d’attente et rubriques et recevoir des messages de files d’attente et d’abonnements. Les connecteurs sont utilisés dans les applications logiques dans le cadre d’un « flux de travail ».

## Déclencheurs et actions
Les déclencheurs sont des événements qui se produisent. Par exemple, lorsqu'une commande est mise à jour ou lorsqu'un nouveau client est ajouté. Une action est le résultat du déclencheur. Par exemple, lorsqu’une commande ou un nouveau message est placé dans une file d’attente, une alerte ou un message est envoyé.

Le connecteur Azure Service Bus peut être utilisé comme un déclencheur ou une action dans une application logique et prend en charge les données aux formats JSON et XML.

Le connecteur Azure Service Bus propose les déclencheurs et les actions suivants :

Déclencheurs | Actions
--- | ---
Message disponible | Envoyer un message

## Créer le connecteur Azure Service Bus
Un connecteur peut être créé dans une application logique ou directement à partir d'Azure Marketplace. Pour créer un connecteur à partir de Marketplace :

1. Dans le tableau d'accueil Azure, sélectionnez **Marketplace**.
2. Recherchez « Connecteur Azure Service Bus », sélectionnez-le et sélectionnez **Créer**.
3. Entrez le nom, le plan App Service et d'autres propriétés :
![][1]

4. Entrez les paramètres de package suivants :

	Nom | Description
--- | ---
Chaîne de connexion | La chaîne de connexion pour Azure Service Bus. Par exemple, entrez : *Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=[name];SharedAccessKey=[key]*.
Nom de l’entité | Entrez le nom de la file d’attente ou de la rubrique.
Nom d'abonnement | Entrez le nom de l’abonnement à partir duquel recevoir les messages.

5. Cliquez sur **Create**.

## Utilisation du connecteur Service Bus dans votre application logique
Une fois votre connecteur créé, vous pouvez utiliser le connecteur Azure Service Bus comme déclencheur ou action pour votre application logique. Pour ce faire :

1.	Créez une application logique et choisissez le groupe de ressources qui contient le connecteur Azure Service Bus :
![][2]

2.	Ouvrez « Déclencheurs et actions » pour ouvrir le concepteur d'applications logiques et configurer votre flux :
![][3]

3. Le connecteur Azure Service Bus s'affiche dans la section « Applications API dans ce groupe de ressources » de la galerie située à droite :
![][4]

4. Vous pouvez déposer le connecteur Azure Service Bus dans l’éditeur en cliquant sur « Connecteur Azure Service Bus ».

5.	Vous pouvez maintenant utiliser le connecteur Azure Service Bus dans le flux de travail. Vous pouvez utiliser le message récupéré du déclencheur Azure Service Bus (« Message disponible ») dans les autres actions du flux :
![][5]

	![][6]

Vous pouvez également recourir à l'action « Envoyer un message » d'Azure Service Bus :
![][7]

![][8]

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Pour plus d'informations, consultez [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-azureservicebus/img1.PNG
[2]: ./media/app-service-logic-connector-azureservicebus/img2.PNG
[3]: ./media/app-service-logic-connector-azureservicebus/img3.png
[4]: ./media/app-service-logic-connector-azureservicebus/img4.PNG
[5]: ./media/app-service-logic-connector-azureservicebus/img5.PNG
[6]: ./media/app-service-logic-connector-azureservicebus/img6.PNG
[7]: ./media/app-service-logic-connector-azureservicebus/img7.PNG
[8]: ./media/app-service-logic-connector-azureservicebus/img8.PNG

<!---HONumber=August15_HO9-->