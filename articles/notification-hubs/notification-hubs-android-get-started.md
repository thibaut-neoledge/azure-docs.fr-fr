<properties
	pageTitle="Prendre en main Azure Notification Hubs pour les applications Android | Microsoft Azure"
	description="Dans ce didacticiel, vous découvrirez comment utiliser Azure Notification Hubs pour envoyer des notifications Push à une application Android."
	services="notification-hubs"
	documentationCenter="android"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>
<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.date="10/23/2015"
	ms.author="wesmc"/>

# Prendre en main Notification Hubs pour les applications Android

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Vue d’ensemble

Ce didacticiel montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application Android. Vous allez créer une application Android vide qui reçoit des notifications Push à l’aide de Google Cloud Messaging (GCM). Une fois l’opération terminée, vous pouvez utiliser votre hub de notification pour diffuser des notifications Push sur tous les appareils exécutant votre application.

Ce didacticiel présente un scénario de diffusion simple utilisant les Notification Hubs. Suivez scrupuleusement le didacticiel suivant pour apprendre à utiliser Notification Hubs pour accéder à des utilisateurs et groupes d’appareils spécifiques.


## Avant de commencer

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Le code complet de ce didacticiel est disponible sur GitHub [ici](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStarted).


##Composants requis

Ce didacticiel requiert les éléments suivants :

+ Android Studio, que vous pouvez télécharger depuis <a href="http://go.microsoft.com/fwlink/?LinkId=389797">le site Android</a>.
+ Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-FR%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started%2F).


Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels Notification Hubs pour les applications Android.


##Créer un projet qui prend en charge Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]


##Configurer un nouveau hub de notification


[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


<ol start="7">
<li><p>Cliquez sur l’onglet <b>Configurer</b> dans la partie supérieure, saisissez la valeur de <b>clé d’API</b> obtenue à la section précédente, puis cliquez sur <b>Enregistrer</b>.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hub-configure-android.png)

Votre concentrateur de notification est à présent configuré pour GCM, et vous disposez des chaînes de connexion vous permettant d’inscrire votre application pour la réception de notifications et l’envoi de notifications Push.

##<a id="connecting-app"></a>Connexion de votre application au hub de notification

###Créer un projet Android

1. Dans Android Studio, démarrez un nouveau projet Android Studio.

   	![][13]

2. Choisissez le format **Phone and Tablet** et le kit de développement logiciel (SDK) minimal (**Minimum SDK**) que vous souhaitez prendre en charge. Cliquez ensuite sur **Next**.

   	![][14]

3. Choisissez **Activité vide** comme activité principale, cliquez sur **Suivant**, puis cliquez sur **Terminer**.

###Ajout de services Google Play au projet

[AZURE.INCLUDE [Ajout de services Google Play](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

###Ajout de code

1. Téléchargez le <a href="https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409">Kit de développement logiciel (SDK) Android pour Notification Hubs</a>. Décompressez le fichier .zip et copiez **notificationhubs\\notification-hubs-0.3.jar** et **notifications\\notifications-1.0.1.jar** dans le répertoire **app\\libs** de votre projet. Vous pouvez effectuer cette opération en faisant glisser les fichiers directement dans le dossier **libs** dans la fenêtre Project View d'Android Studio. Actualisez le dossier **libs**.


    > [AZURE.NOTE]Les numéros à la fin du nom du fichier peuvent changer dans les versions ultérieures du Kit de développement logiciel (SDK).

2. Configurez l’application afin d’obtenir un ID d’enregistrement depuis GCM, et utilisez-la pour inscrire l’instance d’application auprès du hub de notification.

	Dans le fichier AndroidManifest.xml, ajoutez les autorisations suivantes juste sous la balise `</application>`. Veillez à remplacer `<your package>` par le nom du package qui apparaît en haut du fichier AndroidManifest.xml (`com.example.testnotificationhubs` dans cet exemple).

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

		<permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
		<uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

3. Dans la classe **MainActivity**, ajoutez les instructions `import` suivantes au-dessus de la déclaration de la classe.

		import android.app.AlertDialog;
		import android.content.DialogInterface;
		import android.os.AsyncTask;
		import com.google.android.gms.gcm.*;
		import com.microsoft.windowsazure.messaging.*;
		import com.microsoft.windowsazure.notifications.NotificationsManager;


4. Ajoutez les membres privés suivants dans la partie supérieure de la classe.

		private String SENDER_ID = "<your project number>";
		private GoogleCloudMessaging gcm;
		private NotificationHub hub;
    	private String HubName = "<Enter Your Hub Name>";
		private String HubListenConnectionString = "<Your default listen connection string>";
	    private static Boolean isVisible = false;


	Veillez à mettre à jour les trois espaces réservés: * **SENDER\_ID** : définissez `SENDER_ID` sur le numéro de projet obtenu précédemment depuis le projet que vous avez créé dans [Google Cloud Console](http://cloud.google.com/console). * **HubListenConnectionString** : définissez `HubListenConnectionString` sur la chaîne de connexion **DefaultListenAccessSignature** pour votre hub. Vous pouvez copier cette chaîne de connexion en cliquant sur **Afficher la chaîne de connexion** sous l’onglet **Tableau de bord** de votre hub sur le [portail de gestion Azure]. * **HubName** : nom de votre hub de notification qui s’affiche en haut de la page dans Azure pour votre hub (**pas** l’URL complète). Par exemple, utilisez `"myhub"`.



5. Dans la méthode **OnCreate** de la classe **MainActivity**, ajoutez le code suivant pour effectuer l’inscription auprès du hub de notification lorsque l’activité est créée.

        MyHandler.mainActivity = this;
        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
        gcm = GoogleCloudMessaging.getInstance(this);
        hub = new NotificationHub(HubName, HubListenConnectionString, this);
        registerWithNotificationHubs();

6. Dans **MainActivity.java**, ajoutez le code ci-dessous pour la méthode **registerWithNotificationHubs()**. Cette méthode permet de signaler que l’inscription auprès du hub de notification et de Google Cloud Messaging a réussi :

    	@SuppressWarnings("unchecked")
    	private void registerWithNotificationHubs() {
        	new AsyncTask() {
            	@Override
            	protected Object doInBackground(Object... params) {
                	try {
                    	String regid = gcm.register(SENDER_ID);
                    DialogNotify("Registered Successfully","RegId : " +
						hub.register(regid).getRegistrationId());
                	} catch (Exception e) {
                    	DialogNotify("Exception",e.getMessage());
                    	return e;
                	}
                	return null;
            	}
        	}.execute(null, null, null);
    	}


7. Ajoutez la méthode `DialogNotify` à l’activité pour afficher la notification lorsque l’application est en cours d’exécution et visible. Substituez également `onStart`, `onPause`, `onResume` et `onStop` afin de déterminer si l’activité est visible pour afficher la boîte de dialogue.

	    @Override
	    protected void onStart() {
	        super.onStart();
	        isVisible = true;
	    }
	
	    @Override
	    protected void onPause() {
	        super.onPause();
	        isVisible = false;
	    }
	
	    @Override
	    protected void onResume() {
	        super.onResume();
	        isVisible = true;
	    }
	
	    @Override
	    protected void onStop() {
	        super.onStop();
	        isVisible = false;
	    }

		/**
		  * A modal AlertDialog for displaying a message on the UI thread
		  * when there's an exception or message to report.
		  *
		  * @param title   Title for the AlertDialog box.
		  * @param message The message displayed for the AlertDialog box.
		  */
    	public void DialogNotify(final String title,final String message)
    	{
	        if (isVisible == false)
	            return;

        	final AlertDialog.Builder dlg;
        	dlg = new AlertDialog.Builder(this);

        	runOnUiThread(new Runnable() {
            	@Override
            	public void run() {
                	AlertDialog dlgAlert = dlg.create();
                	dlgAlert.setTitle(title);
                	dlgAlert.setButton(DialogInterface.BUTTON_POSITIVE,
						(CharSequence) "OK",
						new DialogInterface.OnClickListener() {
                            public void onClick(DialogInterface dialog, int which) {
                                dialog.dismiss();
                            }
                        });
                	dlgAlert.setMessage(message);
                	dlgAlert.setCancelable(false);
                	dlgAlert.show();
            	}
        	});
    	}

8. Android n’affichant pas de notifications, vous devez écrire votre propre récepteur. Dans **AndroidManifest.xml**, ajoutez l'élément ci-après dans l'élément `<application>`.

	> [AZURE.NOTE]Remplacez l’espace réservé par votre nom de package.

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>


9. Dans la vue du projet, développez **app** -> **src** -> **main** -> **java**. Cliquez avec le bouton droit sur le dossier de votre package sous **java**, cliquez sur **Nouveau**, puis cliquez sur **Classe Java**.

	![][6]

10. Dans le champ **Nom** associé à la nouvelle classe, saisissez **MyHandler**, puis cliquez sur **OK**.


11. Ajoutez les instructions import suivantes au début de **MyHandler.java** :

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		import com.microsoft.windowsazure.notifications.NotificationsHandler;


12. Mettez à jour la déclaration de la classe comme suit pour faire de `MyHandler` une sous-classe de `com.microsoft.windowsazure.notifications.NotificationsHandler` comme indiqué ci-dessous.

		public class MyHandler extends NotificationsHandler {


13. Ajoutez le code suivant pour la classe `MyHandler`.

	Comme ce code remplace la méthode `OnReceive`, le gestionnaire affiche une `AlertDialog` pour répertorier les notifications reçues. Le gestionnaire envoie également la notification au gestionnaire de notifications Android en utilisant la méthode `sendNotification()`.

    	public static final int NOTIFICATION_ID = 1;
    	private NotificationManager mNotificationManager;
    	NotificationCompat.Builder builder;
    	Context ctx;

    	static public MainActivity mainActivity;

    	@Override
    	public void onReceive(Context context, Bundle bundle) {
        	ctx = context;
        	String nhMessage = bundle.getString("message");

        	sendNotification(nhMessage);
        	mainActivity.DialogNotify("Received Notification",nhMessage);
    	}

    	private void sendNotification(String msg) {
        	mNotificationManager = (NotificationManager)
                ctx.getSystemService(Context.NOTIFICATION_SERVICE);

        	PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);

			NotificationCompat.Builder mBuilder =
				new NotificationCompat.Builder(ctx)
					.setSmallIcon(R.mipmap.ic_launcher)
					.setContentTitle("Notification Hub Demo")
					.setStyle(new NotificationCompat.BigTextStyle()
					.bigText(msg))
					.setContentText(msg);

			mBuilder.setContentIntent(contentIntent);
			mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		}

14. Dans Android Studio, sur la barre de menus, cliquez sur **Build** -> **Rebuild Project** pour vous assurer qu’aucune erreur n’est détectée.

##Envoi de notifications



Vous pouvez tester la réception de notifications dans votre application en envoyant des notifications dans le portail Azure via l’onglet Déboguer du hub de notification, comme indiqué dans l’écran ci-dessous.

![][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## (Facultatif) Envoyer des notifications depuis l’application


1. Dans la vue de projet Android Studio, développez **App** -> **src** -> **main** -> **res** -> **layout**. Ouvrez le fichier de disposition **activity\_main.xml** et cliquez sur l'onglet **Texte** pour mettre à jour le texte du fichier. Mettez-le à jour avec le code suivant, qui ajoute deux nouveaux contrôles `Button` et `EditText` pour l’envoi des messages de notification au hub de notification. Ajoutez ce code en bas juste avant `</RelativeLayout>`.

	    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true"
        android:onClick="sendNotificationButtonOnClick" />

	    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_above="@+id/sendbutton"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="42dp"
        android:hint="@string/notification_message_hint" />

2. Dans la vue de projet Android Studio, développez **App** -> **src** -> **main** -> **res** -> **values**. Ouvrez le fichier **strings.xml** et ajoutez les valeurs de chaîne référencées par les nouveaux contrôles `Button` et `EditText`. Ajoutez-les en bas du fichier juste avant `</resources>`.

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


3. Dans votre fichier **MainActivity.java**, ajoutez les instructions `import` suivantes au-dessus de la classe `MainActivity`.

		import java.net.URLEncoder;
		import javax.crypto.Mac;
		import javax.crypto.spec.SecretKeySpec;

		import android.util.Base64;
		import android.view.View;
		import android.widget.EditText;

		import org.apache.http.HttpResponse;
		import org.apache.http.client.HttpClient;
		import org.apache.http.client.methods.HttpPost;
		import org.apache.http.entity.StringEntity;
		import org.apache.http.impl.client.DefaultHttpClient;


3. Dans votre fichier **MainActivity.java**, ajoutez les membres suivants en haut de la classe `MainActivity`.

	Mettez à jour `HubFullAccess` avec la chaîne de connexion **DefaultFullSharedAccessSignature** correspondant à votre hub. Vous pouvez copier cette chaîne de connexion depuis le [portail Azure] en cliquant sur **Afficher la chaîne de connexion** sous l’onglet **Tableau de bord** de votre hub de notification.

	    private String HubEndpoint = null;
	    private String HubSasKeyName = null;
	    private String HubSasKeyValue = null;
		private String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";

4. Votre activité conserve le nom du hub et la chaîne de connexion d'accès partagé complet pour le hub. Vous devez créer un jeton SaS (Software Access Signature) pour authentifier une demande POST d'envoi de messages à votre hub de notification. Cette opération est effectuée grâce à l’analyse des données clés de la chaîne de connexion, puis en créant le jeton SaS, comme indiqué sur la page [Concepts courants](http://msdn.microsoft.com/library/azure/dn495627.aspx) des informations de référence sur l’API REST.

	Dans **MainActivity.java**, ajoutez la méthode suivante à la classe `MainActivity` pour analyser votre chaîne de connexion.

	    /**
    	 * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx
    	 * to parse the connection string so a SaS authentication token can be
    	 * constructed.
    	 *
    	 * @param connectionString This must be the DefaultFullSharedAccess connection
    	 *                         string for this example.
	     */
	    private void ParseConnectionString(String connectionString)
	    {
	        String[] parts = connectionString.split(";");
	        if (parts.length != 3)
	            throw new RuntimeException("Error parsing connection string: "
	                    + connectionString);

	        for (int i = 0; i < parts.length; i++) {
	            if (parts[i].startsWith("Endpoint")) {
	                this.HubEndpoint = "https" + parts[i].substring(11);
	            } else if (parts[i].startsWith("SharedAccessKeyName")) {
	                this.HubSasKeyName = parts[i].substring(20);
	            } else if (parts[i].startsWith("SharedAccessKey")) {
	                this.HubSasKeyValue = parts[i].substring(16);
	            }
	        }
	    }

5. Dans **MainActivity.java**, ajoutez la méthode suivante à la classe `MainActivity` pour créer un jeton d'authentification SaS.

        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
         * construct a SaS token from the access key to authenticate a request.
         *
         * @param uri The unencoded resource URI string for this operation. The resource
         *            URI is the full URI of the Service Bus resource to which access is
         *            claimed. For example,
         *            "http://<namespace>.servicebus.windows.net/<hubName>"
         */
        private String generateSasToken(String uri) {

            String targetUri;
            try {
                targetUri = URLEncoder
                        .encode(uri.toString().toLowerCase(), "UTF-8")
                        .toLowerCase();

                long expiresOnDate = System.currentTimeMillis();
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60 * 1000;
                long expires = expiresOnDate / 1000;
                String toSign = targetUri + "\n" + expires;

                // Get an hmac_sha1 key from the raw key bytes
                byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
                SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");

                // Get an hmac_sha1 Mac instance and initialize with the signing key
                Mac mac = Mac.getInstance("HmacSHA256");
                mac.init(signingKey);

                // Compute the hmac on input data bytes
                byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));

            	// Using android.util.Base64 for Android Studio instead of
	            // Apache commons codec
                String signature = URLEncoder.encode(
                        Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");

                // Construct authorization string
                String token = "SharedAccessSignature sr=" + targetUri + "&sig="
                        + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
                return token;
            } catch (Exception e) {
                DialogNotify("Exception Generating SaS",e.getMessage().toString());
            }

            return null;
        }


6. Dans **MainActivity.java**, ajoutez la méthode suivante à la classe `MainActivity` pour gérer le clic sur le bouton **Envoyer une notification** et envoyer le message de notification au hub à l’aide de l’API REST.

        /**
         * Send Notification button click handler. This method parses the
         * DefaultFullSharedAccess connection string and generates a SaS token. The
         * token is added to the Authorization header on the POST request to the
         * notification hub. The text in the editTextNotificationMessage control
         * is added as the JSON body for the request to add a GCM message to the hub.
         *
         * @param v
         */
        public void sendNotificationButtonOnClick(View v) {
            EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
            final String json = "{"data":{"message":"" + notificationText.getText().toString() + ""}}";

            new Thread()
            {
                public void run()
                {
                    try
                    {
                        HttpClient client = new DefaultHttpClient();

                        // Based on reference documentation...
                        // http://msdn.microsoft.com/library/azure/dn223273.aspx
                        ParseConnectionString(HubFullAccess);
                        String url = HubEndpoint + HubName + "/messages/?api-version=2015-01";
                        HttpPost post = new HttpPost(url);

                        // Authenticate the POST request with the SaS token
                        post.setHeader("Authorization", generateSasToken(url));

                        // JSON content for GCM
                        post.setHeader("Content-Type", "application/json;charset=utf-8");

                        // Notification format should be GCM
                        post.setHeader("ServiceBusNotification-Format", "gcm");
                        post.setEntity(new StringEntity(json));

                        HttpResponse response = client.execute(post);
                    }
                    catch(Exception e)
                    {
                        DialogNotify("Exception",e.getMessage().toString());
                    }
                }
            }.start();
        }



##Test de l'application

####Test sur un émulateur

Si vous souhaitez effectuer les tests sur un émulateur, vérifiez que votre image d’émulateur prend en charge le niveau d’API Google que vous choisissez pour votre application. Si votre image ne prend pas en charge les API Google, l’exception **SERVICE\_NOT\_AVAILABLE** est levée.

Assurez-vous également que vous avez ajouté votre compte Google à l’émulateur en cours d’exécution sous **Paramètres** > **Comptes**. Sinon, vos tentatives d'inscription auprès de GCM peuvent entraîner la levée de l'exception **AUTHENTICATION\_FAILED**.

####Test de l'application

1. Exécutez l’application et notez qu’un ID d’inscription apparaît quand l’inscription réussit.

   	![][18]

2. Entrez le message de notification à envoyer à tous les appareils Android inscrits auprès du hub.

   	![][19]

3. Appuyez sur **Envoyer une notification**. Tous les appareils sur lesquels l'application est en cours d'exécution affichent une `AlertDialog` comportant le message de notification. Les appareils sur lesquels l'application n'est pas en cours d'exécution, mais qui ont déjà été inscrits aux notifications, reçoivent une notification dans le gestionnaire de notifications. Vous pouvez afficher les notifications en effectuant un balayage vers le bas depuis l’angle supérieur gauche.

   	![][21]

##Étapes suivantes

Dans cet exemple simple, vous avez envoyé des notifications à tous vos appareils Windows en utilisant le portail ou une application de console. Nous vous recommandons de consulter le didacticiel [Utiliser Notification Hubs pour envoyer des notifications Push aux utilisateurs] comme prochaine étape. Il vous expliquera comment envoyer des notifications à partir d’un serveur principal ASP.NET en utilisant des balises pour cibler des utilisateurs spécifiques.

Pour segmenter vos utilisateurs par groupes d'intérêt, consultez la page [Utilisation des Notification Hubs pour diffuser les dernières nouvelles].

Pour obtenir des informations générales sur Notification Hubs, consultez la page [Recommandations relatives à Notification Hubs].




<!-- Images. -->
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png

[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-new-project.png
[14]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-choose-form-factor.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png

[18]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-registered.png
[19]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-set-message.png

[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-received-message.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png

[31]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-add-ui.png


<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[portail Azure]: https://manage.windowsazure.com/
[portail de gestion Azure]: https://manage.windowsazure.com/
[Recommandations relatives à Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Utiliser Notification Hubs pour envoyer des notifications Push aux utilisateurs]: notification-hubs-aspnet-backend-android-notify-users.md
[Utilisation des Notification Hubs pour diffuser les dernières nouvelles]: notification-hubs-aspnet-backend-android-breaking-news.md

<!---HONumber=Nov15_HO1-->