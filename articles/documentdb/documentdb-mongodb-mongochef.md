<properties 
	pageTitle="Utiliser MongoChef avec un compte DocumentDB prenant en charge le protocole MongoDB | Microsoft Azure" 
	description="Découvrez comment utiliser MongoChef avec un compte DocumentDB prenant en charge le protocole MongoDB (actuellement disponible en version préliminaire)." 
	keywords="mongochef"
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2016" 
	ms.author="stbaro"/>

# Utiliser MongoChef avec un compte DocumentDB prenant en charge le protocole MongoDB

Pour vous connecter à un compte DocumentDB prenant en charge le protocole MongoDB à l’aide de MongoChef, vous devez :

- Télécharger et installer [MongoChef](http://3t.io/mongochef)
- Posséder un compte DocumentDB avec les informations de la [chaîne de connexion](documentdb-connect-mongodb-account.md) pour la prise en charge du protocole MongoDB

## Créer la connexion dans MongoChef  

Pour ajouter votre compte DocumentDB prenant en charge le protocole MongoDB au gestionnaire de connexions MongoChef, procédez comme suit.

1. Récupérez votre compte DocumentDB avec les informations de connexion pour la prise en charge du protocole MongoDB à l’aide de [ces instructions](documentdb-connect-mongodb-account.md).

	![Capture d’écran du panneau Chaîne de connexion](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

2. Cliquez sur **Connexion** pour ouvrir le gestionnaire de connexions, puis cliquez sur **Nouvelle connexion**.

	![Capture d’écran du Gestionnaire de connexions MongoChef](./media/documentdb-mongodb-mongochef/ConnectionManager.png)
	
2. Sous l’onglet **Serveur** de la fenêtre **Nouvelle connexion**, entrez l’HÔTE (FQDN) du compte DocumentDB prenant en charge le protocole MongoDB et le PORT.
	
	![Capture d’écran de l’onglet Serveur du Gestionnaire de connexions MongoChef](./media/documentdb-mongodb-mongochef/ConnectionManagerServerTab.png)

3. Dans la fenêtre **Nouvelle connexion**, sous l’onglet **Authentification**, choisissez le mode d’authentification **Standard (MONGODB-CR ou SCARM-SHA-1)** et entrez les NOM D’UTILISATEUR et MOT DE PASSE. Acceptez la base de données d’authentification par défaut (admin) ou indiquez votre propre valeur.

	![Capture d’écran de l’onglet Authentification du Gestionnaire de connexions MongoChef](./media/documentdb-mongodb-mongochef/ConnectionManagerAuthenticationTab.png)

4. Dans la fenêtre **Nouvelle connexion**, sous l’onglet **SSL**, cochez la case **Use SSL protocol to connect** (Utiliser le protocole SSL pour se connecter) et cliquez sur la case d’option **Accept self-signed SSL certificates** (Accepter les certificats SSL auto-signés).

	![Capture d’écran de l’onglet SSL du Gestionnaire de connexions MongoChef](./media/documentdb-mongodb-mongochef/ConnectionManagerSSLTab.png)

5. Cliquez sur le bouton **Tester la connexion** pour valider les informations de connexion, cliquez sur **OK** pour revenir à la fenêtre Nouvelle connexion, puis cliquez sur **Enregistrer**.

	![Capture d’écran de la fenêtre Tester la connexion de MongoChef](./media/documentdb-mongodb-mongochef/TestConnectionResults.png)

## Utiliser MongoChef pour créer une base de données, une collection et des documents  

Pour créer une base de données, une collection et des documents à l’aide de MongoChef, procédez comme suit.

1. Dans le **Gestionnaire de connexions**, sélectionnez la connexion et cliquez sur **Connexion**.

	![Capture d’écran du Gestionnaire de connexions MongoChef](./media/documentdb-mongodb-mongochef/ConnectToAccount.png)

2. Cliquez avec le bouton droit sur l’hôte et choisissez **Ajouter une base de données**. Spécifiez un nom de base de données, puis cliquez sur **OK**.
	
	![Capture d’écran de l’option Ajouter une base de données de MongoChef](./media/documentdb-mongodb-mongochef/AddDatabase1.png)

3. Cliquez avec le bouton droit sur la base de données et sélectionnez **Ajouter une collection**. Spécifiez un nom de collection et cliquez sur **Créer**.

	![Capture d’écran de l’option Ajouter une collection de MongoChef](./media/documentdb-mongodb-mongochef/AddCollection.png)

4. Cliquez sur l’élément de menu **Collection**, puis cliquez sur **Ajouter un document**.

	![Capture d’écran de l’option Ajouter un document de MongoChef](./media/documentdb-mongodb-mongochef/AddDocument1.png)

5. Dans la boîte de dialogue Ajouter un document, collez les éléments suivants, puis cliquez sur **Ajouter un document**.

		{
    	"_id": "AndersenFamily",
    	"lastName": "Andersen",
    	"parents": [
       		{ "firstName": "Thomas" },
       		{ "firstName": "Mary Kay"}
    	],
    	"children": [
       	{
           	"firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
           	"pets": [{ "givenName": "Fluffy" }]
       	}
    	],
    	"address": { "state": "WA", "county": "King", "city": "seattle" },
    	"isRegistered": true
		}

	
6. Ajoutez un autre document, cette fois avec le contenu suivant.

		{
	    "_id": "WakefieldFamily",
	    "parents": [
    	    { "familyName": "Wakefield", "givenName": "Robin" },
        	{ "familyName": "Miller", "givenName": "Ben" }
    	],
    	"children": [
	        {
            	"familyName": "Merriam", 
             	"givenName": "Jesse", 
            	"gender": "female", "grade": 1,
            	"pets": [
	                { "givenName": "Goofy" },
                	{ "givenName": "Shadow" }
            	]
        	},
        	{ 
	            "familyName": "Miller", 
             	"givenName": "Lisa", 
             	"gender": "female", 
             	"grade": 8 }
    	],
    	"address": { "state": "NY", "county": "Manhattan", "city": "NY" },
    	"isRegistered": false
		}

7. Exécutez un exemple de requête. Par exemple, recherchez des familles portant le nom « Andersen » en retournant les champs parents et état.

	![Capture d’écran des résultats de requête MongoChef](./media/documentdb-mongodb-mongochef/QueryDocument1.png)
	

## Étapes suivantes

- Découvrez des [exemples](documentdb-mongodb-samples.md) de prise en charge du protocole MongoDB dans DocumentDB.
- Pour en savoir plus sur la prise en charge du protocole MongoDB dans DocumentDB, consultez ces [directives de développement (version préliminaire)](documentdb-mongodb-guidelines.md).

 

<!---HONumber=AcomDC_0629_2016-->