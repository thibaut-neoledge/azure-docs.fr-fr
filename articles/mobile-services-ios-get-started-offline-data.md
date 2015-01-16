<properties urlDisplayName="Using Offline Data" pageTitle="Utilisation de la synchronisation des données hors connexion dans Mobile Services (iOS) | Centre de développement mobile" metaKeywords="" description="Découvrez comment utiliser Azure Mobile Services pour mettre en cache et synchroniser les données hors connexion dans votre application iOS" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using Offline data sync in Mobile Services" authors="krisragh" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Prise en main de la synchronisation des données hors connexion dans Mobile Services


[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]


Ce didacticiel traite de la fonctionnalité de synchronisation hors connexion de Mobile Services sur iOs, qui permet aux développeurs d'écrire des applications utilisables même lorsque l'utilisateur final n'a pas accès au réseau.

La synchronisation hors connexion possède plusieurs utilisations potentielles :

* Améliorer la réactivité de l'application en mettant en cache les données de serveur localement sur l'appareil
* Rendre les applications résistantes à une connectivité réseau intermittente
* Permettre aux utilisateurs finaux de créer et de modifier des données même en l'absence d'accès au réseau, prenant ainsi en charge des scénarios avec une connectivité faible ou nulle
* Synchroniser des données sur plusieurs appareils et détecter des conflits lorsque le même enregistrement est modifié par deux appareils

Ce didacticiel explique comment mettre à jour l'application à partir du didacticiel [Prise en main de Mobile Services] afin de prendre en charge les fonctionnalités hors connexion d'Azure Mobile Services. Ensuite, vous allez ajouter les données dans le cadre d'un scénario hors connexion déconnecté, synchroniser ces éléments dans la base de données en ligne, puis vous connecter au portail de gestion Azure pour afficher les modifications apportées aux données lors de l'exécution de l'application.

>[WACOM.NOTE] Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir jusqu'à 10&nbsp;services mobiles gratuits que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Version d'évaluation gratuite d'Azure</a>.

Ce didacticiel vise à mieux vous faire comprendre en quoi Mobile Services vous permet de stocker et de récupérer des données dans une application Windows Store à l'aide d'Azure. À cet effet, il vous guide tout au long des nombreuses étapes exécutées automatiquement dans le démarrage rapide Mobile Services. Si vous n'avez aucune expérience de Mobile Services, nous vous invitons à suivre d'abord le didacticiel [Prise en main de Mobile Services].

>[WACOM.NOTE] Vous pouvez ignorer cette section et télécharger une version du projet de prise en main disposant déjà d'une prise en charge hors connexion et de tous les éléments décrits dans cette rubrique.  Pour télécharger un projet pour lequel la prise en charge hors connexion est activée, consultez la rubrique [Prise en main de l'exemple iOS hors connexion].


Ce didacticiel vous familiarise avec ces étapes de base :

1. [Obtention de l'exemple d'application Quickstart]
2. [Téléchargement du Kit de développement logiciel (SDK) préliminaire et mise à jour de l'infrastructure]
3. [Configuration des données de base]
4. [Définition du modèle des données de base]
5. [Initialisation et utilisation de la table et du contexte de synchronisation]
6. [Test de l'application]

## <a name="get-app"></a>Obtention de l'exemple d'application Quickstart

Suivez les instructions de la section [Prise en main de Mobile Services] et téléchargez le projet de démarrage rapide.

## <a name="update-app"></a>Téléchargement du Kit de développement logiciel (SDK) préliminaire et mise à jour de l'infrastructure

1. Pour ajouter la prise en charge hors connexion à notre application, nous allons obtenir une version du Kit de développement logiciel (SDK) Mobile Services iOS qui prend en charge la synchronisation hors connexion. Disponible en tant que fonctionnalité préliminaire, elle ne figure pas encore dans le Kit de développement logiciel (SDK) téléchargeable officiellement. [Téléchargez le Kit de développement logiciel (SDK) préliminaire ici].

2. Supprimez ensuite la référence **WindowsAzureMobileServices.framework** existante du projet dans Xcode en la sélectionnant, en cliquant sur le menu **Modifier** et en sélectionnant ensuite " Déplacer vers la Corbeille " pour supprimer les fichiers.

      ![][update-framework-1]

3. Décompressez le contenu du nouveau Kit de développement logiciel (SDK) préliminaire et faites glisser le nouveau SDK **WindowsAzureMobileServices.framework** à la place de l'ancien. Assurez-vous que l'option " Copier les éléments dans le dossier du groupe de destination (si nécessaire) " est sélectionnée.

      ![][update-framework-2]


## <a name="setup-core-data"></a>Configuration des données de base

1. Le Kit de développement logiciel (SDK) Mobile Services iOS vous permet d'utiliser n'importe quel magasin permanent, à condition qu'il soit conforme au protocole **MSSyncContextDataSource**. Une source de données qui implémente ce protocole sur la base des [données de base] est incluse dans ce Kit de développement logiciel (SDK).

2. Étant donné que l'application utilise des données de base, accédez à **Cibles** --> **Générer les phases** puis, sous **Lier le binaire aux bibliothèques**, ajoutez **CoreData.framework**.

      ![][core-data-1]

      ![][core-data-2]

3. Nous ajoutons des données de base à un projet existant dans Xcode qui n'en assure pas encore la prise en charge. Par conséquent, nous devons ajouter du code réutilisable supplémentaire à différentes parties du projet. Ajoutez d'abord le code suivant dans **QSAppDelegate.h** :

        #import <UIKit/UIKit.h>  
        #import <CoreData/CoreData.h>  

        @interface QSAppDelegate : UIResponder <UIApplicationDelegate>  

        @property (strong, nonatomic) UIWindow *window;  

        @property (readonly, strong, nonatomic) NSManagedObjectContext *managedObjectContext;  
        @property (readonly, strong, nonatomic) NSManagedObjectModel *managedObjectModel;  
        @property (readonly, strong, nonatomic) NSPersistentStoreCoordinator *persistentStoreCoordinator;  

        - (void)saveContext;  
        - (NSURL *)applicationDocumentsDirectory;  

        @end

4. Remplacez ensuite le contenu de **QSAppDelegate.m** par le code suivant. Il s'agit pratiquement du même code que celui obtenu lorsque vous créez une application dans Xcode et activez la case à cocher " Utiliser les données de base ", si ce n'est que vous utilisez un type d'accès simultané à la file d'attente privée lors de l'initialisation de **_managedObjectContext**. Avec cette modification, vous êtes presque prêt à utiliser des données de base, mais vous n'en faites encore rien.

        #import "QSAppDelegate.h"

        @implementation QSAppDelegate

        @synthesize managedObjectContext = _managedObjectContext;
        @synthesize managedObjectModel = _managedObjectModel;
        @synthesize persistentStoreCoordinator = _persistentStoreCoordinator;

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            return YES;
        }

        - (void)saveContext
        {
            NSError *error = nil;
            NSManagedObjectContext *managedObjectContext = self.managedObjectContext;
            if (managedObjectContext != nil) {
                if ([managedObjectContext hasChanges] && ![managedObjectContext save:&error]) {
                    // Replace this implementation with code to handle the error appropriately.
                    // abort() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.
                    NSLog(@"Unresolved error %@, %@", error, [error userInfo]);
                    abort();
                }
            }
        }

        #pragma mark - Core Data stack

        // Returns the managed object context for the application.
        // If the context doesn't already exist, it is created and bound to the persistent store coordinator for the application.
        - (NSManagedObjectContext *)managedObjectContext
        {
            if (_managedObjectContext != nil) {
                return _managedObjectContext;
            }

            NSPersistentStoreCoordinator *coordinator = [self persistentStoreCoordinator];
            if (coordinator != nil) {
                _managedObjectContext = [[NSManagedObjectContext alloc] initWithConcurrencyType:NSPrivateQueueConcurrencyType];
                [_managedObjectContext setPersistentStoreCoordinator:coordinator];
            }
            return _managedObjectContext;
        }

        // Returns the managed object model for the application.
        // If the model doesn't already exist, it is created from the application's model.
        - (NSManagedObjectModel *)managedObjectModel
        {
            if (_managedObjectModel != nil) {
                return _managedObjectModel;
            }
            NSURL *modelURL = [[NSBundle mainBundle] URLForResource:@"QSTodoDataModel" withExtension:@"momd"];
            _managedObjectModel = [[NSManagedObjectModel alloc] initWithContentsOfURL:modelURL];
            return _managedObjectModel;
        }

        // Returns the persistent store coordinator for the application.
        // If the coordinator doesn't already exist, it is created and the application's store added to it.
        - (NSPersistentStoreCoordinator *)persistentStoreCoordinator
        {
            if (_persistentStoreCoordinator != nil) {
                return _persistentStoreCoordinator;
            }

            NSURL *storeURL = [[self applicationDocumentsDirectory] URLByAppendingPathComponent:@"qstodoitem.sqlite"];

            NSError *error = nil;
            _persistentStoreCoordinator = [[NSPersistentStoreCoordinator alloc] initWithManagedObjectModel:[self managedObjectModel]];
            if (![_persistentStoreCoordinator addPersistentStoreWithType:NSSQLiteStoreType configuration:nil URL:storeURL options:nil error:&error]) {
                /*
                 Replace this implementation with code to handle the error appropriately.

                 abort() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.

                 Typical reasons for an error here include:
                 * The persistent store is not accessible;
                 * The schema for the persistent store is incompatible with current managed object model.
                 Check the error message to determine what the actual problem was.

                 If the persistent store is not accessible, there is typically something wrong with the file path. Often, a file URL is pointing into the application's resources directory instead of a writeable directory.

                 If you encounter schema incompatibility errors during development, you can reduce their frequency by:
                 * Simply deleting the existing store:
                 [[NSFileManager defaultManager] removeItemAtURL:storeURL error:nil]

                 * Performing automatic lightweight migration by passing the following dictionary as the options parameter:
                 @{NSMigratePersistentStoresAutomaticallyOption:@YES, NSInferMappingModelAutomaticallyOption:@YES}

                 Lightweight migration will only work for a limited set of schema changes; consult "Core Data Model Versioning and Data Migration Programming Guide" for details.

                 */

                NSLog(@"Unresolved error %@, %@", error, [error userInfo]);
                abort();
            }

            return _persistentStoreCoordinator;
        }

        #pragma mark - Application's Documents directory

        // Returns the URL to the application's Documents directory.
        - (NSURL *)applicationDocumentsDirectory
        {
            return [[[NSFileManager defaultManager] URLsForDirectory:NSDocumentDirectory inDomains:NSUserDomainMask] lastObject];
        }

        @end

## <a name="defining-core-data"></a>Définition du modèle des données de base

1. La configuration de l'application avec les données de base se poursuit avec la définition du modèle de données. Nous ne commencerons pas à utiliser ce modèle tout de suite. Définissons tout d'abord le schéma ou modèle de données de base. Pour commencer, cliquez sur **Fichier** -> **Nouveau fichier** et sélectionnez **Modèle de données** dans la section **Données de base**. Lorsque vous êtes invité à saisir un nom de fichier, utilisez **QSTodoDataModel.xcdatamodeld**.

      ![][defining-core-data-main-screen]

2. Nous allons ensuite définir les entités (tables) réelles dont nous avons besoin. Nous allons créer trois tables (entités) à l'aide de l'éditeur de modèle de données de base. Pour plus d'informations, consultez l'[Aide de l'éditeur de modèle de données de base].

  * TodoItem : pour stocker les éléments proprement dits
  * MS_TableOperations : pour le suivi des éléments qui doivent être synchronisés avec le serveur (nécessaire pour la fonctionnalité hors connexion)
  * MS_TableOperationErrors : pour le suivi des erreurs qui se produisent lors de la synchronisation hors connexion (nécessaire pour la fonctionnalité hors connexion)

      ![][defining-core-data-model-editor]

3. Définissez les trois entités comme illustré ci-dessous. Enregistrez le modèle et générez le projet pour vous assurer que tout fonctionne correctement. Nous avons maintenant terminé la configuration de l'application pour qu'elle fonctionne avec les données de base. Cependant, l'application ne les utilise pas encore.

      ![][defining-core-data-todoitem-entity]

      ![][defining-core-data-tableoperations-entity]

      ![][defining-core-data-tableoperationerrors-entity]


    **TodoItem**

    | Attribut  |  Type   |
    |----------- |  ------ |
    | id         | Chaîne  |
    | complete   | Booléen |
    | text       | Chaîne  |
    | ms_version | Chaîne  |

    **MS_TableOperations**

    | Attribut  |    Type     |
    |----------- |   ------    |
    | id         | Entier 64  |
    | properties | Données binaires |
    | itemId     | Chaîne      |
    | table      | Chaîne      |

    **MS_TableOperationErrors**

    | Attribut  |    Type     |
    |----------- |   ------    |
    | id         | Chaîne      |
    | properties | Données binaires |

## <a name="setup-sync"></a> Initialisation et utilisation de la table et du contexte de synchronisation

1. Pour lancer la mise en cache des données hors connexion, remplaçons l'utilisation de **MSTable** par **MSSyncTable** afin d'accéder au service mobile. Contrairement à une propriété **MSTable** standard, une table de synchronisation est comparable à une table locale qui offre, en outre, la possibilité d'envoyer les modifications apportées en local à une table distante et à extraire ces modifications localement. Dans **QSTodoService.h**, supprimez la définition de la propriété **table** :

        @property (nonatomic, strong)   MSTable *table;

    Add a new line to define the **syncTable** property:

        @property (nonatomic, strong)   MSTable *syncTable;

2. Add the following import statement at the top of **QSTodoService.m**:

        #import "QSAppDelegate.h"

3. Dans **QSTodoService.m**, supprimez les deux lignes suivantes dans **init** :

        // Create an MSTable instance to allow us to work with the TodoItem table
        self.table = [_client tableWithName:@"TodoItem"];

    Instead, add these two new lines in its place:

        // Create an MSSyncTable instance to allow us to work with the TodoItem table
        self.syncTable = [self.client syncTableWithName:@"TodoItem"];

4. Toujours dans **QSTodoService.m**, nous allons ensuite initialiser le contexte **MSClient** avec l'implémentation du magasin de données basé sur les données de base ci-dessus. Le contexte est responsable du suivi des éléments qui ont été modifiés en local et de leur envoi vers le serveur lors du démarrage d'une opération d'envoi. Pour initialiser le contexte, nous avons besoin d'une source de données (implémentation **MSCoreDataStore** du protocole) et d'une implémentation **MSSyncContextDelegate** facultative. Insérez ces lignes juste au-dessus des deux lignes insérées ci-dessus.

        QSAppDelegate *delegate = (QSAppDelegate *)[[UIApplication sharedApplication] delegate];
        NSManagedObjectContext *context = delegate.managedObjectContext;
        MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];

5. Nous allons ensuite mettre à jour les opérations dans **QSTodoService.m** afin d'utiliser la table de synchronisation plutôt que la table standard. Remplacez tout d'abord **refreshDataOnSuccess** par l'implémentation suivante. Cela a pour effet de récupérer les données du service. Nous allons donc effectuer une mise à jour en vue d'utiliser une table de synchronisation, demander à cette dernière d'extraire uniquement les éléments qui répondent à nos critères et lancer le chargement des données à partir de la table de synchronisation locale dans la propriété **items** du service. Avec ce code,   **refreshDataOnSuccess** extrait les données de la table distante dans la table (de synchronisation) locale. En règle générale, il ne faut extraire qu'un sous-ensemble de la table afin de ne pas surcharger le client d'informations qui peuvent s'avérer inutiles.

    Pour cette opération et les autres plus bas, nous encapsulons les appels vers les blocs completion dans un appel **dispatch_async** vers le thread principal. Lorsque nous initialisons le contexte de synchronisation, aucun paramètre de rappel n'est transmis. L'infrastructure crée donc une file d'attente série par défaut qui distribue les résultats de toutes les opérations syncTable dans un thread d'arrière-plan. Lors de la modification des composants d'interface utilisateur, nous devons redistribuer le code au thread d'interface utilisateur.

          -(void) refreshDataOnSuccess:(QSCompletionBlock)completion
          {
              NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
              MSQuery *query = [self.syncTable queryWithPredicate:predicate];

              [query orderByAscending:@"text"];
              [query readWithCompletion:^(MSQueryResult *result, NSError *error) {
                  [self logErrorIfNotNil:error];

                  self.items = [result.items mutableCopy];

                  // Let the caller know that we finished
                  dispatch_async(dispatch_get_main_queue(), ^{
                      completion();
                  });
              }];
          }

6. Remplacez ensuite **addItem** dans **QSTodoService.m** comme suit. Avec cette modification, l'opération est placée en file d'attente, de telle sorte que les changements soient envoyés vers le service distant, et elle devient visible par tout le monde :

        -(void)addItem:(NSDictionary *)item completion:(QSCompletionWithIndexBlock)completion
        {
            // Insert the item into the TodoItem table and add to the items array on completion
            [self.syncTable insert:item completion:^(NSDictionary *result, NSError *error)
             {
                 [self logErrorIfNotNil:error];

                 NSUInteger index = [items count];
                 [(NSMutableArray *)items insertObject:result atIndex:index];

                 // Let the caller know that we finished
                 dispatch_async(dispatch_get_main_queue(), ^{
                     completion(index);
                 });
             }];
        }

7. Mettez à jour **completeItem** dans **QSTodoService.m** comme suit. Contrairement à ce que l'on observe dans **MSTable**, il n'y a pas d'éléments mis à jour dans le bloc completion de l'opération **update** pour **MSSyncTable**. Avec **MSTable**, le serveur modifie l'élément en cours de mise à jour ; cette modification est répercutée sur le client. Avec **MSSyncTable**, les éléments mis à jour ne sont pas modifiés et le bloc completion est dépourvu de paramètre.

        -(void) completeItem:(NSDictionary *)item completion:(QSCompletionWithIndexBlock)completion
        {
            // Cast the public items property to the mutable type (it was created as mutable)
            NSMutableArray *mutableItems = (NSMutableArray *) items;

            // Set the item to be complete (we need a mutable copy)
            NSMutableDictionary *mutable = [item mutableCopy];
            [mutable setObject:@YES forKey:@"complete"];

            // Replace the original in the items array
            NSUInteger index = [items indexOfObjectIdenticalTo:item];
            [mutableItems replaceObjectAtIndex:index withObject:item];

            // Update the item in the TodoItem table and remove from the items array on completion
            [self.syncTable update:mutable completion:^(NSError *error) {

                [self logErrorIfNotNil:error];

                NSUInteger index = [items indexOfObjectIdenticalTo:mutable];
                if (index != NSNotFound)
                {
                    [mutableItems removeObjectAtIndex:index];
                }

                // Let the caller know that we have finished
                dispatch_async(dispatch_get_main_queue(), ^{
                    completion(index);
                });

            }];
        }

8. Ajoutez la déclaration d'opération suivante de **syncData** à **QSTodoService.h** :

        - (void)syncData:(QSCompletionBlock)completion;

     Add the corresponding implementation of **syncData** to **QSTodoService.m**. We're adding this operation to update the sync table with remote changes.

          -(void)syncData:(QSCompletionBlock)completion
           {
              // Create a predicate that finds items where complete is false
              NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

              MSQuery *query = [self.syncTable queryWithPredicate:predicate];

              // Pulls data from the remote server into the local table. We're only
              // pulling the items which we want to display (complete == NO).
              [self.syncTable pullWithQuery:query completion:^(NSError *error) {
                  [self logErrorIfNotNil:error];
                  [self refreshDataOnSuccess:completion];
           }];
          }

9. Back in **QSTodoListViewController.m**, change the implementation of **refresh** to call **syncData** instead of **refreshDataOnSuccess**:

        -(void) refresh
        {
            [self.refreshControl beginRefreshing];
            [self.todoService syncData:^
             {
                  [self.refreshControl endRefreshing];
                  [self.tableView reloadData];
             }];
        }

10. À nouveau dans **QSTodoListViewController.m**, remplacez l'appel vers **[self refresh]** à la fin de l'opération **viewDidLoad** par le code suivant :

        // load the local data, but don't pull from server
        [self.todoService refreshDataOnSuccess:^
         {
             [self.refreshControl endRefreshing];
             [self.tableView reloadData];
         }];

11. Passons à présent au test réel de l'application hors connexion. Ajoutez quelques éléments à l'application, puis visitez le portail de gestion Azure et consultez l'onglet **Données** de votre application. Comme vous pouvez le constater, aucun élément n'a encore été ajouté.

12. Effectuez ensuite le geste d'actualisation sur l'application en la faisant glisser depuis le haut de l'écran. Visitez à nouveau le portail de gestion Azure et consultez l'onglet **Données**. Les données sont maintenant enregistrées dans le cloud. Vous pouvez également fermer l'application après avoir ajouté un élément (ou après l'avoir modifié, si la fonctionnalité de modification d'éléments est activée dans l'application). Une fois l'application relancée, elle se synchronise avec le serveur et enregistre les modifications.

13. Lorsque le client apporte des modifications aux éléments en local, celles-ci sont stockées dans le contexte de synchronisation en vue d'être envoyées au serveur. Une opération *push* envoie le suivi des modifications au serveur distant. Cependant, dans le cas présent, il n'y a pas d'appel Push vers le serveur. Toutefois, *avant l'exécution d'une extraction, toutes les opérations en attente sont généralement envoyées au serveur*. Un envoi se produit donc automatiquement pour éviter les conflits. C'est pourquoi il n'y a aucun appel explicite vers *push* dans cette application.

## <a name="test-app"></a>Test de l'application

Pour terminer, nous allons tester l'application en mode hors connexion. Ajoutez quelques éléments dans l'application. Accédez ensuite au portail et parcourez les données (ou utilisez un outil réseau tel que PostMan ou Fiddler pour interroger directement la table).

Vous constaterez que les éléments n'ont pas encore été ajoutés au service. Effectuez maintenant le geste d'actualisation dans l'application en la faisant glisser depuis le haut de l'écran. Vous constaterez que les données sont à présent enregistrées dans le cloud. Vous pouvez même fermer l'application après avoir ajouté des éléments. Lorsque vous relancez l'application, elle se synchronise avec le serveur et vos modifications sont enregistrées.

## Étapes suivantes

* [Gestion des conflits liés à la prise en charge hors connexion de Mobile Services]

<!-- URLs. -->

[Obtention de l'exemple d'application Quickstart]: #get-app
[Téléchargement du Kit de développement logiciel (SDK) préliminaire et mise à jour de l'infrastructure]: #update-app
[Configuration des données de base]: #setup-core-data
[Définition du modèle des données de base]: #defining-core-data
[Initialisation et utilisation de la table et du contexte de synchronisation]: #setup-sync
[Test de l'application]: #test-app

[core-data-1]: ./media/mobile-services-ios-get-started-offline-data/core-data-1.png
[core-data-2]: ./media/mobile-services-ios-get-started-offline-data/core-data-2.png
[core-data-3]: ./media/mobile-services-ios-get-started-offline-data/core-data-3.png
[defining-core-data-main-screen]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-main-screen.png
[defining-core-data-model-editor]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-model-editor.png
[defining-core-data-tableoperationerrors-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-todoitem-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-todoitem-entity.png
[update-framework-1]: ./media/mobile-services-ios-get-started-offline-data/update-framework-1.png
[update-framework-2]: ./media/mobile-services-ios-get-started-offline-data/update-framework-2.png




[Aide de l'éditeur de modèle de données de base]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Création d'une connexion de sortie]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Création d'une interface utilisateur]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Ajout d'un Segue entre les scènes d'une animation]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Ajout d'une scène à une animation]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html

[Données de base]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Téléchargez le Kit de développement logiciel (SDK) préliminaire ici]: http://aka.ms/Gc6fex
[Utilisation de la bibliothèque cliente Mobile Services pour iOS]: /fr-fr/documentation/articles/mobile-services-ios-how-to-use-client-library/
[Prise en main de l'exemple iOS hors connexion]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611


[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-ios-get-started/
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-ios-get-started-data/
[Gestion des conflits liés à la prise en charge hors connexion de Mobile Services]: /fr-fr/documentation/articles/mobile-services-ios-handling-conflicts-offline-data/
