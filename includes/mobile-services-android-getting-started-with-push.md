1. Dans votre projet d’**application**, ouvrez le fichier `AndroidManifest.xml`. Dans le code des deux prochaines étapes, remplacez _`**my_app_package**`_ par le nom du package de l'application de votre projet, qui est la valeur de l'attribut `package` de la balise`manifest`. 

2. Ajoutez les nouvelles autorisations suivantes après l'élément `uses-permission` existant :

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3. Ajoutez le code suivant après la balise de début `application` :

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            						 	android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


4. Ajoutez la ligne suivante sous *dependencies* dans le fichier **build.gradle** dans le répertoire d’application, puis synchronisez à nouveau le fichier gradle avec le projet :

	    compile(group: 'com.microsoft.azure', name: 'azure-notifications-handler', version: '1.0.1', ext: 'jar')


5. Ouvrez le fichier *ToDoItemActivity.java*, puis ajoutez l’instruction import suivante :

		import com.microsoft.windowsazure.notifications.NotificationsManager;


6. Ajoutez la variable privée suivante à la classe : remplacez _`<PROJECT_NUMBER>`_ par le numéro de projet attribué par Google à votre application dans la procédure précédente :

		public static final String SENDER_ID = "<PROJECT_NUMBER>";

7. Modifiez la définition de *MobileServiceClient* de **private** pour **public static**, afin qu'elle ressemble à ceci :

		public static MobileServiceClient mClient;



8. Ensuite, nous devons ajouter une nouvelle classe pour gérer les notifications. Dans l’Explorateur de projets, ouvrez les nœuds **src** => **main** => **java**, cliquez avec le bouton droit sur le nœud de nom de package, cliquez sur **Nouveau**, puis cliquez sur **Classe Java**.

9. Dans **Nom**, tapez `MyHandler`, puis cliquez sur **OK**.


	![](./media/mobile-services-android-get-started-push/android-studio-create-class.png)


10. Dans le fichier MyHandler, remplacez la déclaration de classe par

		public class MyHandler extends NotificationsHandler {


11. Ajoutez les instructions import suivantes pour la classe `MyHandler` :

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.AsyncTask;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;

	
12. Ajoutez ensuite les membres suivants pour la classe `MyHandler` :

		public static final int NOTIFICATION_ID = 1;
		private NotificationManager mNotificationManager;
		NotificationCompat.Builder builder;
		Context ctx;


13. Dans la classe `MyHandler`, ajoutez le code suivant pour remplacer la méthode **onRegistered**, qui permet d’enregistrer votre appareil auprès du hub de notification du service mobile.

		@Override
		public void onRegistered(Context context,  final String gcmRegistrationId) {
		    super.onRegistered(context, gcmRegistrationId);
	
		    new AsyncTask<Void, Void, Void>() {
		    		    	
		    	protected Void doInBackground(Void... params) {
		    		try {
		    		    ToDoActivity.mClient.getPush().register(gcmRegistrationId, null);
		    		    return null;
	    		    }
	    		    catch(Exception e) { 
			    		// handle error    		    
	    		    }
					return null;  		    
	    		}
		    }.execute();
		}



14. Dans la classe `MyHandler`, ajoutez le code suivant pour remplacer la méthode **onReceive** qui entraîne l'affichage de la notification lors de sa réception.

		@Override
		public void onReceive(Context context, Bundle bundle) {
		    ctx = context;
		    String nhMessage = bundle.getString("message");
	
		    sendNotification(nhMessage);
		}
	
		private void sendNotification(String msg) {
			mNotificationManager = (NotificationManager)
		              ctx.getSystemService(Context.NOTIFICATION_SERVICE);
	
		    PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
		          new Intent(ctx, ToDoActivity.class), 0);
	
		    NotificationCompat.Builder mBuilder =
		          new NotificationCompat.Builder(ctx)
		          .setSmallIcon(R.drawable.ic_launcher)
		          .setContentTitle("Notification Hub Demo")
		          .setStyle(new NotificationCompat.BigTextStyle()
		                     .bigText(msg))
		          .setContentText(msg);
	
		     mBuilder.setContentIntent(contentIntent);
		     mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		}


15. Dans le fichier TodoActivity.java, mettez à jour la méthode **onCreate** de la classe *ToDoActivity* pour enregistrer la classe de gestionnaire de notification. Veillez à ajouter ce code après l'instanciation de *MobileServiceClient*.


		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    L'application est mise à jour et prend en charge les notifications Push.

<!-- URLs. -->
[Mobile Services Android SDK]: http://aka.ms/Iajk6q

<!---HONumber=62-->