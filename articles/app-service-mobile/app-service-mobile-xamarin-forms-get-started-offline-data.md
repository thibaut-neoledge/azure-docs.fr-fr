---
title: Activation de la synchronisation hors connexion pour votre application Azure Mobile App (Xamarin.Forms) | Microsoft Docs
description: Découvrez comment utiliser App Service Mobile App pour mettre en cache et synchroniser des données hors connexion dans votre application Xamarin.Forms
documentationcenter: xamarin
author: ggailey777
manager: erikre
editor: ''
services: app-service\mobile

ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/18/2016
ms.author: glenga

---
# Activer la synchronisation hors connexion pour votre application mobile Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## Vue d'ensemble
Ce didacticiel présente la fonctionnalité de synchronisation hors connexion d’Azure Mobile Apps pour Xamarin.Forms. La synchronisation hors connexion permet aux utilisateurs finaux d'interagir avec une application mobile pour afficher, ajouter ou modifier des données, même lorsqu'il n'existe aucune connexion réseau. Les modifications sont stockées dans une base de données locale. Une fois l'appareil de nouveau en ligne, ces modifications sont synchronisées avec le service distant.

Ce didacticiel est basé sur la solution de démarrage rapide de Xamarin.Forms pour les applications mobiles créées en suivant le didacticiel [Création d’une application Xamarin iOS]. La solution de démarrage rapide de Xamarin.Forms contient le code de prise en charge de la synchronisation hors connexion, qui doit simplement être activé. Dans ce didacticiel, vous allez mettre à jour la solution de démarrage rapide pour activer les fonctionnalités hors connexion d’Azure Mobile Apps. Vous allez également en savoir plus sur le code hors connexion spécifique dans l’application. Si vous n’utilisez pas la solution de démarrage rapide téléchargée, vous devez ajouter les packages d’extension d’accès aux données à votre projet. Pour plus d'informations sur les packages d'extension de serveur, consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Pour plus d’informations sur la fonctionnalité de synchronisation hors connexion, consultez la rubrique [Synchronisation des données hors connexion dans Azure Mobile Apps].

## Activer la fonctionnalité de synchronisation hors connexion dans la solution de démarrage rapide
Le code de synchronisation hors connexion est inclus dans le projet à l’aide de directives de préprocesseur C#. Lorsque le symbole **OFFLINE\_SYNC\_ENABLED** est défini, ces chemins de code sont inclus dans la build. Pour les applications Windows, vous devez également installer la plateforme SQLite.

1. Dans Visual Studio, cliquez sur la solution > **Gérer les packages NuGet pour la solution...**, puis recherchez et installez le package NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** pour tous les projets dans la solution.
2. Dans l’Explorateur de solutions, ouvrez le fichier TodoItemManager.cs du projet comportant **Portable** dans son nom (projet de bibliothèque de classes portables), puis supprimez les marques de commentaire de la directive de préprocesseur suivante :
   
        #define OFFLINE_SYNC_ENABLED
3. Dans l’Explorateur de solutions, ouvrez le fichier TodoList.xaml.cs du projet **Portable**, recherchez la méthode **OnAppearing** et assurez-vous que la valeur `true` est transmise pour *syncItems* lors de l’appel de **RefreshItems**, comme suit :
   
        await RefreshItems(true, syncItems: true);
    Cela signifie que l’application tente de se synchroniser avec le serveur principal lorsqu’elle démarre.
4. (Facultatif) Si vous prenez en charge les appareils iOS, ouvrez le fichier AppDelegate.cs du projet **iOS** et supprimez les marques de commentaire de la ligne de code suivante dans la méthode **FinishedLaunching** :
   
        SQLitePCL.CurrentPlatform.Init();
   
    Cette opération initialise le magasin SQLite sur les appareils iOS. Les tâches restantes sont uniquement requises pour prendre en charge des appareils Windows.
5. (Facultatif) Pour prendre en charge des appareils Windows, installez l’un des packages runtime SQLite suivants :
   
   * **Windows 8.1 Runtime :** installez [SQLite pour Windows 8.1](http://go.microsoft.com/fwlink/p/?LinkID=716919).
   * **Windows Phone 8.1 :** installez [SQLite for Windows Phone 8.1](http://go.microsoft.com/fwlink/p/?LinkID=716920).
   * **Plateforme Windows universelle** : installez [SQLite pour plateforme Windows universelle](http://sqlite.org/2016/sqlite-uwp-3120200.vsix). Pour le moment, le démarrage rapide ne comprend pas de projet UWP (plateforme Windows universelle).
6. (Facultatif) Dans chaque projet d’application Windows, cliquez avec le bouton droit sur **Références** > **Ajouter une référence**, développez le dossier **Windows** > **Extensions**, puis activez le Kit de développement logiciel (SDK) **SQLite pour Windows Runtime** approprié en même temps que le SDK **Runtime Visual C++ 2013 pour Windows**. Notez que les noms de SDK SQLite varient légèrement en fonction de la plate-forme Windows utilisée. Pour un projet UWP, installez le SDK **Runtime Visual C++ 2015 pour les applications de la plateforme Windows universelle**.

## Examiner le code de synchronisation client
Voici un bref aperçu de ce qui est déjà inclus dans le code du didacticiel au sein des directives `#if OFFLINE_SYNC_ENABLED`. Notez que la fonctionnalité de synchronisation hors connexion se trouve dans le fichier TodoItemManager.cs du projet de bibliothèque de classes portables. Pour obtenir une vue d’ensemble conceptuelle de la fonctionnalité, consultez [Synchronisation des données hors connexion dans Azure Mobile Apps].

* Avant de pouvoir effectuer des opérations de table, le magasin local doit être initialisé. La base de données du magasin local est initialisée dans le constructeur de classe **TodoItemManager** en utilisant le code suivant :
  
        var store = new MobileServiceSQLiteStore("localstore.db");
        store.DefineTable<TodoItem>();
  
        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);
  
        this.todoTable = client.GetSyncTable<TodoItem>();
  
    Ce code crée une nouvelle base de données SQLite locale à l’aide de la classe **MobileServiceSQLiteStore**. La méthode **DefineTable** crée une table dans le magasin local qui correspond aux champs dans le type fourni (ici, `ToDoItem`). Ce type n’a pas besoin d’inclure toutes les colonnes qui se trouvent dans la base de données distante. Il est possible de stocker uniquement un sous-ensemble de colonnes.
* Le champ **todoTable** dans **TodoItemManager** est un type **IMobileServiceSyncTable** au lieu du type **IMobileServiceTable**. Cette classe utilise la base de données locale pour toutes les opérations de table CRUD (Create, Read, Update et Delete). Vous décidez à quel moment ces modifications sont envoyées au serveur principal Mobile App en appelant **PushAsync** sur le type **IMobileServiceSyncContext** utilisé par le client. Le contexte de synchronisation permet de conserver les relations entre les tables grâce au suivi et à l’envoi des modifications dans toutes les tables qu’une application cliente modifie quand **PushAsync** est appelé.
  
    La méthode **SyncAsync** suivante est appelée pour la synchronisation avec le serveur principal Mobile App :
  
        public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;
  
            try
            {
                await this.client.SyncContext.PushAsync();
  
                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }
  
            // Simple error/conflict handling. 
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }
  
                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
                        error.TableName, error.Item["id"]);
                }
            }
        }
  
    Cet exemple utilise la gestion simple des erreurs avec le gestionnaire de synchronisation par défaut. Une application réelle gèrerait les différentes erreurs telles que les conditions du réseau, les conflits de serveur et autres à l’aide d’une implémentation personnalisée de **IMobileServiceSyncHandler**.

## Considérations relatives à la synchronisation hors connexion
Dans l’exemple, la méthode **SyncAsync** est appelée uniquement au démarrage et lorsqu’une synchronisation est spécifiquement demandée. Pour lancer la synchronisation dans une application Android ou iOS, extrayez la liste des éléments ; pour Windows, utilisez le bouton **Synchronisation**. Dans une application réelle, vous pourriez également provoquer le déclenchement de la fonctionnalité de synchronisation lorsque l’état du réseau change.

Si une extraction est exécutée sur une table qui comporte des mises à jour locales en attente faisant l’objet d’un suivi en fonction du contexte, cette opération déclenche automatiquement un envoi préalable sur le contexte. Lors de l’actualisation, de l’ajout et de l’achèvement des éléments dans cet exemple, vous pouvez omettre l’appel explicite de **PushAsync**, puisqu’il est redondant.

Dans le code fourni, tous les enregistrements de la table TodoItem distante sont interrogés, mais il est également possible de filtrer les enregistrements en transmettant un ID de requête et une requête à **PushAsync**. Pour plus d’informations, consultez la section *Synchronisation incrémentielle* dans [Synchronisation des données hors connexion dans Azure Mobile Apps].

## Exécuter l’application cliente
Maintenant que la synchronisation hors connexion est activée, vous pouvez exécuter l’application cliente au moins une fois sur chaque plateforme pour remplir la base de données du magasin local. Ensuite, vous allez simuler un scénario hors connexion et modifier les données du magasin local pendant que l’application est hors connexion.

## Mettre à jour le comportement de synchronisation de l’application cliente
Dans cette section, vous allez modifier le projet client pour simuler un scénario hors connexion à l’aide d’une URL d’application non valide pour votre backend. Quand vous ajoutez ou modifiez des éléments de données, ces modifications sont conservées dans le magasin local, mais ne sont synchronisées avec le magasin de données principal qu’une fois la connexion rétablie.

1. Dans l’Explorateur de solutions, ouvrez le fichier Constants.cs dans le projet **Portable** et changez la valeur de `ApplicationURL` pour pointer vers une URL non valide, comme suit :
   
        publis static string ApplicationURL = @"https://your-service.azurewebsites.xxx/";
2. Ouvrez le fichier TodoItemManager.cs du projet **Portable**, puis ajoutez un **catch** supplémentaire pour la classe **Exception** de base au bloc **try... catch** dans **SyncAsync**. Ce bloc **catch** écrit le message d’exception dans la console, comme suit :
   
            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. Créez et exécutez l’application cliente, ajoutez de nouveaux éléments et notez l’exception consignée dans la console pour chaque tentative de synchronisation avec le serveur principal. Ces nouveaux éléments se trouvent uniquement dans le magasin local jusqu’à ce qu’ils puissent être transmis par push vers le backend mobile. L’application cliente se comporte comme si elle est connectée au backend et prend en charge toutes les opérations de création, lecture, mise à jour et suppression.
4. Fermez l’application et redémarrez-la pour vérifier que les nouveaux élément que vous avez créés sont conservés dans le magasin local.
5. (Facultatif) À l’aide de Visual Studio, affichez votre table Azure SQL Database pour constater que les données dans la base de données principale n’ont pas changé.
   
    Dans Visual Studio, ouvrez l'**Explorateur de serveurs**. Accédez à votre base de données dans **Azure**->**Bases de données SQL**. Cliquez avec le bouton droit sur votre base de données, puis sélectionnez **Ouvrir dans l’Explorateur d’objets SQL Server**. Maintenant, vous pouvez accéder à votre table de base de données SQL et à son contenu.

## Mettre à jour l’application cliente pour reconnecter votre backend mobile
Dans cette section, vous allez reconnecter l'application au backend mobile, ce qui simule le retour de l'application à un état en ligne. Quand vous effectuez le mouvement d'actualisation, les données sont synchronisées avec votre backend mobile.

1. Rouvrez le fichier Constants.cs, puis corrigez `applicationURL` pour pointer vers l’URL correcte.
2. Régénérez et exécutez l’application cliente. Après son lancement, l’application tente une synchronisation avec le serveur principal Mobile App. Vérifiez qu’aucune exception n’est consignée dans la console de débogage.
3. (Facultatif) Affichez les données mises à jour à l’aide de l’Explorateur d’objets SQL Server ou d’un outil REST tel que Fiddler. Notez que les données n’ont pas été synchronisées entre la base de données du serveur principal et le magasin local.
   
    Notez que les données ont été synchronisées entre la base de données et le magasin local et qu’elles contiennent les éléments que vous avez ajoutés pendant que votre application était déconnectée.

## Ressources supplémentaires
* [Synchronisation des données hors connexion dans Azure Mobile Apps]
* [Cloud Cover : synchronisation hors connexion dans Azure Mobile Services](remarque : le contexte de la vidéo est Mobile Services, mais la synchronisation hors connexion fonctionne de la même manière dans Azure Mobile Apps.md)

<!-- ##Summary

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Next steps

* [Handling conflicts with offline support for Mobile Services]

* [How to use the Xamarin Component client for Azure Mobile Services]
 -->

<!-- Images -->

<!-- URLs. -->
[Création d’une application Xamarin iOS]: app-service-mobile-xamarin-ios-get-started.md
[Synchronisation des données hors connexion dans Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[How to use the Xamarin Component client for Azure Mobile Services]: partner-xamarin-mobile-services-how-to-use-client-library.md
[Cloud Cover : synchronisation hors connexion dans Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri

<!---HONumber=AcomDC_0629_2016-->