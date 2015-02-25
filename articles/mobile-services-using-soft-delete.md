<properties pageTitle="Utilisation de la suppression réversible dans Mobile Services (Windows Store) | Centre de développement mobile" description="Découvrez comment utiliser la fonctionnalité de suppression réversible Azure Mobile Services dans votre application" documentationCenter="" authors="wesmc7777" manager="dwrede" editor="" services="mobile-services"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="wesmc"/>

# Utilisation de la suppression réversible dans Mobile Services

La suppression réversible peut être activée pour les tables créées avec un serveur principal JavaScript ou .NET. Lors de l'utilisation de la suppression réversible, une nouvelle colonne nommée *\__deleted* de [type de bit SQL] est ajoutée à la base de données. Lorsque la suppression réversible est activée, une opération de suppression ne supprime pas physiquement des lignes d'une base de données, mais définit la valeur de la colonne supprimée sur TRUE.

Lors de l'interrogation des enregistrements d'une table avec la suppression réversible activée, les lignes supprimées ne sont pas renvoyées dans la requête par défaut. Pour demander ces lignes, vous devez transmettre un paramètre de requête *\__includeDeleted=true* dans votre [opération de requête REST](http://msdn.microsoft.com/fr-fr/library/azure/jj677199.aspx). Dans le Kit de développement logiciel (SDK) .NET, vous pouvez également utiliser la méthode d'assistance `IMobileServiceTable.IncludeDeleted()`.

La suppression réversible pour le serveur principal .NET a été prise en charge pour la première fois avec la version 1.0.402 du serveur principal .NET de Microsoft Azure Mobile Services. Les packages NuGet les plus récents sont disponibles ici : [Serveur principal .NET de Microsoft Azure Mobile Services](http://go.microsoft.com/fwlink/?LinkId=513165).


Voici quelques-uns des avantages de la suppression réversible :

* Lors de l'utilisation de la [synchronisation des données hors connexion pour Mobile Services], le Kit de développement logiciel (SDK) client recherche automatiquement les enregistrements supprimés et les supprime de la base de données locale. Lorsque la suppression réversible est désactivée, vous devez écrire du code supplémentaire sur le serveur principal afin que le Kit de développement logiciel (SDK) client sache quels enregistrements supprimer du magasin local. Sinon, le magasin local client et le serveur principal ne traiteront pas les enregistrements supprimés de manière cohérente et la méthode cliente `PurgeAsync()` doit être appelée pour effacer le magasin local.
* Certaines applications ont comme condition requise de ne jamais supprimer physiquement de données, ou de les supprimer uniquement après les avoir auditées. La suppression réversible peut s'avérer utile dans ce cas.
* La suppression réversible peut servir à implémenter une fonctionnalité d'annulation de la suppression, pour pouvoir restaurer des données supprimées par inadvertance.
Toutefois, les enregistrements supprimés de façon réversible prennent de la place dans la base de données. Vous devez donc envisager de créer une tâche programmée pour supprimer définitivement ces enregistrements. Pour voir un exemple, consultez les sections [Utilisation de la suppression réversible avec le serveur principal .NET] et [Utilisation de la suppression réversible avec le serveur principal JavaScript]. Votre code client doit également régulièrement appeler `PurgeAsync()` afin que les enregistrements supprimés de manière définitive ne soient pas conservés dans le magasin de données local de l'appareil.



Vue d'ensemble de cette rubrique :

1. [Activation de la suppression réversible avec le serveur principal .NET]
2. [Activation de la suppression réversible avec le serveur principal JavaScript]
3. [Utilisation de la suppression réversible avec le serveur principal .NET]
4. [Utilisation de la suppression réversible avec le serveur principal JavaScript]



## <a name="enable-for-dotnet"></a>Activation de la suppression réversible avec le serveur principal .NET

La suppression réversible pour le serveur principal .NET a été prise en charge pour la première fois avec la version 1.0.402 du serveur principal .NET de Microsoft Azure Mobile Services. Les packages NuGet les plus récents sont disponibles ici : [Serveur principal .NET de Microsoft Azure Mobile Services](http://go.microsoft.com/fwlink/?LinkId=513165).

Les étapes suivantes vous guident lors de l'activation de la suppression réversible pour un service mobile principal .NET.

1. Ouvrez votre projet de service mobile principal .NET dans Visual Studio.
2. Cliquez avec le bouton droit sur le projet principal .NET, puis cliquez sur **Gérer les packages NuGet**. 
3. Dans la boîte de dialogue du gestionnaire de package, cliquez sur **Nuget.org** sous les mises à jour et installez la version 1.0.402 ou ultérieure des packages NuGet du [serveur principal .NET de Microsoft Azure Mobile Services](http://go.microsoft.com/fwlink/?LinkId=513165).
3. Dans l'Explorateur de solutions de Visual Studio, développez le nœud **Contrôleurs** sous votre projet principal .NET et ouvrez la source du contrôleur. Par exemple, *TodoItemController.cs*.
4. Dans la méthode `Initialize()` du contrôleur, transmettez le paramètre `enableSoftDelete: true` au constructeur EntityDomainManager.

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            MobileService1Context context = new MobileService1Context();
            DomainManager = new EntityDomainManager<TodoItem>(context, Request, Services, enableSoftDelete: true);
        }


## <a name="enable-for-javascript"></a>Activation de la suppression réversible avec le serveur principal JavaScript

Si vous créez une table pour votre service mobile, vous pouvez activer la suppression réversible dans la page de création de la table.

![][2]

Pour activer la suppression réversible sur une table existante du serveur principal JavaScript :

1. Dans le [portail de gestion], cliquez sur votre service mobile. Cliquez ensuite sur l'onglet Données.
2. Dans la page des données, cliquez pour sélectionner la table souhaitée. Cliquez ensuite sur le bouton **Activer la suppression réversible** dans la barre de commande. Si la suppression réversible est déjà activée pour la table, ce bouton n'apparaît pas, mais vous pourrez voir la colonne *\__deleted* en cliquant sur l'onglet **Parcourir** ou **Colonnes** de la table.

    ![][0]

    Pour désactiver la suppression réversible pour votre table, cliquez sur l'onglet **Colonnes**, puis sur la colonne *\__deleted* et le bouton **Supprimer**.  

    ![][1]

## <a name="using-with-dotnet"></a>Utilisation de la suppression réversible avec le serveur principal .NET


La tâche planifiée suivante purge les enregistrements supprimés de manière réversible datant de plus d'un mois :

    public class SampleJob : ScheduledJob
    {
        private MobileService1Context context;
     
        protected override void Initialize(ScheduledJobDescriptor scheduledJobDescriptor, 
            CancellationToken cancellationToken)
        {
            base.Initialize(scheduledJobDescriptor, cancellationToken);
            context = new MobileService1Context();
        }
     
        public override Task ExecuteAsync()
        {
            Services.Log.Info("Purging old records");
            var monthAgo = DateTimeOffset.UtcNow.AddDays(-30);
     
            var toDelete = context.TodoItems.Where(x => x.Deleted == true && x.UpdatedAt <= monthAgo).ToArray();
            context.TodoItems.RemoveRange(toDelete);
            context.SaveChanges();
     
            return Task.FromResult(true);
        }
    }

Pour en savoir plus sur les tâches planifiées avec le serveur principal .NET de Mobiles services, consultez la page suivante : [Planification de tâches périodiques avec le serveur principal JavaScript de Mobiles Services](/fr-fr/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/) 




## <a name="using-with-javascript"></a>Utilisation de la suppression réversible avec le serveur principal JavaScript

Vous pouvez utiliser les scripts de table pour ajouter une logique à la fonctionnalité de suppression réversible avec les services mobiles principaux JavaScript.

Pour détecter une demande d'annulation de suppression, utilisez la propriété " undelete " dans votre script de mise à jour de table :
    
    function update(item, user, request) {
        if (request.undelete) { /* any undelete specific code */; }
    }
To include deleted records in query result in a script, set the "includeDeleted" parameter to true:
    
    tables.getTable('softdelete_scenarios').read({
        includeDeleted: true,
        success: function (results) {
            request.respond(200, results)
        }
    });

Pour récupérer les enregistrements supprimés via une demande HTTP, ajoutez le paramètre de requête " __includedeleted=true " :

    http://youservice.azure-mobile.net/tables/todoitem?__includedeleted=true

### Exemple de tâche planifiée pour la purge des enregistrements supprimés de manière réversible.

Voici un exemple de tâche planifiée qui supprime les enregistrements qui ont été mis à jour avant une date donnée :

    function purgedeleted() {
         mssql.query('DELETE FROM softdelete WHERE __deleted=1', {
            success: function(results) {
                console.log(results);
            },
            error: function(err) {
                console.log("error is: " + err);
        }});
    }

Pour en savoir plus sur les tâches planifiées avec le serveur principal JavaScript de Mobiles Services, consultez la page suivante : [Planification de tâches périodiques avec le serveur principal JavaScript de Mobiles Services](/fr-fr/documentation/articles/mobile-services-schedule-recurring-tasks/).




<!-- Anchors. -->
[Activation de la suppression réversible avec le serveur principal .NET]: #enable-for-dotnet
[Activation de la suppression réversible avec le serveur principal JavaScript]: #enable-for-javascript
[Utilisation de la suppression réversible avec le serveur principal .NET]: #using-with-dotnet
[Utilisation de la suppression réversible avec le serveur principal JavaScript]: #using-with-javascript

<!-- Images -->
[0]: ./media/mobile-services-using-soft-delete/enable-soft-delete-button.png
[1]: ./media/mobile-services-using-soft-delete/disable-soft-delete.png
[2]: ./media/mobile-services-using-soft-delete/enable-soft-delete-new-table.png

<!-- URLs. -->
[Type de bit SQL]: http://msdn.microsoft.com/fr-fr/library/ms177603.aspx
[Synchronisation des données hors connexion pour Mobile Services]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data/
[Portail de gestion]: https://manage.windowsazure.com/




<!--HONumber=42-->
