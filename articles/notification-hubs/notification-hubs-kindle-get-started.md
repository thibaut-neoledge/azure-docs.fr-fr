<properties
	pageTitle="Prendre en main Azure Notification Hubs pour les applications Kindle | Microsoft Azure"
	description="Ce didacticiel montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application Kindle."
	services="notification-hubs"
	documentationCenter=""
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-kindle"
	ms.devlang="Java"
	ms.topic="hero-article"
	ms.date="02/29/2016"
	ms.author="wesmc"/>

# Prendre en main Notification Hubs pour les applications Kindle

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Vue d’ensemble

Ce didacticiel vous montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application Kindle. Dans ce didacticiel, vous allez créer une application Kindle vierge recevant des notifications Push à l’aide d’Amazon Device Messaging (ADM).

##Composants requis

Ce didacticiel requiert les éléments suivants :

+ Obtenir le Kit de développement logiciel Android (nous supposons que vous utiliserez Eclipse) depuis le <a href="http://go.microsoft.com/fwlink/?LinkId=389797">site Android</a>.
+ Suivez les étapes de <a href="https://developer.amazon.com/appsandservices/resources/development-tools/ide-tools/tech-docs/01-setting-up-your-development-environment">Configuration de votre environnement de développement</a> pour configurer votre environnement de développement pour Kindle.

##Ajout d'une nouvelle application au portail des développeurs

1. Tout d’abord, créez une application dans le [portail des développeurs Amazon].

	![][0]

2. Copiez la **clé d'application**.

	![][1]

3. Dans le portail, cliquez sur le nom de votre application, puis cliquez sur l’onglet **Device Messaging**.

	![][2]

4. Cliquez sur **Create a New Security Profile**, puis créez un nouveau profil de sécurité (par exemple, le **profil de sécurité TestAdm**). Cliquez ensuite sur **Enregistrer**.

	![][3]

5. Cliquez sur **Profils de sécurité** pour afficher le profil de sécurité que vous venez de créer. Copiez les valeurs **ID client** et **Clé secrète client** pour une utilisation ultérieure.

	![][4]

## Création d'une clé API

1. Ouvrez une invite de commandes avec les privilèges Administrateur.
2. Accédez au dossier du Kit de développement logiciel (SDK) Android
3. Entrez la commande suivante :

    	keytool -list -v -alias androiddebugkey -keystore ./debug.keystore

	![][5]

4.  Pour le mot de passe **keystore**, tapez **android**.

5.  Copiez l'empreinte digitale **MD5**.
6.  De retour dans le portail des développeurs, dans l’onglet **Messagerie**, cliquez sur **Android/Kindle**et entrez le nom du package correspondant à votre application (par exemple, **com.sample.notificationhubtest**) ainsi que la valeur **MD5**, puis cliquez sur **Generate API Key**.

## Ajout d’informations d’identification au hub

Dans le portail, ajoutez la clé secrète client et l’ID client à l’onglet **Configurer** de votre Notification Hub.

## Configuration de votre application

> [AZURE.NOTE] Lors de la création d’une application, utilisez au moins l’API de niveau 17.

Ajoutez les bibliothèques ADM à votre projet Eclipse :

1. Pour obtenir la bibliothèque ADM, [téléchargez le Kit de développement logiciel (SDK)]. Extrayez le fichier zip du Kit de développement logiciel (SDK).
2. Dans Eclipse, cliquez avec le bouton droit sur votre projet, puis cliquez sur **Propriétés**. Sélectionnez **Java Build Path** sur la gauche, puis l’onglet **Libraries** en haut de l’écran. Cliquez sur **Add External Jar** et sélectionnez le fichier `\SDK\Android\DeviceMessaging\lib\amazon-device-messaging-*.jar` dans le répertoire dans lequel vous avez extrait le Kit de développement logiciel (SDK) Amazon.
3. Téléchargez le Kit de développement logiciel (SDK) Android Notification Hubs (lien).
4. Décompressez le package, puis faites glisser le fichier `notification-hubs-sdk.jar` dans le dossier `libs` dans Eclipse.

Modifiez le manifeste de l'application afin qu'il prenne en charge ADM :

1. Ajoutez l'espace de noms Amazon dans l'élément du manifeste racine :


		xmlns:amazon="http://schemas.amazon.com/apk/res/android"

2. Ajoutez les autorisations comme le premier élément sous l'élément du manifeste. Remplacez **[VOTRE NOM DE PACKAGE]** par le package utilisé pour créer votre application.

		<permission
	     android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
	     android:protectionLevel="signature" />

		<uses-permission android:name="android.permission.INTERNET"/>

		<uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />

		<!-- This permission allows your app access to receive push notifications
		from ADM. -->
		<uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />

		<!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
		<uses-permission android:name="android.permission.WAKE_LOCK" />

3. Insérez l'élément suivant comme le premier enfant de l'élément de l'application. N'oubliez pas de remplacer **[VOTRE NOM DE SERVICE]** par le nom de votre gestionnaire de messages ADM que vous créez dans la section suivante (y compris le package), puis remplacez **[VOTRE NOM DE PACKAGE]** par le nom de package avec lequel vous avez créé votre application.

		<amazon:enable-feature
		      android:name="com.amazon.device.messaging"
		             android:required="true"/>
		<service
		    android:name="[YOUR SERVICE NAME]"
		    android:exported="false" />

		<receiver
		    android:name="[YOUR SERVICE NAME]$Receiver" />

		    <!-- This permission ensures that only ADM can send your app registration broadcasts. -->
		    android:permission="com.amazon.device.messaging.permission.SEND" >

		    <!-- To interact with ADM, your app must listen for the following intents. -->
		    <intent-filter>
		  <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
		  <action android:name="com.amazon.device.messaging.intent.RECEIVE" />

		  <!-- Replace the name in the category tag with your app's package name. -->
		  <category android:name="[YOUR PACKAGE NAME]" />
		    </intent-filter>
		</receiver>

## Création de votre gestionnaire de messages ADM

1. Créez une classe qui hérite de `com.amazon.device.messaging.ADMMessageHandlerBase` et nommez-la `MyADMMessageHandler`, comme indiqué dans la figure suivante :

	![][6]

2. Ajoutez les instructions `import` suivantes :

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.support.v4.app.NotificationCompat;
		import com.amazon.device.messaging.ADMMessageReceiver;
		import com.microsoft.windowsazure.messaging.NotificationHub

3. Ajoutez le code suivant dans la classe que vous avez créée. N’oubliez pas de remplacer le nom du hub et la chaîne de connexion (écoute) :

		public static final int NOTIFICATION_ID = 1;
		private NotificationManager mNotificationManager;
		NotificationCompat.Builder builder;
      	private static NotificationHub hub;
		public static NotificationHub getNotificationHub(Context context) {
			Log.v("com.wa.hellokindlefire", "getNotificationHub");
			if (hub == null) {
				hub = new NotificationHub("[hub name]", "[listen connection string]", context);
			}
			return hub;
		}

		public MyADMMessageHandler() {
				super("MyADMMessageHandler");
			}

			public static class Receiver extends ADMMessageReceiver
    		{
        		public Receiver()
        		{
            		super(MyADMMessageHandler.class);
        		}
    		}

			private void sendNotification(String msg) {
				Context ctx = getApplicationContext();

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

4. Ajoutez le code suivant à la méthode `OnMessage()` :

		String nhMessage = intent.getExtras().getString("msg");
		sendNotification(nhMessage);

5. Ajoutez le code suivant à la méthode `OnRegistered` :

			try {
		getNotificationHub(getApplicationContext()).register(registrationId);
			} catch (Exception e) {
		Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
			}

6.	Ajoutez le code suivant à la méthode `OnUnregistered` :

			try {
				getNotificationHub(getApplicationContext()).unregister();
			} catch (Exception e) {
				Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
			}

7. Dans la méthode `MainActivity`, ajoutez ensuite l’instruction d’importation suivante :

		import com.amazon.device.messaging.ADM;

8. Ajoutez le code suivant à la fin de la méthode `OnCreate` :

		final ADM adm = new ADM(this);
		if (adm.getRegistrationId() == null)
		{
		   adm.startRegister();
		} else {
			new AsyncTask() {
			      @Override
			      protected Object doInBackground(Object... params) {
			         try {			        	 MyADMMessageHandler.getNotificationHub(getApplicationContext()).register(adm.getRegistrationId());
			         } catch (Exception e) {
			        	 Log.e("com.wa.hellokindlefire", "Failed registration with hub", e);
			        	 return e;
			         }
			         return null;
			     }
			   }.execute(null, null, null);
		}

## Ajout de la clé API à votre application

1. Dans Eclipse, créez un nouveau fichier nommé **api\_key.txt** dans les composants de répertoire de votre projet.
2. Ouvrez le fichier et copiez la clé API que vous avez générée dans le portail des développeurs Amazon.

## Exécution de l'application

1. Démarrez l'émulateur.
2. Dans l’émulateur, balayez l’écran depuis le haut et cliquez sur **Paramètres**, puis sur **Mon compte**, et inscrivez-vous avec un compte Amazon valide.
3. Dans Eclipse, exécutez l'application.

> [AZURE.NOTE] Si un problème se produit, vérifiez l'heure de l'émulateur (ou de l'appareil). L'heure doit être exacte. Pour modifier l'heure de l'émulateur Kindle, vous pouvez exécuter la commande suivante depuis le répertoire des outils de plateforme du Kit de développement logiciel (SDK) Android :

		adb shell  date -s "yyyymmdd.hhmmss"

## Envoi d'un message

Pour envoyer un message avec .NET :

		static void Main(string[] args)
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

            hub.SendAdmNativeNotificationAsync("{"data":{"msg" : "Hello from .NET!"}}").Wait();
        }

![][7]

<!-- URLs. -->
[portail des développeurs Amazon]: https://developer.amazon.com/home.html
[téléchargez le Kit de développement logiciel (SDK)]: https://developer.amazon.com/public/resources/development-tools/sdk

[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png

<!---HONumber=AcomDC_0302_2016-->