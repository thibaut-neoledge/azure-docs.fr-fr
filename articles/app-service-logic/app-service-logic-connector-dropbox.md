<properties 
	pageTitle="Connecteur Dropbox"
	description="Prise en main du connecteur Dropbox"
	authors="anuragdalmia" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/31/2015"
	ms.author="adgoda"/>

# Utilisation du connecteur Dropbox dans votre application logique #

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux.

Le connecteur Dropbox vous permet de télécharger des fichiers vers ou depuis votre compte Dropbox.

## Création d’un connecteur Dropbox pour votre application logique ##
Pour utiliser le connecteur Dropbox, vous devez d’abord créer une instance de l’application API de ce connecteur. Pour cela, procédez comme suit :

1.	Ouvrez Azure Marketplace à l’aide de l’option + NOUVEAU en bas à gauche du portail Azure.
2.	Accédez à « Web et mobilité --> Azure Marketplace » et recherchez « Connecteur Dropbox ».
3.	Configurez le connecteur Dropbox comme suit :
 
	![][1] - **Emplacement** : choisissez l'emplacement géographique dans lequel vous voulez déployer le connecteur - **Abonnement** : choisissez un abonnement dans lequel vous souhaitez créer ce connecteur - **Groupe de ressources** : sélectionnez ou créez un groupe de ressources où le connecteur doit résider - **Plan de service de l'application** : sélectionnez ou créez un plan d'hébergement Web - **Niveau de tarification** : choisissez un niveau de tarification pour le connecteur - **Nom** : donnez un nom à votre connecteur Dropbox
4. Cliquez sur Créer. Un connecteur Dropbox est créé.
5. Une fois l’installation de l’application API créée, vous pouvez créer une application logique dans le même groupe de ressources pour utiliser le connecteur Dropbox.

## Utilisation du connecteur Dropbox dans votre application logique ##
Une fois votre application API créée, vous pouvez utiliser le connecteur Dropbox comme une action pour votre application logique. Pour cela, vous devez procéder comme suit :

1.	Créez une application logique et choisissez le groupe de ressources qui contient le connecteur Dropbox.
 	
	![][2]
2.	Ouvrez « Déclencheurs et actions » pour ouvrir le concepteur d’applications logiques et configurer votre flux. 
 	
	![][3]
3.	Le connecteur Dropbox s’affiche dans la section « Applications API dans ce groupe de ressources » de la galerie à droite.
 
	![][4]
4.	Vous pouvez déposer l’application API du connecteur Dropbox dans l’éditeur en cliquant sur « Connecteur Dropbox ». Cliquez sur le bouton Autoriser. Indiquez vos informations d’identification Dropbox. Cliquez sur « Autoriser ».
 
	![][5]
	![][6]
	![][7]
6.	Vous pouvez maintenant utiliser le connecteur Dropbox dans le flux. Vous pouvez utiliser l’action Dropbox « Upload File » pour télécharger un fichier dans votre compte Dropbox.
 
	![][8]
	![][9]

Configurez les propriétés d’entrée de l’action « Charger le fichier » comme suit :

- **Chemin du fichier** : spécifiez le chemin de destination Dropbox du fichier à charger. Exemple : Photos/image.png
- **Contenu** : spécifie le contenu du fichier à télécharger. Souvent, cette opération s'effectue à partir d'une étape précédente de l'application logique.
- **Encodage de transfert de contenu** : spécifiez none ou base64.
- **Remplacer** : spécifiez « true » pour remplacer le fichier existant.


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-dropbox/img1.PNG
[2]: ./media/app-service-logic-connector-dropbox/img2.PNG
[3]: ./media/app-service-logic-connector-dropbox/img3.png
[4]: ./media/app-service-logic-connector-dropbox/img4.png
[5]: ./media/app-service-logic-connector-dropbox/img5.PNG
[6]: ./media/app-service-logic-connector-dropbox/img6.PNG
[7]: ./media/app-service-logic-connector-dropbox/img7.PNG
[8]: ./media/app-service-logic-connector-dropbox/img8.PNG
[9]: ./media/app-service-logic-connector-dropbox/img9.PNG
 

<!----HONumber=62-->