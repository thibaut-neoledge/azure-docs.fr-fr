<properties 
	pageTitle="Création d'un service à l'aide d'un magasin de données non relationnelles - Azure Mobile Services" 
	description="Découvrez comment utiliser un magasin de données non relationnelles tel que MongoDB ou Azure Table Storage avec votre service mobile .NET" 
	services="" 
	documentationCenter="windows" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="mahender"/>

# Création d'un service à l'aide du magasin de données MongoDB avec le projet .NET principal

Cette rubrique vous explique comment utiliser un magasin de données non relationnelles pour votre service mobile. Dans ce didacticiel, vous allez modifier le projet de démarrage rapide Mobile Services pour utiliser le magasin de données MongoDB plutôt que SQL.

Ce didacticiel vous familiarise avec la procédure de configuration d'un magasin de données non relationnelles :

1. [Création d'un magasin de données non relationnelles]
2. [Modification des données et des contrôleurs]
3. [Test de l'application]

Notez que vous devez avoir déjà suivi le didacticiel [Prise en main de Mobile Services] ou [Prise en main des données].

## <a name="create-store"></a>Création d'un magasin de données non relationnelles

1. Dans le [portail de gestion Azure], cliquez sur **Nouveau**, puis sélectionnez **Magasin**.

2. Sélectionnez le module complémentaire **MongoLab**, puis parcourez l'Assistant pour créer un compte. Pour plus d'informations sur MongoLab, consultez la [page dédiée au module complémentaire MongoLab].

    ![][0]

2. Une fois le compte configuré, sélectionnez **Informations de connexion** et copiez la chaîne de connexion.

3. Accédez à la section Mobile Services du portail, puis cliquez sur l'onglet **Configurer**.

4. Sous **Paramètres d'application**, entrez votre chaîne de connexion avec la clé " MongoConnectionString ", puis cliquez sur **Enregistrer**.

    ![][1]

2. Ajoutez le bloc de code suivant à  `TodoItemController` :

        static bool connectionStringInitialized = false;

        private void InitializeConnectionString(string connectionStringName)
        {
            if (!connectionStringInitialized)
            {
                connectionStringInitialized = true;
                if (!this.Services.Settings.Connections.ContainsKey(connectionStringName))
                {
                    var connectionString = this.Services.Settings[connectionStringName];
                    var connectionSetting = new ConnectionSettings(connectionStringName, connectionString);
                    this.Services.Settings.Connections.Add(connectionStringName, connectionSetting);
                }
            }
        }
    
    Ce code charge le paramètre d'application et indique au service mobile de le traiter en tant que connexion pouvant être utilisée par un  `TableController`. Vous pourrez appeler ultérieurement cette méthode lors de l'appel de la classe  `TodoItemController`.



## <a name="modify-service"></a>Modification des données et des contrôleurs

1. Installation du package Nuget **WindowsAzure.MobileServices.Backend.Mongo**

2. Modifiez  `TodoItem` pour qu'il dérive de  `DocumentData` plutôt que de  `EntityData`.

        public class TodoItem : DocumentData
        {
            public string Text { get; set; }

            public bool Complete { get; set; }
        }

3. Dans la classe  `TodoItemController`, remplacez la méthode  `Initialize` par le code suivant :

        protected override async void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            string connectionStringName = "MongoConnectionString";
            string databaseName = "<YOUR-DATABASE-NAME>";
            string collectionName = "todoItems";
            InitializeConnectionString(connectionStringName);
            DomainManager = new MongoDomainManager<TodoItem>(connectionStringName, databaseName, collectionName, Request, Services);
        }

4. Dans le code de la méthode  `Initialize` ci-dessus, remplacez **YOUR-DATABASE-NAME** par le nom que vous avez choisi lorsque vous avez configuré le module complémentaire MongoLab.


## <a name="test-application"></a>Test de l'application

1. Publiez à nouveau votre projet de service mobile principal.

2. Exécutez l'application cliente. Notez qu'aucun des éléments précédemment stockés dans la base de données SQL du didacticiel de démarrage rapide n'est présent.

3. Créez un élément. L'application se comporte comme avant, mais les données sont envoyées au magasin de données non relationnelles.


<!-- Anchors. -->
[Création d'un magasin de données non relationnelles]: #create-store
[Modification des données et des contrôleurs]: #modify-service
[Test de l'application]: #test-application


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/create-mongo-lab.png
[1]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png


<!-- URLs. -->
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
[Portail de gestion Azure]: https://manage.windowsazure.com/
[Présentation du service de table]: /fr-fr/documentation/articles/storage-dotnet-how-to-use-tables/#what-is
[Page du module complémentaire MongoLab]: /fr-fr/gallery/store/mongolab/mongolab

<!--HONumber=42-->
