<properties
   pageTitle="Utilisation du connecteur Yammer dans des applications logiques | Microsoft Azure App Service"
   description="Comment créer et configurer le connecteur Yammer ou une application API et l'utiliser dans une application logique d’Azure App Service"
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
   ms.date="03/16/2016"
   ms.author="sameerch"/>


# Utilisation du connecteur Yammer dans votre application logique #
>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma 2014-12-01-preview des applications logiques. Pour la version de schéma 2015-08-01-preview, cliquez sur [API Yammer](../connectors/connectors-create-api-yammer.md).

Connexion à Yammer, action de publication de messages et un déclencheur pour récupérer les nouveaux messages.

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux.

## Création d’un connecteur Yammer pour votre application logique ##
Pour utiliser le connecteur Yammer, vous devez d’abord créer une instance de l’application API de ce connecteur. Pour cela, procédez comme suit :

1. Dans le tableau d'accueil Azure, sélectionnez **Marketplace**.
2. Recherchez « Connecteur Yammer », sélectionnez-le et sélectionnez **Créer**.
3. Configurez le connecteur Yammer comme suit : ![][1]

	- **Emplacement** : choisissez l’emplacement géographique dans lequel vous voulez déployer le connecteur.
	- **Abonnement** : choisissez un abonnement dans lequel vous souhaitez créer ce connecteur.
	- **Groupe de ressources** : sélectionnez ou créez un groupe de ressources où doit résider le connecteur.
	- **Plan App Service** : sélectionnez ou créez un plan d'hébergement web.
	- **Niveau de tarification** : choisissez un niveau de tarification pour le connecteur.
	- **Nom** : indiquez le nom de votre connecteur Yammer.

4. Cliquez sur Créer. Un connecteur Yammer est créé.
5. Une fois l’instance de l’application API créée, vous pouvez créer une application logique pour utiliser le connecteur Yammer.

## Utilisation du connecteur Yammer dans votre application logique ##
Une fois votre application API créée, vous pouvez utiliser le connecteur Yammer comme déclencheur ou action pour votre application logique. Pour cela, vous devez procéder comme suit :

1.	Créer une application logique : ![][2]

2.	Ouvrez « Déclencheurs et actions » pour ouvrir le concepteur d’applications logiques et configurer votre flux : ![][3]

3.	Le connecteur Yammer apparaît dans la section « Applications API dans ce groupe de ressources » de la galerie située à droite : ![][4]

4. Vous pouvez déposer l’application API du connecteur Yammer dans l’éditeur en cliquant sur « Connecteur Yammer ». Cliquez sur le bouton Autoriser. Indiquez vos informations d’identification Yammer. Cliquez sur « Autoriser » : ![][5] ![][6] ![][7]

Vous pouvez maintenant utiliser le connecteur Yammer dans le flux.

## Utiliser le connecteur Yammer comme un déclencheur

Vous pouvez utiliser le nouveau message récupéré du déclencheur Yammer (« Nouveau message ») dans d’autres actions du flux. Configurez les propriétés d’entrée du déclencheur Yammer comme suit :

- **ID de groupe** : ID du groupe à partir duquel le nouveau message doit être récupéré. Si l’ID de groupe n’est pas fourni, le message sera récupéré du flux suivant. L’ID de groupe peut être récupéré à partir de l’URL du groupe dans Yammer.
		
	Exemple : l'ID de groupe de l'URL suivant est « 5453203 » : https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203 ![][8] ![][9]

## Utiliser le connecteur de Yammer pour publier un message

Vous pouvez également utiliser le connecteur Yammer comme une action dans vos applications logiques. Tout d'abord, spécifiez un déclencheur pour votre application logique ou cochez la case « exécuter cette logique manuellement » (comme indiqué ci-dessous). Ajoutez le connecteur Yammer, définissez les autorisations et choisissez l'action « Message Post ». Configurez les propriétés d’entrée de l’action « Publier le message » comme suit :

- **Texte du message** : corps du message à publier.
- **ID de groupe** : indiquez l'ID du groupe vers lequel publier le nouveau message. Si aucun ID de groupe n’est fourni, le message sera publié dans tous les flux de l’entreprise. L’ID de groupe peut être récupéré à partir de l’URL du groupe dans Yammer.  

	Exemple : l'ID de groupe de l'URL suivant est « 5453203 » : https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203
- 	**Baliser les utilisateurs** : tableau des noms d'utilisateur du réseau qui doivent être balisés dans le message : ![][10]![][11]

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Si vous voulez vous familiariser avec Azure Logic Apps avant d'ouvrir un compte Azure, accédez à la page [Essayer Logic App](https://tryappservice.azure.com/?appservice=logic), où vous pourrez créer immédiatement une application logique temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Consultez la page [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-yammer/img1.PNG
[2]: ./media/app-service-logic-connector-yammer/img2.PNG
[3]: ./media/app-service-logic-connector-yammer/img3.png
[4]: ./media/app-service-logic-connector-yammer/img4.png
[5]: ./media/app-service-logic-connector-yammer/img5.PNG
[6]: ./media/app-service-logic-connector-yammer/img6.PNG
[7]: ./media/app-service-logic-connector-yammer/img7.png
[8]: ./media/app-service-logic-connector-yammer/img8.PNG
[9]: ./media/app-service-logic-connector-yammer/img9.PNG
[10]: ./media/app-service-logic-connector-yammer/img10.PNG
[11]: ./media/app-service-logic-connector-yammer/img11.PNG

<!---HONumber=AcomDC_0323_2016-->