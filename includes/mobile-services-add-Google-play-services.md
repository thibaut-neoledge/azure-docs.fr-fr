1. Ouvrez le Gestionnaire du Kit de développement logiciel Android en cliquant sur l'icône de la barre d'outils d'Android Studio (à l'extrémité droite : en cas de doute, placez le pointeur de la souris sur l'icône). Recherchez la version cible du Kit de développement logiciel Android utilisé pour votre projet, ouvrez-la et choisissez **Google APIs**, s'il n'est pas déjà installé.

2. Accédez à l'option **Extras**, développez-la et choisissez **Services Google Play**, comme indiqué ci-dessous. Cliquez sur **Packages d'installation**. Notez le chemin du Kit de développement logiciel (SDK) à utiliser à l'étape suivante. 

   	![](./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png)


3. Ouvrez le fichier **build.gradle** dans le répertoire de l'application.

	![](./media/mobile-services-android-get-started-push/android-studio-push-build-gradle.png)

4. Ajoutez la ligne suivante sous *dependencies* : 

   		compile 'com.google.android.gms:play-services-base:6.5.87'

5. Sous *defaultConfig*, changez *minSdkVersion* à 9.
 
6. Cliquez sur le bouton **Synchronisation du projet avec les fichiers Gradle** dans la barre d'outils.

7. Ouvrez **AndroidManifest.xml** et ajoutez cette balise à la balise *application*.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 





<!--HONumber=45--> 
