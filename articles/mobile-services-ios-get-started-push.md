<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app." metaCanonical="http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="" solutions="" manager="" editor="" />

Prise en main des notifications Push dans Mobile Services
=========================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-push-android "Android")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android")

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push vers une application iOS. Dans ce didacticiel, vous allez ajouter des notifications Push à l'aide du service de notifications Push Apple (APNS) au projet de démarrage rapide. Une fois la procédure terminée, votre service mobile envoie une notification Push chaque fois qu'un enregistrement est inséré.

Vous pouvez regarder une version vidéo de ce didacticiel en cliquant sur le clip à droite.

[regarder le didacticiel](http://channel9.msdn.com/posts/iOS-Support-in-Windows-Azure-Mobile-Services) [Lire la vidéo](http://channel9.msdn.com/posts/iOS-Support-in-Windows-Azure-Mobile-Services) 10:37:00

> [WACOM.NOTE] Cette rubrique présente l'activation des notifications Push à l'aide de la prise en charge héritée par Mobile Services. Azure Notification Hubs s'intègre avec Mobile Services pour vous permettre d'envoyer des notifications Push basées sur un modèle et interplateforme à des millions d'appareils. Par défaut, les notifications Push avec Notification Hubs ne sont pas activées et actuellement, il n'existe pas de prise en charge de Notification Hub pour iOS dans les bibliothèques Mobile Services. Toutefois, vous pouvez envoyer des notifications Push à partir de votre service mobile à l'aide des bibliothèques Notification Hub. Pour plus d'informations, consultez la page [Prise en main de Notification Hubs](/fr-fr/documentation/articles/notification-hubs-ios-get-started/).

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Génération de la demande de signature de certificat](#certificates)
2.  [Inscription de votre application et activation des notifications Push](#register)
3.  [Création d'un profil d'approvisionnement pour l'application](#profile)
4.  [Configuration de Mobile Services](#configure)
5.  [Ajout de notifications Push à l'application](#add-push)
6.  [Mise à jour des scripts pour l'envoi de notifications Push](#update-scripts)
7.  [Insertion de données pour recevoir des notifications](#test)

Ce didacticiel requiert les éléments suivants :

-   [Kit de développement logiciel (SDK) Mobile Services iOS](https://go.microsoft.com/fwLink/p/?LinkID=266533)
-   [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532)
-   Un appareil compatible iOS 5.0 (ou versions ultérieures)
-   Un abonnement au programme pour développeurs iOS

    > [WACOM.NOTE] En raison de la configuration requise pour les notifications Push, vous devez déployer et tester les notifications Push sur un appareil compatible iOS (iPhone ou iPad) au lieu d'un émulateur.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer ce didacticiel, vous devez effectuer le didacticiel [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started-ios).

Le service de notifications Push Apple (APNS) utilise des certificats pour authentifier votre service mobile. Suivez ces instructions pour créer les certificats requis et les télécharger dans votre service mobile. Pour obtenir la documentation complète des fonctionnalités APNS, consultez la page [Service de notification Push Apple](http://go.microsoft.com/fwlink/p/?LinkId=272584).

Génération du fichier de demande de signature de certificat
-----------------------------------------------------------

Vous devez d'abord générer le fichier de demande de signature de certificat (CSR), qu'Apple utilise pour générer un certificat signé.

1.  Dans le dossier Utilitaires, exécutez l'outil Trousseau d'accès.

2.  Cliquez sur **Trousseau d'accès**, développez **Assistant Certificat**, puis cliquez sur **Demander un certificat à partir d'une autorité de certification...**.

  	![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png)

3. Sélectionnez votre **Adresse de messagerie de l'utilisateur** et votre **Nom commun**, vérifiez que la case à cocher **Enregistré sur le disque** est activée, puis cliquez sur **Continuer**. Laissez le champ **Adresse de messagerie d'autorité de certification** vide, car il n'est pas requis.

  	![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png)

1.  Entrez un nom pour le fichier de demande de signature de certificat dans **Enregistrer sous**, sélectionnez l'emplacement dans **Où**, puis cliquez sur **Enregistrer**.

  	![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png) 
  	Ceci enregistre le fichier de demande de signature de certificat dans l'emplacement sélectionné : l'emplacement par défaut est dans le Bureau. Notez l'emplacement sélectionnez pour ce fichier.

Ensuite, vous allez inscrire votre application auprès d'Apple, activer les notifications Push, puis télécharger ce fichier de demande de signature de certificat exporté pour créer un certificat Push.

Inscription de votre application pour les notifications Push
------------------------------------------------------------

Pour pouvoir envoyer des notifications Push vers une application iOS à partir des services mobiles, vous devez inscrire votre application auprès d'Apple mais aussi l'inscrire aux notifications Push.

1.  Si vous n'avez pas déjà inscrit votre application, accédez au [Portail d'approvisionnement iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) auprès du centre de développement Apple, connectez-vous avec votre ID Apple, cliquez sur **Identifiers**, puis sur **App IDs**, puis sur le signe **+** pour inscrire une nouvelle application.

   	![][102] 

2.  Entrez un nom pour votre application dans **Description**, entrez la valeur *MobileServices.Quickstart* dans **Bundle Identifier**, activez l'option « Push Notifications » dans la section « App Services », puis cliquez sur **Continue**. Cet exemple utilise l'ID **MobileServices.Quickstart**, mais vous ne pouvez pas réutiliser le même ID, car chaque ID d'application doit être unique pour chaque utilisateur. Ainsi, nous vous recommandons d'ajouter votre nom complet ou vos initiales après le nom de l'application.

   	![][103]
   
   	Ceci génère l'ID de votre application et vous demande d'**Envoyer** les informations. Cliquez sur **Envoyer**
   
   	![][104] 
   
   	Lorsque vous avez cliqué sur **Envoyer**, le message **Inscription terminée** s'affiche à l'écran, comme montré ci-dessous. Cliquez sur **Terminé**.
   
   	![][105]

    > [WACOM.NOTE] Si vous choisissez d'entrer une autre valeur **Bundle Identifier** que *MobileServices.Quickstart*, vous devez également mettre à jour la valeur d'identificateur de lot dans votre projet Xcode.

3.  Recherchez l'ID de l'application que vous venez de créer, puis cliquez sur sa ligne.

   	![][106]
           
   	Le fait de cliquer sur l'ID de l'application affiche les informations détailles de cette application et de l'ID d'application. Cliquez sur le bouton **Paramètres**.
           
   	![][107] 

4.  Faites défiler jusqu'en bas de l'écran, puis cliquez sur le bouton **Create Certificate...** sous la section **Development Push SSL Certificate**.

   	![][108] 

   	Ceci affiche l'Assistant « Ajouter un certificat iOS ».
           
   	![][108] 

    > [WACOM.NOTE] Ce didacticiel utilise un certificat de développement. Le même processus est utilisé lors de l'inscription d'un certificat de production. Assurez-vous simplement que vous avez défini le même type de certificat lorsque vous avez téléchargé le certificat vers Mobile Services.

5.  Cliquez sur **Choose File**, accédez à l'emplacement où vous avez enregistré le fichier de demande de signature de certificat que vous avez créé lors de la première tâche, puis cliquez sur **Generate**.

   	![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-10.png)

1.  Une fois le certificat créé par le portail, cliquez sur le bouton **Download**, puis cliquez sur **Done**.

   	![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-11.png)
 Ceci entraîne le téléchargement du certificat de signature et l'enregistre sur votre ordinateur dans le dossier Téléchargements. 
   	![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png)

    > [WACOM.NOTE] Par défaut, le certificat de développement du fichier téléchargé est nommé **aps_development.cer**.

1.  Double-cliquez sur le certificat Push téléchargé **aps\_development.cer**.

   	Ceci installe le nouveau certificat dans le Trousseau d'accès, comme indiqué ci-dessous :

   	![][10]

    > [WACOM.NOTE] Le nom de votre certificat peut être différent, il aura tout de même le préfixe **Apple Development iOS Push Notification Services:**.

Plus tard, vous devrez utiliser ce certificat pour générer un fichier .p12 et le télécharger vers Mobile Services pour activer l'authentification avec APNS.

Création d'un profil d'approvisionnement pour l'application
-----------------------------------------------------------

1.  Une fois de retour sur le [Portail d'approvisionnement iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456), sélectionnez **Profils d'approvisionnement**, sélectionnez **Tous**, puis cliquez sur le signe **+** pour créer un nouveau profil. Ceci démarre l'Assistant **Ajouter le profil d'approvisionnement iOS**

   	![][112]

2.  Sélectionnez **Développement d'application iOS** sous **Développement** en tant que type de profil d'approvisionnement, puis cliquez sur **Continuer**

   	![][113]

3.  Ensuite, sélectionnez l'ID d'application de l'application de démarrage rapide Mobile Services dans la liste déroulante **ID d'application**, puis cliquez sur **Continuer**.

   	![][114]

4.  Sur l'écran **Sélectionner les certificats**, sélectionnez le certificat créé plus tôt, puis cliquez sur **Continuer**.

   	![][115]

5.  Ensuite, sélectionnez les **Appareils** à utiliser pour le test, puis cliquez sur **Continuer**.

   	![][116]

6.  Ensuite, sélectionnez un nom pour le profil dans **Nom du profil**, cliquez sur **Générer**, puis cliquez sur **Terminé**.

   	![][117]

   	Ceci entraîne la création d'un profil d'approvisionnement.

1.  Dans Xcode, ouvrez l'Organisateur, sélectionnez la vue Appareils, sélectionnez **Profils d'approvisionnement** dans la section **Bibliothèque** dans le volet de gauche, puis cliquez sur le bouton **Actualiser** en bas du volet du milieu.

   	![][101]

2.  Sous **Cibles**, cliquez sur **Démarrage rapide**, développez **Identité de signature du code**, puis sous **Débogage**, sélectionnez le nouveau profil.

   	![][17]

Ceci vérifie que le projet Xcode utilise le nouveau profil pour la signature du code. Ensuite, vous devez télécharger le certificat vers Mobile Services.

Configuration de Mobile Services pour l'envoi de requêtes Push
--------------------------------------------------------------

Après l'inscription de votre application avec APNS et une fois votre projet configuré, vous devez configurer votre service mobile pour l'intégrer à APNS.

1.  Dans Trousseau d'accès, cliquez avec le bouton droit sur le nouveau certificat, cliquez sur **Exporter**, nommez votre fichier QuickstartPusher, sélectionnez le format **.p12** puis cliquez sur **Enregistrer**.

   	![][28]

  Notez le nom du fichier et l'emplacement du certificat exporté.

	> [WACOM.NOTE] Ce didacticiel crée un fichier QuickstartPusher.p12. Il est possible que le nom de votre fichier et son emplacement sont différents.

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur l'application.

   	![][18]

2.  Cliquez sur l'onglet **Push**, puis sur **Télécharger**.

   	![][19]

    Ceci affiche la boîte de dialogue Télécharger le certificat.

3.  Cliquez sur **Fichier**, sélectionnez le fichier QuickstartPusher.p12 du certificat exporté, entrez le **Mot de passe**, vérifiez que le **Mode** sélectionné est correct, cliquez sur l'icône en forme de coche, puis cliquez sur **Enregistrer**.

   	![][20] 

    > [WACOM.NOTE] Ce didacticiel utilise des certificats de développement.

Vos deux services mobiles sont configurés pour fonctionner avec APNS.

Ajout de notifications Push à votre application
-----------------------------------------------

1.  Dans Xcode, ouvrez le fichier QSAppDelegate.h puis ajoutez la propriété suivante sous la propriété **\*window** :

         @property (strong, nonatomic) NSString *deviceToken;

    > [WACOM.NOTE] Lorsque le schéma dynamique est activé pour votre service mobile, une nouvelle colonne « deviceToken » est ajouté automatiquement à la table **TodoItem** lorsqu'un nouvel élément contenant cette propriété est inséré.

2.  Dans QSAppDelegate.m, remplacez la méthode de gestionnaire suivante dans l'implémentation :

         - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
         (NSDictionary *)launchOptions
         {
             // Inscrivez-vous pour obtenir des notifications distantes
             [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
             UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
             return YES;
         }

3.  Dans QSAppDelegate.m, ajoutez la méthode de gestionnaire suivante à l'implémentation :

         // À présent que vous êtes inscrits, stockez le jeton de l'appareil (en tant que chaîne) sur l'instance AppDelegate
         // pensez d'abord à supprimer les crochets.
         - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
         (NSData *)deviceToken {
             NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
             self.deviceToken = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
         }

4.  Dans QSAppDelegate.m, ajoutez la méthode de gestionnaire suivante à l'implémentation :

         // Gérez toutes les erreurs d'inscription. Dans ce cas, définissez le deviceToken comme une chaîne
         // vide pour empêcher l'échec de l'insertion.
         - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
         (NSError *)error {
             NSLog(@"Échec de l'inscription aux notifications distantes : %@", error);
             self.deviceToken = @"";
         }

5.  Dans QSAppDelegate.m, ajoutez la méthode de gestionnaire suivante à l'implémentation :

         // Comme les alertes toast ne fonctionnent pas lorsque l'application est exécutée, l'application les gère.
         // Ceci utilise les userInfo dans la charge utile pour afficher une UIAlertView.
         - (void)application:(UIApplication *)application didReceiveRemoteNotification:
         (NSDictionary *)userInfo {
             NSLog(@"%@", userInfo);
             UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
             [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
             @"OK" otherButtonTitles:nil, nil];
             [alert show];
         }

6.  Dans QSTodoListViewController.m, importez le fichier QSAppDelegate.h pour pouvoir utiliser le délégué pour obtenir le jeton de l'appareil :

         #import "QSAppDelegate.h"

7.  Dans QSTodoListViewController.m, modifiez l'action **(IBAction)onAdd** en recherchant la ligne suivante :

         NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) }; 

    Remplacez-la par le code suivant :

         // Obtenez une référence au AppDelegate pour récupérer facilement le deviceToken
         QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

         NSDictionary *item = @{
             @"text" : itemText.text,
             @"complete" : @(NO),
             // ajoutez la propriété du jeton de l'appareil à la charge utile de l'élément todo
             @"deviceToken" : delegate.deviceToken
         };

   	Ceci ajoute une référence au **QSAppDelegate** pour obtenir le jeton de l'appareil, puis modifie la charge utile de la demande pour inclure le jeton de l'appareil.

   	> [WACOM.NOTE] Vous devez ajouter ce code avant l'appel de la méthode <strong>addItem</strong>.

L'application est mise à jour et prend en charge les notifications Push.

Mise à jour du script de la fonction insert inscrite dans le portail de gestion
-------------------------------------------------------------------------------

1.  Dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

   	![][21]

2.  Dans **todoitem**, cliquez sur l'onglet **Script** et sélectionnez **Insérer**.

   	![](./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png) 
   	La fonction appelée lors d'une insertion dans la table **TodoItem** s'affiche.

1.  Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

         function insert(item, user, request) {
             request.execute();
             // Définissez le délai d'expiration pour reporter la notification, afin de laisser le temps à 
             // l'application de se fermer sur l'appareil pour illustrer les notifications toast
             setTimeout(function() {
                 push.apns.send(item.deviceToken, {
                     alert: "Toast: " + item.text,
                     payload: {
                         inAppMessage: "Un nouvel élément est arrivé : '" + item.text + "'"
                     }
                 });
             }, 2500);
         }

   	Ceci inscrit un nouveau script d'insertion, qui utilise [apns object] pour envoyer une notification Push (le texte inséré) à l'appareil fourni dans la demande d'insertion 


   	> [WACOM.NOTE] Ce script reporte l'envoi de la notification pour vous laisser le temps de fermer l'application pour recevoir une notification toast.

Test des notifications Push dans votre application
--------------------------------------------------

1.  Appuyez sur le bouton **Démarrer** pour générer le projet, puis démarrez l'application sur un appareil compatible iOS, et enfin cliquez sur **OK** pour accepter les notifications Push.

  	![](./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png)

    > [WACOM.NOTE] Vous devez accepter explicitement les notifications Push de votre application. Cette demande s'effectue uniquement lors du premier démarrage de l'application.

1.  Dans l'application, entrez un texte explicite, tel que *Nouvelle tâche Mobile Services* puis cliquez sur l'icône Ajouter (**+**).

  	![](./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png)

1.  Vérifiez que vous avez reçu une notification, puis cliquez sur **OK** pour fermer celle-ci.

  	![](./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png)

1.  Répétez l'étape 2 et fermez immédiatement l'application, puis vérifiez que la notification toast suivante est affichée.

   	![](./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png)

Vous avez terminé ce didacticiel.

Étapes suivantes
----------------

Dans cet exemple simple, un utilisateur reçoit une notification Push avec les données venant d'être insérées. Le jeton de l'appareil utilisé par APNS est fourni au service mobile par le client dans la requête. Dans le didacticiel suivant, [Notifications Push pour les utilisateurs d'application](/en-us/develop/mobile/tutorials/push-notifications-to-users-ios), vous allez créer une table Devices distincte dans laquelle stocker les jetons d'appareil et envoyer une notification Push à tous les canaux stockés en cas d'insertion.

<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure Mobile Services]: #configure
[Update scripts to send push notifications]: #update-scripts
[Add push notifications to the app]: #add-push
[Insert data to receive notifications]: #test
[Next Steps]:#next-steps

<!-- Images. -->
[5]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png
[17]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-ios
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /en-us/develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-ios
[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333