Le service de notification Push Apple (APNS) utilise des certificats pour authentifier votre service mobile. Suivez ces instructions pour créer les certificats requis et les télécharger dans votre service mobile. Pour obtenir la documentation complète des fonctionnalités APNS, consultez la page [Service de notification Push Apple][].

## Génération du fichier de demande de signature de certificat

Vous devez d'abord générer le fichier de demande de signature de certificat (CSR, Certificate Signing Request), qu'Apple utilise pour générer un certificat signé.

1.  Dans le dossier Utilitaires, exécutez l'outil Trousseaux d'accès.

2.  Cliquez sur **Trousseaux d'accès**, développez **Assistant de certification**, puis cliquez sur **Demander un certificat à une autorité de certification**.

    ![][]

3.  Sélectionnez votre **Adresse de messagerie de l'utilisateur** et votre **Nom commun**, vérifiez que la case à cocher **Enregistré sur le disque** est activée, puis cliquez sur **Continuer**. Laissez le champ **Adresse de messagerie d'autorité de certification** vide, car il n'est pas requis.

    ![][1]

4.  Entrez un nom pour le fichier de demande de signature de certificat dans **Enregistrer sous**, sélectionnez l'emplacement dans **Où**, puis cliquez sur **Enregistrer**.

    ![][2]

    Ceci enregistre le fichier de demande de signature de certificat dans l'emplacement sélectionné : l'emplacement par défaut est dans le Bureau. Notez l'emplacement sélectionné pour ce fichier.

Ensuite, vous allez inscrire votre application auprès d'Apple, activer les notifications Push, puis télécharger ce fichier de demande de signature de certificat exporté pour créer un certificat Push.

## Inscription de votre application pour les notifications Push

Pour pouvoir envoyer des notifications Push vers une application iOS à partir des services mobiles, vous devez inscrire votre application auprès d'Apple, ainsi qu'aux notifications Push.

1.  Si vous n'avez pas déjà inscrit votre application, accédez au [Portail de mise en service iOS][] du centre de développement Apple, connectez-vous avec votre ID Apple, cliquez sur **Identifiers**, sur **App IDs**, puis sur le signe **+** pour inscrire une nouvelle application.

    ![][3]

2.  Entrez un nom pour votre application dans **Description**, entrez la valeur *MobileServices.Quickstart* dans **Bundle Identifier**, activez l'option "Push Notifications" dans la section "App Services", puis cliquez sur **Continue**. Cet exemple utilise l'ID **MobileServices.Quickstart**, mais vous ne pouvez pas réutiliser le même ID, car chaque ID d'application doit être unique pour chaque utilisateur. Ainsi, nous vous recommandons d'ajouter votre nom complet ou vos initiales après le nom de l'application.

    ![][4]

    L'ID de votre application est généré et vous êtes invité à **Envoyer** les informations. Cliquez sur **Envoyer**.

    ![][5]

    Lorsque vous avez cliqué sur **Submit**, le message **Registration complete** s'affiche, comme montré ci-dessous. Cliquez sur **Done**.

    ![][6]

    > [WACOM.NOTE] Si vous choisissez d'entrer une autre valeur **Bundle Identifier** que *MobileServices.Quickstart*, vous devez également mettre à jour la valeur d'identificateur de lot dans votre projet Xcode.

3.  Recherchez l'ID de l'application que vous venez de créer, puis cliquez sur sa ligne.

    ![][7]

    Le fait de cliquer sur l'ID de l'application affiche les informations détaillées de cette application et de l'ID d'application. Cliquez sur le bouton **Paramètres**.

    ![][8]

4.  Faites défiler jusqu'en bas de l'écran, puis cliquez sur le bouton **Create Certificate...** sous la section **Development Push SSL Certificate**.

    ![][9]

    Ceci affiche l'Assistant « Add iOS Certificate ».

    > [WACOM.NOTE] Ce didacticiel utilise un certificat de développement. Le même processus est utilisé lors de l'inscription d'un certificat de production. Assurez-vous simplement que vous avez défini le même type de certificat lorsque vous avez téléchargé le certificat vers Mobile Services.

5.  Cliquez sur **Choose File**, accédez à l'emplacement où vous avez enregistré le fichier de demande de signature de certificat que vous avez créé lors de la première tâche, puis cliquez sur **Generate**.

    ![][10]

6.  Une fois le certificat créé par le portail, cliquez sur le bouton **Download**, puis cliquez sur **Done**.

    ![][11]

    Ceci entraîne le téléchargement du certificat de signature et l'enregistre sur votre ordinateur dans le dossier Téléchargements.

    ![][12]

    > [WACOM.NOTE] Par défaut, le certificat de développement du fichier téléchargé est nommé **aps\_development.cer**.

7.  Double-cliquez sur le certificat Push téléchargé **aps\_development.cer**.

    Ceci installe le nouveau certificat dans le Trousseau d'accès, comme indiqué ci-dessous :

    ![][13]

    > [WACOM.NOTE] Le nom de votre certificat peut être différent, il aura tout de même le préfixe **Apple Development iOS Push Notification Services:**.

Plus tard, vous devrez utiliser ce certificat pour générer un fichier .p12 et le télécharger vers Mobile Services pour activer l'authentification avec APNS.

## Création d'un profil de mise en service pour l'application

1.  Une fois de retour sur le [Portail de mise en service iOS][Portail de mise en service iOS], sélectionnez **Provisioning Profiles**, **All**, puis cliquez sur le signe **+** pour créer un profil. Ceci démarre l'Assistant **Ajouter le profil d'approvisionnement iOS**

    ![][14]

2.  Sélectionnez **Développement d'application iOS** sous **Développement** en tant que type de profil d'approvisionnement, puis cliquez sur **Continuer**

    ![][15]

3.  Ensuite, sélectionnez l'ID d'application de l'application de démarrage rapide Mobile Services dans la liste déroulante **ID d'application**, puis cliquez sur **Continuer**.

    ![][16]

4.  Sur l'écran **Sélectionner les certificats**, sélectionnez le certificat créé plus tôt, puis cliquez sur **Continuer**.

    ![][17]

5.  Ensuite, sélectionnez les **Appareils** à utiliser pour le test, puis cliquez sur **Continuer**.

    ![][18]

6.  Ensuite, sélectionnez un nom pour le profil dans **Nom du profil**, cliquez sur **Générer**, puis sur **Terminé**

    ![][19]

    Ceci entraîne la création d'un profil de mise en service.

7.  Dans Xcode, ouvrez l'Organisateur, sélectionnez la vue Appareils, sélectionnez **Profils d'approvisionnement** dans la section **Bibliothèque** dans le volet de gauche, puis cliquez sur le bouton **Actualiser** en bas du volet du milieu.

    ![][20]

8.  Sous **Cibles**, cliquez sur **Démarrage rapide**, développez **Identité de signature du code**, puis sous **Débogage**, sélectionnez le nouveau profil.

    ![][21]

Ceci vérifie que le projet Xcode utilise le nouveau profil pour la signature du code. Vous devez ensuite télécharger le certificat vers Azure.

  [Service de notification Push Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
  []: ./media/enable-apple-push-notifications/mobile-services-ios-push-step5.png
  [1]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step6.png
  [2]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step7.png
  [Portail de mise en service iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
  [3]: ./media/enable-apple-push-notifications/mobile-services-ios-push-02.png
  [4]: ./media/enable-apple-push-notifications/mobile-services-ios-push-03.png
  [5]: ./media/enable-apple-push-notifications/mobile-services-ios-push-04.png
  [6]: ./media/enable-apple-push-notifications/mobile-services-ios-push-05.png
  [7]: ./media/enable-apple-push-notifications/mobile-services-ios-push-06.png
  [8]: ./media/enable-apple-push-notifications/mobile-services-ios-push-07.png
  [9]: ./media/enable-apple-push-notifications/mobile-services-ios-push-08.png
  [10]: ./media/enable-apple-push-notifications/mobile-services-ios-push-10.png
  [11]: ./media/enable-apple-push-notifications/mobile-services-ios-push-11.png
  [12]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step9.png
  [13]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step10.png
  [14]: ./media/enable-apple-push-notifications/mobile-services-ios-push-12.png
  [15]: ./media/enable-apple-push-notifications/mobile-services-ios-push-13.png
  [16]: ./media/enable-apple-push-notifications/mobile-services-ios-push-14.png
  [17]: ./media/enable-apple-push-notifications/mobile-services-ios-push-15.png
  [18]: ./media/enable-apple-push-notifications/mobile-services-ios-push-16.png
  [19]: ./media/enable-apple-push-notifications/mobile-services-ios-push-17.png
  [20]: ./media/enable-apple-push-notifications/mobile-services-ios-push-01.png
  [21]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step17.png
