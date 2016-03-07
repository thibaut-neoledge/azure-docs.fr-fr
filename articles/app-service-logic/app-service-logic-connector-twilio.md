<properties
   pageTitle="Utilisation du connecteur Twilio dans des applications logiques | Microsoft Azure App Service"
   description="Comment créer et configurer le connecteur Twilio ou une application API et l'utiliser dans une application logique d’Azure App Service"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/11/2016"
   ms.author="sameerch"/>


# Prise en main du connecteur Twilio et ajout de celui-ci à votre application logique
>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma 2014-12-01-preview des applications logiques. Pour la version de schéma 2015-08-01-preview, cliquez sur [API Twilio](../connectors/create-api-twilio.md).

Connectez-vous à votre compte Twilio pour envoyer et recevoir des messages SMS. Vous pouvez également de récupérer des numéros de téléphone et des données d'utilisation. Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux. Vous pouvez ajouter le connecteur Twilio à votre flux de travail professionnel et traiter les données dans le cadre de ce flux de travail dans une application logique.

## Création d’un connecteur Twilio pour votre application logique ##
Un connecteur peut être créé dans une application logique ou directement à partir d'Azure Marketplace. Pour créer un connecteur à partir de Marketplace :

1. Dans le tableau d'accueil Azure, sélectionnez **Marketplace**.
2. Recherchez « Connecteur Twilio », sélectionnez-le et sélectionnez **Créer**.
3. Configurez le connecteur Twilio comme suit : ![][1]  
	- **Emplacement** : choisissez l’emplacement géographique dans lequel vous voulez déployer le connecteur.
	- **Abonnement** : choisissez un abonnement dans lequel vous souhaitez créer ce connecteur.
	- **Groupe de ressources** : sélectionnez ou créez un groupe de ressources où doit résider le connecteur.
	- **Plan d’hébergement web** : sélectionnez ou créez un plan d’hébergement web.
	- **Niveau de tarification** : choisissez un niveau de tarification pour le connecteur.
	- **Nom** : indiquez le nom de votre connecteur Twilio.
	- **Paramètres du package**
		- **SID de compte** : identifiant unique du compte. Vous pouvez récupérer le SID de votre compte à l'adresse <https://www.twilio.com/user/account/settings>
		- **Jeton d’autorisation** : jeton d’autorisation associé au compte. Vous pouvez récupérer ce jeton à l'adresse <https://www.twilio.com/user/account/settings>


4.	Cliquez sur Créer. Un connecteur Twilio est créé.
5.	Une fois l’instance de l’application API créée, vous pouvez créer une application logique pour utiliser le connecteur Twilio.

## Utilisation du connecteur Twilio dans votre application logique ##
Une fois votre application API créée, vous pouvez utiliser le connecteur Twilio comme action dans votre application logique. Pour cela, vous devez procéder comme suit :

1.	Créez une application logique et choisissez le même groupe de ressources qui contient le connecteur Twilio : ![][2]
2.	Ouvrez « Déclencheurs et actions » pour ouvrir le concepteur d’applications logiques et configurer votre flux : ![][3]
3.	Le connecteur Twilio apparaît dans la section « Applications API dans ce groupe de ressources » de la galerie située à droite : ![][4]
4. Vous pouvez déposer l’application API du connecteur Twilio dans l’éditeur en cliquant sur « Connecteur Twilio ».

5.	Vous pouvez maintenant utiliser le connecteur Twilio dans le flux. Vous pouvez utiliser l’action « Envoyer un message » dans le flux pour envoyer un message. Configurez les propriétés d’entrée de l’action « Envoyer un message » comme suit :
	- **À partir du numéro de téléphone** : saisissez un numéro de téléphone Twilio autorisant le type de message que vous souhaitez envoyer. Seuls les numéros de téléphone ou les codes courts achetés auprès de Twilio fonctionnent avec ce connecteur.
	- **Vers le numéro de téléphone** : numéro de téléphone de destination. Le format accepté est : + suivi du préfixe du pays et du numéro de téléphone. Par exemple, +16175551212. Si vous oubliez le +, Twilio utilise le préfixe de pays que vous avez saisi dans le champ « À partir ».
	- **Texte** : texte du message à envoyer.

	![][5] ![][6]

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Si vous voulez vous familiariser avec Azure Logic Apps avant d'ouvrir un compte Azure, accédez à la page [Essayer Logic App](https://tryappservice.azure.com/?appservice=logic), où vous pourrez créer immédiatement une application logique temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Consultez la page [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-twilio/img1.PNG
[2]: ./media/app-service-logic-connector-twilio/img2.PNG
[3]: ./media/app-service-logic-connector-twilio/img3.png
[4]: ./media/app-service-logic-connector-twilio/img4.png
[5]: ./media/app-service-logic-connector-twilio/img5.PNG
[6]: ./media/app-service-logic-connector-twilio/img6.PNG

<!---HONumber=AcomDC_0224_2016-->