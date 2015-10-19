<properties
	pageTitle="Gestion des conflits liés aux données hors connexion dans Mobile Services (iOS) | Centre de développement mobile"
	description="Apprenez à gérer les conflits à l'aide d'Azure Mobile Services pendant la synchronisation des données hors connexion dans votre application iOS"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="krisragh;donnam"/>


# Gestion des conflits liés à la synchronisation des données hors connexion dans Mobile Services

[AZURE.INCLUDE [mobile-services-selector-offline-conflicts](../../includes/mobile-services-selector-offline-conflicts.md)]

Cette rubrique vous explique comment synchroniser les données et gérer les conflits lors de l'utilisation des fonctionnalités hors connexion d'Azure Mobile Services. Ce didacticiel s’appuie sur le didacticiel [Prise en main des données hors connexion].

>[AZURE.NOTE]Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Version d'évaluation gratuite d'Azure</a>.


## Téléchargement de projet iOS

Pour ce didacticiel, vous devez télécharger [un projet Xcode mis à jour à partir de Github](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS). Nous avons utilisé le projet Xcode de la fin du didacticiel [Prise en main des données hors connexion] comme point de départ, puis l’avons mis à jour pour permettre la modification d’éléments. Nous avons également ajouté la prise en charge des classes et des méthodes afin de pouvoir ajouter un gestionnaire de conflit dans la section suivante.

À la fin de ce didacticiel, si vous exécutez cette application sur deux téléphones, modifiez le même élément sur ceux-ci localement, puis renvoyez les modifications au serveur, vous autorisez l’utilisateur de chaque téléphone à choisir la version à conserver : * conserver la version client (qui remplace la version sur le serveur), * conserver la version serveur (qui met à jour la table locale du client), ou * ne conserver aucune version (annule l’opération push et laisse l’opération en attente).

Maintenant, ajoutons le gestionnaire de conflits pour activer cette fonctionnalité.

## <a name="add-conflict-handling"></a>Ajout d'un gestionnaire de conflits au contrôleur d'affichage de la liste des tâches

1. Dans **QSTodoListViewController.m**, modifiez **viewDidLoad**. Remplacez l'appel à **defaultService** par un appel à **defaultServiceWithDelegate** :

        self.todoService = [QSTodoService defaultServiceWithDelegate:self];

2. Dans **QSTodoListViewController.h**, ajoutez **&lt;MSSyncContextDelegate&gt;** à la déclaration d'interface. Nous implémentons ainsi le protocole **MSSyncContextDelegate**.

        @interface QSTodoListViewController : UITableViewController<MSSyncContextDelegate, NSFetchedResultsControllerDelegate>

3. Ajoutez l'instruction d'importation suivante au début de **QSTodoListViewController.m** :

        #import "QSUIAlertViewWithBlock.h"

4. Pour finir, ajoutons les deux opérations suivantes à **QSTodoListViewController.m** pour utiliser cette classe d'assistance et demander à l'utilisateur de résoudre le conflit en utilisant l'une des trois méthodes disponibles.

        - (void)tableOperation:(MSTableOperation *)operation onComplete:(MSSyncItemBlock)completion
        {
            [self doOperation:operation complete:completion];
        }

        -(void)doOperation:(MSTableOperation *)operation complete:(MSSyncItemBlock)completion
        {
            [operation executeWithCompletion:^(NSDictionary *item, NSError *error) {

                NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];

                if (error.code == MSErrorPreconditionFailed) {
                    QSUIAlertViewWithBlock *alert = [[QSUIAlertViewWithBlock alloc] initWithCallback:^(NSInteger buttonIndex) {
                        if (buttonIndex == 1) { // Client
                            NSMutableDictionary *adjustedItem = [operation.item mutableCopy];

                            [adjustedItem setValue:[serverItem objectForKey:MSSystemColumnVersion] forKey:MSSystemColumnVersion];
                            operation.item = adjustedItem;

                            [self doOperation:operation complete:completion];
                            return;

                        } else if (buttonIndex == 2) { // Server
                            NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
                            completion(serverItem, nil);
                        } else { // Cancel
                            [operation cancelPush];
                            completion(nil, error);
                        }
                    }];

                    NSString *message = [NSString stringWithFormat:@"Client value: %@\nServer value: %@", operation.item[@"text"], serverItem[@"text"]];

                    [alert showAlertWithTitle:@"Server Conflict"
                                      message:message
                            cancelButtonTitle:@"Cancel"
                            otherButtonTitles:[NSArray arrayWithObjects:@"Use Client", @"Use Server", nil]];
                } else {
                    completion(item, error);
                }
            }];
        }

## <a name="test-app"></a>Test de l'application

Testons l'application qui présente des conflits. Modifiez le même élément dans deux instances différentes de l'application exécutées simultanément ou utilisez l'application et un client REST.

Effectuez le geste d'actualisation dans les instances de l'application en faisant un glisser depuis le haut de l'écran. Vous êtes alors invité à résoudre le conflit :

![][conflict-ui]

<!-- URLs. -->

[Update the App Project to Allow Editing]: #update-app
[Update Todo List View Controller]: #update-list-view
[Add Todo Item View Controller]: #add-view-controller
[Add Todo Item View Controller and Segue to Storyboard]: #add-segue
[Add Item Details to Todo Item View Controller]: #add-item-details
[Add Support for Saving Edits]: #saving-edits
[Conflict Handling Problem]: #conflict-handling-problem
[Update QSTodoService to Support Conflict Handling]: #service-add-conflict-handling
[Add UI Alert View Helper to Support Conflict Handling]: #add-alert-view
[Add Conflict Handler to Todo List View Controller]: #add-conflict-handling
[Test the App]: #test-app


[add-todo-item-view-controller-3]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-3.png
[add-todo-item-view-controller-4]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-4.png
[add-todo-item-view-controller-5]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-5.png
[add-todo-item-view-controller-6]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-6.png
[todo-list-view-controller-add-segue]: ./media/mobile-services-ios-handling-conflicts-offline-data/todo-list-view-controller-add-segue.png
[update-todo-list-view-controller-2]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-2.png
[conflict-handling-problem-1]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-handling-problem-1.png
[conflict-ui]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-ui.png


[Segmented Controls]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UISegmentedControl.html
[Core Data Model Editor Help]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Creating an Outlet Connection]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Build a User Interface]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Adding a Segue Between Scenes in a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Adding a Scene to a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html
[Core Data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Download the preview SDK here]: http://aka.ms/Gc6fex
[How to use the Mobile Services client library for iOS]: mobile-services-ios-how-to-use-client-library.md
[Getting Started Offline iOS Sample]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[Prise en main des données hors connexion]: mobile-services-ios-get-started-offline-data.md
[Get started with Mobile Services]: mobile-services-ios-get-started.md

<!---HONumber=Oct15_HO2-->