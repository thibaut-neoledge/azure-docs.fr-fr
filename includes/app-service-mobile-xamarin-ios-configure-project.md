#### <a name="configure-the-ios-project-in-xamarin-studio"></a>Configurer le projet iOS dans Xamarin Studio
1. Dans Xamarin.Studio, ouvrez **Info.plist**, puis mettez à jour la valeur du champ **Bundle Identifier (Identificateur d’offre groupée)** avec l’ID d’offre groupée que vous avez créé précédemment avec votre nouvel ID d’application.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)
2. Faites défiler l’écran jusqu’à **Background Modes (Modes d’arrière-plan)**. Cochez les cases **Enable Background Modes (Activer les modes d’arrière-plan)** et **Remote notifications (Notifications distantes)**.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)
3. Double-cliquez sur votre projet dans Solution Panel (Panneau de solutions) pour ouvrir **Project Options (Options de projet)**.
4. Sous **Build (Build)**, choisissez **iOS Bundle Signing (Signature d’offre groupée iOS)**, puis sélectionnez le profil d’identité et d’approvisionnement correspondant que vous venez de configurer pour ce projet.

   ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

   Cela permet de vérifier que le projet utilise le nouveau profil pour la signature du code. Pour obtenir la documentation officielle de l’approvisionnement des appareils Xamarin, consultez la page [Approvisionnement des appareils Xamarin].

#### <a name="configure-the-ios-project-in-visual-studio"></a>Configurer le projet iOS dans Visual Studio
1. Dans Visual Studio, cliquez avec le bouton droit sur le projet, puis cliquez sur **Propriétés**.
2. Dans les pages de propriétés, cliquez sur l’onglet **iOS Application**, puis mettez à jour la valeur de la zone **Identifier** avec l’ID que vous avez créé précédemment.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)
3. Dans l’onglet **iOS Bundle Signing (Signature d’offre groupée iOS)**, sélectionnez le profil d’identité et d’approvisionnement correspondant que vous venez de configurer pour ce projet.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Cela permet de vérifier que le projet utilise le nouveau profil pour la signature du code. Pour obtenir la documentation officielle de l’approvisionnement des appareils Xamarin, consultez la page [Approvisionnement des appareils Xamarin].
4. Double-cliquez sur Info.plist pour l’ouvrir, puis activez **RemoteNotifications (Notifications distantes)** sous **Modes d’arrière-plan**.

[Approvisionnement des appareils Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/
