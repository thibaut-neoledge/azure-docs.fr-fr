<properties 
	pageTitle="Création d'un service à l'aide d'un magasin de données non relationnelles - Azure Mobile Services" 
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
	ms.date="04/24/2015" 
	ms.author="mahender"/>

# Création d'un service à l'aide du magasin de données MongoDB avec le backend .NET

Cette rubrique vous explique comment utiliser un magasin de données non relationnelles pour votre service mobile. Dans ce didacticiel, vous allez modifier le projet de démarrage rapide Mobile Services pour utiliser le magasin de données MongoDB plutôt que SQL.

Ce didacticiel vous familiarise avec la procédure de configuration d'un magasin de données non relationnelles :

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

4. Sous **Paramètres d'application**, entrez votre chaîne de connexion avec la clé « MongoConnectionString », puis cliquez sur **Enregistrer**.

    ![][1]

2. Ajoutez le bloc de code suivant à `TodoItemController` :

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
    
    Ce code charge le paramètre d'application et indique au service mobile de le traiter en tant que connexion pouvant être utilisée par un `TableController`. Vous pourrez appeler ultérieurement cette méthode au moment de l'appel de la classe `TodoItemController`.



## <a name="modify-service"></a>Modification des données et des contrôleurs

1. Installez le package NuGet **WindowsAzure.MobileServices.Backend.Mongo**.

2. Modifiez `TodoItem` pour qu'il dérive de `DocumentData` plutôt que de `EntityData`.

        public class TodoItem : DocumentData
        {
            public string Text { get; set; }

            public bool Complete { get; set; }
        }

3. Dans la classe `TodoItemController`, remplacez la méthode `Initialize` par le code suivant :

        protected override async void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            string connectionStringName = "MongoConnectionString";
            string databaseName = "<YOUR-DATABASE-NAME>";
            string collectionName = "todoItems";
            InitializeConnectionString(connectionStringName);
            DomainManager = new MongoDomainManager<TodoItem>(connectionStringName, databaseName, collectionName, Request, Services);
        }

4. Dans le bloc de code de la méthode `Initialize` ci-avant, remplacez **YOUR-DATABASE-NAME** par le nom défini pendant la configuration du module MongoLab.


## <a name="test-application"></a>Test de l'application

1. Publiez à nouveau votre projet de backend de service mobile.

2. Exécutez l'application cliente. Notez qu'aucun des éléments précédemment stockés dans la base de données SQL du didacticiel de démarrage rapide n'est présent.

3. Créez un élément. L'application se comporte comme avant, mais les données sont envoyées au magasin de données non relationnelles.


<!-- Anchors. -->
[Création d'un magasin de données non relationnelles]: #create-store
[Modification des données et des contrôleurs]: #modify-service
[Test de l'application]: #test-application


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/create-mongo-lab.png
[1]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png


<!-- URLs. -->
[Prise en main de Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Prise en main des données]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[portail de gestion Azure]: https://manage.windowsazure.com/
[What is the Table Service]: ../storage-dotnet-how-to-use-tables.md#what-is
[page dédiée au module complémentaire MongoLab]: /gallery/store/mongolab/mongolab

<!--HONumber=54--> 