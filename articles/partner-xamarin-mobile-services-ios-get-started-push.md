<properties linkid="develop-mobile-tutorials-get-started-with-push-xamarin-ios" urlDisplayName="Get Started with Push Notifications" pageTitle="Get started with push notifications (Xamarin.iOS) - Mobile Services" metaKeywords="" description="Learn how to use push notifications in Xamarin.iOS apps with Azure Mobile Services." metaCanonical="" disqusComments="0" umbracoNaviHide="1" editor="mollybos" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="" />

Prise en main des notifications Push dans Mobile Services
=========================================================

[Windows Store C\#](/fr-fr/develop/mobile/tutorials/get-started-with-push-dotnet "Windows Store C#")[Windows Store JavaScript](/fr-fr/develop/mobile/tutorials/get-started-with-push-js "Windows Store JavaScript")[Windows Phone](/fr-fr/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone")[iOS](/fr-fr/develop/mobile/tutorials/get-started-with-push-ios "iOS")[Android](/fr-fr/develop/mobile/tutorials/get-started-with-push-android "Android")[Xamarin.iOS](/fr-fr/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/fr-fr/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android")

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push vers une application Xamarin.iOS. Dans ce didacticiel, vous allez ajouter des notifications Push à l'aide du service de notifications Push Apple (APNS) au projet de démarrage rapide. Une fois la procédure terminée, votre service mobile envoie une notification Push chaque fois qu'un enregistrement est inséré.

**Remarque**

Ce didacticiel explique comment envoyer simplement des notifications Push en attachant un jeton d'appareil de notification Push à l'enregistrement inséré. Veuillez suivre le didacticiel suivant afin de mieux comprendre comment incorporer des notifications Push dans vos applications réelles.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Génération de la demande de signature de certificat](#certificates)
2.  [Inscription de votre application et activation des notifications Push](#register)
3.  [Création d'un profil de mise en service pour l'application](#profile)
4.  [Configuration de Mobile Services](#configure)
5.  [Ajout de notifications Push à l'application](#add-push)
6.  [Mise à jour des scripts pour l'envoi de notifications Push](#update-scripts)
7.  [Insertion de données pour recevoir des notifications](#test)

Ce didacticiel requiert les éléments suivants :

-   [XCode 5.0](https://go.microsoft.com/fwLink/p/?LinkID=266532)
-   Un appareil compatible iOS 5.0 (ou versions ultérieures)
-   Un abonnement au programme pour développeurs iOS
-   [Xamarin.iOS]
-   [Composant Azure Mobile Services](http://components.xamarin.com/view/azure-mobile-services/)

    **Remarque**

    En raison de la configuration requise pour les notifications Push, vous devez déployer et tester les notifications Push sur un appareil compatible iOS (iPhone ou iPad) au lieu d'un émulateur.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer ce didacticiel, vous devez effectuer le didacticiel [Prise en main de Mobile Services](/fr-fr/develop/mobile/tutorials/get-started-xamarin-ios).

Le service de notifications Push Apple (APNS) utilise des certificats pour authentifier votre service mobile. Suivez ces instructions pour créer les certificats requis et les télécharger dans votre service mobile. Pour obtenir la documentation complète des fonctionnalités APNS, consultez la page [Service de notification Push Apple](http://go.microsoft.com/fwlink/p/?LinkId=272584).

Génération d'un fichier CSRGénération d'un fichier de demande de signature de certificat (CSR)
----------------------------------------------------------------------------------------------

Vous devez d'abord générer le fichier de demande de signature de certificat (CSR), qu'Apple utilise pour générer un certificat signé.

1.  Dans le dossier Utilitaires, exécutez l'outil Trousseau d'accès.

2.  Cliquez sur **Trousseau d'accès**, développez **Assistant Certificat**, puis cliquez sur **Demander un certificat à partir d'une autorité de certification...**.

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png)

1.  Sélectionnez votre **Adresse de messagerie de l'utilisateur**, entrez les valeurs **Nom commun** et **Adresse de messagerie d'autorité de certification**, vérifiez que la case à cocher **Enregistré sur le disque** est activée, puis cliquez sur **Continuer**.

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png)

1.  Entrez un nom pour le fichier de demande de signature de certificat dans **Enregistrer sous**, sélectionnez l'emplacement dans **Où**, puis cliquez sur **Enregistrer**.

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png) Ceci enregistre le fichier de demande de signature de certificat dans l'emplacement sélectionné : l'emplacement par défaut est dans le Bureau. Notez l'emplacement sélectionné pour ce fichier.

Ensuite, vous allez inscrire votre application auprès d'Apple, activer les notifications Push, puis télécharger ce fichier de demande de signature de certificat exporté pour créer un certificat Push.

Inscription de votre applicationInscription de votre application pour les notifications Push
--------------------------------------------------------------------------------------------

Pour pouvoir envoyer des notifications Push vers une application iOS à partir des services mobiles, vous devez inscrire votre application auprès d'Apple, ainsi qu'aux notifications Push.

1.  Si vous n'avez pas déjà inscrit votre application, accédez au [Portail de mise en service iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) auprès du centre de développement Apple, connectez-vous avec votre ID Apple, cliquez sur **Identifiers**, puis sur **App IDs**, puis sur le signe **+**.

	![][102] 

2.  Entrez un nom pour votre application dans **Description**, entrez la valeur *MobileServices.Quickstart* dans **Bundle Identifier**, activez l'option « Push Notifications » dans la section « App Services », puis cliquez sur **Continue**. Cet exemple utilise l'ID **MobileServices.Quickstart**, mais vous ne pouvez pas réutiliser le même ID, car chaque ID d'application doit être unique pour chaque utilisateur. Ainsi, nous vous recommandons d'ajouter votre nom complet ou vos initiales après le nom de l'application.

	![][103]
           
	Ceci génère l'ID de votre application et vous demande d'**Envoyer** les informations. Cliquez sur **Envoyer**
           
	![][104] 
           
	Lorsque vous avez cliqué sur **Envoyer**, le message **Inscription terminée** s'affiche à l'écran, comme montré ci-dessous. Cliquez sur **Terminé**.
           
	![][105]

	Remarque : Si vous choisissez d'entrer une valeur **Bundle Identifier** autre que *MobileServices.Quickstart*, vous devez également mettre à jour la valeur d'identificateur de lot dans votre projet Xcode.

3.  Recherchez l'ID de l'application que vous venez de créer, puis cliquez sur sa ligne.

	![][106]
           
	Le fait de cliquer sur l'ID de l'application affiche les informations détaillées de cette application et de l'ID d'application. Cliquez sur le bouton **Paramètres**.
           
	![][107] 

4.  Faites défiler jusqu'en bas de l'écran, puis cliquez sur le bouton **Create Certificate...** sous la section **Development Push SSL Certificate**.

	![][108] 

	Ceci affiche l'Assistant « Ajouter un certificat iOS ».
           
	![][108] 

	Remarque : ce didacticiel utilise un certificat de développement. Le même processus est utilisé lors de l'inscription d'un certificat de production. Assurez-vous simplement que vous avez défini le même type de certificat lorsque vous avez téléchargé le certificat vers Mobile Services.

5.  Cliquez sur **Choose File**, accédez à l'emplacement où vous avez enregistré le fichier de demande de signature de certificat que vous avez créé lors de la première tâche, puis cliquez sur **Generate**.

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-10.png)

1.  Une fois le certificat créé par le portail, cliquez sur le bouton **Download**, puis cliquez sur **Done**.

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-11.png)

	Ceci entraîne le téléchargement du certificat de signature et l'enregistre sur votre ordinateur dans le dossier Téléchargements.

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png)

    Remarque : par défaut, le certificat de développement du fichier téléchargé est nommé <strong>aps_development.cer</strong>.

1.  Double-cliquez sur le certificat Push téléchargé **aps\_development.cer**.

	Ceci installe le nouveau certificat dans le Trousseau d'accès, comme indiqué ci-dessous :

	![][10]

    Remarque : il se peut que le nom de votre certificat soit différent. Il portera toutefois le préfixe **Apple Development iOS Push Notification Services:**.

Plus tard, vous devrez utiliser ce certificat pour générer un fichier .p12 et le télécharger vers Mobile Services pour activer l'authentification avec APNS.

Mise en service de l'applicationCréation du profil de mise en service de l'application
--------------------------------------------------------------------------------------

1.  Une fois de retour sur le [Portail de mise en service iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456), sélectionnez **Provisioning Profiles**, sélectionnez **All**, puis cliquez sur le signe **+** pour créer un nouveau profil. Ceci démarre l'Assistant **Ajouter le profil d'approvisionnement iOS**

	![][112]

2.  Sélectionnez **Développement d'application iOS** sous **Développement** en tant que type de profil d'approvisionnement, puis cliquez sur **Continuer**

	![][113]

3.  Ensuite, sélectionnez l'ID d'application de l'application de démarrage rapide Mobile Services dans la liste déroulante **ID d'application**, puis cliquez sur **Continuer**.

	![][114]

4.  Sur l'écran **Sélectionner les certificats**, sélectionnez le certificat créé plus tôt, puis cliquez sur **Continuer**.

	![][115]

5.  Ensuite, sélectionnez les **Appareils** à utiliser pour le test, puis cliquez sur **Continuer**.

	![][116]

6.  Ensuite, sélectionnez un nom pour le profil dans **Nom du profil**, cliquez sur **Générer**, puis sur **Terminé**

	![][117]

	Ceci entraîne la création d'un profil de mise en service.

1.  Dans Xcode, ouvrez l'Organisateur, sélectionnez la vue Appareils, sélectionnez **Profils d'approvisionnement** dans la section **Bibliothèque** dans le volet de gauche, puis cliquez sur le bouton **Actualiser** en bas du volet du milieu.

	![][101]

2.  Sous **Cibles**, cliquez sur **Démarrage rapide**, développez **Identité de signature du code**, puis sous **Débogage**, sélectionnez le nouveau profil.

	![][17]

	Ceci vérifie que le projet Xcode utilise le nouveau profil pour la signature du code. Ensuite, vous devez télécharger le certificat vers Mobile Services.

<h2>Configuration du serviceConfiguration de Mobile Services pour l'envoi de requêtes Push</h2>

Après l'inscription de votre application auprès d'APNS et une fois votre projet configuré, vous devez configurer votre service mobile pour l'intégrer à APNS.

1.  Dans Trousseau d'accès, cliquez avec le bouton droit sur le nouveau certificat, cliquez sur **Exporter**, nommez votre fichier QuickstartPusher, sélectionnez le format **.p12** puis cliquez sur **Enregistrer**.

	![][28]

	Notez le nom du fichier et l'emplacement du certificat exporté.

    <div class="dev-callout"><b>Remarque</b>
    <p>Ce didacticiel crée un fichier QuickstartPusher.p12. Il est possible que le nom de votre fichier et son emplacement soient différents.</p>
    </div>

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur l'application.

	![][18]

2.  Cliquez sur l'onglet **Push**, puis sur **Télécharger**.

	![][19]

	Ceci affiche la boîte de dialogue Télécharger le certificat.

3.  Cliquez sur **Fichier**, sélectionnez le fichier QuickstartPusher.p12 du certificat exporté, entrez le **Mot de passe**, vérifiez que le **Mode** sélectionné est correct, cliquez sur l'icône en forme de coche, puis cliquez sur **Enregistrer**.

	![][20] 

    **Remarque**

    Ce didacticiel utilise des certificats de développement.

Vos deux services mobiles sont configurés pour fonctionner avec APNS.

<h2>Ajout de notifications PushAjout de notifications Push à votre application</h2>

1.  Dans Xamarin.Studio, ouvrez le fichier AppDelegate.cs, puis ajoutez la propriété suivante :

         public string DeviceToken { get; set; }

2.  Ouvrez la classe **TodoItem** et ajoutez la propriété suivante :

         [DataMember(Name = "deviceToken")]
         public string DeviceToken { get; set; }

    **Remarque**

    Lorsque le schéma dynamique est activé pour votre service mobile, une nouvelle colonne « deviceToken » est ajoutée automatiquement à la table **TodoItem** lorsqu'un nouvel élément contenant cette propriété est inséré.

3.  Dans **AppDelegate**, remplacez l'événement **FinishedLaunching** :

         public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
         {
             UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | 
                 UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
             UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes); 

             return true;
         }

4.  Dans **AppDelegate**, remplacez l'événement **RegisteredForRemoteNotifications** :

         public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
         {
             string trimmedDeviceToken = deviceToken.Description;
             if (!string.IsNullOrWhiteSpace(trimmedDeviceToken))
             {
                 trimmedDeviceToken = trimmedDeviceToken.Trim('<');
                 trimmedDeviceToken = trimmedDeviceToken.Trim('>');
             }
             DeviceToken = trimmedDeviceToken;
         }

5.  Dans **AppDelegate**, remplacez l'événement **ReceivedRemoteNotification** :

         public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
         {
             Debug.WriteLine(userInfo.ToString());
             NSObject inAppMessage;

             bool success = userInfo.TryGetValue(new NSString("inAppMessage"), out inAppMessage);

             if (success)
             {
                 var alert = new UIAlertView("Got push notification", inAppMessage.ToString(), null, "OK", null);
                 alert.Show();
             }
         }

6.  Dans **TodoListViewController**, modifiez l'action **OnAdd** pour que le jeton d'appareil soit stocké dans **AppDelegeate**, puis stockez-le dans la table **TodoItem** à ajouter.

    string deviceToken = ((AppDelegate)UIApplication.SharedApplication.Delegate).DeviceToken;

             var newItem = new TodoItem() 
             {
                 Text = itemText.Text, 
                 Complete = false,
                 DeviceToken = deviceToken
             };

L'application est mise à jour et prend en charge les notifications Push.

Mise à jour du script d'insertionMise à jour du script d'insertion inscrit dans le portail de gestion
-----------------------------------------------------------------------------------------------------

1.  Dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

	![][21]

2.  Dans **todoitem**, cliquez sur l'onglet **Script** et sélectionnez **Insérer**.

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-insert-script-push2.png)

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

	Ceci inscrit un nouveau script d'insertion, qui utilise [apns object] pour envoyer une notification Push (le texte inséré) à l'appareil fourni dans la demande d'insertion. 


	<div class="dev-callout"><b>Remarque</b>

	Ce script reporte l'envoi de la notification pour vous laisser le temps de fermer l'application pour recevoir une notification toast.

    </div>

Test de l'applicationTest des notifications Push dans l'application
-------------------------------------------------------------------

1.  Appuyez sur le bouton **Démarrer** pour générer le projet, puis démarrez l'application sur un appareil compatible iOS, et enfin cliquez sur **OK** pour accepter les notifications Push.

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png)

    <div class="dev-callout"><b>Remarque</b>
    <p>Vous devez accepter explicitement les notifications Push de votre application. Cette demande s'effectue uniquement lors du premier démarrage de l'application.</p>
    </div>

1.  Dans l'application, entrez un texte explicite, tel que *Nouvelle tâche Mobile Services* puis cliquez sur l'icône Ajouter (**+**).

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png)

1.  Vérifiez que vous avez reçu une notification, puis cliquez sur **OK** pour fermer celle-ci.

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png)

1.  Répétez l'étape 2 et fermez immédiatement l'application, puis vérifiez que la notification toast suivante est affichée.

	![](./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png)

Vous avez terminé ce didacticiel.

Téléchargement de l'exemple terminé
-----------------------------------

Téléchargez le [projet d'exemple terminé](http://go.microsoft.com/fwlink/p/?LinkId=331303). Veillez à mettre à jour les variables **applicationURL** et **applicationKey** avec vos propres paramètres Azure.

Étapes suivantes
----------------

Dans cet exemple simple, un utilisateur reçoit une notification Push avec les données venant d'être insérées. Le jeton de l'appareil utilisé par APNS est fourni au service mobile par le client dans la requête. Dans le didacticiel suivant, [Notifications Push pour les utilisateurs d'application](/fr-fr/develop/mobile/tutorials/push-notifications-to-users-ios), vous allez créer une table Devices distincte dans laquelle stocker les jetons d'appareil et envoyer une notification Push à tous les canaux stockés en cas d'insertion.

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




[5]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png






[17]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-xamarin-ios
[Get started with data]: /fr-fr/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with authentication]: /fr-fr/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Get started with push notifications]: /fr-fr/develop/mobile/tutorials/get-started-with-push-xamarin-ios
[Push notifications to app users]: /fr-fr/develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /fr-fr/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios


[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[completed example project]: http://go.microsoft.com/fwlink/p/?LinkId=331303