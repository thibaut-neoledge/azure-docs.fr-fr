<properties pageTitle="L'utilisation de Code First Migrations .NET principal (Mobile Services)" metaKeywords="" description="" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="How to make data model changes to a .NET backend mobile service" authors="glenga" solutions="mobile" writer="glenga" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="dotnet" ms.topic="article" ms.date="09/27/2014" ms.author="glenga" />

# Modifications des modèles de données pour un service mobile principal .NET

Cette rubrique montre comment utiliser Entity Framework Code First Migrations pour apporter des modifications de modèles de données à une base de données SQL existante afin d'éviter de perdre les données existantes. Cette procédure suppose que vous avez déjà publié votre projet de service mobile sur Azure, que votre base de données comporte des données existantes et que les modèles de données distants et locaux sont toujours synchronisés. Cette rubrique décrit également les initialiseurs Code First par défaut implémentés par Azure Mobile Services qui sont utilisés pendant le développement. Ces initialiseurs vous permettent de réaliser facilement des modifications du schéma sans utiliser Code First Migrations lorsqu'il n'est pas nécessaire de conserver vos données existantes. 

## Initialiseurs du modèle de données

Mobile Services prend en charge deux classes de base d'initialiseurs de modèle de données dans un projet de service mobile principal .NET. Ces initialiseurs indiquent à Entity Framework de supprimer et de créer à nouveau la base de données chaque fois qu'il détecte une modification de modèle de données indiquée par votre [DbContext]. Ces initialiseurs sont conçus pour fonctionner à la fois lorsque votre service mobile est en cours d'exécution sur un ordinateur local et lorsqu'il est hébergé dans Azure. Les deux classes d'initialiseurs de base suppriment toutes les tables, les vues, les fonctions et les procédures du schéma utilisées par le service mobile. 

+ **Effacer les changements de modèles de schéma de base de données** <br/> Les objets du schéma sont supprimés uniquement lorsque Code First détecte un changement apporté au modèle de données. Dans un projet principal .NET, l'initialiseur par défaut téléchargé à partir du [portail de gestion Azure] hérite de cette classe de base.
 
+ **Toujours effacer le schéma de base de données** : <br/> Les objets du schéma sont supprimés chaque fois que vous accédez au modèle de données. Utilisez cette classe de base pour réinitialiser la base de données sans devoir modifier le modèle de données.   	 	

Dans le projet de démarrage rapide téléchargé, l'initialiseur de Code First est défini dans le fichier WebApiConfig.cs. Remplacez la méthode **Seed** pour ajouter de nouvelles lignes de données à de nouvelles tables. Pour obtenir des exemples de données d'amorçage, consultez la section [Données d'amorçage dans les migrations].Vous pouvez utiliser d'autres initialiseurs du modèle de données Code First lorsque vous utilisez un ordinateur local. Toutefois, les initialiseurs qui tentent de supprimer des bases de données échoueront dans Azure, car l'utilisateur n'a pas l'autorisation de supprimer la base de données, ce qui est une bonne chose. 

Vous devez continuer à utiliser ces initialiseurs pendant le développement local de votre projet de service mobile. Les didacticiels consacrés au service principal .NET supposent que vous les utilisez. Néanmoins, dans le cas où vous souhaitez apporter des modifications à des modèles de données et conserver les données existantes dans la base de données, vous devez utiliser Code First Migrations. 

>[WACOM.NOTE]Lors du développement et du test de votre projet de service mobile par rapport aux services Azure actifs, vous devez toujours utiliser une instance de service mobile dédiée au test. Vous ne devez jamais développer ou tester par rapport à un service mobile qui est actuellement en production ou en cours d'utilisation par les applications clientes. 

## <a name="migrations"></a>Activation de Code First Migrations

Code First Migrations utilise une méthode de capture instantanée qui génère un code qui, lorsqu'il est exécuté, réalise des modifications du schéma dans la base de données. Avec Migrations, vous pouvez apporter des modifications incrémentielles à votre modèle de données et conserver les données existantes dans la base de données. 

>[WACOM.NOTE]Si vous avez déjà publié votre projet de service mobile principal .NET sur Azure et que votre schéma de tables de base de données SQL ne correspond pas au modèle de données actif de votre projet, vous devez utiliser un initialiseur, supprimer les tables ou, sinon, synchroniser manuellement le schéma et le modèle de données avant de tenter de publier à l'aide de Code First Migrations.

Les étapes suivantes activent Migrations et appliquent les modifications de modèles de données dans le projet, la base de données locale et Azure. 

1. Dans Visual Studio, dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet de service mobile et cliquez sur **Définir comme projet de démarrage**.
 
2. Dans le menu **Outils**, développez **NuGet Package Manager**, puis cliquez sur **Console du Gestionnaire de package**.

	Ainsi, la Console du Gestionnaire de package s'affiche, que vous utiliserez pour gérer Code First Migrations.

3. Dans la Console du Gestionnaire de package, entrez la commande suivante :

		PM> Enable-Migrations

	Cette commande active Code First Migrations pour votre projet.

4. Dans la console, entrez la commande suivante :

		PM> Add-Migration Initial

	Cette commande crée une nouvelle migration nommée *Initial*. Le code de migration est stocké dans le dossier du projet Migrations.

5. Développez le dossier App_Start, ouvrez le fichier de projet WebApiConfig.cs et ajoutez les instructions **using** suivantes :

		using System.Data.Entity.Migrations;
		using todolistService.Migrations;

	Dans le code ci-dessus, vous devez remplacer la chaîne _todolistService_ par l'espace de noms de votre projet, qui, pour le projet de démarrage rapide téléchargé, est le service <em>mobile&#95;service&#95;name</em>.  
 
6. Dans le même fichier de code, mettez en commentaire l'appel à la méthode **Database.SetInitializer** et ajoutez le code suivant :

        var migrator = new DbMigrator(new Configuration());
        migrator.Update();

	Ce code désactive l'initialiseur de base de données Code First par défaut qui supprime et recrée la base de données et la remplace par une demande explicite afin d'appliquer la migration la plus récente. À ce stade, toute modification de modèle de données résulte en une exception InvalidOperationException lors de l'accès aux données, sauf si une migration a été créée pour la modification. Ultérieurement, votre service doit utiliser Code First Migrations afin de faire migrer les modifications de modèles de données dans la base de données.

7.  Appuyez sur F5 pour démarrer le projet de service mobile sur l'ordinateur local.
 
	À ce stade, la base de données est synchronisée avec le modèle de données. Si vous avez fourni des données d'amorçage, vous pouvez les vérifier en cliquant sur **Try it out**, **GET tables/todoitem**, puis sur **Try this out** et enfin **Envoyer**. Pour plus d'informations, consultez la section [Données d'amorçage dans les migrations].

8.   À présent, apportez une modification à votre modèle de donnés, par exemple, en ajoutant une nouvelle propriété UserId au type TodoItem, régénérez le projet, puis, dans le Gestionnaire de package, exécutez la commande suivante :

		PM> Add-Migration NewUserId
                                                               
	Cette commande crée une nouvelle migration nommée *NewUserId*. Un nouveau fichier de code, qui implémente cette modification, est ajouté dans le dossier Migrations  

9.  Appuyez à nouveau sur F5 pour redémarrer le projet de service mobile sur l'ordinateur local.

	La migration est appliquée à la base de données et cette dernière est à nouveau synchronisée avec le modèle de données. Si vous avez fourni des données d'amorçage, vous pouvez les vérifier en cliquant sur **Try it out**, **GET tables/todoitem**, puis sur **Try this out** et enfin **Envoyer**. Pour plus d'informations, consultez la section [Données d'amorçage dans les migrations].

10. Publiez à nouveau le service mobile sur Azure, puis exécutez l'application cliente afin d'accéder aux données et de vérifier que les données se chargent sans erreur. 

13. (Facultatif) Dans le [portail de gestion Azure], sélectionnez votre service mobile, cliquez sur l'onglet **Configurer**, puis sur le lien **Base de données SQL**. 

	![][0]

	Vous accédez ainsi à la page de la base de données SQL de la base de données de votre service mobile.

14. (Facultatif) Cliquez sur **Gérer**, connectez-vous au serveur de base de données SQL, puis cliquez sur **Conception** et vérifiez que les modifications de schéma ont été effectuées dans Azure. 

    ![][1] 


##<a name="seeding"></a>Données d'amorçage dans Migrations

Migrations peut ajouter des données d'amorçage à la base de données lorsqu'une migration est exécutée. La classe **Configuration** comporte une méthode **Seed** que vous pouvez remplacer afin d'insérer ou de mettre à jour des données. Le fichier de code Configuration.cs est ajouté au dossier Migrations lorsque Migrations est activé. Ces exemples montrent comment remplacer la méthode [Seed] pour amorcer des données à la table **TodoItems**. La méthode [Seed] est appelée après la migration vers la version la plus récente. 

###Amorçage d'une nouvelle table

Le code suivant amorce la table **TodoItems** avec de nouvelles lignes de données :

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

Le code suivant amorce seulement la colonne UserId :
 		    
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
[Données d'amorçage dans Migrations]: #seeding

<!-- Images -->
[0]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/navagate-to-sql-database.png
[1]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/manage-sql-database.png
[2]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/sql-database-drop-tables.png

<!-- URLs -->
[DropCreateDatabaseIfModelChanges]: http://msdn.microsoft.com/fr-fr/library/gg679604(v=vs.113).aspx
[Seed]: http://msdn.microsoft.com/fr-fr/library/hh829453(v=vs.113).aspx
[Azure Management Portal]: https://manage.windowsazure.com/
[DbContext]: http://msdn.microsoft.com/fr-fr/library/system.data.entity.dbcontext(v=vs.113).aspx
[AddOrUpdate]: http://msdn.microsoft.com/fr-fr/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx
