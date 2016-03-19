1. Ouvrez le Gestionnaire du Kit de développement logiciel Android en cliquant sur l’icône de la barre d’outils d’Android Studio ou sur **Outils** -> **Android** -> **Gestionnaire du Kit de développement logiciel (SDK)** à partir du menu. Recherchez la version cible du Kit de développement logiciel Android utilisé pour votre projet, ouvrez-la et choisissez **Google APIs** s’il n’est pas déjà installé.

2. Allez à **Fichier**, **Structure de projet**. Puis activez les notifications Push dans **Notifications**.

3. Ouvrez **AndroidManifest.xml** et ajoutez cette balise à la balise *application*.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 

<!---HONumber=AcomDC_1203_2015-->