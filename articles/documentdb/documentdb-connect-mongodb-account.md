<properties 
	pageTitle="Connexion d’un compte DocumentDB prenant en charge le protocole MongoDB | Microsoft Azure" 
	description="Découvrez comment vous connecter à un compte DocumentDB prenant en charge le protocole MongoDB (actuellement disponible en version préliminaire). Connectez-vous à l’aide de votre chaîne de connexion MongoDB." 
	keywords="chaîne de connexion mongodb"
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/23/2016" 
	ms.author="mimig"/>

# Connexion d’un compte DocumentDB prenant en charge le protocole MongoDB

Découvrez comment vous connecter à un compte Azure DocumentDB prenant en charge le protocole MongoDB à l’aide du format d’URI de chaîne de connexion MongoDB standard.

## Obtenir les informations de chaîne de connexion du compte

1. Dans une nouvelle fenêtre, connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans la barre de **Navigation de gauche** du panneau Compte, cliquez sur **Chaîne de connexion**. Pour accéder au **Panneau Compte**, dans la barre de lancement, cliquez sur **Plus de services**, cliquez sur **DocumentDB (NoSQL)**, puis sélectionnez le compte DocumentDB prenant en charge le protocole MongoDB.

	![Capture d’écran du panneau Tous les paramètres](./media/documentdb-connect-mongodb-account/SettingsBlade.png)

3. Le panneau **Informations de chaîne de connexion** s’ouvre ; il contient toutes les informations nécessaires pour vous connecter au compte à l’aide d’un pilote MongoDB, y compris une chaîne de connexion préalablement construite.

	![Capture d’écran du panneau Chaîne de connexion](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## Exigences relatives à la chaîne de connexion

Il est important de noter que DocumentDB prend en charge le format d’URI de chaîne de connexion MongoDB standard, sous réserve que quelques exigences spécifiques soient satisfaites : les comptes DocumentDB requièrent une authentification et une communication sécurisée via SSL. Le format de chaîne de connexion est donc :

	mongodb://username:password@host:port/[database]?ssl=true

Les valeurs de cette chaîne sont disponibles dans le panneau Chaîne de connexion illustré ci-dessus.

- Nom d’utilisateur (obligatoire)
	- Nom du compte DocumentDB
- Mot de passe (obligatoire)
	- Mot de passe du compte DocumentDB
- Hôte (obligatoire)
	- Nom de domaine complet du compte DocumentDB
- Port (obligatoire)
	- 10250
- Base de données (facultatif)
	- Base de données par défaut utilisée par la connexion
- ssl = true (obligatoire)

Par exemple, observez le compte décrit dans les informations de chaîne de connexion ci-dessus. Exemple de chaîne de connexion valide :
	
	mongodb://contoso123:<password@contoso123.documents.azure.com:10250/mydatabase?ssl=true

## Connexion avec le pilote C# pour MongoDB
Comme nous l’avons déjà mentionné, tous les comptes DocumentDB requièrent une authentification et une communication sécurisée via SSL. Le format URI de chaîne de connexion MongoDB prend en charge un paramètre de chaîne de connexion de type ssl = true ; l’utilisation du pilote C# MongoDB suppose en revanche d’utiliser l’objet MongoClientSettings lors de la création d’un MongoClient. Au vu des informations de compte ci-dessus, l’extrait de code suivant vous montre comment vous connecter au compte et utiliser la base de données « Tâches ».

	        MongoClientSettings settings = new MongoClientSettings();
            settings.Server = new MongoServerAddress("contoso123.documents.azure.com", 10250);
            settings.UseSsl = true;
            settings.SslSettings = new SslSettings();
            settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

            MongoIdentity identity = new MongoInternalIdentity("Tasks", "contoso123");
            MongoIdentityEvidence evidence = new PasswordEvidence("<password>");

            settings.Credentials = new List<MongoCredential>()
            {
                new MongoCredential("SCRAM-SHA-1", identity, evidence)
            };
            MongoClient client = new MongoClient(settings);
            var database = client.GetDatabase("Tasks",);
	

## Étapes suivantes


- Apprenez à [utiliser MongoChef](documentdb-mongodb-mongochef.md) avec un compte DocumentDB prenant en charge le protocole MongoDB.
- Découvrez des [exemples](documentdb-mongodb-samples.md) de prise en charge du protocole MongoDB dans DocumentDB.

 

<!---HONumber=AcomDC_0928_2016-->