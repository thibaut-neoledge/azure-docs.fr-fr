<properties linkid="develop-net-tutorials-push-notifications-to-users-ios" urlDisplayName="Push Notifications to Users (iOS)" pageTitle="Push notifications to users (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Push notifications to users by using Mobile Services" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# Notifications Push adressées aux utilisateurs à l'aide de Mobile Services

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/fr-fr/develop/mobile/tutorials/push-notifications-to-users-wp8" title="Windows Phone">Windows Phone</a><a href="/fr-fr/develop/mobile/tutorials/push-notifications-to-users-ios" title="iOS" class="current">iOS</a><a href="/fr-fr/develop/mobile/tutorials/push-notifications-to-users-android" title="Android" class="current">Android</a>
</div>

Cette rubrique s'inscrit dans le prolongement du [didacticiel précédent sur les notifications Push][] en ajoutant une nouvelle table pour le stockage des jetons APNS (Apple Push Notification Service). Ceux-ci peuvent ensuite être utilisés pour envoyer des notifications Push aux utilisateurs de l'application iPhone ou iPad.

Ce didacticiel vous guide tout au long de la procédure de mise à jour des notifications Push dans votre application :

1.  [Création de la table Devices][]
2.  [Mise à jour de l'application][]
3.  [Mise à jour des scripts serveur][]
4.  [Vérification du comportement des notifications Push][]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services et s'appuie sur le didacticiel précédent [Prise en main des notifications Push][didacticiel précédent sur les notifications Push]. Avant de commencer ce didacticiel, vous devez suivre le didacticiel [Prise en main des notifications Push][didacticiel précédent sur les notifications Push].

## <a name="create-table"></a>

## <span class="short-header">Création de la table</span>Création de la table Devices

1.  Connectez-vous au [portail de gestion Azure][], cliquez sur **Mobile Services**, puis sur l'application.

    ![][]

2.  Cliquez sur l'onglet **Data**, puis sur **Create**.

    ![][1]

    La boîte de dialogue **Create new table** s’affiche.

3.  Conservez le paramètre **Anybody with the application key** par défaut pour toutes les autorisations, tapez *Devices* dans **Table name**, puis cliquez sur le bouton de vérification.

    ![][2]

    La table **Devices** est alors créée, dans laquelle sont stockés les jetons d'appareil utilisés pour envoyer des notifications Push indépendamment des données d'élément.

La prochaine étape consiste à modifier l'application de notifications Push pour stocker les données dans cette nouvelle table, et non dans la table **TodoItem**.

## <a name="update-app"></a>Mise à jour de votre application

1.  Dans Xcode, ouvrez le fichier QSTodoService.h et ajoutez la déclaration de méthode suivante :

        // Declare method to register device token for other users
        - (void)registerDeviceToken:(NSString *)deviceToken;

    Cela permet au délégué de l'application d'inscrire un deviceToken auprès de Mobile Service.

2.  Dans QSTodoService.m, ajoutez la méthode d'instance suivante :

        // Instance method to register deviceToken in Devices table.
        // Called in AppDelegate.m when APNS registration succeeds.
        - (void)registerDeviceToken:(NSString *)deviceToken
        {
            MSTable *devicesTable = [self.client tableWithName:@"Devices"]; 
            NSDictionary *device = @{ @"deviceToken" : deviceToken };

            // Insert the item into the devices table and add to the items array on completion
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

        // We have registered, so now store the device token (as a string) on the AppDelegate instance
        // taking care to remove the angle brackets first.
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {

           // Register the APNS deviceToken with the Mobile Service Devices table.
           NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
           NSString *token = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];

           QSTodoService *instance = [QSTodoService defaultService];
           [instance registerDeviceToken:token];
        }

5.  Dans QSTodoListViewController.m, recherchez la méthode **(IBAction)onAdd** et *supprimez* le code suivant :

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : delegate.deviceToken
        };

    Remplacez-la par le code suivant :

        // We removed the delegate; this application no longer passes the deviceToken here.
        // Remove the device token from the payload
        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) };

Maintenant que votre application a été mise à jour, elle va utiliser la nouvelle table Devices pour stocker les jetons de l'appareil qui serviront à renvoyer les notifications Push à l'appareil.

## <a name="update-scripts"></a>Mise à jour des scripts serveur

1.  Dans le portail de gestion, cliquez sur l'onglet **Data**, puis sur la table **Devices**.

    ![][3]

2.  Dans **devices**, cliquez sur l'onglet **Script**, puis sélectionnez **Insert**.

    ![][4]

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

    ![][5]

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
                      // Set timeout to delay the notifications, 
                      // to provide time for the app to be closed 
                      // on the device to demonstrate toast notifications.
                      setTimeout(function() {
                          devices.forEach(function(device) {

                              push.apns.send(device.deviceToken, {
                                  alert: "Toast: " + item.text,
                                  payload: {
                                      inAppMessage: 
                                      "Hey, a new item arrived: '" + 
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

## <a name="test"></a><span class="short-header">Test de l'application</span>Test des notifications Push dans l'application

1.  Appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l'application dans un appareil compatible iOS puis, dans l'application, tapez un texte explicite, tel que *Nouvelle tâche Mobile Services*. Cliquez enfin sur l'icône plus (**+**).

    ![][6]

2.  Vérifiez que vous avez reçu une notification, puis cliquez sur **OK** pour fermer celle-ci.

    ![][7]

3.  Répétez l'étape 2 et fermez immédiatement l'application, puis vérifiez que la notification toast suivante est affichée.

    ![][8]

Vous avez terminé ce didacticiel.

## Étapes suivantes

Cela met fin à la série de didacticiels établissant les principes de base de l'utilisation de notifications Push. Pour plus d'informations sur les rubriques Mobile Services suivantes :

-   [Prise en main des données][]

    En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main de l'authentification][]

    En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.

-   [Référence de script serveur Mobile Services][]

    En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.

  [Windows Phone]: /fr-fr/develop/mobile/tutorials/push-notifications-to-users-wp8 "Windows Phone"
  [iOS]: /fr-fr/develop/mobile/tutorials/push-notifications-to-users-ios "iOS"
  [Android]: /fr-fr/develop/mobile/tutorials/push-notifications-to-users-android "Android"
  [didacticiel précédent sur les notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-ios
  [Création de la table Devices]: #create-table
  [Mise à jour de l'application]: #update-app
  [Mise à jour des scripts serveur]: #update-scripts
  [Vérification du comportement des notifications Push]: #test-app
  [portail de gestion Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-ios-push-notifications-app-users/mobile-services-selection.png
  [1]: ./media/mobile-services-ios-push-notifications-app-users/mobile-create-table.png
  [2]: ./media/mobile-services-ios-push-notifications-app-users/mobile-create-devices-table.png
  [3]: ./media/mobile-services-ios-push-notifications-app-users/mobile-portal-data-tables-devices.png
  [4]: ./media/mobile-services-ios-push-notifications-app-users/mobile-insert-script-devices.png
  [5]: ./media/mobile-services-ios-push-notifications-app-users/mobile-insert-script-push2.png
  [6]: ./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push2-ios.png
  [7]: ./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push3-ios.png
  [8]: ./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push4-ios.png
  [Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-ios
  [Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-ios
  [Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
