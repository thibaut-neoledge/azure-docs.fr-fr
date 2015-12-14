<properties
	pageTitle="Modifications des modèles de données pour un service mobile de backend .NET"
	description="Cette rubrique décrit les initialiseurs de modèle de données et explique comment modifier des modèle de données dans un service mobile de backend .NET."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	writer="glenga"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="NA"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="09/14/2015"
	ms.author="glenga"/>

# Modifications des modèles de données pour un service mobile de backend .NET

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


Cette rubrique montre comment utiliser Code First Migrations dans Entity Framework pour apporter des modifications de modèles de données à une base de données SQL existante afin d'éviter de perdre les données existantes. Cette procédure suppose que vous avez déjà publié votre projet de service principal .NET sur Azure, que votre base de données comporte des données existantes et que les modèles de données distants et locaux sont toujours synchronisés. Cette rubrique décrit également les initialiseurs Code First par défaut implémentés par Azure Mobile Services qui sont utilisés pendant le développement. Ces initialiseurs vous permettent de réaliser facilement des modifications du schéma sans utiliser Code First Migrations lorsqu’il n’est pas nécessaire de conserver vos données existantes.

>[AZURE.NOTE]Le nom de schéma utilisé pour préfixer les tables de la base de données SQL est défini par le paramètre d’application MS\_MobileServiceName dans le fichier web.config. Quand vous téléchargez le projet de démarrage à partir du portail, cette valeur est déjà définie sur le nom du service mobile. Quand votre nom de schéma correspond au service mobile, plusieurs services mobiles peuvent partager en toute sécurité la même instance de base de données.

Notez que les migrations automatiques ne sont pas prises en charge dans un projet de service principal .NET.

## Mise à jour du modèle de données

Lorsque vous ajoutez des fonctionnalités à votre service mobile principal .NET, vous ajoutez de nouveaux contrôleurs pour exposer de nouveaux points de terminaison dans votre API. Vous créez une nouvelle API comme contrôleur personnalisé ou contrôleur de table. [TableController<TEntity>] expose un type de données qui hérite de [EntityData]. Pour que les données soient persistantes dans la base de données, ce type de données doit également être ajouté au modèle de données en tant que nouveau [DbSet<T>] dans [DbContext]. Pour plus d’informations sur Code First dans Entity Framework, consultez la page [Création d’un modèle avec Code First](https://msdn.microsoft.com/data/ee712907#codefirst).

Visual Studio simplifie la création du contrôleur de table pour exposer un nouveau type de données aux applications clientes. Pour plus d’informations, consultez la page [Utilisation de contrôleurs pour accéder aux données dans les services mobiles](https://msdn.microsoft.com/library/windows/apps/xaml/dn614132.aspx).

## Initialiseurs du modèle de données

Mobile Services fournit deux classes de base d’initialiseurs de modèle de données dans un projet de service mobile principal .NET. Les deux initialiseurs indiquent à Entity Framework de supprimer et de recréer la base de données dès qu’il détecte une modification de modèle de données indiquée par votre [DbContext]. Ces initialiseurs sont conçus pour fonctionner à la fois lorsque votre service mobile est en cours d’exécution sur un ordinateur local et lorsqu’il est hébergé dans Azure.

>[AZURE.NOTE]Lorsque vous publiez un service mobile de backend .NET, l'initialiseur n'est pas exécuté jusqu'à ce qu'une opération d'accès aux données ait lieu. Cela signifie que pour un service publié récemment, les tables de données utilisés pour le stockage ne sont pas créées, jusqu'à ce qu'une opération d'accès aux données, telle une requête, soit demandée par le client.
>
>Vous pouvez également exécuter une opération d'accès aux données à l'aide de la fonctionnalité d'aide API intégrée, accessible à partir du lien **Essayer** sur la page de démarrage. Pour plus d’informations sur l’utilisation des pages de l’API pour tester votre service mobile, consultez la section tester le projet de service mobile localement dans [Ajout de Mobile Services à une application existante](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md#test-the-service-locally).

Les deux initialiseurs suppriment toutes les tables, les vues, les fonctions et les procédures dans le schéma utilisé par le service mobile.

+ **Effacer les changements de modèles de schéma de base de données** <br/> Les objets du schéma sont supprimés uniquement lorsque Code First détecte un changement apporté au modèle de données. Dans un projet de backend .NET, l'initialiseur par défaut téléchargé à partir du [portail Azure Classic] hérite de cette classe de base.

+ **ClearDatabaseSchemaAlways** : <br/> les objets du schéma sont supprimés chaque fois que vous accédez au modèle de données. Utilisez cette classe de base pour réinitialiser la base de données sans devoir modifier le modèle de données.

Vous pouvez utiliser d’autres initialiseurs de modèle de données Code First lors de l'exécution sur un ordinateur local. Toutefois, les initialiseurs qui tentent de supprimer des bases de données échoueront dans Azure, car l'utilisateur n'a pas l'autorisation de supprimer la base de données, ce qui est une bonne chose.

Vous devez continuer à utiliser ces initialiseurs pendant le développement local de votre projet de service mobile. Les didacticiels consacrés au backend .NET supposent que vous les utilisez. Néanmoins, dans le cas où vous souhaitez apporter des modifications à des modèles de données et conserver les données existantes dans la base de données, vous devez utiliser Code First Migrations.

>[AZURE.IMPORTANT]Lors du développement et du test de votre projet de service mobile par rapport aux services Azure actifs, vous devez toujours utiliser une instance de service mobile dédiée au test. Vous ne devez jamais développer ou tester par rapport à un service mobile qui est actuellement en production ou en cours d'utilisation par les applications clientes.

Dans le projet de démarrage rapide téléchargé, l'initialiseur de Code First est défini dans le fichier WebApiConfig.cs. Remplacez la méthode **Seed** pour ajouter de nouvelles lignes de données à de nouvelles tables. Pour obtenir des exemples de données d'amorçage, consultez [Données d'amorçage dans les migrations].

## <a name="migrations"></a>Activation de Code First Migrations

Code First Migrations utilise une méthode de capture instantanée qui génère un code qui, lorsqu'il est exécuté, réalise des modifications du schéma dans la base de données. Avec Migrations, vous pouvez apporter des modifications incrémentielles à votre modèle de données et conserver les données existantes dans la base de données.

>[AZURE.NOTE]Si vous avez déjà publié votre projet de service mobile de backend .NET sur Azure et que votre schéma de tables de base de données SQL ne correspond pas au modèle de données actif de votre projet, vous devez utiliser un initialiseur, supprimer les tables ou, sinon, synchroniser manuellement le schéma et le modèle de données avant de tenter de publier à l'aide de Code First Migrations.

Les étapes suivantes activent Migrations et appliquent les modifications de modèles de données dans le projet, la base de données locale et Azure.

1. Dans Visual Studio, dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet de service mobile et cliquez sur **Définir comme projet de démarrage**.

2. Dans le menu **Outils**, développez **NuGet Package Manager**, puis cliquez sur **Console du gestionnaire de package**.

	Ainsi, la Console du Gestionnaire de package s'affiche, que vous utiliserez pour gérer Code First Migrations.

3. Dans la Console du Gestionnaire de package, entrez la commande suivante :

		PM> Enable-Migrations

	Cette commande active Code First Migrations pour votre projet.

4. Dans la console, entrez la commande suivante :

		PM> Add-Migration Initial

	Cette commande crée une nouvelle migration nommée *Initial*. Le code de migration est stocké dans le dossier du projet Migrations.

5. Développez le dossier App\_Start, ouvrez le fichier de projet WebApiConfig.cs et ajoutez les instructions **using** suivantes :

		using System.Data.Entity.Migrations;
		using todolistService.Migrations;

	Dans le code ci-dessus, vous devez remplacer la chaîne _todolistService_ par l'espace de noms de votre projet, qui, pour le projet de démarrage rapide téléchargé, est <em>nom&#95;service&#95;mobile</em>Service.

6. Dans le même fichier de code, mettez en commentaire l'appel à la méthode **Database.SetInitializer** et ajoutez le code suivant juste après :

        var migrator = new DbMigrator(new Configuration());
        migrator.Update();

	Ce code désactive l'initialiseur de base de données Code First par défaut qui supprime et recrée la base de données et la remplace par une requête explicite afin d'appliquer la migration la plus récente. À ce stade, toute modification de modèle de données résulte en une exception InvalidOperationException lors de l'accès aux données, sauf si une migration a été créée pour la modification. Ultérieurement, votre service doit utiliser Code First Migrations afin de faire migrer les modifications de modèles de données dans la base de données.

7.  Appuyez sur F5 pour démarrer le projet de service mobile sur l'ordinateur local.

	À ce stade, la base de données est synchronisée avec le modèle de données. Si vous avez fourni des données d'amorçage, vous pouvez les vérifier en cliquant sur **Try it out**, **GET tables/todoitem**, puis sur **Try this out** et enfin **Envoyer**. Pour plus d'informations, consultez la section [Données d'amorçage dans les migrations].

8.   À présent, apportez une modification à votre modèle de donnés, par exemple, en ajoutant une nouvelle propriété UserId au type TodoItem, régénérez le projet, puis, dans le Gestionnaire de package, exécutez la commande suivante :

		PM> Add-Migration NewUserId

	Cette commande crée une nouvelle migration nommée *NewUserId*. Un nouveau fichier de code, qui implémente cette modification, est ajouté dans le dossier Migrations.

9.  Appuyez à nouveau sur F5 pour redémarrer le projet de service mobile sur l'ordinateur local.

	La migration est appliquée à la base de données et cette dernière est à nouveau synchronisée avec le modèle de données. Si vous avez fourni des données d'amorçage, vous pouvez les vérifier en cliquant sur **Try it out**, **GET tables/todoitem**, puis sur **Try this out** et enfin **Envoyer**. Pour plus d'informations, consultez la section [Données d'amorçage dans les migrations].

10. Publiez à nouveau le service mobile sur Azure, puis exécutez l'application cliente afin d'accéder aux données et de vérifier que les données se chargent sans erreur.

13. (Facultatif) Dans le [portail Azure Classic], sélectionnez votre service mobile, cliquez sur **Configurer** > **Base de données SQL**. Vous accédez ainsi à la page de la base de données SQL de la base de données de votre service mobile.

14. (Facultatif) Cliquez sur **Gérer**, connectez-vous au serveur de base de données SQL, puis cliquez sur **Conception** et vérifiez que les modifications de schéma ont été effectuées dans Azure.


## Utilisation de Code First Migrations sans initialiseur
Avant d'utiliser Code First Migrations avec votre projet de service principal .NET, vous devez exécuter un initialiseur de modèle de données. Si vous n'utilisez PAS d’initialiseur, des erreurs peuvent se produire lorsque vous tentez d'appliquer des migrations. Si vous choisissez de ne pas utiliser l’un des initialiseurs de modèle de données prédéfinis, assurez-vous que Migrations est configuré pour utiliser EntityTableSqlGenerator comme SqlGenerator dans le fichier Migrations\\Configuration.cs, comme dans l'exemple suivant :

    public Configuration()
    {
        AutomaticMigrationsEnabled = false;
        SetSqlGenerator("System.Data.SqlClient", new EntityTableSqlGenerator());
    }

##<a name="seeding"></a>Données d'amorçage dans Migrations

Migrations peut ajouter des données d'amorçage à la base de données lorsqu'une migration est exécutée. La classe **Configuration** comporte une méthode **Seed** que vous pouvez remplacer afin d'insérer ou de mettre à jour des données. Le fichier de code Configuration.cs est ajouté au dossier Migrations lorsque Migrations est activé. Ces exemples montrent comment remplacer la méthode [Seed] pour amorcer des données à la table **TodoItems**. La méthode [Seed] est appelée après la migration vers la version la plus récente.

###Amorçage d'une nouvelle table

Le code suivant amorce la table **TodoItems** avec de nouvelles lignes de données :

        List<TodoItem> todoItems = new List<TodoItem>
        {
            new TodoItem { Id = "1", Text = "First item", Complete = false },
            new TodoItem { Id = "2", Text = "Second item", Complete = false },
        };

        foreach (TodoItem todoItem in todoItems)
        {
            context.Set<TodoItem>().Add(todoItem);
        }
        base.Seed(context);

###Amorçage d'une nouvelle colonne dans une table

Le code suivant amorce seulement la colonne UserId :

        context.TodoItems.AddOrUpdate(
            t => t.UserId,
                new TodoItem { UserId = 1 },
                new TodoItem { UserId = 1 },
                new TodoItem { UserId = 2 }
            );
        base.Seed(context);

Ce code appelle la méthode d'extension de l'assistance [AddOrUpdate] pour ajouter des données d'amorçage à la nouvelle colonne UserId. Si [AddOrUpdate] est utilisé, aucune ligne en double n'est créée.

<!-- Anchors -->
[Migrations]: #migrations
[Données d'amorçage dans les migrations]: #seeding

<!-- Images -->
[0]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/navagate-to-sql-database.png
[1]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/manage-sql-database.png
[2]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/sql-database-drop-tables.png

<!-- URLs -->
[DropCreateDatabaseIfModelChanges]: http://msdn.microsoft.com/library/gg679604(v=vs.113).aspx
[Seed]: http://msdn.microsoft.com/library/hh829453(v=vs.113).aspx
[portail Azure Classic]: https://manage.windowsazure.com/
[DbContext]: http://msdn.microsoft.com/library/system.data.entity.dbcontext(v=vs.113).aspx
[AddOrUpdate]: http://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx
[TableController<TEntity>]: https://msdn.microsoft.com/library/azure/dn643359.aspx
[EntityData]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.entitydata.aspx
[DbSet<T>]: https://msdn.microsoft.com/library/azure/gg696460.aspx

<!---HONumber=AcomDC_1203_2015-->