<properties urlDisplayName="Get Started with Push Notifications" pageTitle="Prise en main des notifications Push (Xamarin.iOS) - Mobile Services" metaKeywords="" description="Découvrez comment utiliser les notifications Push dans les applications Xamarin.iOS avec Azure Mobile Services." metaCanonical="" disqusComments="0" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="yuaxu" manager="dwrede" services="mobile-services"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="Java" ms.topic="article" ms.date="10/20/2014" ms.author="yuaxu" />

# Ajout de notifications push à votre application Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-push-xamarin](../includes/mobile-services-selector-get-started-push-xamarin.md)]

<p>Cette rubrique vous présente l'utilisation d'Azure Mobile Services pour envoyer des notifications Push vers une application Xamarin.iOS 8. Dans ce didacticiel, vous allez ajouter des notifications push au projet [Prise en main de Mobile Services] à l'aide du service de notification Push Apple. Une fois la procédure terminée, votre service mobile envoie une notification Push chaque fois qu'un enregistrement est inséré.</p>

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1. [Génération de la demande de signature de certificat] 
2. [Inscription de votre application et activation des notifications Push]
3. [Création d'un profil de mise en service pour l'application]
4. [Configuration de Mobile Services]
5. [Configuration de l'application Xamarin.iOS]
6. [Ajout de notifications Push à l'application]
7. [Mise à jour des scripts pour l'envoi de notifications Push]
8. [Insertion de données pour recevoir des notifications]

Ce didacticiel requiert les éléments suivants :

+ Un appareil iOS 8
+ Un abonnement au programme pour développeurs iOS
+ [Xamarin.iOS Studio]
+ [Composant Azure Mobile Services]

   <div class="dev-callout"><b>Remarque</b>
   <p>En raison de la configuration requise pour les notifications Push, vous devez déployer et tester les notifications Push sur un appareil compatible iOS (iPhone ou iPad) au lieu d'un émulateur.</p>
   </div>

Le service de notification Push Apple (APNS) utilise des certificats pour authentifier votre service mobile. Suivez ces instructions pour créer les certificats requis et les télécharger dans votre service mobile. Pour obtenir la documentation complète des fonctionnalités APNS, consultez la page [Service de notification Push Apple].

## <a name="certificates"></a>Génération du fichier de demande de signature de certificat

Vous devez d'abord générer le fichier de demande de signature de certificat (CSR, Certificate Signing Request), qu'Apple utilise pour générer un certificat signé.

1. Depuis Utilitaires, exécutez l'outil **Trousseaux d'accès**.

2. Cliquez sur **Trousseaux d'accès**, développez **Assistant de certification**, puis cliquez sur **Demander un certificat à une autorité de certification**.

    ![][5]

3. Entrez l'**Adresse électronique de l'utilisateur**, entrez la valeur **Nom commun**, vérifiez que la case à cocher **Enregistré sur le disque** est activée, puis cliquez sur **Continuer**.

    ![][6]

4. Entrez un nom pour le fichier de demande de signature de certificat dans **Enregistrer sous**, sélectionnez l'emplacement dans **Où**, puis cliquez sur **Enregistrer**.

    ![][7]
  
    N'oubliez pas l'emplacement que vous avez choisi.

Ensuite, vous allez inscrire votre application auprès d'Apple, activer les notifications Push, puis télécharger ce fichier de demande de signature de certificat exporté pour créer un certificat Push.

## <a name="register"></a>Inscription de votre application pour les notifications Push

Pour pouvoir envoyer des notifications Push vers une application iOS à partir des services mobiles, vous devez inscrire votre application auprès d'Apple, ainsi qu'aux notifications Push. 

1. Si vous n'avez pas déjà inscrit votre application, accédez au <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portail de mise en service iOS</a> du centre de développement Apple, connectez-vous avec votre ID Apple, cliquez sur **Identifiers**, sur **App IDs**, puis sur le signe **+** pour inscrire une nouvelle application.
    
    ![][102]

2. Entrez un nom pour votre application dans **Description**, entrez une valeur dans **Bundle Identifier** et retenez-la, activez l'option " Push Notifications " dans la section " App Services ", puis cliquez sur **Continue**. Cet exemple utilise l'ID **MobileServices.Quickstart**, mais vous ne pouvez pas réutiliser le même ID, car chaque ID d'application doit être unique pour chaque utilisateur. Ainsi, nous vous recommandons d'ajouter votre nom complet ou vos initiales après le nom de l'application. 

    ![][103]
   
    L'ID de votre application est généré et vous êtes invité à **Envoyer** les informations. Cliquez sur **Submit**.
   
    ![][104] 
   
    Lorsque vous avez cliqué sur **Submit**, le message **Registration complete** s'affiche, comme présenté ci-dessous. Cliquez sur **Done**.
   
    ![][105]    

3. Recherchez l'ID de l'application que vous venez de créer, puis cliquez sur sa ligne. 

    ![][106]
   
    Le fait de cliquer sur l'ID de l'application affiche les informations détaillées de cette application et de l'ID d'application. Cliquez sur le bouton **Settings**.
   
    ![][107] 
   
4. Faites défiler jusqu'en bas de l'écran, puis cliquez sur le bouton **Create Certificate** sous la section **Development Push SSL Certificate**.

    ![][108] 

    Ceci affiche l'Assistant " Add iOS Certificate ".
   
Remarque : Ce didacticiel utilise un certificat de développement. Le même processus est utilisé lors de l'inscription d'un certificat de production. Assurez-vous simplement que vous avez défini le même type de certificat lorsque vous avez téléchargé le certificat vers Mobile Services.

5. Cliquez sur **Choose File**, accédez à l'emplacement où vous avez enregistré le fichier de demande de signature de certificat que vous avez créé lors de la première tâche, puis cliquez sur **Generate**. 

    ![][110]
  
6. Une fois le certificat créé par le portail, cliquez sur le bouton **Download**, puis sur **Done**.
 
    ![][111]  

    Ceci entraîne le téléchargement du certificat de signature et l'enregistre sur votre ordinateur dans le dossier Téléchargements. 

    ![][9] 

Remarque : Par défaut, le fichier téléchargé est un certificat de développement nommé <strong>aps_development.cer</strong>.

7. Double-cliquez sur le certificat Push téléchargé **aps_development.cer**.

    Ceci installe le nouveau certificat dans le Trousseau d'accès, comme indiqué ci-dessous :

    ![][10]

Remarque : il se peut que le nom de votre certificat soit différent. Il portera toutefois le préfixe Apple <strong>Development iOS Push Notification Services:</strong>.

Plus tard, vous devrez utiliser ce certificat pour générer un fichier .p12 et le télécharger vers Mobile Services pour activer l'authentification avec APNS.

## <a name="profile"></a>Création d'un profil de mise en service pour l'application
 
1. Une fois de retour sur le <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portail de mise en service iOS</a>, sélectionnez **Provisioning Profiles**, **All**, puis cliquez sur le signe **+** pour créer un profil. Ainsi, l'Assistant **Add iOS Provisioning Profile** s'affiche.

    ![][112]

2. Sélectionnez **iOS App Development** sous **Development** en tant que type de profil de mise en service, puis cliquez sur **Continue**.

3. Ensuite, sélectionnez l'ID de l'application de démarrage rapide Mobile Services dans la liste déroulante **App ID**, puis cliquez sur **Continue**.

    ![][113]

4. Dans l'écran **Select certificates**, sélectionnez le certificat que vous avez créé précédemment, puis cliquez sur **Continue**.

    ![][114]

5. Ensuite, sélectionnez les appareils (**Devices**) à utiliser pour le test, puis cliquez sur **Continue**.
  
    ![][115]

6. Enfin, choisissez le nom du profil dans **Profile Name**, cliquez sur **Generate**, puis sur **Done**.

    ![][116]

    Ceci entraîne la création d'un profil de mise en service.

    ![][117]

## <a name="configure-mobileServices"></a>Configuration de Mobile Services pour l'envoi de requêtes Push

Après l'inscription de votre application auprès d'APNS et une fois votre projet configuré, vous devez configurer votre service mobile pour l'intégrer à APNS.

1. Dans Trousseaux d'accès, cliquez avec le bouton droit sur le nouveau certificat, cliquez sur **Exporter**, donnez un nom à votre fichier, sélectionnez le format **.p12**, puis cliquez sur **Enregistrer**.

    ![][28]

    Notez le nom du fichier et l'emplacement du certificat exporté.

2. Connectez-vous au [portail de gestion Azure], cliquez sur **Mobile Services**, puis sur votre application.

    ![][18]

3. Cliquez sur l'onglet **Push**, puis sur **Télécharger** sous **Paramètres de notification push Apple**.

    ![][19]

    Ceci affiche la boîte de dialogue Télécharger le certificat.

4. Cliquez sur **Fichier**, sélectionnez le fichier .p12 du certificat exporté, entrez le **Mot de passe**, vérifiez que le bon **Mode** est sélectionné, cliquez sur l'icône en forme de coche, puis cliquez sur **Enregistrer**.

    ![][20] 

Votre service mobile est maintenant configuré pour fonctionner avec APNS.

## <a name="configure-app"></a>Configuration de votre application Xamarin.iOS

1. Dans Xamarin.Studio, ouvrez **Info.plist** et mettez à jour la valeur de **Bundle Identifier** avec l'ID que vous avez créé précédemment.

    ![][121]

2. Descendez jusqu'à **Background Modes**, puis activez les cases à cocher **Enable Background Modes** et **Remote notifications**. 

    ![][122]

3. Double-cliquez sur votre projet dans Solution Panel pour ouvrir les **options de projet**.

4.  Sélectionnez **iOS Bundle Signing** sous **Build**, puis sélectionnez les éléments **Identity** et **Provisioning profile** que vous venez de configurer pour ce projet. 

    ![][120]

    Ceci vérifie que le projet Xamarin utilise le nouveau profil pour la signature du code. Pour obtenir la documentation officielle de l'approvisionnement des appareils Xamarin, consultez la page [Approvisionnement des appareils Xamarin].

## <a name="add-push"></a>Ajout de notifications Push à votre application

1. Dans Xamarin.Studio, ouvrez le fichier AppDelegate.cs, puis ajoutez la propriété suivante :

        public string DeviceToken { get; set; }

2. Ouvrez la classe **TodoItem** et ajoutez la propriété suivante :

        [JsonProperty(PropertyName = "deviceToken")]
        public string DeviceToken { get; set; }

3. Dans **QSTodoService**, remplacez la déclaration de client existante par :
        
        public MobileServiceClient client { get; private set; }

4. Ajoutez ensuite la méthode suivante pour que **AppDelegate** puisse acquérir le client ultérieurement pour enregistrer les notifications push :

        public MobileServiceClient GetClient {
            get{ 
                return client;
            }
        }

5. Dans **AppDelegate**, remplacez l'événement **FinishedLaunching** : 

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert 
                | UIUserNotificationType.Badge 
                | UIUserNotificationType.Sound, 
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings); 
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            return true;
        }

6. Dans **AppDelegate**, remplacez l'événement **RegisteredForRemoteNotifications** :

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            // Modify device token
            DeviceToken = deviceToken.Description;
            DeviceToken = DeviceToken.Trim ('<', '>').Replace (" ", "");

            // Get Mobile Services client
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            // Register for push with Mobile Services
            IEnumerable<string> tag = new List<string>() { "uniqueTag" };
            var push = client.GetPush ();
            push.RegisterNativeAsync (DeviceToken, tag);
        }

7. Dans **AppDelegate**, remplacez l'événement **ReceivedRemoteNotification** :

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

8. Dans **TodoListViewController**, modifiez l'action **OnAdd** pour que le jeton d'appareil soit stocké dans **AppDelegeate**, puis stockez-le dans la table **TodoItem** à ajouter.

        string deviceToken = ((AppDelegate)UIApplication.SharedApplication.Delegate).DeviceToken;

        var newItem = new TodoItem() 
        {
            Text = itemText.Text, 
            Complete = false,
            DeviceToken = deviceToken
        };

L'application est mise à jour et prend en charge les notifications Push.

## <a name="update-scripts"></a>Mise à jour du script d'insertion inscrit dans le portail de gestion

1. Dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItem**. 

    ![][21]

2. Dans **TodoItem**, cliquez sur l'onglet **Script** et sélectionnez **Insérer**.
   
    ![][22]

    La fonction appelée lors d'une insertion dans la table **TodoItem** s'affiche.

3. Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the 
            // app to be closed on the device to demonstrate toast notifications
            setTimeout(function() {
                push.apns.send("uniqueTag", {
                    alert: "Toast: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

    Ceci inscrit un nouveau script d'insertion, qui utilise l'[objet apns] pour envoyer une notification Push (le texte inséré) à l'appareil fourni dans la demande d'insertion. 

    <div class="dev-callout"><b>Remarque</b>
   <p>Ce script reporte l'envoi de la notification pour vous laisser le temps de fermer l'application pour recevoir une notification toast.</p>
   </div> 

## <a name="test"></a>Test des notifications Push dans votre application

1. Appuyez sur le bouton **Démarrer** pour générer le projet, puis démarrez l'application sur un appareil compatible iOS, et enfin cliquez sur **OK** pour accepter les notifications Push.

    ![][23]

    <div class="dev-callout"><b>Remarque</b>
    <p>Vous devez accepter explicitement les notifications Push de votre application. Cette demande s'effectue uniquement lors du premier démarrage de l'application.</p>
    </div>

2. Dans l'application, entrez un texte explicite, tel que _Nouvelle tâche Mobile Services_, puis cliquez sur l'icône Ajouter (**+**).

    ![][24]

3. Vérifiez que vous avez reçu une notification, puis cliquez sur **OK** pour la fermer.

    ![][25]

4. Répétez l'étape 2 et fermez immédiatement l'application, puis vérifiez que la notification toast suivante est affichée.

    ![][26]

Vous avez terminé ce didacticiel.

<!-- Anchors. -->
[Génération de la demande de signature de certificat]: #certificates
[Inscription de votre application et activation des notifications Push]: #register
[Création d'un profil de mise en service pour l'application]: #profile
[Configuration de Mobile Services]: #configure-mobileServices
[Configuration de l'application Xamarin.iOS]: #configure-app
[Mise à jour des scripts pour l'envoi de notifications Push]: #update-scripts
[Ajout de notifications Push à l'application]: #add-push
[Insertion de données pour recevoir des notifications]: #test

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

[120]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-20.png
[121]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-21.png
[122]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-22.png

[Xamarin.iOS Studio]: http://xamarin.com/platform
[Installer Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Portail d'approvisionnement iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Kit de développement logiciel (SDK) Mobile Services iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Services de notification Push Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-xamarin-ios
[Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-xamarin-ios
[Notifications Push pour les utilisateurs de l'application]: /fr-fr/develop/mobile/tutorials/push-notifications-to-users-ios
[Autorisation des utilisateurs avec des scripts]: /fr-fr/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Approvisionnement des appareils Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


[Portail de gestion Azure]: https://manage.windowsazure.com/
[objet apns]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Composant Azure Mobile Services]: http://components.xamarin.com/view/azure-mobile-services/
[Exemple de projet terminé]: http://go.microsoft.com/fwlink/p/?LinkId=331303
