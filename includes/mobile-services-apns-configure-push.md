
* Pour exporter au format .p12 le certificat que vous avez téléchargé à l'étape précédente, suivez les étapes décrites dans l'article [Installation d'une identité de signature SSL du client sur le serveur](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW16).

* Dans le portail Azure Classic, cliquez sur **Mobile Services** > votre application > l’onglet **Push** > **Paramètres de notification push Apple** > **Télécharger**. Téléchargez le fichier .p12, en vous assurant que le bon **Mode** est sélectionné (Bac à sable ou Production, selon que le certificat client SSL que vous avez généré était de type Développement ou Distribution.) Votre service mobile est désormais configuré et prêt à fonctionner avec les notifications Push sur iOS !

<!---HONumber=AcomDC_0309_2016-->