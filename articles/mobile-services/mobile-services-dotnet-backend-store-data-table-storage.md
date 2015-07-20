<properties 
	pageTitle="Créer un service qui utilise le stockage de tables au lieu d’une base de données SQL | Azure Mobile Services" 
	description="Découvrez comment utiliser le stockage de tables Azure avec votre service mobile principal .NET." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/09/2015" 
	ms.author="glenga"/>

# Créer un service mobile principal .NET qui utilise le stockage de tables au lieu d’une base de données SQL

Cette rubrique vous explique comment utiliser un magasin de données non relationnelles pour votre service mobile principal .NET. Dans ce didacticiel, vous allez modifier le projet de démarrage rapide Azure Mobile Services pour utiliser le stockage de tables Azure à la place de Base de données SQL Azure comme magasin de données par défaut.

Vous devez d’abord suivre le didacticiel [Prise en main de Mobile Services] ou [Ajout de Mobile Services à une application existante]. Vous aurez également besoin d’un compte de stockage Azure.

##Configurer le stockage de tables Azure dans votre service mobile principal .NET

Tout d’abord, vous devez configurer votre service mobile et le projet de code principal .NET pour vous connecter au stockage Azure.

1. Dans l’**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le projet de service principal .NET, puis sélectionnez **Gérer les packages NuGet**.

2. Dans le volet gauche, sélectionnez la catégorie **Online**, sélectionnez **Stabile Only**, recherchez **MobileServices**, cliquez sur **Install** au niveau du package **Microsoft Azure Mobile Services .NET Backend Azure Storage Extension**, puis acceptez les contrats de licence.

  	![](./media/mobile-services-dotnet-backend-store-data-table-storage/mobile-add-storage-nuget-package-dotnet.png)

  	Le support des services de stockage Azure est alors ajouté au projet de service mobile principal .NET.

3. Si vous n'avez pas encore créé de compte de stockage, consultez la rubrique [Création d'un compte de stockage](../storage-create-storage-account.md).

4. Dans le portail de gestion, cliquez sur **Stockage**, sur le compte de stockage, puis sur **Gérer les clés**.

5. Notez le **nom du compte de stockage** et la **clé d'accès**.
 
6. Dans votre service mobile, cliquez sur l’onglet **Configurer**, faites défiler jusqu’à **Chaînes de connexion**, puis entrez une chaîne de connexion composée d’un **Nom** `StorageConnectionString` et d’une **Valeur** au format suivant.

		DefaultEndpointsProtocol=https;AccountName=<ACCOUNT_NAME>;AccountKey=<ACCESS_KEY>;

	![](./media/mobile-services-dotnet-backend-store-data-table-storage/mobile-blob-storage-app-settings.png)

7. Dans la chaîne ci-dessus, remplacez les valeurs de `<ACCOUNT_NAME>` et `<ACCESS_KEY>` par les valeurs issues du portail, puis cliquez sur **Enregistrer**.

	La chaîne de connexion du compte de stockage, chiffrée, est stockée dans les paramètres de l’application. Vous pouvez accéder à cette chaîne dans n’importe quel contrôleur de table au moment de l’exécution.

8. Dans l’Explorateur de solutions de Visual Studio, ouvrez le fichier Web.config du projet de service mobile et ajoutez la nouvelle chaîne de connexion suivante :

		<add name="StorageConnectionString" connectionString="<STORAGE_CONNECTION_STRING>" />

9. Remplacez l’espace réservé `<STORAGE_CONNECTION_STRING>` par la chaîne de connexion évoquée à l’étape 6.

	Le service mobile utilise cette chaîne de connexion quand il est exécuté sur votre ordinateur local, ce qui vous permet de tester le code avant de le publier. Au cours de l’exécution dans Azure, le service mobile utilise la valeur de la chaîne de connexion définie dans le portail et ignore la chaîne de connexion dans le projet.

## <a name="modify-service"></a>Modifier les types de données et les contrôleurs de table

Le projet de démarrage rapide TodoList étant conçu pour fonctionner avec une base de données SQL via Entity Framework, vous devez effectuer certaines mises à jour dans le projet pour utiliser le stockage de tables.

1. Modifiez comme suit le type de données **TodoItem** de manière à ce qu’il soit issu de **StorageData** au lieu de **EntityData**.

	    public class TodoItem : StorageData
	    {
	        public string Text { get; set; }
	        public bool Complete { get; set; }
	    }

	>[AZURE.NOTE]Le type **StorageData** a une propriété d’ID qui requiert une clé composée, laquelle est une chaîne au format *partitionId*,*rowValue*.

2. Dans **TodoItemController**, ajoutez l’instruction using suivante :

		using System.Web.Http.OData.Query;
		using System.Collections.Generic;

3. Remplacez la méthode **Initialize** de **TodoItemController** par les éléments suivants :

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);

            // Create a new Azure Storage domain manager using the stored 
            // connection string and the name of the table exposed by the controller.
            string connectionStringName = "StorageConnectionString";
            var tableName = controllerContext.ControllerDescriptor.ControllerName.ToLowerInvariant();
            DomainManager = new StorageDomainManager<TodoItem>(connectionStringName, 
                tableName, Request, Services);          
        }

	Cela crée un nouveau gestionnaire de domaine de stockage pour le contrôleur demandé à l’aide de la chaîne de connexion du compte de stockage.

3. Remplacez la méthode **GetAllTodoItems** existante par le code suivant :

		public Task<IEnumerable<TodoItem>> GetAllTodoItems(ODataQueryOptions options)
        {
            // Call QueryAsync, passing the supplied query options.
            return DomainManager.QueryAsync(options);
        } 

	Contrairement à une base de données SQL, cette version ne renvoie pas IQueryable<TEntity>, de sorte que le résultat peut être lié, mais pas plus composé dans une requête.

## Mettre à jour l’application cliente

Vous devez apporter une modification côté client pour que l’application de démarrage rapide fonctionne avec le service principal .NET via le stockage de tables. Ceci s’explique par le fait qu’une clé composée est attendue par le fournisseur de stockage de tables.

1. Ouvrez le fichier de code client qui contient le code d’accès aux données et identifiez la méthode dans laquelle l’opération d’insertion est effectuée.

2. Mettez à jour l’instance TodoItem ajoutée pour explicitement définir le champ d’ID au format de chaîne `<partitionID>,<rowValue>`.

	Il s’agit d’un exemple de la façon dont cet ID peut être défini dans une application C#, où la partie de la partition est fixe et la partie de la ligne est basée sur le GUID.

		 todoItem.Id = string.Format("partition,{0}", Guid.NewGuid());

Vous êtes maintenant prêt à tester l’application.

## <a name="test-application"></a>Test de l’application

1. (Facultatif) Publiez à nouveau votre projet principal .NET de service mobile. 
	
	Vous pouvez également tester votre service mobile localement avant de publier le projet principal .NET sur Azure. Si vous effectuez le test en local ou dans Microsoft Azure, le service mobile utilise le stockage de tables Azure.

4. Exécutez l’application cliente de démarrage rapide connectée à votre service mobile.

	Notez que les éléments que vous avez ajoutés précédemment à l’aide du didacticiel de démarrage rapide ne sont pas visibles. Ceci est dû au fait que le magasin de tables est actuellement vide.

5. Ajoutez de nouveaux éléments pour générer des modifications de la base de données.
 
	L’application et le service mobile doivent se comporter comme avant, excepté qu’à présent vos données sont stockées dans votre magasin de données non relationnelles plutôt que dans la base de données SQL.

##Étapes suivantes

Vous savez désormais qu’il est facile d’utiliser le stockage de table avec le serveur principal .NET. Vous pouvez donc explorer d’autres options de stockage principal :

+ [Utiliser MongoDB comme magasin de données avec votre service principal Mobile Services.NET](mobile-services-dotnet-backend-use-non-relational-data-store.md)</br>Comme les didacticiels que vous venez de terminer, cette rubrique vous montre comment utiliser un magasin de données non relationnelles pour votre service mobile. Dans ce didacticiel, vous allez modifier le projet de démarrage rapide Mobile Services pour utiliser MongoDB plutôt que la base de données SQL comme magasin de données.
 
+ [Connexion à un serveur SQL local à l’aide de connexions hybrides](mobile-services-dotnet-backend-hybrid-connections-get-started.md)</br> Les connexions hybrides permettent à votre service mobile de se connecter en toute sécurité à vos ressources locales. De cette façon, vos données locales sont accessibles aux clients mobiles à l'aide d'Azure. Les ressources prises en charge incluent toute ressource s'exécutant sur un port TCP statique, y compris Microsoft SQL Server, MySQL, les API web HTTP et la plupart des services Web personnalisés.

+ [Télécharger des images dans le stockage Azure à l’aide de Mobile Services](mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage.md)</br>Vous montre comment étendre l’exemple de projet TodoList pour vous permettre de télécharger des images à partir de votre application pour le stockage d’objets Blob Azure.

<!-- Anchors. -->
[Create a non-relational store]: #create-store
[Modify data and controllers]: #modify-service
[Test the application]: #test-application


<!-- Images. -->


<!-- URLs. -->
[Prise en main de Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Ajout de Mobile Services à une application existante]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[Azure Management Portal]: https://manage.windowsazure.com/
[What is the Table Service]: ../storage-dotnet-how-to-use-tables.md#what-is
[MongoLab Add-on Page]: /gallery/store/mongolab/mongolab
 

<!---HONumber=July15_HO2-->