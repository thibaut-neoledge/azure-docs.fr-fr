<properties
	pageTitle="Création d'applications en temps réel avec Pusher (iOS) - Mobile Services"
	description="Découvrez comment utiliser Pusher pour envoyer des notifications à votre application Azure Media Services sur iOS."
	services="mobile-services"
	documentationCenter="ios"
	authors="lindydonna"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="donnam"/>


# Génération d'applications en temps réel avec Mobile Services et Pusher

Cette rubrique vous explique comment ajouter une fonctionnalité en temps réel à votre application basée sur Azure Mobile Services. Une fois cette opération terminée, vos données TodoList seront synchronisées en temps réel sur toutes les instances en cours d'exécution de votre application.

Le didacticiel [Notifications Push pour les utilisateurs][] vous montre comment utiliser des notifications Push pour informer les utilisateurs de la présence de nouveaux éléments dans la liste ToDo. Les notifications Push constituent une excellente façon de présenter des changements occasionnels. Cependant, une application a parfois besoin d'envoyer des notifications en temps réel fréquentes. L'API Pusher permet d'ajouter des notifications en temps réel à votre service mobile. Dans ce didacticiel, nous allons utiliser Pusher avec Mobile Services pour maintenir une liste ToDo synchronisée lorsque des modifications sont apportées dans une instance en cours d'exécution de l'application.

Pusher est un service basé sur le cloud qui, tout comme Mobile Services, facilite grandement la génération d'applications en temps réel. Vous pouvez utiliser Pusher pour générer rapidement des sondages en direct, des salles de chat, des jeux multijoueurs, des applications de collaboration, pour diffuser des données et du contenu en direct. Et ce ne sont que des exemples ! Pour plus d'informations, consultez la page [http://pusher.com](http://pusher.com).

Ce didacticiel vous présente les étapes de base permettant d'ajouter une collaboration en temps réel à l'application de liste ToDo :

1. [Création d'un compte Pusher][]
2. [Mise à jour de votre application][]
3. [Installation des scripts serveur][]
4. [Test de l'application][]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez effectuer le didacticiel [Prise en main de Mobile Services][].

## <a name="sign-up"></a>Création d'un compte Pusher

[AZURE.INCLUDE [pusher-sign-up](../../includes/pusher-sign-up.md)]

## <a name="update-app"></a>Mise à jour de votre application

Maintenant que votre compte Pusher est configuré, l'étape suivante consiste à modifier le code de l'application iOS pour la nouvelle fonctionnalité.

###Installation de la bibliothèque libPusher

La bibliothèque [libPusher][] vous permet d'accéder à Pusher à partir d'iOS.

1. Téléchargez la bibliothèque libPusher [ici][libPusherDownload].

2. Créez un groupe nommé _libPusher_ dans votre projet.

3. Dans Finder, décompressez le fichier. zip téléchargé, sélectionnez les dossiers **libPusher-combined.a** et **/headers** et faites-les glisser dans le groupe **libPusher** de votre projet.

4. Activez la case à cocher **Copy items in destination group's folder**, puis cliquez sur **Terminer**.

	![][add-files-to-group]

   Les fichiers libPusher seront copiés dans votre projet.

5. Dans la racine de projet de l'Explorateur de projets, cliquez sur **Build Phases**, puis sur **Add Build Phase** et sur **Add Copy Files**.

6. Faites glisser le fichier **libPusher-combined.a** depuis l'Explorateur de projets dans la nouvelle phase de génération.

7. Modifiez la **Destination** en **Frameworks** et cliquez sur **Copy only when installing**.

	![][add-build-phase]

8. Ajoutez les bibliothèques suivantes dans la zone **Link Binary With Libraries** :

	- libicucore.dylib
	- CFNetwork.framework
	- Security.framework
	- SystemConfiguration.framework

9. Enfin, dans **Build Settings**, localisez le paramètre de génération cible **Other Linker Flags** et ajoutez l'indicateur **-all\_load**.

	![][add-linker-flag]

	L'ensemble d'indicateurs **-all\_load** s'affiche pour la cible de génération Debug.

La bibliothèque est désormais installée et prête à l'emploi.

### Ajout de code à l'application

1. Dans Xcode, ouvrez le fichier **QSTodoService.h** et ajoutez les déclarations de méthode suivantes :

        // Allows retrieval of items by id
        - (NSUInteger) getItemIndex:(NSDictionary *)item;

        // To be called when items are added by other users
        - (NSUInteger) itemAdded:(NSDictionary *)item;

        // To be called when items are completed by other users
        - (NSUInteger) itemCompleted:(NSDictionary *)item;

2. Remplacez les déclarations existantes d'**addItem** et **completeItem** par ce qui suit :

		- (void) addItem:(NSDictionary *) item;
		- (void) completeItem: (NSDictionary *) item;

3. Dans **QSTodoService.m**, ajoutez le code suivant pour implémenter les nouvelles méthodes :

        // Allows retrieval of items by id
		- (NSUInteger) getItemIndex:(NSDictionary *)item
		{
		    NSInteger itemId = [[item objectForKey: @"id"] integerValue];

		    return [items indexOfObjectPassingTest:^BOOL(id currItem, NSUInteger idx, BOOL *stop)
                 {
                     return ([[currItem objectForKey: @"id"] integerValue] == itemId);
                 }];
		}

        // To be called when items are added by other users
        -(NSUInteger) itemAdded:(NSDictionary *)item
		{
		    NSUInteger index = [self getItemIndex:item];

		    // Only complete action if item not already in list
		    if(index == NSNotFound)
		    {
        		NSUInteger newIndex = [items count];
		        [(NSMutableArray *)items insertObject:item atIndex:newIndex];
		        return newIndex;
		    }
		    else
        		return -1;
		}

        // To be called when items are completed by other users
		- (NSUInteger) itemCompleted:(NSDictionary *)item
		{
		    NSUInteger index = [self getItemIndex:item];

		    // Only complete action if item exists in items list
		    if(index != NSNotFound)
		    {
		        NSMutableArray *mutableItems = (NSMutableArray *) items;
		        [mutableItems removeObjectAtIndex:index];
		    }
		    return index;
		}

	QSTodoService vous permet désormais de trouver des éléments par **id**, ainsi que d'ajouter et de terminer des éléments localement sans envoyer des requêtes explicites au service distant.

4. Remplacez les méthodes existantes **addItem** et **completeItem** par le code ci-dessous :

		-(void) addItem:(NSDictionary *)item
		{
		    // Insert the item into the TodoItem table and add to the items array on completion
		    [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
        		[self logErrorIfNotNil:error];
		    }];
		}

		-(void) completeItem:(NSDictionary *)item
		{
		    // Set the item to be complete (we need a mutable copy)
		    NSMutableDictionary *mutable = [item mutableCopy];
		    [mutable setObject:@(YES) forKey:@"complete"];

		    // Update the item in the TodoItem table and remove from the items array on completion
		    [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {
		        [self logErrorIfNotNil:error];
		    }];
		}


	Les éléments sont à présent ajoutés et terminés, de même que les mises à jour de l'interface utilisateur, lorsque Pusher envoie des événements et non plus lorsque la table de données est mise à jour.

5. Dans le fichier **QSTodoListViewController.h**, ajoutez les instructions import suivantes :

		#import "PTPusherDelegate.h"
		#import "PTPusher.h"
		#import "PTPusherEvent.h"
		#import "PTPusherChannel.h"

6. Modifiez la déclaration d'interface et ajoutez **PTPusherDelegate** pour obtenir le résultat suivant :

		@interface QSTodoListViewController : UITableViewController<UITextFieldDelegate, PTPusherDelegate>

7. Ajoutez la nouvelle propriété suivante :

		@property (nonatomic, strong) PTPusher *pusher;

8. Ajoutez le code suivant, qui déclare une nouvelle méthode :

		// Sets up the Pusher client
		- (void) setupPusher;

9. Dans **QSTodoListViewController.m**, ajoutez la ligne suivante sous les autres lignes **@synthesise** pour implémenter la nouvelle propriété :

		@synthesize pusher = _pusher;

10. À présent, ajoutez le code suivant pour implémenter la nouvelle méthode :

		// Sets up the Pusher client
		- (void) setupPusher {

			// Create a Pusher client, using your Pusher app key as the credential
		    // TODO: Move Pusher app key to configuration file
		    self.pusher = [PTPusher pusherWithKey:@"**your_app_key**" delegate:self encrypted:NO];
		    self.pusher.reconnectAutomatically = YES;

    		// Subscribe to the 'todo-updates' channel
		    PTPusherChannel *todoChannel = [self.pusher subscribeToChannelNamed:@"todo-updates"];

    		// Bind to the 'todo-added' event
		    [todoChannel bindToEventNamed:@"todo-added" handleWithBlock:^(PTPusherEvent *channelEvent) {

		    	// Add item to the todo list
        		NSUInteger index = [self.todoService itemAdded:channelEvent.data];

        		// If the item was not already in the list, add the item to the UI
		        if(index != -1)
        		{
		            NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
        		    [self.tableView insertRowsAtIndexPaths:@[ indexPath ]
                                  withRowAnimation:UITableViewRowAnimationTop];
		        }
		    }];

    		// Bind to the 'todo-completed' event
		    [todoChannel bindToEventNamed:@"todo-completed" handleWithBlock:^(PTPusherEvent *channelEvent) {

		    	// Update the item to be completed
		        NSUInteger index = [self.todoService itemCompleted:channelEvent.data];

        		// As long as the item did exit in the list, update the UI
		        if(index != NSNotFound)
		        {
		            NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
        		    [self.tableView deleteRowsAtIndexPaths:@[ indexPath ]
                                  withRowAnimation:UITableViewRowAnimationTop];
		        }
		    }];
		}

11. Remplacez l'espace réservé `**your_app_key**` par la valeur app\_key que vous avez précédemment copiée dans la boîte de dialogue Informations de connexion.

12. Remplacez la méthode **onAdd** par le code suivant :

		- (IBAction)onAdd:(id)sender
		{
		    if (itemText.text.length  == 0) {
        		return;
		    }

		    NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) };
		    [self.todoService addItem:item];

		    itemText.text = @"";
		}

13. Recherchez la méthode (void)viewDidLoad dans le fichier **QSTodoListViewController.m** et ajoutez un appel à la méthode **setupPusher**. Les premières lignes doivent se présenter comme suit :

		- (void)viewDidLoad
		{
		    [super viewDidLoad];
		    [self setupPusher];

14. À la fin de la méthode **tableView:commitEditingStyle:forRowAtIndexPath**, remplacez l'appel en **completeItem** grâce au code suivant :

		// Ask the todoService to set the item's complete value to YES
	    [self.todoService completeItem:item];

L'application peut désormais recevoir des événements de Pusher et mettre à jour la liste ToDo locale en fonction.



##<a name="install-scripts"></a>Installation des scripts serveur



Il ne reste plus qu'à configurer vos scripts serveur. Nous allons insérer un script pour les insertions ou mises à jour d'un élément dans la table TodoList.



1. Connectez-vous au [portail de gestion Azure], cliquez sur **Mobile Services**, puis sur le service mobile.


2. Dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

	![][1]



3. Dans **TodoItem**, cliquez sur l'onglet **Script** et sélectionnez **Insérer**.


	![][2]



	La fonction appelée lors d'une insertion dans la table **TodoItem** s'affiche.


4. Remplacez la fonction d'insertion par le code suivant :


		var Pusher = require('pusher');

		function insert(item, user, request) {

			request.execute({
	    		success: function() {
			        // After the record has been inserted, trigger immediately to the client
	    		    request.respond();

			        // Publish event for all other active clients
			        publishItemCreatedEvent(item);
			    }
			});

			function publishItemCreatedEvent(item) {

				// Ideally these settings would be taken from config
			    var pusher = new Pusher({
				  appId: '**your_app_id**',
				  key: '**your_app_key**',
				  secret: '**your_app_secret**'
				});

				// Publish event on Pusher channel
			    pusher.trigger( 'todo-updates', 'todo-added', item );
			}
		}



5. Remplacez les espaces réservés du script ci-dessus par les valeurs que vous avez copiées précédemment dans la boîte de dialogue Informations de connexion :

	- **`**your_app_id**`** : valeur app&#95;id
	- **`**your_app_key**`** : valeur app&#95;key
	- **`**your_app_key_secret**`** : valeur app&#95;key&#95;secret


6. Cliquez sur le bouton **Enregistrer**. Vous avez à présent configuré un script pour publier un événement dans Pusher à chaque fois qu'un nouvel élément est inséré dans la table **TodoItem**.


7. Sélectionnez **Mettre à jour** dans la liste déroulante **Opération**.


8. Remplacez la fonction de mise à jour par le code suivant :

		var Pusher = require('pusher');

		function update(item, user, request) {

			request.execute({
	    		success: function() {
			        // After the record has been updated, trigger immediately to the client
	    		    request.respond();

			        // Publish event for all other active clients
			        publishItemUpdatedEvent(item);
			    }
			});

			function publishItemUpdatedEvent(item) {

				// Ideally these settings would be taken from config
			    var pusher = new Pusher({
				  appId: '**your_app_id**',
				  key: '**your_app_key**',
				  secret: '**your_app_secret**'
				});

				// Publish event on Pusher channel
			    pusher.trigger( 'todo-updates', 'todo-completed', item );

			}
		}



9. Répétez l'étape 5 pour que ce script remplace les espaces réservés.


10. Cliquez sur le bouton **Enregistrer**. Vous avez à présent configuré un script pour publier un événement dans Pusher à chaque fois qu'un nouvel élément est mis à jour.



##<a name="test-app"></a>Test de l’application



Vous devrez exécuter deux instances pour tester l'application. Vous pouvez en exécuter une sur un appareil iOS et l'autre sur le simulateur iOS.

1. Connectez votre appareil iOS, appuyez sur le bouton **Exécuter** (ou sur les touches Cmd+R) pour lancer l'application sur l'appareil, puis arrêtez le débogage.

	L'application est à présent installée sur votre appareil.

2. Exécutez l'application sur le simulateur iOS, puis démarrez-la sur votre appareil iOS.

	Deux instances de l'application sont à présent en cours d'exécution.

3. Ajoutez un nouvel élément ToDo dans l'une des instances de l'application.

	Vérifiez que l'élément ajouté s'affiche dans l'autre instance.

4. Cochez un élément ToDo pour le marquer comme terminé dans une instance de l'application.

	Vérifiez que l'élément disparaît de l'autre instance.

Félicitations, vous avez bien configuré votre application de service mobile pour la synchronisation en temps réel sur tous les clients.

## <a name="nextsteps"> </a>Étapes suivantes

Maintenant que vous avez pu constater combien il était simple d'utiliser le service Pusher avec Mobile Services, consultez les liens ci-dessous pour en savoir plus sur Pusher.

-   Documentation d'API Pusher : <http://pusher.com/docs>
-   Didacticiels Pusher : <http://pusher.com/tutorials>

Pour en savoir plus sur l'enregistrement et l'utilisation de scripts serveur, consultez la page [Référence de script serveur Mobile Services].

<!-- Anchors. -->
[Création d'un compte Pusher]: #sign-up
[Mise à jour de votre application]: #update-app
[Installation des scripts serveur]: #install-scripts
[Test de l'application]: #test-app

<!-- Images. -->
[1]: ./media/mobile-services-ios-build-realtime-apps-pusher/mobile-portal-data-tables.png
[2]: ./media/mobile-services-ios-build-realtime-apps-pusher/mobile-insert-script-push2.png

[add-files-to-group]: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-files-to-group.png
[add-build-phase]: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-build-phase.png
[add-linker-flag]: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-linker-flag.png

<!-- URLs. -->
[Notifications Push pour les utilisateurs]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Prise en main de Mobile Services]: /develop/mobile/tutorials/get-started
[libPusher]: http://go.microsoft.com/fwlink/p?LinkId=276999
[libPusherDownload]: http://go.microsoft.com/fwlink/p/?LinkId=276998


[portail de gestion Azure]: https://manage.windowsazure.com/

[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/p/?LinkId=262293

<!---HONumber=September15_HO1-->