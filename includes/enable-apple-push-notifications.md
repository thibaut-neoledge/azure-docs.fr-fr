
## <a id="register"></a>Inscrire une application pour les notifications push

* [Inscrivez un ID d'application pour votre application](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingProfiles/MaintainingProfiles.html#//apple_ref/doc/uid/TP40012582-CH30-SW991). Cochez la case **Notifications push** dans **App Services** lors de l'inscription de l'application.

> [AZURE.NOTE]Créez un ID d'application explicite (pas un ID d’application générique) et pour la valeur **Bundle ID**, utilisez exactement la valeur**Bundle ID** qui se trouve dans votre projet de démarrage rapide Xcode. Il est également essentiel de cocher l’option **Notifications push** dans **App Services** lors de l'inscription de l'ID d’application. Les notifications push ne fonctionneront pas si vous ne cochez pas cette case.

* Puis, [activez les notifications push pour l’app](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW6). Vous pouvez créer un certificat SSL de « développement » ou de « distribution » (n'oubliez pas de sélectionner l'option correspondante -- « Sandbox » ou « Production » -- ultérieurement dans le portail Mobile Services).

* Ensuite, [vérifiez que l'ID d'application active les notifications push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW8).

* Enfin, [actualisez les profils de mise en service dans le projet de démarrage rapide Xcode](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW10) puis [vérifiez que le profil de mise en service a été créé ou régénéré pour activer les notifications push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW12).

<!---HONumber=62-->