<properties 
	pageTitle="Utilisation du connecteur Slack dans des applications logiques | Microsoft Azure App Service"
	description="Comment créer et configurer le connecteur Slack ou une application API et l'utiliser dans une application logique d’Azure App Service"
	authors="rajeshramabathiran" 
	manager="erikre" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/16/2016"
	ms.author="rajram"/>

# Prise en main du connecteur Slack et ajout de celui-ci à votre application logique
>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma 2014-12-01-preview des applications logiques. Pour la version de schéma 2015-08-01-preview, cliquez sur [API Slack](../connectors/connectors-create-api-slack.md).

Connectez-vous aux canaux Slack et publiez des messages à l’attention de votre équipe. Les connecteurs peuvent être utilisés dans les applications logiques dans le cadre d’un « flux de travail » pour effectuer différentes tâches. En utilisant le connecteur Slack dans votre flux de travail, vous pouvez effectuez une multitude d’opérations à l’aide des autres connecteurs. Par exemple, vous pouvez utiliser le [connecteur Facebook](app-service-logic-connector-facebook.md) dans votre flux de travail pour envoyer un message à votre canal Slack.

## Déclencheurs et actions
Les *déclencheurs* sont des événements qui se produisent. Par exemple, lorsqu'une commande est mise à jour ou lorsqu'un nouveau client est ajouté. Une *action* est le résultat du déclencheur. Par exemple, lorsqu'une commande est mise à jour, envoyer une alerte au vendeur. Ou bien, lorsqu'un nouveau client est ajouté, lui envoyer un message de bienvenue.

Le connecteur Slack peut être utilisé comme une action dans une application logique et prend en charge les données aux formats JSON et XML. Actuellement, aucun déclencheur n’est disponible pour le connecteur Slack.

Le connecteur Slack propose les déclencheurs et les actions suivants :

Déclencheurs | Actions
--- | ---
Aucun | Publier un message

## Créer le connecteur Slack
Un connecteur peut être créé dans une application logique ou directement à partir d'Azure Marketplace. Pour créer un connecteur à partir de Marketplace :

1. Dans le tableau d'accueil Azure, sélectionnez **Marketplace**.
2. Sélectionnez **API Apps** et recherchez « Connecteur Slack ».
3. Entrez le nom, le plan App Service et d'autres propriétés :
![][1] 

4. Cliquez sur **Create**.

## Utilisation du connecteur en tant qu’action dans votre application logique

> [AZURE.IMPORTANT] Les connecteurs et les applications logiques doivent toujours être créés dans le même groupe de ressources.

Une fois le connecteur Slack créé, vous pouvez l’ajouter en tant qu’action à votre application logique :

1.	Dans votre application logique, ouvrez **Déclencheurs et actions**. [Créer une application logique](app-service-logic-create-a-logic-app.md)

2.	Le connecteur Slack est répertorié dans la galerie sur le côté droit :
![][2]

3.	Sélectionnez le connecteur Slack que vous avez créé pour l’ajouter automatiquement à votre application logique.
4.	Sélectionnez **Autoriser**. Connectez-vous à votre compte Slack. À la fin, vous êtes invité à autoriser votre connecteur à accéder à votre compte Slack. Sélectionnez **Autoriser**:
![][3]
![][4]
![][5]
![][6]
	
5.	Vous pouvez maintenant utiliser le connecteur Slack dans le flux. L’action Publier un message est disponible :
![][7]


Testons l'action « Publier le message ». Vous pouvez utiliser cette action pour publier un message vers n’importe quel canal Slack :
![][8]

Configurez les propriétés d’entrée de l’action « Publier un message » :

Propriété | Description
--- | ---
Texte | Entrez le texte du message à publier.
Nom du canal | Indiquez le canal Slack sur lequel ce message est publié. Si le canal n’est pas indiqué, le message est publié sur #general.
Propriétés avancées | **Nom d’utilisateur du robot** : nom du robot à utiliser pour ce message. Le message est publié en tant que « Robot » si cette valeur n’est pas spécifiée.<p><p>**URL de l’icône** : URL vers une image à utiliser comme icône pour ce message.<p><p>**Emoji de l’icône** : Emoji à utiliser comme icône pour ce message. Cette propriété remplace la propriété de l’URL de l’icône.


Le connecteur Slack dispose d’API REST afin que vous puissiez l’utiliser en dehors d’une application logique. Ouvrez votre connecteur Slack et sélectionnez **Définition d’API**:
![][9]


## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Si vous voulez vous familiariser avec Azure Logic Apps avant d'ouvrir un compte Azure, accédez à la page [Essayer Logic App](https://tryappservice.azure.com/?appservice=logic), où vous pourrez créer immédiatement une application logique temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Consultez la page [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md).


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-slack/img1.PNG
[2]: ./media/app-service-logic-connector-slack/img2.PNG
[3]: ./media/app-service-logic-connector-slack/img3.PNG
[4]: ./media/app-service-logic-connector-slack/img4.PNG
[5]: ./media/app-service-logic-connector-slack/img5.PNG
[6]: ./media/app-service-logic-connector-slack/img6.PNG
[7]: ./media/app-service-logic-connector-slack/img7.PNG
[8]: ./media/app-service-logic-connector-slack/img8.PNG
[9]: ./media/app-service-logic-connector-slack/img9.PNG

<!---HONumber=AcomDC_0323_2016-->