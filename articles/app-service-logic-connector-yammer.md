<properties 
   pageTitle="Application API du connecteur Yammer" 
   description="Utilisation du connecteur Yammer" 
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
   ms.date="03/31/2015"
   ms.author="adgoda"/>


# Utilisation du connecteur Yammer dans votre application logique #

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux. 

Le connecteur Yammer vous permet de vous connecter à Yammer et d'exécuter l'action Publier le message et un déclencheur pour récupérer le nouveau message.

## Création d'un connecteur Yammer pour votre application logique ##
Pour utiliser le connecteur Yammer, vous devez d'abord créer une instance de l'application API de ce connecteur. Pour cela, procédez comme suit :

1.	Ouvrez Azure Marketplace à l'aide de l'option + NOUVEAU en bas à gauche du portail Azure.
2.	Accédez à " Web et mobilité > API Apps " et recherchez " Connecteur Yammer ".
3.	Configurez le connecteur Yammer comme suit :
 
	![][1]
	- **Emplacement** - Choisissez l'emplacement géographique où vous souhaitez déployer le connecteur.
	- **Abonnement** - Choisissez l'abonnement dans lequel vous souhaitez créer ce connecteur.
	- **Groupe de ressources** - Sélectionnez ou créez le groupe de ressources dans lequel le connecteur doit résider.
	- **Plan d'hébergement web** - Sélectionnez ou créez un plan d'hébergement web.
	- **Niveau de tarification** - Choisissez le niveau de tarification du connecteur.
	- **Nom** - Indiquez le nom de votre connecteur Yammer. 

4.	Cliquez sur Créer. Un connecteur Yammer est créé.
5.	Une fois l'instance de l'application API créée, vous pouvez créer une application logique dans le même groupe de ressources pour utiliser le connecteur Yammer. 

## Utilisation du connecteur Yammer dans votre application logique ##
Une fois votre application API créée, vous pouvez utiliser le connecteur Yammer comme déclencheur ou action pour votre application logique. Pour cela, vous devez procéder comme suit :

1.	Créez une application logique et choisissez le groupe de ressources qui contient le connecteur Yammer.
 
	![][2]
2.	Ouvrez " Déclencheurs et actions " pour ouvrir le concepteur d'applications logiques et configurer votre flux. 
 
	![][3]
3.	Le connecteur Yammer apparaît dans la section " Applications API dans ce groupe de ressources " de la galerie située à droite.
 
	![][4]
4. Vous pouvez déposer l'application API du connecteur Yammer dans l'éditeur en cliquant sur " Connecteur Yammer ". Cliquez sur le bouton Autoriser. Indiquez vos informations d'identification Yammer. Cliquez sur " Autoriser ".
 
	![][5]
	![][6]
	![][7]
5.	Vous pouvez maintenant utiliser le connecteur Yammer dans le flux. Vous pouvez utiliser le nouveau message récupéré du déclencheur Yammer (" Nouveau message ") dans d'autres actions du flux. Configurez les propriétés d'entrée du déclencheur Yammer comme suit :

	- **ID de groupe** : ID du groupe à partir duquel le nouveau message doit être récupéré. Si l'ID de groupe n'est pas fourni, le message sera récupéré du flux suivant. L'ID de groupe peut être récupéré à partir de l'URL du groupe dans Yammer.
		Exemple : l'ID de groupe dans l'URL ci-dessous est " 5453203 ".
		https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203
 
	![][8]
	![][9]

	

6.	De même, vous pouvez utiliser l'action Yammer dans le flux pour publier un message, en sélectionnant l'action " Publier le message ". Configurez les propriétés d'entrée de l'action " Publier le message " comme suit :
	- **Texte du message** - Contenu du message à publier.
	- **ID de groupe** - Spécifiez l'ID du groupe auquel le nouveau message doit être publié. Si aucun ID de groupe n'est fourni, le message sera publié dans tous les flux de l'entreprise. L'ID de groupe peut être récupéré à partir de l'URL du groupe dans Yammer. 
	Exemple : l'ID de groupe dans l'URL ci-dessous est " 5453203 ".
	https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203
	- 	**Baliser les utilisateurs** - Tableau des noms d'utilisateur du réseau qui doivent être balisés dans le message. 
	- 	**Pièces jointes** : pièces jointes à publier avec la publication. Il contient les champs suivants :
		- Nom de fichier
		- Contenu
		- Encodage de transfert de contenu
		- Type de contenu

	![][10]
	![][11]




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


<!--HONumber=52-->