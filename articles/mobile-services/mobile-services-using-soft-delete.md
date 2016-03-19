<properties
	pageTitle="Utilisation de la suppression réversible dans Mobile Services (Windows Store) | Microsoft Azure"
	description="Découvrez comment utiliser la fonctionnalité de suppression réversible Azure Mobile Services dans votre application"
	documentationCenter=""
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="wesmc"/>

# Utilisation de la suppression réversible dans Mobile Services

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


##Vue d'ensemble

La suppression réversible peut être activée pour les tables créées avec un backend JavaScript ou .NET. Lors de l'utilisation de la suppression réversible, une nouvelle colonne nommée *\_\_deleted* de [type de bit SQL] est ajoutée à la base de données. Lorsque la suppression réversible est activée, une opération de suppression ne supprime pas physiquement des lignes d'une base de données, mais définit la valeur de la colonne supprimée sur TRUE.

Lors de l'interrogation des enregistrements d'une table avec la suppression réversible activée, les lignes supprimées ne sont pas renvoyées dans la requête par défaut. Pour demander ces lignes, vous devez transmettre un paramètre de requête *\_\_includeDeleted=true* dans votre [opération de requête REST](http://msdn.microsoft.com/library/azure/jj677199.aspx). Dans le Kit de développement logiciel (SDK) .NET, vous pouvez également utiliser la méthode d'assistance `IMobileServiceTable.IncludeDeleted()`.

La suppression réversible pour le backend .NET a été prise en charge pour la première fois avec la version 1.0.402 du backend .NET de Microsoft Azure Mobile Services. Les packages NuGet les plus récents sont disponibles ici : [Serveur principal .NET de Microsoft Azure Mobile Services](http://go.microsoft.com/fwlink/?LinkId=513165).


Voici quelques-uns des avantages de la suppression réversible :

* Lors de l'utilisation de la [synchronisation des données hors connexion pour Mobile Services], le Kit de développement logiciel (SDK) client recherche automatiquement les enregistrements supprimés et les supprime de la base de données locale. Lorsque la suppression réversible est désactivée, vous devez écrire du code supplémentaire sur le backend afin que le Kit de développement logiciel (SDK) client sache quels enregistrements supprimer du magasin local. Sinon, le magasin local client et le backend ne traitent pas les enregistrements supprimés de manière cohérente et la méthode cliente `PurgeAsync()` doit être appelée pour effacer le magasin local.
* Certaines applications ont comme condition requise de ne jamais supprimer physiquement de données, ou de les supprimer uniquement après les avoir auditées. La suppression réversible peut s'avérer utile dans ce cas.
* La suppression réversible peut servir à implémenter une fonctionnalité d'annulation de la suppression, pour pouvoir restaurer des données supprimées par inadvertance. Toutefois, les enregistrements supprimés de façon réversible prennent de la place dans la base de données. Vous devez donc envisager de créer une tâche programmée pour supprimer définitivement ces enregistrements. Pour voir un exemple, consultez les sections [Utilisation de la suppression réversible avec le backend .NET](#using-with-dotnet) et [Utilisation de la suppression réversible avec le backend JavaScript](#using-with-javascript). Votre code client doit également régulièrement appeler `PurgeAsync()` afin que les enregistrements supprimés de manière définitive ne soient pas conservés dans le magasin de données local de l'appareil.





##Activation de la suppression réversible avec le backend .NET

La suppression réversible pour le backend .NET a été prise en charge pour la première fois avec la version 1.0.402 du backend .NET de Microsoft Azure Mobile Services. Les packages NuGet les plus récents sont disponibles ici : [Backend .NET de Microsoft Azure Mobile Services](http://go.microsoft.com/fwlink/?LinkId=513165).

Les étapes suivantes vous guident lors de l'activation de la suppression réversible pour un service mobile de backend .NET.

1. Ouvrez votre projet de service mobile de backend .NET dans Visual Studio.
2. Cliquez avec le bouton droit sur le projet principal .NET, puis cliquez sur **Gérer les packages NuGet**.
3. Dans la boîte de dialogue du gestionnaire de package, cliquez sur **Nuget.org** sous les mises à jour et installez la version 1.0.402 ou ultérieure des packages NuGet du [backend .NET de Microsoft Azure Mobile Services](http://go.microsoft.com/fwlink/?LinkId=513165).
3. Dans l'Explorateur de solutions de Visual Studio, développez le nœud **Contrôleurs** sous votre projet de backend .NET et ouvrez la source du contrôleur. Par exemple, *TodoItemController.cs*.
4. Dans la méthode `Initialize()` du contrôleur, transmettez le paramètre `enableSoftDelete: true` au constructeur EntityDomainManager.

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            MobileService1Context context = new MobileService1Context();
            DomainManager = new EntityDomainManager<TodoItem>(context, Request, Services, enableSoftDelete: true);
        }


##Activation de la suppression réversible avec le backend JavaScript

Si vous créez une table pour votre service mobile, vous pouvez activer la suppression réversible dans la page de création de la table.

![][2]

Pour activer la suppression réversible sur une table existante du backend JavaScript :

1. Dans le [portail Azure Classic], cliquez sur votre service mobile. Cliquez ensuite sur l'onglet Données.
2. Dans la page des données, cliquez pour sélectionner la table souhaitée. Cliquez ensuite sur le bouton **Activer la suppression réversible** dans la barre de commande. Si la suppression réversible est déjà activée pour la table, ce bouton n'apparaît pas, mais vous pourrez voir la colonne *\_\_deleted* en cliquant sur l'onglet **Parcourir** ou **Colonnes** de la table.

    ![][0]

    Pour désactiver la suppression réversible pour votre table, cliquez sur l'onglet **Colonnes**, puis sur la colonne *\_\_deleted* et le bouton **Supprimer**.

    ![][1]

## <a name="using-with-dotnet"></a>Utilisation de la suppression réversible avec le backend .NET


La tâche planifiée suivante purge les enregistrements supprimés de manière réversible datant de plus d'un mois :

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

Pour en savoir plus sur les tâches planifiées avec le backend .NET Mobile Services, consultez : [Planifier les tâches récurrentes avec le backend JavaScript Mobile Services](mobile-services-dotnet-backend-schedule-recurring-tasks.md)




## <a name="using-with-javascript"></a>Utilisation de la suppression réversible avec le backend JavaScript

Vous pouvez utiliser les scripts de table pour ajouter une logique à la fonctionnalité de suppression réversible à l'aide des services mobiles de backend JavaScript.

Pour détecter une demande d'annulation de suppression, utilisez la propriété « undelete » dans votre script de mise à jour de table :

    function update(item, user, request) {
        if (request.undelete) { /* any undelete specific code */; }
    }
Pour inclure les enregistrements supprimés dans les résultats de requête d'un script, définissez le paramètre includeDeleted sur True :

    tables.getTable('softdelete_scenarios').read({
        includeDeleted: true,
        success: function (results) {
            request.respond(200, results)
        }
    });

Pour récupérer les enregistrements supprimés via une demande HTTP, ajoutez le paramètre de requête « \_\_includedeleted=true » :

    http://youservice.azure-mobile.net/tables/todoitem?__includedeleted=true

### Exemple de tâche planifiée pour la purge des enregistrements supprimés de manière réversible.

Voici un exemple de tâche planifiée qui supprime les enregistrements qui ont été mis à jour avant une date donnée :

    function purgedeleted() {
         mssql.query('DELETE FROM softdelete WHERE __deleted=1', {
            success: function(results) {
                console.log(results);
            },
            error: function(err) {
                console.log("error is: " + err);
        }});
    }

Pour en savoir plus sur les tâches planifiées avec Mobile Services du backend JavaScript, consultez : [Planifier les tâches récurrentes avec le backend JavaScript Mobile Services](mobile-services-schedule-recurring-tasks.md)





<!-- Images -->
[0]: ./media/mobile-services-using-soft-delete/enable-soft-delete-button.png
[1]: ./media/mobile-services-using-soft-delete/disable-soft-delete.png
[2]: ./media/mobile-services-using-soft-delete/enable-soft-delete-new-table.png

<!-- URLs. -->
[type de bit SQL]: http://msdn.microsoft.com/library/ms177603.aspx
[synchronisation des données hors connexion pour Mobile Services]: mobile-services-windows-store-dotnet-get-started-offline-data.md
[portail Azure Classic]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0218_2016-->