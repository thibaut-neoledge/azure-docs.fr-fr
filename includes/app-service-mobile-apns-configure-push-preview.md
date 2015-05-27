Après avoir inscrit votre application auprès d’APNS et configuré votre projet, vous devez configurer votre application mobile pour l’intégrer à APNS.

1. Dans le trousseau d’accès, cliquez avec le bouton droit sur le nouveau certificat de l’application de démarrage rapide dans **Clés** ou **Mes certificats**, cliquez sur **Exporter**, nommez votre fichier QuickstartPusher, sélectionnez le format **.p12**, puis cliquez sur **Enregistrer**.

   	![](./media/mobile-services-apns-configure-push/mobile-services-ios-push-step18.png)

    Notez le nom du fichier et l'emplacement du certificat exporté.

>[AZURE.NOTE]Ce didacticiel crée un fichier QuickstartPusher.p12. Il est possible que le nom de votre fichier et son emplacement sont différents.

2. Connectez-vous au [portail Azure en version préliminaire], sélectionnez **Parcourir** et **Mobile Apps**, puis cliquez sur votre application et sur Services de notification Push.

3. Dans Apple Push Notification Service, chargez votre certificat avec le fichier **.p12**, le mot de passe que vous lui avez associé, puis sélectionnez le mode souhaité.

Vous application mobile App Service est désormais configurée pour fonctionner avec APNS.

<!-- URLs. -->
[portail Azure en version préliminaire]: https://portal.azure.com/

<!--HONumber=54-->