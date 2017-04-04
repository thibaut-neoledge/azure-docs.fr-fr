
Pour enregistrer l’application de notifications Push via Apple Push Notification Service (APNS), vous devez créer un certificat Push, un ID d’application et un profil de configuration pour le projet sur le portail des développeurs d’Apple.

L’ID d’application contient les paramètres de configuration qui permettent à votre application d’envoyer et de recevoir des notifications Push. Ces paramètres incluent le certificat de notification Push nécessaire pour l’authentification auprès d’APNS lorsque l’application envoie et reçoit des notifications Push.

Pour plus d’informations sur ces concepts, consultez la documentation officielle [d’Apple Push Notification Service](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

## <a name="generate-the-certificate-signing-request-file-for-the-push-certificate"></a>Génération d’un fichier de demande de signature de certificat pour le certificat Push
Ces étapes vous guident tout au long du processus de création de la demande de signature de certificat, qui vous permet de générer un certificat Push utilisé avec APNS.

1. Sur votre Mac, exécutez l’outil Trousseaux d’accès. Il peut être ouvert à partir de dossiers **Utilitaires** ou du dossier **Autre** sur Launch Pad.

2. Sélectionnez **Trousseaux d’accès**, développez **Assistant de certification**, puis cliquez sur **Demander un certificat à une autorité de certification...**.

      ![Demande de certificat](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. Sélectionnez votre **adresse e-mail utilisateur** et un **nom commun**.

4. Vérifiez que **Saved to disk** (Enregistré sur le disque) est sélectionné, puis sélectionnez **Continuer**. Laissez le champ **Adresse de messagerie d’autorité de certification** vide, car il n’est pas requis.

      ![Informations sur le certificat](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Entrez un nom pour le fichier de demande de signature de certificat (CSR) dans **Enregistrer sous**.
5. Sélectionnez l’emplacement dans **Where** (Où), puis sélectionnez **Enregistrer**.

      ![Enregistrement de la demande de signature de certificat](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-save-csr.png)

      Cette opération enregistre le fichier CSR dans l’emplacement sélectionné. (L’emplacement par défaut est sur votre bureau). Notez l’emplacement que vous avez sélectionné pour ce fichier.

## <a name="register-your-app-for-push-notifications"></a>Inscription de votre application pour les notifications Push
Créez un nouvel ID d’application explicite pour votre application auprès d’Apple et configurez-le également pour les notifications Push.  

1. Accédez au [Portail de mise en service iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) dans le centre de développement Apple, puis connectez-vous avec votre ID Apple.

2. Sélectionnez **Identifiants**, puis sélectionnez **App IDs** (ID d’application).

3. Sélectionnez le symbole **+** pour inscrire une nouvelle application.

      ![Inscription d’une nouvelle application](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-ios-appids.png)

4. Mettez à jour les trois champs suivants pour votre nouvelle application, puis sélectionnez **Continuer** :

   * **Nom** : tapez un nom descriptif pour votre application dans la section **Description de l’ID d’application**.

   * **Identificateur d’offres groupées** : sous la section **ID d’application spécifique**, saisissez une valeur **Identificateur d’offres groupées** sous la forme `<Organization Identifier>.<Product Name>` comme décrit dans le [Guide de distribution d’application](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8). Cela correspond en principe à ce qui est utilisé dans le projet XCode, Xamarin ou Cordova de votre application.

   * **Notifications Push** : sélectionnez l’option **Notifications Push** dans la section **App Services**.

     ![Inscription de l’ID d’application](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

5. Sur **l’écran de confirmation de votre ID d’application**, passez en revue les paramètres. Après les avoir vérifiés, sélectionnez **Inscrire**.

6. Une fois le nouvel ID d’application transmis, le message **Inscription terminée** s’affiche. Sélectionnez **Terminé**.

7. Dans le centre de développement, sous **App IDs** (ID d’application), recherchez l’ID de l’application que vous avez créé, puis sélectionnez la ligne correspondante. Sélectionnez la ligne d’ID de l’application pour afficher les informations détaillées de cette application. Sélectionnez ensuite le bouton **Modifier** au bas de l’écran.

8. Faites défiler jusqu’en bas de l’écran, puis sélectionnez le bouton **Créer un certificat...** sous la section **Development Push SSL Certificate** (Certificat Push SSL de développement).

      ![Certificat Push SSL de développement](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

 Ceci affiche l'Assistant « Add iOS Certificate ».

   > [!NOTE]
   > Ce didacticiel utilise un certificat de développement. Vous utilisez le même processus que lors de l’inscription d’un certificat de production. Vérifiez que vous utilisez le même type de certificat que lorsque vous envoyez des notifications.
   >

9. Sélectionnez **Choose File** (Choisir un fichier), puis accédez à l’emplacement où vous avez enregistré le fichier CSR de votre certificat Push. Sélectionnez ensuite **Générer**.

      ![Génération du certificat](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

10. Une fois le certificat créé par le portail, sélectionnez le bouton **Télécharger**.

      ![Certificat prêt](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

       Ceci entraîne le téléchargement du certificat de signature et l'enregistre sur votre ordinateur dans le dossier Téléchargements.

      ![Dossier de téléchargement](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

   > [!NOTE]
   > Par défaut, le fichier téléchargé est un certificat de développement nommé **aps_development.cer**.
   >
   >
11. Double-cliquez sur le certificat Push téléchargé **aps_development.cer**. Cela installe le nouveau certificat dans le Trousseau d’accès, comme indiqué dans la capture d’écran ci-dessous :

       ![Trousseau d’accès](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

   > [!NOTE]
   > Il se peut que le nom de votre certificat soit différent. Il portera toutefois le préfixe **Apple Development iOS Push Notification Services**.
   >
   >
12. Dans **Trousseau d’accès**, sélectionnez le certificat Push que vous avez créé dans la catégorie **Certificats**. Sélectionnez **Exporter**, nommez le fichier, sélectionnez le format **.p12**, puis sélectionnez **Enregistrer**.

    Notez le nom du fichier et l’emplacement du certificat .p12 exporté. Vous l’utilisez pour activer l’authentification avec APNS en le téléchargeant sur le portail Azure Classic. Si le format **.p12** n’est pas disponible, il vous faudra peut-être redémarrer l’outil Trousseau d’accès.

## <a name="create-a-provisioning-profile-for-the-app"></a>Création d’un profil d’approvisionnement pour l’application
1. Une fois de retour dans le <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portail de mise en service iOS</a>, sélectionnez **Provisioning Profiles** (Profils d’approvisionnement), **All** (Tous), puis sélectionnez le bouton **+** pour créer un profil. Cela démarre l’outil **Ajouter le profil d’approvisionnement iOS**.

      ![Profils d’approvisionnement](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. Sélectionnez **Développement d’application iOS** sous **Développement** en tant que type de profil d’approvisionnement, puis sélectionnez **Continuer**.

3. Dans la liste déroulante **ID d’application**, sélectionnez l’ID d’application que vous avez créé, puis sélectionnez **Continuer**.

      ![Sélection d’un ID d’application](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

4. Dans l’écran **Select certificates** (Sélectionner des certificats), sélectionnez le certificat de développement utilisé pour la signature de code, puis sélectionnez **Continuer**. Vous obtenez le certificat de signature, et non le certificat Push que vous avez créé.

       ![Signing certificate](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

5. Sélectionnez les **appareils** à utiliser pour le test, puis sélectionnez **Continuer**.

     ![Ajout de profil d’approvisionnement](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

6. Enfin, sélectionnez un nom pour le profil dans le **Nom du profil** puis sélectionnez **Générer**.

      ![Attribution d’un nom au profil](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)
