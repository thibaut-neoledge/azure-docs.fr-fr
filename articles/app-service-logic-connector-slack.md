<properties 
	pageTitle="Connecteur Slack"
	description="Prise en main du connecteur Slack"
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
	ms.date="04/21/2015"
	ms.author="andalmia"/>

# Utilisation du connecteur Slack dans votre application logique #

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux.

Le connecteur Slack vous permet de publier des messages dans des canaux Slack.

## Création d'un connecteur Slack pour votre application logique ##
Pour utiliser le connecteur Slack, vous devez d'abord créer une instance de l'application API de ce connecteur. Pour cela, procédez comme suit :

1.	Ouvrez Azure Marketplace à l'aide de l'option « +NOUVEAU » située dans la partie inférieure gauche du portail Azure.
2.	Accédez à « Web et mobilité \> Azure Marketplace » et recherchez « Connecteur Slack ».
3.	Configurez le connecteur Slack comme suit :
 
	![][1] - **Nom** : donnez un nom à votre connecteur Slack - **Plan App Service** : sélectionnez ou créez un plan App Service - **Niveau de tarification** : choisissez un niveau de tarification pour le connecteur - **Groupe de ressources** : sélectionnez ou créez un groupe de ressources où le connecteur doit résider - **Abonnement** : choisissez un abonnement dans lequel vous souhaitez que ce connecteur soit créé - **Emplacement** : choisissez l'emplacement géographique dans lequel vous souhaitez que le connecteur soit déployé

4. Cliquez sur Créer. Un connecteur Slack est créé.
5. Une fois l'instance d'application API créée, vous pouvez créer une application dans le même groupe de ressources pour utiliser le connecteur Slack.

## Utilisation du connecteur Slack dans votre application logique ##
Une fois votre application API créée, vous pouvez utiliser le connecteur Slack comme action pour votre application logique. Pour cela, vous devez procéder comme suit :

1.	Créez une application logique et choisissez le groupe de ressources qui contient le connecteur Slack. Suivez les instructions indiquées dans la rubrique [Création d'une application logique].  	
	
2.	Ouvrez « Déclencheurs et actions » dans l'application logique créée pour ouvrir le Concepteur d'applications logiques et configurez votre flux.
	
3.	Le connecteur Slack apparaît dans la section « Applications API dans ce groupe de ressources » de la galerie située à droite.
 
	![][2]
4.	Vous pouvez déposer l'application API du connecteur Slack dans l'éditeur en cliquant sur « Connecteur Slack ». Cliquez sur le bouton Autoriser. Indiquez vos informations d'identification Microsoft \(si vous n'êtes pas connecté automatiquement\). Connectez-vous à votre compte Slack en suivant les étapes indiquées. À la fin, vous serez invité à autoriser votre connecteur à accéder à votre compte Slack. Cliquez sur « Autoriser ».
 
	![][3] ![][4] ![][5] ![][6]
	
5.	Vous pouvez maintenant utiliser le connecteur Slack dans le flux. Actuellement, aucun déclencheur n'est disponible dans le connecteur Slack. Les actions disponibles sont : Publier le message
 
	![][7]

6.	Testons l'action « Publier le message ». Vous pouvez utiliser cette action pour publier un message vers n'importe quel canal Slack.
 
	![][8]

	Configurez les propriétés d'entrée de l'action « Publier le message » comme suit :

 - **Texte** : spécifiez le texte du message à publier
 - **Nom du canal** : spécifiez le canal Slack vers lequel ce message doit être téléchargé. Si cette valeur n'est pas spécifiée, le message est publié dans \#general.

 **Propriétés avancées** - **Nom d'utilisateur du robot** : nom du robot à utiliser pour ce message. Le message est publié en tant que « Robot » si cette valeur n'est pas spécifiée. - **URL de l'icône** : URL vers une image à utiliser comme icône pour ce message - **Emoji de l'icône** : Emoji à utiliser comme icône pour ce message. Remplace l'URL de l'icône.
 

7. Pour utiliser le connecteur hors d'une application logique, vous devez faire appel aux API REST exposées par celui-ci. Pour afficher ces définitions d'API, sélectionnez Parcourir -\> Application API -\> Connecteur Slack. Maintenant, cliquez sur le filtre Définition des API sous la section Résumé pour afficher toutes les API exposées par ce connecteur.

	![][9]

9. Pour plus d'informations sur les API, consultez la rubrique [Définition des API Slack ].

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

<!-- Links -->
[Création d'une application logique]: app-service-logic-create-a-logic-app.md
[Définition des API Slack ]: https://msdn.microsoft.com/fr-fr/library/dn708020.aspx
<!--HONumber=52-->
