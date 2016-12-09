#### <a name="configuring-the-ios-project-in-xamarin-studio"></a>Configuration du projet iOS dans Xamarin Studio
1. Dans Xamarin.Studio, ouvrez **Info.plist** et mettez à jour la valeur de **Bundle Identifier** avec l’ID de groupe que vous avez créé précédemment avec votre nouvel ID d’application.
   
    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)
2. Descendez jusqu’à **Background Modes**, puis cochez les cases **Enable Background Modes** et **Remote notifications**. 
   
    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)
3. Double-cliquez sur votre projet dans Solution Panel pour ouvrir les **options de projet**.
4. Choisissez **iOS Bundle Signing** sous **Build**, puis sélectionnez les éléments **Identity** et **Provisioning profile** correspondants que vous venez de configurer pour ce projet. 
   
   ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)
   
   Cela permet de vérifier que le projet utilise le nouveau profil pour la signature du code. Pour obtenir la documentation officielle de l’approvisionnement des appareils Xamarin, consultez la page [Approvisionnement des appareils Xamarin].

#### <a name="configuring-the-ios-project-in-visual-studio"></a>Configuration du projet iOS dans Visual Studio
1. Dans Visual Studio, cliquez avec le bouton droit sur le projet, puis cliquez sur **Propriétés**.
2. Dans les pages de propriétés, cliquez sur l’onglet **iOS Application**, puis mettez à jour la valeur de la zone **Identifier** avec l’ID que vous avez créé précédemment.
   
    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)
3. Sous l’onglet **iOS Bundle Signing**, sélectionnez les éléments **Identity** et **Provisioning profile** correspondants que vous venez de configurer pour ce projet. 
   
    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)
   
    Cela permet de vérifier que le projet utilise le nouveau profil pour la signature du code. Pour obtenir la documentation officielle de l’approvisionnement des appareils Xamarin, consultez la page [Approvisionnement des appareils Xamarin].
4. Double-cliquez sur Info.plist pour l’ouvrir et ensuite activer **RemoteNotifications** dans Modes d’arrière-plan. 

[Approvisionnement des appareils Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/

<!--HONumber=Nov16_HO3-->


