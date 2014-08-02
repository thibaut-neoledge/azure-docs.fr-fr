<properties linkid="develop-net-tutorials-push-notifications-to-users-ios" urlDisplayName="Push Notifications to Users (iOS)" pageTitle="Push notifications to users (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Push notifications to users by using Mobile Services" authors="" solutions="" manager="" editor="" />

Notifications Push adressées aux utilisateurs à l'aide de Mobile Services
=========================================================================

[Windows Phone](/en-us/develop/mobile/tutorials/push-notifications-to-users-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/push-notifications-to-users-ios "iOS")[Android](/en-us/develop/mobile/tutorials/push-notifications-to-users-android "Android")

Cette rubrique s'inscrit dans le prolongement du [didacticiel précédent sur les notifications Push](/en-us/develop/mobile/tutorials/get-started-with-push-ios) en ajoutant une nouvelle table pour le stockage des jetons APNS (Apple Push Notification Service). Ceux-ci peuvent ensuite être utilisés pour envoyer des notifications Push aux utilisateurs de l'application iPhone ou iPad.

Ce didacticiel vous guide tout au long de la procédure de mise à jour des notifications Push dans votre application :

1.  [Création de la table Devices](#create-table)
2.  [Mise à jour de l'application](#update-app)
3.  [Mise à jour des scripts serveur](#update-scripts)
4.  [Vérification du comportement des notifications Push](#test-app)

Ce didacticiel est basé sur le démarrage rapide de Mobile Services et s'appuie sur le didacticiel précédent [Prise en main des notifications Push](/en-us/develop/mobile/tutorials/get-started-with-push-ios). Avant de commencer ce didacticiel, vous devez suivre le didacticiel [Prise en main des notifications Push](/en-us/develop/mobile/tutorials/get-started-with-push-ios).

## <a name="create-table"></a><h2><span class="short-header">Création de la table</span>Création de la table Devices</h2>


1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur l'application.

    ![](./media/mobile-services-ios-push-notifications-app-users/mobile-services-selection.png)

2.  Cliquez sur l'onglet **Data**, puis sur **Create**.

    ![](./media/mobile-services-ios-push-notifications-app-users/mobile-create-table.png)

    La boîte de dialogue **Create new table** s'affiche.

3.  Conservez le paramètre **Anybody with the application key** par défaut pour toutes les autorisations, tapez *Devices* dans **Table name**, puis cliquez sur le bouton de vérification.

    ![](./media/mobile-services-ios-push-notifications-app-users/mobile-create-devices-table.png)

    La table **Devices** est alors créée, dans laquelle sont stockés les jetons d'appareil utilisés pour envoyer des notifications Push indépendamment des données d'élément.

La prochaine étape consiste à modifier l'application de notifications Push pour stocker les données dans cette nouvelle table, et non dans la table **TodoItem**.

Mise à jour de votre application
--------------------------------

1.  Dans Xcode, ouvrez le fichier QSTodoService.h et ajoutez la déclaration de méthode suivante :

         // Déclarez la méthode pour inscrire le jeton d'appareil pour les autres utilisateurs
         - (void)registerDeviceToken:(NSString *)deviceToken;

    Cela permet au délégué de l'application d'inscrire un deviceToken auprès de Mobile Service.

2.  Dans QSTodoService.m, ajoutez la méthode d'instance suivante :

         // Méthode d'instance destinée à inscrire le deviceToken dans la table Devices.
         // Appelée dans AppDelegate.m lorsque l'inscription APNS aboutit.
         - (void)registerDeviceToken:(NSString *)deviceToken
         {
             MSTable *devicesTable = [self.client tableWithName:@"Devices"]; 
             NSDictionary *device = @{ @"deviceToken" : deviceToken };

             // Insérez l'élément dans la table devices et ajoutez-le au tableau d'éléments à la fin de l'opération
             [devicesTable insert:device completion:^(NSDictionary *result, NSError *error) {
                 if (error) {
                     NSLog(@"ERROR %@", error);
                 }
             }];
         }

    Cela permet aux autres appelants d'inscrire le jeton d'appareil auprès de Mobile Services.

3.  Dans le fichier QSAppDelegate.m, ajoutez l'instruction import suivante :

         #import "QSTodoService.h"

    Ce code informe AppDelegate au sujet de l'implémentation de TodoService.

4.  Dans QSAppDelegate.m, remplacez la méthode **didRegisterForRemoteNotificationsWithDeviceToken** par le code suivant :

         // 
         L'inscription étant réalisée, stockez maintenant le jeton de l'appareil (en tant que chaîne) dans l'instance AppDelegate
         // en veillant dans un premier temps à supprimer les crochets.
         - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
         (NSData *)deviceToken {

            // Inscrivez le deviceToken APNS dans la table Devices de Mobile Service.
            NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
            NSString *token = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
                
            QSTodoService *instance = [QSTodoService defaultService];
            [instance registerDeviceToken:token];
         }

5.  Dans QSTodoListViewController.m, recherchez la méthode **(IBAction)onAdd** et *supprimez* le code suivant :

         // Obtenez une référence au AppDelegate pour récupérer facilement le deviceToken
         QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

         NSDictionary *item = @{
             @"text" : itemText.text,
             @"complete" : @(NO),
             // ajoutez la propriété du jeton de l'appareil à la charge utile de l'élément todo
             @"deviceToken" : delegate.deviceToken
         };
         
    Remplacez-la par le code suivant :

         // Le délégué ayant été supprimé, cette application ne transmet plus ici le deviceToken.
         // Supprimez le jeton d'appareil de la charge utile
         NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) }; 

Maintenant que votre application a été mise à jour, elle va utiliser la nouvelle table Devices pour stocker les jetons de l'appareil qui serviront à renvoyer les notifications Push à l'appareil.

Mise à jour des scripts serveur
-------------------------------

1.  Dans le portail de gestion, cliquez sur l'onglet **Data**, puis sur la table **Devices**.

     ![](./media/mobile-services-ios-push-notifications-app-users/mobile-portal-data-tables-devices.png)

2.  Dans **devices**, cliquez sur l'onglet **Script**, puis sélectionnez **Insert**.

     ![](./media/mobile-services-ios-push-notifications-app-users/mobile-insert-script-devices.png)

    La fonction appelée lors d'une insertion dans la table **Devices** s'affiche.

3.  Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

         function insert(item, user, request) {
            var devicesTable = tables.getTable('Devices');
            devicesTable.where({
                token: item.token
            }).read({
                success: insertTokenIfNotFound
            });

            function insertTokenIfNotFound(existingTokens) {
                if (existingTokens.length > 0) {
                    request.respond(200, existingTokens[0]);
                } else {
                    request.execute();
                }
            }
         }

     Ce script vérifie dans la table **Devices** s'il existe un appareil avec le même jeton. L'insertion n'a lieu que si aucun appareil correspondant n'est trouvé. Cela évite que des enregistrements d'appareil en double soient créés.

4.  Cliquez sur **TodoItem**, puis sur **Script** et sélectionnez **Insérer**.

     ![](./media/mobile-services-ios-push-notifications-app-users/mobile-insert-script-push2.png)

5.  Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

         function insert(item, user, request) {
           request.execute({
               success: function() {
                   request.respond();
                   sendNotifications();
               }
           });

           function sendNotifications() {
               var devicesTable = tables.getTable('Devices');
               devicesTable.read({
                   success: function(devices) {
                       // Définissez un délai d'expiration pour reporter les notifications, 
                       // afin de laisser le temps à l'application de se fermer 
                       // sur l'appareil pour illustrer les notifications toast.
                       setTimeout(function() {
                           devices.forEach(function(device) {

                               push.apns.send(device.deviceToken, {
                                   alert: "Toast: " + item.text,
                                   payload: {
                                       inAppMessage: 
                                       "Un nouvel élément est arrivé : '" + 
                                       item.text + "'"
                                   }
                               });
                           });
                       }, 2500);
                   }
               });
           }

        }

    Ce script d'insertion envoie une notification Push (avec le texte de l'élément inséré) à tous les appareils stockés dans la table **Devices**.

Test de l'applicationTest des notifications Push dans l'application
-------------------------------------------------------------------

1.  Appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l'application dans un appareil compatible iOS puis, dans l'application, tapez un texte explicite, tel que *Nouvelle tâche Mobile Services*. Cliquez enfin sur l'icône plus (**+**).

    ![](./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push2-ios.png)

1.  Vérifiez que vous avez reçu une notification, puis cliquez sur **OK** pour fermer celle-ci.

    ![](./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push3-ios.png)

1.  Répétez l'étape 2 et fermez immédiatement l'application, puis vérifiez que la notification toast suivante est affichée.

    ![](./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push4-ios.png)

Vous avez terminé ce didacticiel.

Étapes suivantes
----------------

Cela met fin à la série de didacticiels établissant les principes de base de l'utilisation de notifications Push. Pour plus d'informations sur les rubriques Mobile Services suivantes :

-   [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-ios)
    
    En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-ios)
    
    En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.

-   [Référence de script serveur Mobile Services](http://go.microsoft.com/fwlink/?LinkId=262293)
    
    En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.


