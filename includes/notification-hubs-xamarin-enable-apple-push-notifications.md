

Pour enregistrer l’application de notifications Push viaApple Push Notification Service (APNS), vous devez créer un nouveau certificat Push, un ID d’application et profil de configuration pour le projet sur le portail des développeurs d’Apple. L’ID d’application contient les paramètres de configuration qui permettent à votre application d’envoyer et de recevoir des notifications Push. Ces paramètres incluent le certificat de notification Push nécessaire pour l’authentification auprès d’Apple Push Notification Service (APNS) lors de l’envoi et la réception des notifications Push. Pour plus d’informations sur ces concepts, consultez la documentation officielle d’[Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584).


####Génération d’un fichier de demande de signature de certificat pour le certificat Push.

Ces étapes vous guident dans la création d’une requête de signature de certificat. Il doit servir à générer un certificat Push à utiliser avec APNS.

1. Sur votre Mac, exécutez l’outil Trousseaux d’accès. Il peut être ouvert à partir de dossiers **Utilitaires** ou du dossier **Autre** sur Launch Pad.

2. Cliquez sur **Trousseaux d'accès**, développez **Assistant de certification**, puis cliquez sur **Demander un certificat à une autorité de certification**.

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. Sélectionnez votre **Adresse de messagerie de l'utilisateur** et votre **Nom commun**, vérifiez que la case à cocher **Enregistré sur le disque** est activée, puis cliquez sur **Continuer**. Laissez le champ **Adresse de messagerie d'autorité de certification** vide, car il n'est pas requis.

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Entrez un nom pour le fichier de demande de signature de certificat dans **Enregistrer sous**, sélectionnez l'emplacement dans **Où**, puis cliquez sur **Enregistrer**.

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-save-csr.png)

  	Ceci enregistre le fichier de demande de signature de certificat dans l'emplacement sélectionné : l'emplacement par défaut est dans le Bureau. Notez l'emplacement sélectionné pour ce fichier.


####Inscription de votre application pour les notifications Push

Créez un nouvel ID d’application explicite pour votre application auprès d’Apple et configurez-le également pour les notifications Push.

1. Accédez au [Portail de mise en service iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) du centre de développement Apple, connectez-vous avec votre ID Apple, cliquez sur **Identifiers**, sur **ID App**, et enfin, sur le signe **+** pour inscrire une nouvelle application.

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Mettez à jour les trois champs suivants pour votre nouvelle application, puis cliquez sur **Continuer** :

	* **Nom** : tapez un nom descriptif pour votre application dans le champ **Nom** de la section **Description de l’ID d’application**.
	
	* **Identificateur d’offres groupées** : sous la section **ID d’application spécifique**, saisissez une valeur **Identificateur d’offres groupées** sous la forme `<Organization Identifier>.<Product Name>` comme indiqué dans le [Guide de distribution d’application](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8). Cela correspond en principe à ce qui est également utilisé dans le projet XCode ou Xamarin de votre application.
	 
	* **Notifications Push** : cochez l’option **Notifications Push** dans la section **App Services**.

	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

3.	Sur l’écran Confirmer votre ID d’application, consultez le paramètre et après vérification, cliquez sur **Envoyer**.

4. 	Une fois l’ID d’application transmis, le message **Inscription terminée** s’affiche, comme indiqué ci-dessous. Cliquez sur **Done**.

5. Dans le centre de développement, recherchez l’ID de l’application que vous venez de créer, puis cliquez sur la ligne correspondante. Le fait de cliquer sur la ligne d’ID de l’application affiche les informations détaillées de cette application. Cliquez sur le bouton **Modifier** au bas de l’écran.

6. Faites défiler jusqu'en bas de l'écran, puis cliquez sur le bouton **Create Certificate...** sous la section **Development Push SSL Certificate**.

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

   	Cette opération permet d’afficher l’Assistant « Add iOS Certificate ».

    > [AZURE.NOTE]Ce didacticiel utilise un certificat de développement. Le même processus est utilisé lors de l’inscription d’un certificat de production. Assurez-vous simplement que vous utilisez le même type de certificat quand vous envoyez des notifications.

7. Cliquez sur **Choose File**, accédez à l’emplacement où vous avez enregistré le fichier de demande de signature de votre certificat Push. Cliquez ensuite sur **Générer**.

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

8. Une fois le certificat créé par le portail, cliquez sur le bouton **Download**.

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

   	Ceci entraîne le téléchargement du certificat de signature et l'enregistre sur votre ordinateur dans le dossier Téléchargements.

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [AZURE.NOTE]Par défaut, le fichier téléchargé est un certificat de développement nommé **aps\_development.cer**.

9. Double-cliquez sur le certificat Push téléchargé **aps\_development.cer**. Ceci installe le nouveau certificat dans le Trousseau d'accès, comme indiqué ci-dessous :

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [AZURE.NOTE]Il se peut que le nom de votre certificat soit différent. Il portera toutefois le préfixe **Apple Development iOS Push Notification Services:**.

10. Dans Trousseau d’accès, cliquez avec le bouton droit sur le certificat Push que vous venez de créer dans la catégorie **Certificats**. Cliquez sur **Exporter**, nommez le fichier, sélectionnez le format **.p12**, puis cliquez sur **Enregistrer**.

	Notez le nom du fichier et l’emplacement du certificat .p12 exporté. Il servira à activer l’authentification avec APNS en le téléchargeant sur le portail Azure.



####Création d’un profil d’approvisionnement pour l’application

1. Une fois de retour dans le <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portail de mise en service iOS</a>, sélectionnez **Provisioning Profiles**, **All**, puis cliquez sur le bouton **+** pour créer un profil. Ceci démarre l'Assistant **Ajouter le profil d'approvisionnement iOS**

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. Sélectionnez **Développement d’application iOS** sous **Développement** en tant que type de profil d’approvisionnement, puis cliquez sur **Continuer**.


3. Ensuite, dans la liste déroulante **App ID**, sélectionnez l'ID d'application que vous venez de créer, puis cliquez sur **Continue**.

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)


4. Dans l’écran **Select certificates**, sélectionnez le certificat de développement habituellement utilisé pour la signature de code, puis cliquez sur **Continue**. Vous obtenez le certificat de signature, et non du certificat Push que vous venez de créer.

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)


5. Ensuite, sélectionnez les **Appareils** à utiliser pour le test, puis cliquez sur **Continuer**.

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)


6. Sélectionnez ensuite un nom pour le profil dans le **Nom du profil**, cliquez sur **Générer**.

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

<!---HONumber=AcomDC_1203_2015-->