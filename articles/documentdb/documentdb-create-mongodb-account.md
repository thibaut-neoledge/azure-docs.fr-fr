<properties 
	pageTitle="Création d’un compte DocumentDB prenant en charge le protocole MongoDB | Microsoft Azure" 
	description="Découvrez comment créer un compte DocumentDB prenant en charge le protocole MongoDB (actuellement disponible en version préliminaire)." 
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

# Créer un compte DocumentDB prenant en charge le protocole MongoDB à l’aide du portail Azure

Pour créer un compte Azure DocumentDB prenant en charge le protocole MongoDB, vous devez :

- Avoir un compte Azure. Si vous n’en avez pas, vous pouvez obtenir un [compte Azure gratuit](https://azure.microsoft.com/free/)

## Créer le compte  

Pour créer un compte DocumentDB prenant en charge le protocole MongoDB, procédez comme suit :

1. Dans une nouvelle fenêtre, connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **NOUVEAU**, cliquez sur **Données + Stockage**, cliquez sur **See all** (Afficher tout), puis recherchez le « protocole DocumentDB » dans la catégorie **Données + Stockage**. Cliquez sur **DocumentDB - Prise en charge de protocole pour MongoDB**.

	![Capture d’écran des panneaux Marketplace et recherche des données et du stockage, mise en surbrillance de DocumentDB - Prise en charge de protocole pour MongoDB, Base de données Mongo](./media/documentdb-create-mongodb-account/marketplacegallery2.png)

3. Sinon, dans la catégorie **Données + Stockage**, sous **Stockage**, cliquez sur **Plus**, puis cliquez sur **Load more** (Charger plus) une ou plusieurs fois pour afficher **DocumentDB - Prise en charge de protocole pour MongoDB**. Cliquez sur **DocumentDB - Prise en charge de protocole pour MongoDB**.

	![Capture d’écran des panneaux Marketplace et Données et stockage, mise en surbrillance de DocumentDB - Prise en charge de protocole pour MongoDB, Base de données Mongo](./media/documentdb-create-mongodb-account/marketplacegallery1.png)

4. Dans le panneau **DocumentDB - Prise en charge de protocole pour MongoDB (version préliminaire)**, cliquez sur **Créer** pour lancer le processus d’inscription à la version préliminaire.

	![Le panneau DocumentDB - Prise en charge de protocole pour MongoDB dans le portail Azure](./media/documentdb-create-mongodb-account/marketplacegallery3.png)

5. Dans le panneau **Compte DocumentDB**, cliquez sur **S’inscrire à la version préliminaire**. Lisez les informations, puis cliquez sur **OK**.

	![Le panneau S’inscrire à la version préliminaire pour DocumentDB - Prise en charge de protocole pour MongoDB dans le portail Azure](./media/documentdb-create-mongodb-account/registerforpreview.png)

6.  Après avoir accepté les termes du contrat de la version préliminaire, vous serez redirigé vers le panneau de création. Dans le panneau **Compte DocumentDB**, indiquez la configuration souhaitée pour le compte.

	![Capture d’écran du panneau du nouveau compte DocumentDB prenant en charge le protocole MongoDB](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-account.png)


	- Dans la zone **ID**, entrez un nom pour identifier le compte. Lorsque l’**ID** est validé, une coche verte s’affiche dans la case **ID**. La valeur de **ID** devient le nom d’hôte dans l’URI. Cet **ID** ne peut contenir que des minuscules, des chiffres, le caractère « - » et doit compter entre 3 et 50 caractères. Notez que *documents.azure.com* sera ajouté au nom du point de terminaison de votre choix. Celui-ci deviendra le point de terminaison de votre compte.

	- Dans **Abonnement**, sélectionnez l’abonnement Azure que vous voulez utiliser pour le compte. Si votre compte ne comporte qu’un seul abonnement, ce compte sera sélectionné par défaut.

	- Dans **Groupe de ressources**, sélectionnez ou créez un groupe de ressources pour le compte. Un groupe de ressources déjà attaché à l’abonnement Azure est sélectionné par défaut. Vous pouvez cependant choisir de sélectionner un nouveau groupe de ressources auquel ajouter le compte. Pour plus d’informations, consultez [Utilisation du portail Azure pour gérer vos ressources Azure](resource-group-portal.md).

	- Utilisez **Emplacement** pour indiquer l’emplacement géographique où héberger le compte.
   
	- Cochez la case **Épingler au tableau de bord**

7.	Une fois les options du nouveau compte configurées, cliquez sur **Créer**. La création du compte peut prendre plusieurs minutes. Pour vérifier l’état, vous pouvez suivre l’avancement sur le Tableau d’accueil. ![Capture d’écran de la vignette Création dans le Tableau d’accueil - créateur de base de données en ligne](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-3.png)

	Vous pouvez aussi surveiller l’avancement depuis le hub de notifications.

	![Création rapide de bases de données - capture d’écran du hub de notifications, indiquant que le compte DocumentDB est en cours de création](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-4.png)

	![Capture d’écran du hub de notifications montrant le compte DocumentDB créé avec succès et déployé vers un groupe de ressources - notification du créateur de base de données en ligne](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-5.png)

8.	Une fois le compte créé, il est prêt à être utilisé avec les paramètres par défaut.

	![Capture d’écran du panneau de compte par défaut](./media/documentdb-create-mongodb-account/defaultaccountblades.png)
	

## Étapes suivantes


- Découvrez comment vous [connecter](documentdb-connect-mongodb-account.md) à un compte DocumentDB prenant en charge le protocole MongoDB.

 

<!---HONumber=AcomDC_0928_2016-->