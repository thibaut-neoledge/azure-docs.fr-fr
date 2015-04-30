<properties 
   pageTitle="Application API du connecteur POP3" 
   description="Utilisation du connecteur POP3" 
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


# Utilisation du connecteur POP3 dans votre application logique #

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux. 

Le connecteur POP3 vous permet de vous connecter au serveur POP3 et fournit un déclencheur qui récupère les messages électroniques avec des pièces jointes.

## Création d'un connecteur POP3 pour votre application logique ##
Pour utiliser le connecteur POP3, vous devez d'abord créer une instance de l'application API de ce connecteur. Pour cela, procédez comme suit :

1.	Ouvrez Azure Marketplace à l'aide de l'option + NOUVEAU en bas à gauche du portail Azure.
2.	Accédez à " Web et mobilité > API Apps " et recherchez " Connecteur POP3 ".
3.	Configurez le connecteur POP3 comme suit :
 
	![][1]
	- **Emplacement** : choisissez l'emplacement géographique dans lequel vous voulez déployer le connecteur.
	- **Abonnement** : choisissez un abonnement dans lequel vous voulez que ce connecteur soit créé.
	- **Groupe de ressources** : sélectionnez ou créez un groupe de ressources dans lequel doit résider le connecteur.
	- **Plan d'hébergement web** : sélectionnez ou créez un plan d'hébergement web.
	- **Niveau de tarification** : choisissez un niveau de tarification pour le connecteur.
	- **Nom** : indiquez le nom de votre connecteur POP3.
	- **Paramètres du package**
		- **Nom d'utilisateur** : spécifiez le nom d'utilisateur de connexion au serveur POP3.
		- **Mot de passe** : spécifiez le mot de passe de connexion au serveur POP3.
		- **Adresse du serveur** : spécifiez le nom ou l'adresse IP du serveur POP3.
		- **Port du serveur** : spécifiez le numéro de port du serveur POP3.
		- **Activer SSL** : spécifiez " true " pour utiliser POP3 sur un canal SSL/TLS sécurisé.
4.	Cliquez sur Créer. Un connecteur POP3 est créé.
5.	Une fois l'instance de l'application API créée, vous pouvez créer une application logique dans le même groupe de ressources pour utiliser le connecteur POP3. 

## Utilisation du connecteur POP3 dans votre application logique ##
Une fois votre application API créée, vous pouvez utiliser le connecteur POP3 comme déclencheur pour votre application logique. Pour cela, vous devez procéder comme suit :

1.	Créez une application logique et choisissez le groupe de ressources qui contient le connecteur POP3.
 
	![][2]
2.	Ouvrez " Déclencheurs et actions " pour ouvrir le concepteur d'applications logiques et configurer votre flux. 
 
	![][3]
3.	Le connecteur POP3 s'affiche dans la section " Applications API dans ce groupe de ressources " de la galerie située à droite. Sélectionnez-le.
 
	![][4]
4.	Vous pouvez déposer l'application API du connecteur POP3 dans l'éditeur en cliquant sur " Connecteur POP3 ". 
	
5.	Vous pouvez maintenant utiliser le connecteur POP3 dans le flux. Sélectionnez le déclencheur " Obtenir le message électronique " et configurez la fréquence et l'intervalle. Vous pouvez utiliser le message électronique récupéré du déclencheur POP3 dans d'autres actions du flux.
		 
	
	![][5]
	![][6]


<!--Image references-->
[1]: ./media/app-service-logic-connector-pop3/img1.PNG
[2]: ./media/app-service-logic-connector-pop3/img2.PNG
[3]: ./media/app-service-logic-connector-pop3/img3.png
[4]: ./media/app-service-logic-connector-pop3/img4.PNG
[5]: ./media/app-service-logic-connector-pop3/img5.PNG
[6]: ./media/app-service-logic-connector-pop3/img6.PNG

<!--HONumber=52-->