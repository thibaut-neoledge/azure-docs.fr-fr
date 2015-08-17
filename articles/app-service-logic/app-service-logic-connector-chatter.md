<properties
   pageTitle="Application API du connecteur Chatter"
   description="Utilisation du connecteur Chatter"
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
   ms.date="07/02/2015"
   ms.author="sameerch"/>


# Utilisation du connecteur Chatter dans votre application logique #

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux.

Le connecteur Chatter vous permet de vous connecter à Chatter et d’effectuer différentes actions comme publier un message ou effectuer une recherche, et d’exécuter un déclencheur pour récupérer les nouveaux messages.

## Création d’un connecteur Chatter pour votre application logique ##
Pour utiliser le connecteur Chatter, vous devez d’abord créer une instance de l’application API de ce connecteur. Pour cela, procédez comme suit :

1.	Ouvrez Azure Marketplace à l’aide de l’option + NOUVEAU en bas à gauche du portail Azure.
2.	Accédez à « Web et mobilité > API Apps » et recherchez « Connecteur Chatter ».
3.	Configurez le connecteur Chatter comme suit :

	![][1]
	- **Emplacement** : choisissez l'emplacement géographique dans lequel vous voulez déployer le connecteur
	- **Abonnement** : choisissez un abonnement dans lequel vous souhaitez créer ce connecteur
	- **Groupe de ressources** : sélectionnez ou créez un groupe de ressources où le connecteur doit résider
	- **Plan d'hébergement Web** : sélectionnez ou créez un plan d'hébergement Web
	- **Niveau de tarification** : choisissez un niveau de tarification pour le connecteur
	- **Nom** : donnez un nom à votre connecteur Chatter

4.	Cliquez sur Créer. Le connecteur Chatter est créé.
5.	Une fois l’instance de l’application API créée, vous pouvez créer une application logique dans le même groupe de ressources pour utiliser le connecteur Chatter.

## Utilisation du connecteur Chatter dans votre application logique ##
Une fois votre application API créée, vous pouvez utiliser le connecteur Chatter comme déclencheur ou action pour votre application logique. Pour cela, vous devez procéder comme suit :

1.	Créez une application logique et choisissez le groupe de ressources qui contient le connecteur Chatter.

	![][2]
2.	Ouvrez « Déclencheurs et actions » pour ouvrir le concepteur d’applications logiques et configurer votre flux.

3.	Le connecteur Chatter s’affiche dans la section « Applications API dans ce groupe de ressources » de la galerie située à droite.

	![][4]
4. Vous pouvez déposer l’application API du connecteur Chatter dans l’éditeur en cliquant sur « Connecteur Chatter ». Cliquez sur le bouton Autoriser. Indiquez vos informations d’identification. Cliquez sur « Autoriser ».

	![][5]
	![][6]
	![][7]
5.	Vous pouvez maintenant utiliser le connecteur Chatter dans le flux. Vous pouvez utiliser le nouveau message récupéré du déclencheur Chatter (« Nouveau message ») dans d’autres actions du flux. Configurez les propriétés d’entrée du déclencheur Chatter comme suit :
	- **ID de groupe** : indiquez l'ID du groupe à partir duquel le nouveau message doit être récupéré. Si l’ID de groupe n’est pas spécifié, le nouveau message est récupéré du flux de l’utilisateur.

  ![][8]
  ![][9]

6. De même, vous pouvez utiliser l’action Chatter dans le flux pour publier un message en sélectionnant l’action « Publier le message ». Configurez les propriétés d’entrée de l’action « Publier le message » comme suit :
	- **Texte du message** : corps du message à publier.
	- **ID de groupe** : indiquez l'ID du groupe à partir duquel publier le nouveau message. Si cet ID n’est pas renseigné, le message est publié dans le flux de l’utilisateur.
	- 	**Nom de fichier** : nom du fichier à joindre à ce message.
	- 	**Données de contenu** : données de contenu de la pièce jointe.
	- 	**Type de contenu** : type de contenu de la pièce jointe.
	- 	**Encodage de transfert de contenu** : encodage de transfert de contenu de la pièce jointe (« none »|« base64 »).
	- 	**Mentions** : tableau des noms d'utilisateur à baliser dans ce message.
	- 	**Hashtags** : tableau des hashtags à publier avec le message.

	![][10]
	![][11]

	<!--Image references-->
[1]: ./media/app-service-logic-connector-chatter/img1.PNG
[2]: ./media/app-service-logic-connector-chatter/img2.PNG
[3]: ./media/app-service-logic-connector-chatter/img3.png
[4]: ./media/app-service-logic-connector-chatter/img4.png
[5]: ./media/app-service-logic-connector-chatter/img5.PNG
[6]: ./media/app-service-logic-connector-chatter/img6.PNG
[7]: ./media/app-service-logic-connector-chatter/img7.PNG
[8]: ./media/app-service-logic-connector-chatter/img8.PNG
[9]: ./media/app-service-logic-connector-chatter/img9.PNG
[10]: ./media/app-service-logic-connector-chatter/img10.PNG
[11]: ./media/app-service-logic-connector-chatter/img11.PNG

<!---HONumber=August15_HO6-->