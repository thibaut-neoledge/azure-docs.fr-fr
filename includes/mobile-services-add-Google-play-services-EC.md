

1. Ouvrez le Kit de développement logiciel (SDK) Android en cliquant sur **Window** dans la barre d'outils supérieure d'Eclipse. Recherchez la version cible du Kit de développement logiciel (SDK) Android spécifié dans les propriétés de votre projet, ouvrez-la et choisissez **Google APIs**.

2. Accédez à l'option **Extras**, développez-la et choisissez **Google Play Services**, comme indiqué ci-dessous. Cliquez sur **Install Packages**. Notez le chemin du Kit de développement logiciel (SDK) à utiliser à l'étape suivante. Redémarrez Eclipse.

   	![](./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png)


3. Installez le Kit de développement logiciel (SDK) des services Google Play dans votre projet. Dans Eclipse, cliquez sur **File**, puis sur **Import**. Sélectionnez **Android**, puis **Existing Android Code into Workspace** et cliquez sur **Next**. Cliquez sur **Parcourir**, naviguez jusqu'à l'emplacement du Kit de développement logiciel Android (généralement dans un dossier nommé `adt-bundle-windows-x86_64` dans le dossier qui contient Eclipse), accédez au sous-dossier `\extras\google\google_play_services\libproject` et sélectionnez le dossier google-play-services-lib, puis cliquez sur **OK**. Activez la case à cocher **Copy projects into workspace**, puis cliquez sur **Finish**.

	![](./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png)

4. Vous devez ensuite référencer la bibliothèque du Kit de développement logiciel (SDK) des services Google Play que vous venez d'importer à partir de votre projet.

5. Dans l'**Explorateur de package**, cliquez avec le bouton droit sur votre projet et choisissez *Propriétés*.
 
6. Dans la fenêtre Propriétés, sélectionnez Android sur la gauche.

	![](./media/mobile-services-android-get-started-push/mobile-google-set-project-properties.png)


7. Sous **Cible de génération du projet**, veillez à ce que `Google APIs x86` (ou `Google APIs` selon votre plateforme de développement) soit sélectionné pour le niveau de Kit de développement logiciel approprié.

 
8. Dans la section **Bibliothèque**, choisissez **Ajouter**, sélectionnez le projet des services Google Play (google-play-services-lib), puis cliquez sur **OK**.

9. Cliquez sur **Appliquer**, puis sur **OK**.

<!---HONumber=62-->