---
title: Activation de la synchronisation hors connexion pour votre application Azure Mobile App (Cordova) | Microsoft Docs
description: "Découvrez comment utiliser App Service Mobile App pour mettre en cache et synchroniser des données hors connexion dans votre application Cordova"
documentationcenter: cordova
author: adrianhall
manager: erikre
editor: 
services: app-service\mobile
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c8f8f0188bb2b35d0d010ac55dd915519c09c159


---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Activation de la synchronisation hors connexion pour votre application mobile Cordova
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

Ce didacticiel présente la fonctionnalité de synchronisation hors connexion d’Azure Mobile Apps pour Cordova. La synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile &mdash;pour afficher, ajouter ou modifier des données&mdash;, même lorsqu’il n’existe aucune connexion réseau. Les modifications sont stockées dans une base de données locale. Une fois l’appareil de nouveau en ligne, ces modifications sont synchronisées avec le service distant.

Ce didacticiel est basé sur la solution de démarrage rapide de Cordova pour les applications mobiles créées en suivant le didacticiel [Démarrage rapide Apache Cordova]. Dans ce didacticiel, vous allez mettre à jour la solution de démarrage rapide pour ajouter les fonctionnalités hors connexion d’Azure Mobile Apps. Vous allez également en savoir plus sur le code hors connexion spécifique dans l’application.

Pour plus d’informations sur la fonctionnalité de synchronisation hors connexion, consultez la rubrique [Synchronisation des données hors connexion dans Azure Mobile Apps]. Pour plus d’informations sur l’utilisation de l’API, consultez le fichier [LISEZ-MOI] dans le plug-in.

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Ajouter la synchronisation hors connexion à la solution de démarrage rapide
Le code de synchronisation hors connexion doit être ajouté à l’application. La synchronisation hors connexion requiert le plug-in cordova-sqlite-storage, qui est automatiquement ajouté à votre application lorsque le plug-in Azure Mobile Apps est inclus dans le projet. Le projet de démarrage rapide inclut ces deux plug-ins.

1. Dans l’Explorateur de solutions de Visual Studio, ouvrez le fichier index.js et remplacez le code suivant
   
        var client,            // Connection to the Azure Mobile App backend
          todoItemTable;      // Reference to a table endpoint on backend
   
    par ce code :
   
        var client,             // Connection to the Azure Mobile App backend
          todoItemTable, syncContext; // Reference to table and sync context
2. Ensuite, remplacez le code suivant :
   
        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');
   
    par ce code :
   
        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');
   
        // Note: Requires at least version 2.0.0-beta6 of the Azure Mobile Apps plugin
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');
   
        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              deleted: 'boolean',
              complete: 'boolean'
          }
        });
   
        // Get the sync context from the client
        syncContext = client.getSyncContext();
   
    Les ajouts de code précédents initialisent le magasin local et définissent une table locale correspondant aux valeurs de colonne utilisées dans votre serveur principal Azure. (Vous n’avez pas besoin d’inclure toutes les valeurs de colonne dans ce code.)
   
    Vous obtenez une référence au contexte de synchronisation en appelant **getSyncContext**. Le contexte de synchronisation permet de conserver les relations entre les tables grâce au suivi et à l’envoi des modifications dans toutes les tables qu’une application cliente modifie quand **push** est appelé.
3. Mettez à jour l’URL de l’application pour l’URL de votre application Mobile App.
4. Ensuite, remplacez ce code :
   
        todoItemTable = client.getTable('todoitem'); // todoitem is the table name
   
    par ce code :
   
        // todoItemTable = client.getTable('todoitem');
   
        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {
   
        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');
   
        syncContext.pushHandler = {
            onConflict: function (serverRecord, clientRecord, pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };
   
        // Call a function to perform the actual sync
        syncBackend();
   
        // Refresh the todoItems
        refreshDisplay();
   
        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);
   
    Le code précédent initialise le contexte de synchronisation, puis appelle getSyncTable (au lieu de getTable) pour obtenir une référence à la table locale.
   
    Ce code utilise la base de données locale pour toutes les opérations de table CRUD (Create, Read, Update et Delete - Créer, Lire, Mettre à jour et Supprimer).
   
    Cet exemple effectue une simple gestion des erreurs sur les conflits de synchronisation. Une application réelle gèrerait les différentes erreurs telles que les conditions du réseau, les conflits de serveur et autres. Pour plus d’exemples de code, consultez l’ [exemple de synchronisation hors connexion].
5. Ensuite, ajoutez cette fonction pour effectuer la synchronisation réelle.
   
        function syncBackend() {
   
          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed
   
          });
   
          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }
   
    Vous décidez du moment auquel envoyer les modifications au backend Mobile App en appelant **push** sur l’objet **syncContext** utilisé par le client. Par exemple, vous pouvez ajouter un appel à **syncBackend** à un gestionnaire d’événement de bouton dans l’application comme un nouveau bouton de synchronisation, ou ajouter l’appel à la fonction **addItemHandler** pour synchroniser chaque fois qu’un nouvel élément est ajouté.

## <a name="offline-sync-considerations"></a>Considérations relatives à la synchronisation hors connexion
Dans l’exemple, la méthode **push** de **syncContext** n’est appelée qu’au démarrage de l’application dans la fonction de rappel pour la connexion.  Dans une application réelle, vous pourriez également provoquer manuellement le déclenchement de la fonctionnalité de synchronisation lorsque l’état du réseau change.

Si une extraction est exécutée sur une table qui comporte des mises à jour locales en attente faisant l’objet d’un suivi en fonction du contexte, cette opération déclenche automatiquement un envoi préalable sur le contexte. Lors de l’actualisation, de l’ajout et de l’achèvement des éléments dans cet exemple, vous pouvez omettre l’appel explicite de **push** , puisqu’il peut être redondant (consultez d’abord le fichier [LISEZ-MOI] pour connaître le statut actuel de la fonctionnalité).

Dans le code fourni, tous les enregistrements de la table TodoItem distante sont interrogés, mais il est également possible de filtrer les enregistrements en transmettant un ID de requête et une requête à **push**. Pour plus d’informations, consultez la section *Synchronisation incrémentielle* dans [Synchronisation des données hors connexion dans Azure Mobile Apps].

## <a name="optional-disable-authentication"></a>(Facultatif) Désactiver l’authentification
Si vous n’avez pas déjà configuré l’authentification et que vous ne souhaitez pas le faire avant de tester la synchronisation hors connexion, commentez la fonction de rappel pour la connexion, mais pas le code à l’intérieur de la fonction de rappel.

Une fois que vous avez commenté les lignes de connexion, voici ce à quoi le code doit ressembler.

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

À présent, l’application se synchronise avec le backend Azure lorsque vous exécutez l’application, et non lorsque vous vous connectez.

## <a name="run-the-client-app"></a>Exécuter l’application cliente
Maintenant que la synchronisation hors connexion est activée, vous pouvez exécuter l’application cliente au moins une fois sur chaque plateforme pour remplir la base de données du magasin local. Ensuite, vous allez simuler un scénario hors connexion et modifier les données du magasin local pendant que l’application est hors connexion.

## <a name="optional-test-the-sync-behavior"></a>(Facultatif) Tester le comportement de synchronisation
Dans cette section, vous allez modifier le projet client pour simuler un scénario hors connexion à l’aide d’une URL d’application non valide pour votre backend. Quand vous ajoutez ou modifiez des éléments de données, ces modifications sont conservées dans le magasin local, mais ne sont synchronisées avec le magasin de données principal qu’une fois la connexion rétablie.

1. Dans l’Explorateur de solutions, ouvrez le fichier de projet index.js et changez l’URL de l’application pour pointer vers une URL non valide, comme suit :
   
        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');
2. Dans index.html, mettez à jour l’élément CSP `<meta>` avec la même URL non valide.
   
        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">
3. Créez et exécutez l’application cliente et notez qu’une exception est consignée dans la console lorsque l’application tente de se synchroniser avec le backend après la connexion. Tous les nouveaux éléments que vous ajoutez se trouvent uniquement dans le magasin local jusqu’à ce qu’ils puissent être transmis par push vers le backend mobile. L’application cliente se comporte comme si elle est connectée au backend et prend en charge toutes les opérations de création, lecture, mise à jour et suppression.
4. Fermez l’application et redémarrez-la pour vérifier que les nouveaux élément que vous avez créés sont conservés dans le magasin local.
5. (Facultatif) À l’aide de Visual Studio, affichez votre table Azure SQL Database pour constater que les données dans la base de données principale n’ont pas changé.
   
    Dans Visual Studio, ouvrez l' **Explorateur de serveurs**. Accédez à votre base de données dans **Azure**->**Bases de données SQL**. Cliquez avec le bouton droit sur votre base de données, puis sélectionnez **Ouvrir dans l’Explorateur d’objets SQL Server**. Maintenant, vous pouvez accéder à votre table de base de données SQL et à son contenu.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Facultatif) Tester la reconnexion à votre backend
Dans cette section, vous allez reconnecter l'application au backend mobile, ce qui simule le retour de l'application à un état en ligne. Lorsque vous vous connectez, les données sont synchronisées avec votre backend mobile.

1. Rouvrez le fichier index.js et corrigez l’URL de l’application pour pointer vers la bonne URL.
2. Rouvrez le fichier index.html et corrigez l’URL de l’application dans l’élément CSP `<meta>` .
3. Régénérez et exécutez l’application cliente. Après la connexion, l’application tente de se synchroniser avec le backend Mobile App. Vérifiez qu’aucune exception n’est consignée dans la console de débogage.
4. (Facultatif) Affichez les données mises à jour à l’aide de l’Explorateur d’objets SQL Server ou d’un outil REST tel que Fiddler. Notez que les données n’ont pas été synchronisées entre la base de données du serveur principal et le magasin local.
   
    Notez que les données ont été synchronisées entre la base de données et le magasin local et qu’elles contiennent les éléments que vous avez ajoutés pendant que votre application était déconnectée.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Synchronisation des données hors connexion dans Azure Mobile Apps]
* [Cloud Cover : synchronisation hors connexion dans Azure Mobile Services] \(remarque : le contexte de la vidéo est Mobile Services, mais la synchronisation hors connexion fonctionne de la même manière dans Azure Mobile Apps\)
* [Visual Studio Tools pour Apache Cordova]

## <a name="next-steps"></a>Étapes suivantes
* L’ [exemple de synchronisation hors connexion]
* Le fichier [LISEZ-MOI]

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Démarrage rapide Apache Cordova]: app-service-mobile-cordova-get-started.md
[LISEZ-MOI]: https://github.com/Azure/azure-mobile-apps-js-client#offline-data-sync-preview
[exemple de synchronisation hors connexion]: https://github.com/shrishrirang/azure-mobile-apps-quickstarts/tree/samples/client/cordova/ZUMOAPPNAME
[Synchronisation des données hors connexion dans Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Cloud Cover : synchronisation hors connexion dans Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[ajoutant l’authentification]: app-service-mobile-cordova-get-started-users.md
[authentification]: app-service-mobile-cordova-get-started-users.md
[Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio Tools pour Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Kit de développement logiciel (SDK) Apache Cordova]: app-service-mobile-cordova-how-to-use-client-library.md
[Kit de développement logiciel du serveur ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Kit de développement logiciel du serveur Node.js]: app-service-mobile-node-backend-how-to-use-server-sdk.md



<!--HONumber=Nov16_HO3-->


