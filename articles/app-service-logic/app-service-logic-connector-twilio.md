<properties
   pageTitle="Application API du connecteur Twilio"
   description="Utilisation du connecteur Twilio"
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


# Utilisation du connecteur Twilio dans votre application logique #

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux.

Le connecteur Twilio vous permet d’envoyer et de recevoir des SMS à l’aide de votre compte Twilio. Il vous permet également de récupérer des numéros de téléphone et des données d'utilisation.

## Création d’un connecteur Twilio pour votre application logique ##
Pour utiliser le connecteur Twilio, vous devez d’abord créer une instance de l’application API de ce connecteur. Pour cela, procédez comme suit :

1.	Ouvrez Azure Marketplace à l’aide de l’option + NOUVEAU en bas à gauche du portail Azure.
2.	Accédez à « API Apps » et recherchez « Connecteur Twilio ».
3.	Configurez le connecteur Twilio comme suit :
 
	![][1]
	- **Emplacement** : choisissez l'emplacement géographique dans lequel vous voulez déployer le connecteur
	- **Abonnement** : choisissez un abonnement dans lequel vous souhaitez créer ce connecteur
	- **Groupe de ressources** : sélectionnez ou créez un groupe de ressources où le connecteur doit résider
	- **Plan d’hébergement Web** : sélectionnez ou créez un plan d’hébergement Web
	- **Niveau de tarification** : choisissez un niveau de tarification pour le connecteur
	- **Nom** : donnez un nom à votre connecteur Twilio
	- **Paramètres du package**
		- **SID du compte** : identificateur unique du compte. Le SID de votre compte peut être récupéré à partir de <https://www.twilio.com/user/account/settings>
		- **Jeton d'autorisation** : jeton d'autorisation associé au compte. Vous pouvez récupérer ce jeton à l'adresse <https://www.twilio.com/user/account/settings>


4.	Cliquez sur Créer. Un connecteur Twilio est créé.
5.	Une fois l’instance de l’application API créée, vous pouvez créer une application logique dans le même groupe de ressources pour utiliser le connecteur Twilio. 

## Utilisation du connecteur Twilio dans votre application logique ##
Une fois votre application API créée, vous pouvez utiliser le connecteur Twilio comme action dans votre application logique. Pour cela, vous devez procéder comme suit :

1.	Créez une application logique et choisissez le groupe de ressources qui contient le connecteur Twilio.
 
	![][2]
2.	Ouvrez « Déclencheurs et actions » pour ouvrir le concepteur d’applications logiques et configurer votre flux. 
 
	![][3]
3.	Le connecteur Twilio apparaît dans la section « Applications API dans ce groupe de ressources » de la galerie située à droite.
 
	![][4]
4. Vous pouvez déposer l’application API du connecteur Twilio dans l’éditeur en cliquant sur « Connecteur Twilio ».
 
5.	Vous pouvez maintenant utiliser le connecteur Twilio dans le flux. Vous pouvez utiliser l’action « Envoyer un message » dans le flux pour envoyer un message. Configurez les propriétés d’entrée de l’action « Envoyer un message » comme suit :
	- **À partir du numéro de téléphone** : saisissez un numéro de téléphone Twilio autorisant le type de message que vous souhaitez envoyer. Seuls les numéros de téléphone ou les codes courts achetés auprès de Twilio fonctionnent avec ce connecteur.
	- **Vers le numéro de téléphone** : numéro de téléphone de destination. Le format accepté est : + suivi du préfixe du pays et du numéro de téléphone. Par exemple, +16175551212. Si vous oubliez le +, Twilio utilise le préfixe de pays que vous avez saisi dans le champ « À partir ».
	- **Texte** : texte du message à envoyer.
 
	![][5]
	![][6]



	<!--Image references-->
[1]: ./media/app-service-logic-connector-twilio/img1.PNG
[2]: ./media/app-service-logic-connector-twilio/img2.PNG
[3]: ./media/app-service-logic-connector-twilio/img3.png
[4]: ./media/app-service-logic-connector-twilio/img4.png
[5]: ./media/app-service-logic-connector-twilio/img5.PNG
[6]: ./media/app-service-logic-connector-twilio/img6.PNG

<!---HONumber=August15_HO6-->