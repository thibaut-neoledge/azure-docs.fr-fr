<properties 
   pageTitle="Application API du connecteur SMTP" 
   description="Utilisation du connecteur SMTP" 
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


# Utilisation du connecteur SMTP dans votre application logique #

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux.

Le connecteur SMTP vous permet de vous connecter à un serveur SMTP et d’exécuter une action pour envoyer un message électronique avec des pièces jointes. Avec l’action « Envoyer le message électronique » du connecteur SMTP, vous pouvez envoyer un message électronique à une ou plusieurs adresses électroniques.

## Création d’un connecteur SMTP pour votre application logique ##
Pour utiliser le connecteur SMTP, vous devez d’abord créer une instance de l’application API de ce connecteur. Pour cela, procédez comme suit :

1.	Ouvrez Azure Marketplace à l’aide de l’option + NOUVEAU en bas à gauche du portail Azure.
2.	Accédez à « Web et mobilité > Azure Marketplace » et recherchez « Connecteur SMTP ».
3.	Configurez le connecteur SMTP comme suit :
 
	![][1] - **Emplacement** : choisissez l'emplacement géographique dans lequel vous voulez déployer le connecteur - **Abonnement** : choisissez un abonnement dans lequel vous souhaitez créer ce connecteur - **Groupe de ressources** : sélectionnez ou créez un groupe de ressources où le connecteur doit résider - **Plan d'hébergement Web** : sélectionnez ou créez un plan d'hébergement Web - **Niveau de tarification** : choisissez un niveau de tarification pour le connecteur - **Nom** : donnez un nom à votre connecteur SMTP - **Paramètres du package** - **Nom d'utilisateur** : spécifiez le nom d'utilisateur pour la connexion au serveur SMTP - **Mot de passe** : spécifiez le mot de passe pour la connexion au serveur SMTP - **Adresse du serveur** : spécifiez le nom ou l'adresse IP du serveur SMTP - **Port du serveur** : spécifiez le numéro de port du serveur SMTP - **Utiliser SSL** : spécifiez true pour utiliser SMTP sur un canal SSL/TLS sécurisé
4.	Cliquez sur Créer. Un connecteur SMTP est créé.
5.	Une fois l’instance d’application API créée, vous pouvez créer une application dans le même groupe de ressources pour utiliser le connecteur SMTP. 

## Utilisation du connecteur SMTP dans votre application logique ##
Une fois votre application API créée, vous pouvez utiliser le connecteur SMTP comme action pour votre application logique. Pour cela, vous devez procéder comme suit :

1.	Créez une application logique et choisissez le groupe de ressources qui contient le connecteur SMTP.
 
	![][2]
2.	Ouvrez « Déclencheurs et actions » pour ouvrir le concepteur d’applications logiques et configurer votre flux. 
 
	![][3]
3.	Le connecteur SMTP apparaît dans la section « Applications API dans ce groupe de ressources » de la galerie située à droite. Sélectionnez-le.
 
	![][4]
4.	Vous pouvez déposer l’application API du connecteur SMTP dans l’éditeur en cliquant sur « Connecteur SMTP ». 
	
7.	Vous pouvez maintenant utiliser le connecteur SMTP dans le flux. Sélectionnez l’action « Envoyer le message électronique » et configurez les propriétés d’entrée comme suit :
	- **À** : adresse électronique du ou des destinataires. Séparez les adresses électroniques avec un point-virgule (;). Par exemple : recipient1@domain.com;recipient2@domain.com.
	- **Cc** : adresse électronique du ou des destinataires en copie. Séparez les adresses électroniques avec un point-virgule (;). Par exemple : recipient1@domain.com;recipient2@domain.com.
	- **Objet** : objet du message électronique.
	- **Corps** : corps du message électronique.
	- **HTML** : lorsque cette propriété est configurée sur true, le contenu du corps est envoyé au format HTML.
	- **Cci** : adresse électronique du ou des destinataires en copie cachée. Séparez les adresses électroniques avec un point-virgule (;). Par exemple : recipient1@domain.com;recipient2@domain.com.
	- **Importance** : importance du message électronique. Les options sont Normale, Faible, Haute.
	- **Pièces jointes** : pièces jointes à envoyer avec le message électronique. Il contient les champs suivants :
		- Contenu (chaîne)
		- Encodage de transfert de contenu (Enum) (« none »|« base64 »)
		- Type de contenu (chaîne)
		- ID de contenu (chaîne)
		- Nom de fichier (chaîne)
	 
	
	![][5] ![][6]


	<!--Image references-->
[1]: ./media/app-service-logic-connector-smtp/img1.PNG
[2]: ./media/app-service-logic-connector-smtp/img2.PNG
[3]: ./media/app-service-logic-connector-smtp/img3.png
[4]: ./media/app-service-logic-connector-smtp/img4.PNG
[5]: ./media/app-service-logic-connector-smtp/img5.PNG
[6]: ./media/app-service-logic-connector-smtp/img6.PNG
 

<!---HONumber=62-->