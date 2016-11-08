
### <a name="update-manifest-file-to-enable-notifications"></a>Mettre à jour le fichier manifeste pour activer les notifications
Copiez les ressources de messagerie in-app ci-dessous dans votre fichier Manifest.xml entre les balises `<application>` et `</application>`.

        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
            </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
            </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
            </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
            </intent-filter>
        </activity>
        <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
            </intent-filter>
        </receiver>
        <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
            <intent-filter>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
            </intent-filter>
        </receiver>

### <a name="specify-an-icon-for-notifications"></a>Spécifier une icône pour les notifications
Collez l’extrait de code XML suivant dans votre fichier Manifest.xml entre les balises `<application>` et `</application>`.

        <meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

Cela définit l’icône qui s’affiche à la fois dans le système et des notifications in-app. Facultative pour les notifications in-app, cette opération est toutefois obligatoire pour les notifications système. Android rejettera les notifications système avec des icônes non valides.

Assurez-vous d’utiliser une icône qui existe dans l’un des dossiers **drawable``engagement_close.png`` (comme **). **mipmap** n’est pas pris en charge.

> [!NOTE]
> Vous ne devez pas utiliser l’icône du **Lanceur** . Elle a une résolution différente et se trouve généralement dans les dossiers mipmap, que nous ne prenons pas en charge.
> 
> 

Pour les applications réelles, vous pouvez utiliser une icône adaptée aux notifications conformément aux [instructions sur la conception Android](http://developer.android.com/design/patterns/notifications.html).

> [!TIP]
> Pour veiller à utiliser des icônes de résolutions correctes, vous pouvez consulter [ces exemples](https://www.google.com/design/icons).
> Faites défiler jusqu’à la section **Notification**, cliquez sur une icône, puis sur `PNGS` pour télécharger le jeu d’icônes. Vous pouvez voir quels dossiers dessinables utiliser avec quelle résolution pour chaque version de l’icône.
> 
> 

### <a name="enable-your-app-to-receive-gcm-push-notifications"></a>Activer votre application pour recevoir des notifications Push GCM
1. Collez le code suivant dans votre fichier Manifest.xml entre les balises `<application>` et `</application>` après le remplacement du **Sender ID** obtenu à partir de votre console de projet Firebase. Le \n est intentionnel alors assurez-vous que le numéro de projet se termine avec ce dernier.
   
        <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
2. Collez le code ci-dessous dans votre fichier Manifest.xml entre les balises `<application>`et`</application>`. Remplacez le nom du package <Your package name>.
   
        <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
        android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
            </intent-filter>
        </receiver>
   
        <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<Your package name>" />
            </intent-filter>
        </receiver>
3. Ajoutez le dernier jeu d’autorisations mis en surbrillance avant la balise `<application>` . Remplacez `<Your package name>` par le nom réel du package de votre application.
   
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
        <permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

<!--HONumber=Oct16_HO2-->


