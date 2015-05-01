<properties 
   pageTitle="Application API du connecteur Azure Service Bus" 
   description="Utilisation du connecteur Azure Service Bus" 
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


# Utilisation du connecteur Azure Service Bus dans votre application logique #

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux. 

Le connecteur Azure Service Bus vous permet d'échanger des messages avec une file d'attente, une rubrique ou un abonnement.

## Création d'un connecteur Azure Service Bus pour votre application logique ##
Pour utiliser le connecteur Azure Service Bus, vous devez d'abord créer une instance de l'application API de ce connecteur. Pour cela, procédez comme suit :

1.	Ouvrez Azure Marketplace à l'aide de l'option + NOUVEAU en bas à gauche du portail Azure.
2.	Accédez à " Web et mobilité > API Apps " et recherchez " Connecteur Azure Service Bus ".
3.	Configurez le connecteur Azure Service Bus comme suit :
 
	![][1]
	- **Emplacement** : choisissez l'emplacement géographique dans lequel vous voulez déployer le connecteur.
	- **Abonnement** : choisissez un abonnement dans lequel vous voulez que ce connecteur soit créé.
	- **Groupe de ressources** : sélectionnez ou créez un groupe de ressources dans lequel doit résider le connecteur.
	- **Plan d'hébergement web** : sélectionnez ou créez un plan d'hébergement web.
	- **Niveau de tarification** : choisissez un niveau de tarification pour le connecteur.
	- **Nom** : indiquez le nom de votre connecteur Azure Service Bus.
	- **Paramètres du package**
		- **Chaîne de connexion** : chaîne de connexion à Azure Service Bus. Par exemple : Point de terminaison=sb://[espace de noms].servicebus.windows.net;SharedAccessKeyName=[nom];SharedAccessKey=[clé]
		- **Nom de l'entité** : nom de la file d'attente ou de la rubrique.
		- **Nom de l'abonnement** : nom de l'abonnement à partir duquel recevoir les messages. 

4.	Cliquez sur Créer. Un connecteur Azure Service Bus est créé.
5.	Une fois l'instance d'application API créée, vous pouvez créer une application logique dans le même groupe de ressources pour utiliser le connecteur Azure Service Bus. 

## Utilisation du connecteur Azure Service Bus dans votre application logique ##
Une fois votre application API créée, vous pouvez utiliser le connecteur Azure Service Bus comme déclencheur ou action pour votre application logique. Pour cela, vous devez procéder comme suit :

1.	Créez une application logique et choisissez le groupe de ressources qui contient le connecteur Azure Service Bus.
 
	![][2]
2.	Ouvrez " Déclencheurs et actions " pour ouvrir le concepteur d'applications logiques et configurez votre flux. 
 
	![][3]
3.	Le connecteur Azure Service Bus s'affiche dans la section " Applications API dans ce groupe de ressources " de la galerie située à droite.
 
	![][4]
4. Vous pouvez déposer l'application API du connecteur Azure Service Bus dans l'éditeur en cliquant sur " Connecteur Azure Service Bus ".
 
5.	Vous pouvez maintenant utiliser le connecteur Azure Service Bus dans le flux. Vous pouvez utiliser le message récupéré du déclencheur Azure Service Bus (" Message disponible ") dans les autres actions du flux.
 
	![][5]
	![][6] 
6.	De même, vous pouvez utiliser l'action Azure Service Bus " Envoyer le message " pour envoyer un message.

	![][7]
	![][8]


<!--Image references-->
[1]: ./media/app-service-logic-connector-azureservicebus/img1.PNG
[2]: ./media/app-service-logic-connector-azureservicebus/img2.PNG
[3]: ./media/app-service-logic-connector-azureservicebus/img3.png
[4]: ./media/app-service-logic-connector-azureservicebus/img4.PNG
[5]: ./media/app-service-logic-connector-azureservicebus/img5.PNG
[6]: ./media/app-service-logic-connector-azureservicebus/img6.PNG
[7]: ./media/app-service-logic-connector-azureservicebus/img7.PNG
[8]: ./media/app-service-logic-connector-azureservicebus/img8.PNG


<!--HONumber=52-->