###Dans Xamarin Studio

1. Dans Xamarin.Studio, ouvrez **Info.plist** et mettez à jour la valeur de **Bundle Identifier** avec l’ID que vous avez créé précédemment.

    ![][121]

2. Descendez jusqu’à **Background Modes**, puis activez les cases à cocher **Enable Background Modes** et **Remote notifications**.

    ![][122]

3. Double-cliquez sur votre projet dans Solution Panel pour ouvrir les **options de projet**.

4.  Sélectionnez **iOS Bundle Signing** sous **Build**, puis sélectionnez les éléments **Identity** et **Provisioning profile** que vous venez de configurer pour ce projet.

    ![][120]

    Cela permet de vérifier que le projet utilise le nouveau profil pour la signature du code. Pour obtenir la documentation officielle de l’approvisionnement des appareils Xamarin, consultez la page [Approvisionnement des appareils Xamarin].

### Dans Visual Studio

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet, puis cliquez sur **Propriétés**.

3. Dans les pages de propriétés, cliquez sur l’onglet **iOS Application** et mettez à jour la valeur de **Identifier** avec l’ID que vous avez créé précédemment.

    ![][123]

4. Sous l’onglet **iOS Bundle Signing**, sélectionnez les éléments **Identity** et **Provisioning profile** correspondants que vous venez de configurer pour ce projet.

    ![][124]

    Cela permet de vérifier que le projet utilise le nouveau profil pour la signature du code. Pour obtenir la documentation officielle de l’approvisionnement des appareils Xamarin, consultez la page [Approvisionnement des appareils Xamarin].

[120]:./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png
[121]:./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png
[122]:./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png
[123]:./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png
[124]:./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png

[Approvisionnement des appareils Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/

<!----HONumber=August15_HO8-->