1. Ouvrez le Gestionnaire du Kit de développement logiciel Android en cliquant sur l’icône de la barre d’outils d’Android Studio ou sur **Outils** -> **Android** -> **Gestionnaire du Kit de développement logiciel (SDK)** à partir du menu. Recherchez la version cible du Kit de développement logiciel Android utilisé pour votre projet, ouvrez-la et choisissez **Google APIs** s’il n’est pas déjà installé.

2. Cliquez sur l’onglet **Outils SDK**. Si vous n’avez pas encore installé Google Play Service, cliquez sur **Services Google Play** comme indiqué ci-dessous. Cliquez ensuite sur **Appliquer** pour procéder à l’installation.
 
	Notez le chemin du Kit de développement logiciel (SDK). Vous l’utiliserez à une étape suivante.

   	![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)


3. Ouvrez le fichier **build.gradle** dans le répertoire de l’application.

	![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)

4. Ajoutez la ligne suivante sous *android*

		useLibrary 'org.apache.http.legacy'

5. Ajoutez la ligne suivante sous *dépendances* :

   		compile 'com.google.android.gms:play-services-base:6.5.87'

7. Sous *defaultConfig*, remplacez la valeur de *minSdkVersion* par 9.
 
8. Cliquez sur l’icône **Synchronisation du projet avec les fichiers Gradle** dans la barre d’outils.

9. Ouvrez **AndroidManifest.xml** et ajoutez cette balise à la balise *application*.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 

<!---HONumber=Nov15_HO1-->