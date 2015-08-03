
###Configuration de l’émulateur pour le test
Lorsque vous exécutez cette application dans l'émulateur, veillez à utiliser un AVD (appareil virtuel Android) qui prend en charge les API Google.

> [AZURE.IMPORTANT]Afin de recevoir des notifications Push, vous devez configurer un compte Google sur votre appareil virtuel Android (dans l'émulateur, accédez à **Paramètres**, puis cliquez sur **Ajouter un compte**). Assurez-vous également que l'émulateur est connecté à Internet.

1. Dans **Outils**, cliquez sur **Open Android Emulator Manager**, sélectionnez votre appareil, puis cliquez sur **Modifier**.

   	![Gestionnaire de périphérique virtuel Android](./media/mobile-services-android-push-notifications-test/notification-hub-create-android-app7.png)

2. Sélectionnez **API Google** dans **Cible**, puis cliquez sur **OK**.

   	![Modifier le périphérique virtuel Android](./media/mobile-services-android-push-notifications-test/notification-hub-create-android-app8.png)

3. Dans la barre d'outils supérieure, cliquez sur **Exécuter**, puis sélectionnez votre application. L’émulateur démarre et l’application est exécutée.

  L'application extrait le *registrationId* de GCM et s'inscrit auprès du concentrateur de notification.

###L’insertion d’un nouvel élément génère une notification.

1. Dans l'application, tapez un texte explicite, comme _Nouvelle tâche Mobile Services_ puis cliquez sur le bouton **Ajouter**.

2. Faites glisser votre doigt du haut vers le bas de l'écran pour ouvrir le centre de notifications de l'appareil et afficher la notification.

	![Afficher la notification dans le Centre de notifications](./media/mobile-services-android-push-notifications-test/notification-area-received.png)

<!---HONumber=July15_HO4-->