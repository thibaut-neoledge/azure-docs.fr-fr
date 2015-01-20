<properties urlDisplayName="Handle Conflicts with Offline Data" pageTitle="Gestion des conflits liés aux données hors connexion dans Mobile Services (iOS) | Centre de développement mobile" metaKeywords="" description="Découvrez comment gérer les conflits à l'aide d'Azure Mobile Services lors de la synchronisation des données hors connexion dans votre application iOS." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling conflicts with offline data in Mobile Services" authors="krisragh" manager="dwrede"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="dotnet" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />


# Gestion des conflits liés à la synchronisation des données hors connexion dans Mobile Services

<div class="dev-center-tutorial-selector sublanding">
<a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data" title="Windows Store C#">Windows Store C#</a>
<a href="/fr-fr/documentation/articles/mobile-services-windows-phone-handling-conflicts-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/fr-fr/documentation/articles/mobile-services-ios-handling-conflicts-offline-data" title="iOS" class="current">iOS</a>
</div>

Cette rubrique vous explique comment synchroniser les données et gérer les conflits lors de l'utilisation des fonctionnalités hors connexion d'Azure Mobile Services. Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données hors connexion]. Avant de commencer, vous devez suivre le didacticiel [Prise en main des données hors connexion].

>[WACOM.NOTE] Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">version d'évaluation gratuite Azure</a>.

## Didacticiel Prise en main des données hors connexion

Suivez les instructions du didacticiel [Prise en main des données hors connexion] et terminez ce projet. Le projet achevé de ce didacticiel va nous servir de point de départ pour le présent didacticiel.

## Mise à jour du projet d'application de manière à autoriser les modifications

Mettons à jour le projet achevé du didacticiel [Prise en main des données hors connexion] de manière à autoriser la modification des éléments. Actuellement, si vous exécutez cette même application sur deux téléphones, modifiez localement le même élément sur les deux téléphones et effectuez une transmission de type push pour envoyer les modifications au serveur, l'opération échoue avec un conflit.

Les fonctions de synchronisation hors connexion du Kit de développement logiciel (SDK) vous permettent de gérer ces conflits par code et de décider dynamiquement ce que vous voulez faire des éléments en conflit. Nous pouvons expérimenter cette fonctionnalité en modifiant le projet de démarrage rapide.

### Mise à jour du contrôleur d'affichage de la liste des tâches

1. Mettons à jour le storyboard de l'iPhone. Si vous utilisez un iPad, suivez la même procédure pour le storyboard de l'iPad.

2. Sélectionnez **MainStoryboard_iPhone.storyboard** dans l'Explorateur de projets Xcode, puis sélectionnez **Contrôleur d'affichage de la liste des tâches**. Dans le menu supérieur, cliquez sur **Éditeur -> Incorporer dans -> Contrôleur de navigation**

      ![][update-todo-list-view-controller-1]

3. Ensuite, dans le **Contrôleur d'affichage de la liste des tâches**, sélectionnez la cellule d'affichage de table et définissez Mode accessoire sur **Indicateur de divulgation**. L'indicateur de divulgation indique aux utilisateurs que s'ils appuient sur le contrôleur d'affichage de table associé, une nouvelle vue s'affichera. L'indicateur de divulgation ne produit aucun événement.

      ![][update-todo-list-view-controller-2]

4. Dans **TodoListViewController.m**, supprimez les opérations suivantes avec leurs contenus. Nous n'en avons pas besoin :

        -(NSString *)tableView:(UITableView *)tableView titleForDeleteConfirmationButtonForRowAtIndexPath:(NSIndexPath *)indexPath

        -(UITableViewCellEditingStyle)tableView:(UITableView *)tableView editingStyleForRowAtIndexPath:(NSIndexPath *)indexPath

        -(void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle
         forRowAtIndexPath:(NSIndexPath *)indexPath

### Ajout d'un contrôleur d'affichage des tâches

1. Ajoutez une nouvelle classe Objective-C appelée **QSTodoItemViewController**, dérivée de **UIViewController**, à votre projet :

      ![][add-todo-item-view-controller-1]

      ![][add-todo-item-view-controller-2]

2. Dans **QSTodoItemViewController.h**, ajoutez une propriété pour contenir l'élément à modifier :

        @property (nonatomic, weak) NSMutableDictionary *item;

3. Dans **QSTodoItemViewController.m**, ajoutez deux propriété privées pour les deux champs de la tâche que nous allons modifier : l'état d'achèvement et le texte de la tâche :

        @interface QSTodoItemViewController ()

        @property (nonatomic, strong) IBOutlet UITextField *itemText;
        @property (nonatomic, strong) IBOutlet UISegmentedControl *itemComplete;

        @end

4. Dans **QSTodoItemViewController.m**, mettez à jour l'implémentation du stub de **viewDidLoad** avec le code suivant :

        - (void)viewDidLoad
        {
            [super viewDidLoad];

            UINavigationItem *nav = [self navigationItem];
            [nav setTitle:@"Todo Item"];

            NSDictionary *theItem = [self item];
            [self.itemText setText:[theItem objectForKey:@"text"]];

            BOOL isComplete = [[theItem objectForKey:@"complete"] boolValue];
            [self.itemComplete setSelectedSegmentIndex:(isComplete ? 0 : 1)];

            [self.itemComplete addTarget:self
                                  action:@selector(completedValueChanged:)
                        forControlEvents:UIControlEventValueChanged];
        }

5. Dans **QSTodoItemViewController.m**, ajoutez quatre méthodes supplémentaires pour gérer plusieurs événements :


        - (BOOL)textFieldShouldEndEditing:(UITextField *)textField {
            [textField resignFirstResponder];
            return YES;
        }

        - (BOOL)textFieldShouldReturn:(UITextField *)textField {
            [textField resignFirstResponder];
            return YES;
        }


        - (void)completedValueChanged:(id)sender {
            [[self view] endEditing:YES];
        }

        - (void)viewWillDisappear:(BOOL)animated {
            [self.item setValue:[self.itemText text] forKey:@"text"];
            [self.item setValue:[NSNumber numberWithBool:self.itemComplete.selectedSegmentIndex == 0] forKey:@"complete"];
        }

### Ajout d'un contrôleur d'affichage des tâches et d'un segue au storyboard

1. Revenez au fichier **MainStoryboard_iPhone.storyboard** à l'aide de l'Explorateur de projets.

2. Ajoutez un nouveau contrôleur d'affichage pour la tâche au storyboard, à droite du **Contrôleur d'affichage de la liste des tâches** existant. Définissez la classe personnalisée de ce nouveau contrôleur d'affichage sur **QSTodoItemViewController**. Pour en savoir plus, consultez la page [Ajout d'une scène à un storyboard].

3. Ajoutez un segue push du **Contrôleur d'affichage de la liste des tâches** dans le **Contrôleur d'affichage des tâches** et nommez ce segue **detailSegue**. Pour en savoir plus, consultez la page [Ajout d'un segue entre des scènes dans un storyboard]. Ne créez pas ce segue à partir d'une cellule ou d'un bouton dans le contrôleur d'affichage d'origine, mais procédez comme suit : en maintenant la touche Ctrl enfoncée, faites glisser l'icône du contrôleur d'affichage sous le **Contrôleur d'affichage de la liste des tâches** dans l'interface du storyboard vers le **Contrôleur d'affichage des tâches**. Si vous exécutez accidentellement un segue à partir d'une cellule, vous déclencherez le segue à deux reprises lorsque vous exécuterez l'application, ce qui provoquera l'erreur suivante :

        Une animation Push imbriquée peut entraîner un endommagement de la barre de navigation

4. Ajoutez un champ texte pour le texte de l'élément et un contrôle segmenté pour l'état d'achèvement au nouveau **Contrôleur d'affichage des tâches**, avec, également, des étiquettes. Dans le contrôle segmenté, définissez le titre de **Segment 0** sur **Oui** et le titre de **Segment 1** sur **Non**. Connectez ces nouveaux champs aux outlets dans le code. Pour en savoir plus, consultez les pages [Création d'une interface utilisateur] et [Contrôles segmentés].

      ![][add-todo-item-view-controller-3]

5. Connectez ces nouveaux champs aux outlets correspondants que vous avez déjà ajoutés à **QSTodoItemViewController.m**. Connectez le champ texte de l'élément à l'outlet **itemText** et le contrôle segmenté de l'état d'achèvement à l'outlet **itemComplete**. Pour en savoir plus, consultez la page [Création d'une connexion d'outlet].

6. Définissez le délégué du champ texte sur le contrôleur d'affichage. Ceci permet d'abandonner le champ texte lorsque vous modifiez un élément et appuyez sur ENTRÉE. En maintenant la touche Ctrl enfoncée, faites glisser le champ texte vers l'icône du contrôleur d'affichage sous le **Contrôleur d'affichage des tâches** dans l'interface du storyboard et sélectionnez l'outlet délégué ; vous indiquez ainsi au storyboard que le délégué de ce champ texte est ce contrôleur d'affichage.

7. Vérifions que l'application fonctionne avec toutes les modifications que vous avez apportées jusqu'ici. Exécutez à présent l'application dans le simulateur. Ajoutez des éléments à la liste des tâches, puis cliquez sur ces éléments. Le contrôleur d'affichage des éléments (actuellement vide) apparaît.

      ![][add-todo-item-view-controller-4]

      ![][add-todo-item-view-controller-5]

### Ajout des détails des éléments au contrôleur d'affichage des tâches

1. Nous allons faire référence à **QSTodoItemViewController** à partir de **QSTodoListViewController.m**. Par conséquent, dans **QSTodoListViewController.m**, ajoutez une ligne pour importer **QSTodoItemViewController.h**.

        #import "QSTodoItemViewController.h"

2. Ajoutez deux nouvelles propriétés à l'interface **QSTodoListViewController** dans **QSTodoListViewController.m** afin de stocker l'élément en cours de modification :

        @property (nonatomic)           NSInteger       editedItemIndex;
        @property (strong, nonatomic)   NSMutableDictionary *editedItem;

3. Implémentez **tableView:didSelectRowAtIndexPath:** dans **QSTodoListViewController.m** pour enregistrer l'élément en cours de modification, puis appelez le segue afin d'afficher la vue détaillée.

          - (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
              self.editedItemIndex = [indexPath row];
              self.editedItem = [[self.todoService.items objectAtIndex:[indexPath row]] mutableCopy];

              [self performSegueWithIdentifier:@"detailSegue" sender:self];
          }

4. Implémentez **prepareForSegue:sender:** dans **QSTodoListViewController.m** pour transmettre l'élément au **Contrôleur d'affichage des tâches**.

        - (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
            if ([[segue identifier] isEqualToString:@"detailSegue"]) {
                QSTodoItemViewController *ivc = (QSTodoItemViewController *)[segue destinationViewController];
                ivc.item = self.editedItem;
            }
        }

5. Vérifions que l'application fonctionne avec toutes les modifications que vous avez apportées jusqu'ici. Exécutez à présent l'application dans le simulateur. Ajoutez des éléments à la liste des tâches, puis cliquez sur ces éléments. Comme vous pouvez le constater, le contrôleur d'affichage n'est plus vide : il affiche les détails des tâches.

      ![][add-todo-item-view-controller-6]

### Ajout de la prise en charge de la sauvegarde des modifications

1. Lorsque vous cliquez sur le bouton " Retour " en mode Navigation, les modifications sont perdues. Nous avons envoyé les données vers la vue détaillée, mais les données ne sont pas renvoyées vers la vue principale. Comme nous avons déjà transmis un pointeur vers une copie de l'élément, nous pouvons nous servir de ce pointeur pour récupérer la liste des mises à jour effectuées sur l'élément et le mettre à jour sur le serveur. Pour commencer, mettez à jour la classe wrapper du serveur de **QSTodoService** dans **QSTodoService.m** en supprimant l'opération **completeItem** et en ajoutant une nouvelle opération **updateItem**. Ceci tient au fait que **completeItem** marque seulement les éléments comme terminés, tandis que **updateItem** les met à jour.

        - (void)updateItem:(NSDictionary *)item atIndex:(NSInteger)index completion:(QSCompletionWithIndexBlock)completion {
            // Cast the public items property to the mutable type (it was created as mutable)
            NSMutableArray *mutableItems = (NSMutableArray *) items;

            // Replace the original in the items array
            [mutableItems replaceObjectAtIndex:index withObject:item];

            // Update the item in the TodoItem table and remove from the items array when we mark an item as complete
            [self.syncTable update:item completion:^(NSError *error) {
                [self logErrorIfNotNil:error];

                NSInteger index = -1;
                if (!error) {
                    BOOL isComplete = [[item objectForKey:@"complete"] boolValue];
                    NSString *remoteId = [item objectForKey:@"id"];
                    index = [items indexOfObjectPassingTest:^BOOL(id obj, NSUInteger idx, BOOL *stop) {
                        return [remoteId isEqualToString:[obj objectForKey:@"id"]];
                    }];

                    if (index != NSNotFound && isComplete)
                    {
                        [mutableItems removeObjectAtIndex:index];
                    }
                }

                // Let the caller know that we have finished
                dispatch_async(dispatch_get_main_queue(), ^{
                    completion(index);
                });
            }];
        }

2. Supprimez la déclaration pour **completeItem** de **QSTodoService.h** et ajoutez cette déclaration pour **updateItem** :

        - (void)updateItem:(NSDictionary *)item atIndex:(NSInteger)index completion:(QSCompletionWithIndexBlock)completion;

3. Dans **QSTodoListViewController.m**, ajoutez l'opération **viewWillAppear** pour appeler la méthode update chaque fois que la vue principale est affichée lorsque vous revenez du contrôleur d'affichage des détails.

        - (void)viewWillAppear:(BOOL)animated {
            if (self.editedItem && self.editedItemIndex >= 0) {
                // Returning from the details view controller
                NSDictionary *item = [self.todoService.items objectAtIndex:self.editedItemIndex];

                BOOL changed = ![item isEqualToDictionary:self.editedItem];
                if (changed) {
                    [self.tableView setUserInteractionEnabled:NO];

                    // Change the appearance to look greyed out until we remove the item
                    NSIndexPath *indexPath = [NSIndexPath indexPathForRow:self.editedItemIndex inSection:0];

                    UITableViewCell *cell = [self.tableView cellForRowAtIndexPath:indexPath];
                    cell.textLabel.textColor = [UIColor grayColor];

                    // Ask the todoService to update the item, and remove the row if it's been completed
                    [self.todoService updateItem:self.editedItem atIndex:self.editedItemIndex completion:^(NSUInteger index) {
                        if ([[self.editedItem objectForKey:@"complete"] boolValue]) {
                            // Remove the row from the UITableView
                            [self.tableView deleteRowsAtIndexPaths:@[ indexPath ]
                                                  withRowAnimation:UITableViewRowAnimationTop];
                        } else {
                            [self.tableView reloadRowsAtIndexPaths:[NSArray arrayWithObject:indexPath]
                                                  withRowAnimation:UITableViewRowAnimationAutomatic];
                        }

                        [self.tableView setUserInteractionEnabled:YES];

                        self.editedItem = nil;
                        self.editedItemIndex = -1;
                    }];
                } else {
                    self.editedItem = nil;
                    self.editedItemIndex = -1;
                }
            }
        }

4. Testons à présent l'application. Vérifions que l'application fonctionne avec toutes les modifications que vous avez apportées jusqu'ici. Exécutez à présent l'application dans le simulateur. Ajoutez des éléments à la liste des tâches, puis cliquez sur ces éléments. Essayez de modifier un élément et revenez en arrière. Vérifiez que la description de l'élément a été mise à jour dans la vue principale de l'application. Actualisez l'application en faisant un glisser vers le bas et vérifiez que la modification est reflétée dans le cloud.

### Problème de gestion de conflit

1. Examinons ce qui se passe lorsque deux clients différents essaient de modifier les mêmes données simultanément. L'exemple de liste ci-dessous contient un élément " Hello world 3 ". Vous allez le modifier de manière à afficher " Hello world 13 " sur un appareil et " Hello world 23 " sur un autre appareil.

      ![][conflict-handling-problem-1]

2. Lancez l'application à deux endroits : sur les deux appareils iOS ou dans le simulateur et sur un appareil iOS. Si vous ne disposez pas d'un appareil physique pour effectuer un test, lancez une instance dans le simulateur et, à l'aide d'un client REST, envoyez une requête PATCH au service mobile. L'URL de la requête PATCH reflète le nom du service mobile, le nom de la table des tâches et l'ID de la table des tâches que vous modifiez, tandis que l'en-tête x-zumo-application correspond à la clé de l'application :

        PATCH https://todolist.azure-mobile.net/tables/todoitem/D265929E-B17B-42D1-8FAB-D0ADF26486FA?__systemproperties=__version
        Content-Type: application/json
        x-zumo-application: shYOoDFdKhmzLEbnMQqPYrCLhwGOVA10

        {
            "id": "D265929E-B17B-42D1-8FAB-D0ADF26486FA",
            "text": "Hello world 23"
        }

3. Actualisez à présent les éléments dans les deux instances de l'application. Une erreur est imprimée dans le journal de sortie dans Xcode :

        TodoList[1575:4a2f] ERROR Error Domain=com.Microsoft.WindowsAzureMobileServices.ErrorDomain Code=-1170 "Not all operations completed successfully" UserInfo=0x8dd6310 {com.Microsoft.WindowsAzureMobileServices.ErrorPushResultKey=(
            "The server's version did not match the passed version"
        ), NSLocalizedDescription=Not all operations completed successfully}

  La raison est la suivante : sur le bloc completion, dans l'appel à **pullWithQuery:completion:**, le paramètre d'erreur est non-nil, ce qui provoque l'impression de l'erreur dans la sortie via **NSLog**.

### Mise à jour de QSTodoService pour prendre en charge la gestion des conflits

1. Laissons l'utilisateur décider comment le conflit doit être géré en le traitant dans le client. À cet effet, implémentons le protocole **MSSyncContextDelegate**. Dans **QSTodoService.h** et **QSTodoService.m**, remplacez la déclaration de la méthode de fabrique **(QSTodoService *)defaultService;** par l'instruction ci-dessous, de manière à recevoir le délégué de contexte de synchronisation en tant que paramètre :

        + (QSTodoService *)defaultServiceWithDelegate:(id)delegate;

2. Dans **QSTodoService.m**, modifiez la ligne **init** comme indiqué ci-après, en recevant ici aussi le délégué de contexte de synchronisation en tant que paramètre :

        -(QSTodoService *)initWithDelegate:(id)syncDelegate

3. Dans **QSTodoService.m**, remplacez l'appel **init** dans **defaultServiceWithDelegate** par **initWithDelegate** :

        service = [[QSTodoService alloc] initWithDelegate:delegate];

4. De retour dans **QSTodoService.m**, modifiez l'initialisation de **self.client.syncContext** afin de transmettre **syncDelegate** au lieu de **nil** pour le délégué :

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:syncDelegate dataSource:store callback:nil];

### Ajout d'une assistance à l'affichage des alertes de l'interface utilisateur pour prendre en charge la gestion des conflits

1. En cas de conflit, autorisons l'utilisateur à choisir la version à conserver :
  * conserver la version client (qui écrase la version sur le serveur),
  * conserver la version serveur (qui met à jour la table locale client), ou
  * ne conserver aucune version (annule l'opération Push et laisse l'opération en suspens).

  Dans la mesure où une autre mise à jour peut avoir lieu pendant que nous affichons l'invite, nous allons continuer d'afficher les options jusqu'à ce que le serveur cesse de renvoyer une réponse d'échec. Dans notre code, utilisons une classe d'assistance qui affiche une vue des alertes et prend un délégué qui est appelé lorsque la vue des alertes est affichée. Définissons d'abord la classe d'assistance **QSUIAlertViewWithBlock**.

2. Ajoutez cette nouvelle classe, **QSUIAlertViewWithBlock**, à l'aide de Xcode, et remplacez **QSUIAlertViewWithBlock.h** par le contenu suivant :

        #import <Foundation/Foundation.h>

        typedef void (^QSUIAlertViewBlock) (NSInteger index);

        @interface QSUIAlertViewWithBlock : NSObject <UIAlertViewDelegate>

        - (id) initWithCallback:(QSUIAlertViewBlock)callback;
        - (void) showAlertWithTitle:(NSString *)title message:(NSString *)message cancelButtonTitle:(NSString *)cancelButtonTitle otherButtonTitles:(NSArray *)otherButtonTitles;

        @end

3. Ensuite, remplacez **QSUIAlertViewWithBlock.m** par le fichier suivant :

        #import "QSUIAlertViewWithBlock.h"
        #import <objc/runtime.h>

        @interface QSUIAlertViewWithBlock()

        @property (nonatomic, copy) QSUIAlertViewBlock callback;

        @end

        @implementation QSUIAlertViewWithBlock

        static const char *key;

        @synthesize callback = _callback;

        - (id) initWithCallback:(QSUIAlertViewBlock)callback
        {
            self = [super init];
            if (self) {
                _callback = [callback copy];
            }
            return self;
        }

        - (void) showAlertWithTitle:(NSString *)title message:(NSString *)message cancelButtonTitle:(NSString *)cancelButtonTitle otherButtonTitles:(NSArray *)otherButtonTitles {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title
                                                            message:message
                                                           delegate:self
                                                  cancelButtonTitle:cancelButtonTitle
                                                  otherButtonTitles:nil];

            if (otherButtonTitles) {
                for (NSString *buttonTitle in otherButtonTitles) {
                    [alert addButtonWithTitle:buttonTitle];
                }
            }

            [alert show];

            objc_setAssociatedObject(alert, &key, self, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
        }

        - (void) alertView:(UIAlertView *)alertView didDismissWithButtonIndex:(NSInteger)buttonIndex
        {
            if (self.callback) {
                self.callback(buttonIndex);
            }
        }

        @end

### Ajout d'un gestionnaire de conflits au contrôleur d'affichage de la liste des tâches

1. Dans **QSTodoListViewController.m**, remplacez l'appel à **defaultService** dans **viewDidLoad** par un appel à **defaultServiceWithDelegate**, comme indiqué ci-après :

        self.todoService = [QSTodoService defaultServiceWithDelegate:self];

2. Dans **QSTodoListViewController.h**, ajoutez **&lt;MSSyncContextDelegate&gt;** à la déclaration d'interface. Nous implémentons ainsi le protocole **MSSyncContextDelegate**.

        @interface QSTodoListViewController : UITableViewController<MSSyncContextDelegate>

3. Ajoutez l'instruction d'importation suivante au début de **QSTodoListViewController.m** :

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

### Test de l'application

Testons l'application qui présente des conflits. Modifiez le même élément dans deux instances différentes de l'application exécutées simultanément. Effectuez maintenant le geste d'actualisation dans les instances de l'application en faisant un glisser depuis le haut de l'écran. Vous êtes alors invité à rapprocher la modification.

### Résumé

Pour configurer le projet terminé du didacticiel [Prise en main des données hors connexion] de manière à détecter les conflits, vous avez commencé par ajouter la possibilité de modifier et de mettre à jour les tâches.

À cet effet, vous avez ajouté un nouveau contrôleur d'affichage des détails des éléments, connecté le contrôleur d'affichage principal et le contrôleur d'affichage des détails, et enfin ajouté la possibilité d'enregistrer les modifications et de les placer dans le cloud.

Ensuite, vous avez découvert ce qui se passe en cas de conflit. Vous avez ajouté la prise en charge d'un gestionnaire de conflits en implémentant le protocole **MSSyncContextDelegate**. Vous avez également activé la prise en charge du délégué de contexte de synchronisation via la classe d'interface de serveur de **QSTodoService** et **QSTodoListViewController**, et les classes de prise en charge.

Au cours du processus, vous avez ajouté une classe d'assistance **QSUIAlertViewWithBlock** pour afficher une alerte destinée aux utilisateurs et, pour terminer, vous avez ajouté du code à **QSTodoListViewController** pour inviter l'utilisateur à résoudre le conflit en utilisant l'une des trois méthodes disponibles.

<!-- URLs. -->

[add-todo-item-view-controller-1]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-1.png
[add-todo-item-view-controller-2]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-2.png
[add-todo-item-view-controller-3]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-3.png
[add-todo-item-view-controller-4]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-4.png
[add-todo-item-view-controller-5]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-5.png
[add-todo-item-view-controller-6]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-6.png
[conflict-handling-problem-1]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-handling-problem-1.png
[update-todo-list-view-controller-1]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-1.png
[update-todo-list-view-controller-2]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-2.png

[Contrôles segmentés]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UISegmentedControl.html
[Aide de l'éditeur de modèle de données de base]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Création d'une connexion d'outlet]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Création d'une interface utilisateur]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Ajout d'un segue entre des scènes dans un storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Ajout d'une scène à un storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html
[Données de base]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Téléchargez le Kit de développement ici]: http://aka.ms/Gc6fex
[Utilisation de la bibliothèque cliente Mobile Services pour iOS]: /fr-fr/documentation/articles/mobile-services-ios-how-to-use-client-library/
[Exemple de guide de prise en main hors connexion pour iOS]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[Prise en main des données hors connexion]: /fr-fr/documentation/articles/mobile-services-ios-get-started-offline-data/
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-ios-get-started/
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-ios-get-started-data/

<!--HONumber=35.2-->
