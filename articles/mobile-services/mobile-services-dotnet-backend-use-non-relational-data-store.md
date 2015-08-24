<properties 
	pageTitle="Création d’un service à l’aide d’un magasin de données non relationnelles | Microsoft Azure" 
	description="Découvrez comment utiliser un magasin de données non relationnelles tel que MongoDB ou Azure Table Storage avec votre service mobile .NET" 
	services="mobile-services" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="08/08/2015" 
	ms.author="mahender"/>

# Création d’un service mobile principal .NET utilisant MongoDB au lieu d’une base de données SQL pour le stockage

Cette rubrique vous explique comment utiliser un magasin de données non relationnelles pour votre service mobile principal .NET. Dans ce didacticiel, vous allez modifier le projet de démarrage rapide Mobile Services pour utiliser MongoDB à la place de la base de données Azure SQL comme magasin de données par défaut.

Vous devez d’abord suivre le didacticiel [Prise en main de Mobile Services] ou [Ajout de Mobile Services à une application existante]. Vous devez également ajouter le service MongoLab à votre abonnement.

## <a name="create-store"></a>Création du magasin de données non relationnelles MongoLab

1. Dans le [portail de gestion Azure], cliquez sur **Nouveau**, puis sélectionnez **Marketplace**.

2. Sélectionnez le module complémentaire **MongoLab**, puis parcourez l’Assistant pour créer un compte MongoLab.

	Pour plus d’informations sur MongoLab, consultez la [page dédiée au module complémentaire MongoLab].

2. Une fois le compte configuré, cliquez sur **Informations de connexion** et copiez la chaîne de connexion.

3. Dans votre service mobile, cliquez sur l’onglet **Configurer**, faites défiler jusqu’à **Chaînes de connexion**, puis entrez une chaîne de connexion avec le **Nom** `MongoConnectionString` et une **Valeur** qui constitue votre connexion MongoDB, puis cliquez sur **Enregistrer**.

	![Ajout de la chaîne de connexion MongoDB](./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png)

	La chaîne de connexion du compte de stockage, chiffrée, est stockée dans les paramètres de l’application. Vous pouvez accéder à cette chaîne dans n’importe quel contrôleur de table au moment de l’exécution.

8. Dans l’Explorateur de solutions de Visual Studio, ouvrez le fichier Web.config du projet de service mobile et ajoutez la nouvelle chaîne de connexion suivante :

		<add name="MongoConnectionString" connectionString="<MONGODB_CONNECTION_STRING>" />

9. Remplacez l’espace réservé `<MONGODB_CONNECTION_STRING>` par la chaîne de connexion MongoDB.

	Le service mobile utilise cette chaîne de connexion quand il est exécuté sur votre ordinateur local, ce qui vous permet de tester le code avant de le publier. Au cours de l’exécution dans Azure, le service mobile utilise la valeur de la chaîne de connexion définie dans le portail et ignore la chaîne de connexion dans le projet.

## <a name="modify-service"></a>Modifier les types de données et les contrôleurs de table

1. Installez le package NuGet **WindowsAzure.MobileServices.Backend.Mongo**.

2. Modifiez **TodoItem** pour qu’il dérive de **DocumentData** au lieu d’**EntityData**.

        public class TodoItem : DocumentData
        {
            public string Text { get; set; }

            public bool Complete { get; set; }
        }

3. Dans **TodoItemController**, remplacez la méthode **Initialize** par les éléments suivants :

        protected override async void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            string connectionStringName = "MongoConnectionString";
            string databaseName = "<YOUR-DATABASE-NAME>";
            string collectionName = "todoItems";
            DomainManager = new MongoDomainManager<TodoItem>(connectionStringName, databaseName, 
				collectionName, Request, Services);
        }

4. Dans le code de la méthode **Initialize** ci-dessus, remplacez `<YOUR-DATABASE-NAME>` par le nom défini quand vous avez approvisionné le module complémentaire MongoLab.

Vous êtes maintenant prêt à tester l’application.

## <a name="test-application"></a>Testez l’application

1. (Facultatif) Publiez à nouveau votre projet principal .NET de service mobile.

	Vous pouvez également tester votre service mobile localement avant de publier le projet principal .NET sur Azure. Si vous testez localement ou dans Azure, le service mobile utilise votre MongoDB pour le stockage.

4. À l’aide du bouton **Essayez maintenant** sur la page de démarrage comme avant, ou à l’aide d’une application cliente connectée à votre application mobile, interrogez des éléments dans la base de données.
 
	Notez qu'aucun des éléments précédemment stockés dans la base de données SQL du didacticiel de démarrage rapide n'est présent.

	>[AZURE.NOTE]Lorsque vous utilisez le bouton **Essayez maintenant** pour ouvrir les pages API d'aide, n'oubliez pas de fournir votre clé d'application comme mot de passe (avec un nom d'utilisateur vide).

3. Créez un élément.

	L’application et le service mobile doivent se comporter comme avant, excepté qu’à présent vos données sont stockées dans votre magasin de données non relationnelles plutôt que dans la base de données SQL.

##Étapes suivantes

Vous savez désormais qu’il est facile d’utiliser le stockage de table avec le serveur principal .NET. Vous pouvez donc explorer d’autres options de stockage principal :

+ [Création d’un service mobile principal .NET qui utilise le stockage de table à la place d’une base de données SQL](mobile-services-dotnet-backend-store-data-table-storage.md)</br>Comme les didacticiels que vous venez de terminer, cette rubrique vous montre comment utiliser un magasin de données non relationnelles pour votre service mobile. Dans ce didacticiel, vous allez modifier le projet de démarrage rapide Mobile Services pour utiliser Azure Storage comme magasin de données à la place de la base de données Azure SQL.
 
+ [Connexion à un serveur SQL local à l’aide de connexions hybrides](mobile-services-dotnet-backend-hybrid-connections-get-started.md)</br> Les connexions hybrides permettent à votre service mobile de se connecter en toute sécurité à vos ressources locales. De cette façon, vos données locales sont accessibles aux clients mobiles à l'aide d'Azure. Les ressources prises en charge incluent toute ressource s'exécutant sur un port TCP statique, y compris Microsoft SQL Server, MySQL, les API web HTTP et la plupart des services web personnalisés.

+ [Télécharger des images dans le stockage Azure à l’aide de Mobile Services](mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage.md)</br>Vous montre comment étendre l’exemple de projet TodoList pour vous permettre de télécharger des images à partir de votre application pour le stockage d’objets Blob Azure.


<!-- Anchors. -->
[Create a non-relational store]: #create-store
[Modify data and controllers]: #modify-service
[Test the application]: #test-application


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/create-mongo-lab.png
[1]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png


<!-- URLs. -->
[Prise en main de Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Ajout de Mobile Services à une application existante]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[portail de gestion Azure]: https://manage.windowsazure.com/
[What is the Table Service]: ../storage-dotnet-how-to-use-tables.md#what-is
[page dédiée au module complémentaire MongoLab]: /gallery/store/mongolab/mongolab
 

<!---HONumber=August15_HO7-->