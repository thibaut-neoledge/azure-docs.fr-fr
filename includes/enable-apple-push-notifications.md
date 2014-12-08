
Le service de notification Push Apple (APNS) utilise des certificats pour authentifier votre service mobile. Suivez ces instructions pour créer les certificats requis et les télécharger dans votre service mobile. Pour obtenir la documentation complète des fonctionnalités APNS, consultez la page [Service de notification Push Apple](http://go.microsoft.com/fwlink/p/?LinkId=272584).

## Génération du fichier de demande de signature de certificat

Vous devez d'abord générer le fichier de demande de signature de certificat (CSR, Certificate Signing Request), qu'Apple utilise pour générer un certificat signé.

1. Dans le dossier Utilitaires, exécutez l'outil Trousseaux d'accès.

2. Cliquez sur **Trousseaux d'accès**, développez **Assistant de certification**, puis cliquez sur **Demander un certificat à une autorité de certification**.

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step5.png)

3. Sélectionnez votre **Adresse de messagerie de l'utilisateur** et votre **Nom commun**, vérifiez que la case à cocher **Enregistré sur le disque** est activée, puis cliquez sur **Continuer**. Laissez le champ **Adresse de messagerie d'autorité de certification** vide, car il n'est pas requis.

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step6.png)

4. Entrez un nom pour le fichier de demande de signature de certificat dans **Enregistrer sous**, sélectionnez l'emplacement dans **Où**, puis cliquez sur **Enregistrer**.

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step7.png)

  	Ceci enregistre le fichier de demande de signature de certificat dans l'emplacement sélectionné : l'emplacement par défaut est dans le Bureau. Notez l'emplacement sélectionné pour ce fichier.

Ensuite, vous allez inscrire votre application auprès d'Apple, activer les notifications Push, puis télécharger ce fichier de demande de signature de certificat exporté pour créer un certificat Push.

## Inscription de votre application pour les notifications Push

Pour pouvoir envoyer des notifications Push vers une application iOS à partir des services mobiles, vous devez inscrire votre application auprès d'Apple, ainsi qu'aux notifications Push.  

1. Si vous n'avez pas déjà inscrit votre application, accédez au <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portail de mise en service iOS</a> du centre de développement Apple, connectez-vous avec votre ID Apple, cliquez sur **Identificateurs**, sur **ID de l'application**, puis sur le signe **+** pour inscrire une nouvelle application.

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-02.png)



> [WACOM.NOTE] Si vous choisissez d'entrer une valeur <strong>Bundle Identifier</strong> autre que <i>MobileServices.Quickstart</i>, vous devez également mettre à jour la valeur d'identificateur de lot dans votre projet Xcode. Nous vous recommandons d'utiliser la valeur d'identificateur de lot exacte que vous avez déjà utilisée dans votre projet de démarrage rapide.

2. Entrez un nom pour votre application dans **Description**, entrez la valeur _MobileServices.Quickstart_ dans **Identificateur de lot**, activez l'option " Notifications de transmission " dans la section " App Services ", puis cliquez sur **Continuer**. Cet exemple utilise l'ID **MobileServices.Quickstart**, mais vous ne pouvez pas réutiliser le même ID, car chaque ID d'application doit être unique pour chaque utilisateur. Ainsi, nous vous recommandons d'ajouter votre nom complet ou vos initiales après le nom de l'application.


    ![](./media/enable-apple-push-notifications/mobile-services-ios-push-03.png)

   	L'ID de votre application est généré et vous êtes invité à **Envoyer** les informations. Cliquez sur **Envoyer**.


    ![](./media/enable-apple-push-notifications/mobile-services-ios-push-04.png)


   	Lorsque vous avez cliqué sur **Envoyer**, le message **Inscription terminée** s'affiche, comme montré ci-dessous. Cliquez sur **Terminé**.


    ![](./media/enable-apple-push-notifications/mobile-services-ios-push-05.png)


3. Recherchez l'ID de l'application que vous venez de créer, puis cliquez sur sa ligne.

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-06.png)

   	Le fait de cliquer sur l'ID de l'application affiche les informations détaillées de cette application et de l'ID d'application. Cliquez sur le bouton **Paramètres**.

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-07.png)

4. Faites défiler jusqu'en bas de l'écran, puis cliquez sur le bouton **Create Certificate...** sous la section **Development Push SSL Certificate**.

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-08.png)

   	Ceci affiche l'Assistant " Add iOS Certificate ".

    > [WACOM.NOTE] Ce didacticiel utilise un certificat de développement. Le même processus est utilisé lors de l'inscription d'un certificat de production. Assurez-vous simplement que vous avez défini le même type de certificat lorsque vous avez téléchargé le certificat vers Mobile Services.

5. Cliquez sur **Choisir un fichier**, accédez à l'emplacement où vous avez enregistré le fichier de demande de signature de certificat que vous avez créé lors de la première tâche, puis cliquez sur **Générer**.

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-10.png)

6. Une fois le certificat créé par le portail, cliquez sur le bouton **Télécharger**, puis cliquez sur **Terminé**.

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-11.png)

   	Ceci entraîne le téléchargement du certificat de signature et l'enregistre sur votre ordinateur dans le dossier Téléchargements.

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step9.png)

    > [WACOM.NOTE] Par défaut, le fichier téléchargé est un certificat de développement nommé **aps_development.cer**.

7. Double-cliquez sur le certificat Push téléchargé **aps_development.cer**.

   	Ceci installe le nouveau certificat dans le Trousseau d'accès, comme indiqué ci-dessous :

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step10.png)

    > [WACOM.NOTE] Il se peut que le nom de votre certificat soit différent. Il portera toutefois le préfixe **Apple Development iOS Push Notification Services:**.

Plus tard, vous devrez utiliser ce certificat pour générer un fichier .p12 et le télécharger vers Mobile Services pour activer l'authentification avec APNS.

## Création d'un profil de mise en service pour l'application

1. Une fois de retour sur le <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portail de mise en service iOS</a>, sélectionnez **Profils d'approvisionnement**, **Tous**, puis cliquez sur le signe **+** pour créer un profil. Ceci démarre l'Assistant **Ajouter le profil d'approvisionnement iOS**

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-12.png)

2. Sélectionnez **Développement d'application iOS** sous **Développement** en tant que type de profil d'approvisionnement, puis cliquez sur **Continuer**.

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-13.png)

3. Ensuite, sélectionnez l'ID d'application de l'application de démarrage rapide Mobile Services dans la liste déroulante **ID d'application**, puis cliquez sur **Continuer**.

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-14.png)

4. Sur l'écran **Sélectionner les certificats**, sélectionnez le certificat créé plus tôt, puis cliquez sur **Continuer**.

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-15.png)

5. Ensuite, sélectionnez les **Appareils** à utiliser pour le test, puis cliquez sur **Continuer**.

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-16.png)

6. Pour finir, sélectionnez un nom pour le profil dans **Nom du profil**, cliquez sur **Générer**, puis sur **Terminé**.

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-17.png)

  	Ceci entraîne la création d'un profil de mise en service.

7. Dans Xcode, ouvrez l'Organisateur, sélectionnez la vue Appareils, sélectionnez **Profils d'approvisionnement** dans la section **Bibliothèque** dans le volet de gauche, puis cliquez sur le bouton **Actualiser** en bas du volet du milieu.

8. Ou bien, dans le menu Xcode, sélectionnez **Préférences**, puis **Comptes**. Dans le volet gauche, sélectionnez votre ID de développeur Apple. Cliquez sur le bouton **Afficher les détails** sur la droite. Dans la fenêtre indépendante, cliquez sur le bouton arrondi **Actualiser**. Cette action actualise la liste des profils de mise en service. Cette opération peut prendre quelques minutes. Nous vous recommandons de cliquer sur **Actualiser** 2 à 3 fois jusqu'à ce que votre nouveau profil d'approvisionnement apparaisse. En outre, vérifiez que l'identificateur de lot de ce projet Xcode est identique à l'identificateur de lot associé à l'ID d'application et au profil d'approvisionnement créé jusqu'à présent.

    ![](./media/enable-apple-push-notifications/mobile-services-ios-push-01.png)

9. Sous **Cibles**, cliquez sur **Démarrage rapide**, développez **Identité de signature du code**, puis sous **Débogage**, sélectionnez le nouveau profil. Ceci vérifie que le projet Xcode utilise le nouveau profil pour la signature du code. Vous devez ensuite télécharger le certificat vers Azure.

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step17.png)
