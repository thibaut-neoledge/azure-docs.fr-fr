<properties
	pageTitle="Utilisation du connecteur OneDrive dans des applications logiques | Microsoft Azure App Service"
	description="Comment créer et configurer le connecteur OneDrive ou une application API et l'utiliser dans une application logique d’Azure App Service"
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
	ms.date="08/23/2015"
	ms.author="andalmia"/>

# Utilisation et ajout du connecteur OneDrive dans votre application logique
Connectez-vous à votre compte OneDrive pour charger, télécharger et supprimer des fichiers. Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux. Vous pouvez ajouter le connecteur OneDrive à votre flux d’entreprise et traiter les données dans le cadre de ce flux de travail dans une application logique.

## Création d’un connecteur OneDrive pour votre application logique ##
Pour utiliser le connecteur OneDrive, vous devez d’abord créer une instance de l’application API de ce connecteur. Cette opération est possible à partir du concepteur d'application logique, directement ou de l'extérieur. Vous pouvez créer une instance de l'extérieur du concepteur en procédant comme suit :

1.	Ouvrez Azure Marketplace à partir de la page d'accueil du portail Azure.
2.	Sous « Tout », recherchez « Connecteur OneDrive ».
3.	Configurez le connecteur OneDrive comme suit :

	![][1]
	- **Nom** : donnez un nom à votre connecteur OneDrive
	- **Plan App Service** : sélectionnez ou créez un plan App Service
	- **Niveau de tarification** : choisissez un niveau de tarification pour le connecteur
	- **Groupe de ressources** : sélectionnez ou créez un groupe de ressources où le connecteur doit résider
	- **Abonnement** : choisissez un abonnement dans lequel vous souhaitez créer ce connecteur
	- **Emplacement** : choisissez l'emplacement géographique dans lequel vous voulez déployer le connecteur

4. Cliquez sur Créer. Un connecteur OneDrive est créé.
5. Une fois l’instance de l’application API créée, vous pouvez créer une application logique dans le même groupe de ressources pour utiliser le connecteur OneDrive.

## Utilisation du connecteur OneDrive dans votre application logique ##
Une fois votre application API créée, vous pouvez utiliser le connecteur OneDrive comme une action pour votre application logique. Pour cela, vous devez procéder comme suit :

1.	Créez une application logique et choisissez le groupe de ressources qui contient le connecteur OneDrive. Suivez les instructions indiquées dans la rubrique [Création d'une application logique].

2.	Ouvrez « Déclencheurs et actions » dans l’application logique créée pour ouvrir le Concepteur d’applications logiques et configurez votre flux.

3.	Le connecteur OneDrive s’affiche dans la section « Applications API dans ce groupe de ressources » de la galerie située à droite.

	![][2]
4.	Vous pouvez déposer l’application API du connecteur OneDrive dans l’éditeur en cliquant sur « Connecteur OneDrive ». Cliquez sur le bouton Autoriser. Indiquez vos informations d’identification Microsoft (si vous n’êtes pas connecté automatiquement). Cliquez sur « Oui » pour autoriser l’accès.

	![][3]
	![][4]

5.	Vous pouvez maintenant utiliser le connecteur OneDrive dans le flux. Actuellement, aucun déclencheur n’est disponible dans le connecteur OneDrive. Les actions disponibles sont Obtenir le fichier, charger un fichier, Supprimer le fichier et Répertorier les fichiers.

	![][5]

6.	Testons l’action « Charger un fichier ». Vous pouvez utiliser l’action OneDrive « Charger un fichier » pour charger un fichier dans votre compte OneDrive.

	![][6]

	Configurez les propriétés d’entrée de l’action « Charger le fichier » comme suit :

 - **Chemin du fichier** : spécifiez le chemin du fichier à charger.
 - **Contenu** : spécifie le contenu du fichier à télécharger.
 - **Encodage de transfert de contenu** : spécifiez none ou base64.
 - **Remplacer** : spécifiez « true » pour remplacer le fichier existant. Il s’agit d’une propriété avancée.

7. Une fois ces propriétés définies, l’action « Charger un fichier » est configurée et utilisable dans votre flux. De même, d’autres actions peuvent être configurées.

8. Pour utiliser le connecteur hors d’une application logique, vous devez faire appel aux API REST exposées par celui-ci. Pour afficher ces définitions d’API, sélectionnez Parcourir -> API Apps -> Connecteur OneDrive. Maintenant, cliquez sur le filtre Définition des API sous la section Résumé pour afficher toutes les API exposées par ce connecteur.

	![][7]

9. Pour plus d'informations sur les API, consultez la rubrique [Définition des API OneDrive].

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure Logic Apps avant d'ouvrir un compte Azure, accédez à la page [Essayer Logic App](https://tryappservice.azure.com/?appservice=logic), où vous pourrez créer immédiatement une application logique temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Pour plus d'informations, consultez [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-onedrive/img1.PNG
[2]: ./media/app-service-logic-connector-onedrive/img2.PNG
[3]: ./media/app-service-logic-connector-onedrive/img3.PNG
[4]: ./media/app-service-logic-connector-onedrive/img4.PNG
[5]: ./media/app-service-logic-connector-onedrive/img5.PNG
[6]: ./media/app-service-logic-connector-onedrive/img6.PNG
[7]: ./media/app-service-logic-connector-onedrive/img7.PNG

<!-- Links -->
[Création d'une application logique]: app-service-logic-create-a-logic-app.md
[Définition des API OneDrive]: https://msdn.microsoft.com/library/dn974227.aspx

<!---HONumber=Oct15_HO1-->