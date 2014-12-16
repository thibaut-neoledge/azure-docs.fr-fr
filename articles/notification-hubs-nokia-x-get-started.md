<properties urlDisplayName="Get Started" pageTitle="Prise en main d'Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="piyushjo" solutions="" manager="timlt" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-nokia-x" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="piyushjo" />
# Prise en main de Notification Hubs

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/fr-fr/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/fr-fr/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/fr-fr/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/fr-fr/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/fr-fr/documentation/articles/notification-hubs-nokia-x-get-started/" title="Nokia X" class="current">Nokia X</a><a href="/fr-fr/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/fr-fr/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Cette rubrique explique comment utiliser **Azure Notification Hubs** pour envoyer des notifications Push vers une application Android sur **Nokia X**. Dans ce didacticiel, vous allez créer une application Android vide qui reçoit des notifications Push à l'aide du service de notification Nokia. Une fois la création terminée, vous pouvez diffuser des notifications Push sur tous les appareils sur lesquels votre application est installée au moyen de votre concentrateur de notification.

Ce didacticiel vous familiarise avec les étapes de base pour activer les notifications Push :

* [Configuration du service de notification Nokia](#register)
* [Configuration de votre concentrateur de notification](#configure-hub)
* [Connexion de votre application au concentrateur de notification](#connect-hub)
* [Envoi d'une notification vers votre application](#send)
* [Test de votre application](#test-app)

<div class="dev-callout"><strong>Remarque</strong> <p>Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Version d'évaluation gratuite d'Azure</a>.</p></div>

Ce didacticiel requiert les éléments suivants :

1.	L'environnement de développement Nokia X que vous pouvez configurer en suivant les instructions indiquées <a href="http://developer.nokia.com/resources/library/nokia-x/getting-started/environment-setup.html">ici</a>. Veillez à installer les packages spécifiques de Nokia X et à configurer l'émulateur Nokia X en suivant les instructions. 
2.	La configuration de l'appareil Nokia X (facultative) que vous pouvez définir en suivant les instructions indiquées <a href="http://developer.nokia.com/resources/library/nokia-x/getting-started/device-setup.html">ici</a>.
3.	Le Kit de développement logiciel (SDK) Android (nous présumons que vous utiliserez Eclipse), que vous pouvez télécharger <a href="http://go.microsoft.com/fwlink/?linkid=389797&clcid=0x409">ici</a>.
4.	Le Kit de développement logiciel (SDK) Mobile Services Android, que vous pouvez télécharger <a href="https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409">ici<a>. 

##<a id="register"></a>Configuration du service de notification Nokia

1. Connectez-vous à la <a href="https://console.push.nokia.com/ncm/Web/index.jsp">console de développement des API de Nokia Notifications</a> 

2. Accédez à l'onglet **Create services** (Créer des services) et créez un service en fournissant les informations suivantes : **Sender ID** (ID d'expéditeur) et **Service description** (Description du service).

	![][11]

3. Notez les valeurs **Sender ID** (ID d'expéditeur) et **Authorization Key** (Clé d'autorisation) une fois que le service a été créé. 

4. Vous pouvez accéder à l'onglet **My services** (Mes services) pour répertorier tous les services que vous avez créés avec les valeurs respectives **Sender ID** (ID d'expéditeur) et **Authorization Key** (Clé d'autorisation).

	![][12]

5. Pour plus d'informations, cliquez sur ce <a href="http://developer.nokia.com/resources/library/nokia-x/nokia-notifications/nokia-notifications-developer-guide.html">lien</a>. 

##<a id="configure-hub"></a>Configuration de votre concentrateur de notification

1. Connectez-vous au [portail de gestion Azure] et cliquez sur **+NOUVEAU** en bas de l'écran.

2. Cliquez sur **App Services**, **Service Bus**, **Notification Hub**, puis **Création rapide**.

	![][3]

3. Tapez un nom pour votre concentrateur de notification, sélectionnez la région et éventuellement l'espace de noms voulus, puis cliquez sur **Créer un concentrateur de notification**.

	![][2]

4. Accédez au concentrateur de notification que vous venez de créer. Cliquez sur l'onglet Service Bus sur le côté gauche et cliquez sur l'espace de noms où vous avez créé le concentrateur de notification, puis cliquez sur l'onglet Notification Hubs. 

	![][9]

5. Une fois dans le concentrateur de notification que vous avez créé, cliquez en haut sur l'onglet **Configurer**.

	![][7]

6. Faites défiler l'écran vers le bas, recherchez les **paramètres de notification nokia x**, collez la clé d'autorisation que vous avez obtenue du service de notification Nokia, puis cliquez sur le bouton **Enregistrer** en bas de la page.

	![][8]

7. Sélectionnez l'onglet **Tableau de bord** en haut et cliquez sur **Informations de connexion** en bas de la page. 

	![][10]

8.	Notez les deux chaînes de connexion SAP pour **DefaultListenSharedAccessSignature** et **DefaultFullSharedAccessSignature**, dont vous vous servirez plus loin dans ce didacticiel. 

##<a id="connect-hub"></a>Connexion de votre application au concentrateur de notification

###Création d'un projet Android

1. Dans Eclipse ADT, créez un projet Android en sélectionnant File (Fichier) -> New (Nouveau) -> Android Application (Application Android).

2. Assurez-vous que Minimum Required SDK (SDK minimum requis) est défini sur **API 16: Android 4.1 (Jelly Bean)** et que les deux entrées SDK suivantes sont définies sur la version la plus récente disponible. Sélectionnez Next (Suivant) et suivez l'Assistant en vous assurant que l'option **Create activity** (Créer une activité) est sélectionnée afin de créer une **activité vide**. Acceptez l'icône Lancer par défaut dans la zone suivante, puis cliquez sur **Terminer** dans la dernière zone.

	![][5]

3. Assurez-vous que la cible de génération du projet est définie correctement (Platform = 4.1.2 et API Level = 16 pour cet exemple). Cliquez avec le bouton droit sur Project (Projet), sélectionnez Properties (Propriétés), puis Android dans la boîte de dialogue Project Properties (Propriétés du projet). 
	
	![][13]

4.	Ajoutez le fichier JAR du service de notification Nokia à votre projet. Ce fichier **push.jar** de la bibliothèque d'aide de Nokia Notifications fournit une API semblable à la bibliothèque d'aide GCM. 
Accédez à Project Properties -> Java Build Path -> Libraries -> Add External JARs et ajoutez le fichier **push.jar** disponible dans **\extras\nokia\nokia_x_services\libs\nna\push.jar**. Le Javadoc de la bibliothèque est situé à l'emplacement où est installé Eclipse IDE : **\extras\nokia\nokia_x_services\javadocs\nna**.

5. Veillez également à copier cette bibliothèque push.jar dans le répertoire \libs de votre projet dans l'explorateur de package. 

6. Téléchargez le Kit de développement logiciel (SDK) Android Notification Hubs<a href="https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409">ici</a>. Décompressez le fichier .zip et copiez le fichier notificationhubs\notification-hubs-0.3.jar dans le répertoire \libs de votre projet dans l'Explorateur de package.

    <div class="dev-callout"><b>Remarque</b>
	<p>Les numéros à la fin du nom du fichier peuvent changer dans les versions ultérieures du Kit de développement logiciel (SDK).</p>
    </div>

###Ajout de code

1. Ouvrez le fichier **AndroidManifest.xml** et remplacez l'élément de l'application par les lignes de code suivantes en veillant à remplacer `[YourPackageName]` par le nom de votre package, que vous avez spécifié lors de la création de l'application Android.
		
		<!-- Push Notifications connects to Internet services. -->
	    <uses-permission android:name="android.permission.INTERNET" />
	
	    <!-- Keeps the processor from sleeping when a message is received. -->
	    <uses-permission android:name="android.permission.WAKE_LOCK" />
	
		 <!--
	     Creates a custom permission so only this app can receive its messages.
	
	     NOTE: the permission *must* be called PACKAGE.permission.C2D_MESSAGE,
	           where PACKAGE is the application's package name.
	    -->
	    <permission 
	        android:name="[YourPackageName].permission.C2D_MESSAGE" 
	        android:protectionLevel="signature" />
	    <uses-permission android:name="[YourPackageName].permission.C2D_MESSAGE" />
	
	    <uses-permission android:name="com.nokia.pushnotifications.permission.RECEIVE" />
	    
	    <application
	        android:allowBackup="true"
	        android:icon="@drawable/ic_launcher"
	        android:label="@string/app_name"
	        android:theme="@style/AppTheme" >
	        <activity
	            android:name="[YourPackageName].MainActivity"
	            android:label="@string/app_name" >
	            <intent-filter>
	                <action android:name="android.intent.action.MAIN" />
	                <category android:name="android.intent.category.LAUNCHER" />
	            </intent-filter>
	        </activity>
	        
	        <receiver
	            android:name="com.nokia.push.PushBroadcastReceiver"
	            android:permission="com.nokia.pushnotifications.permission.SEND" >
	            <intent-filter>
	                
					<!-- Receives the actual messages. -->
	                <action android:name="com.nokia.pushnotifications.intent.RECEIVE" />
	                <!-- Receives the registration id. -->
	                <action android:name="com.nokia.pushnotifications.intent.REGISTRATION" />
	                <category android:name="[YourPackageName]" />
	            </intent-filter>
	        </receiver>
	
	        <service android:name="[YourPackageName].PushIntentService" />
	    </application> 

2. Dans l'explorateur de package, cliquez avec le bouton droit sur le package (sous le nœud `src`), cliquez sur **Nouveau**, puis sur **Classe** et créez une classe appelée **ConfigurationSettings.java** 
![][6]

	Ajoutez-y ensuite le code suivant, qui définit les constantes utilisées dans l'exemple :

		public class ConfigurationSettings {
	    	public static String NotificationHubName = "";
	    	public static String NotificationHubConnectionString = "";
	    	public static String SenderId = "";
		}
	
	Définissez les constantes ci-dessus avec la valeur **SenderId** que vous avez configurée dans la console de notification Push Nokia, et les valeurs **NotificationHubName** et **NotificationHubConnectionString** (DefaultListenSharedAccessSignature) du portail de gestion. 

3. Dans **MainActivity.java**, ajoutez l'instruction import suivante :
	
		import com.nokia.push.PushRegistrar;

	Ajoutez ensuite le code suivant dans la méthode onCreate :
 
		// Make sure the device has the proper dependencies.
		PushRegistrar.checkDevice(this);
	        
		// Make sure the manifest was properly set - comment out this line
		// while developing the app, then uncomment it when it's ready
		PushRegistrar.checkManifest(this);
	        
		// Register the device with the Nokia Notification service
		PushRegistrar.register(this, ConfigurationSettings.SenderId);

4. Ajoutez une autre classe nommée **PushIntentService.java** et ajoutez le code suivant, qui vous inscrira auprès de votre concentrateur de notification et traitera également les messages reçus de ce dernier. 

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		import android.util.Log;
		
		import com.microsoft.windowsazure.messaging.NotificationHub;
		import com.nokia.push.PushBaseIntentService;
		
		/**
		 * IntentService responsible for handling push notification messages.
		 */
		public class PushIntentService extends PushBaseIntentService {
			
			private NotificationManager mNotificationManager;
			private static NotificationHub hub;
		    public static final int NOTIFICATION_ID = 1;
		    private static final String TAG = "NokiaXPush/PushIntentService";
		
		    /**
		     * Constructor.
		     */
		    public PushIntentService() {
		    }

		    @Override
		    protected String[] getSenderIds(Context context) {
		        return new String[] { ConfigurationSettings.SenderId };
		    }   
		    
		    @Override
		    protected void onRegistered(Context context, String registrationId) {
		    	Log.i(TAG, "Nokia Registration ID \"" + registrationId + "\"");
		    	RegisterWithNotificationHub(context, registrationId);
		    }
		
		    public static void RegisterWithNotificationHub(Context context, String registrationId) {
		        if (hub == null) {
		            hub = new NotificationHub(
		            		ConfigurationSettings.NotificationHubName, 
		            		ConfigurationSettings.NotificationHubConnectionString, 
		            		context);
		        }
		        try {
					hub.register(registrationId);
			    	Log.i(TAG, "Registered with Notification Hub - '" 
			    			+ ConfigurationSettings.NotificationHubName + "'" 
			    			+ " with RegistrationID - '"
			    			+ registrationId + "'");
				} catch (Exception e) {
					e.printStackTrace();
				}
		    }
		
		    @Override
		    protected void onMessage(Context context, Intent intent) {
		    	String message = intentExtrasToString(intent.getExtras());
		    	Log.i(TAG, "Received message. Extras: " + message);
		    	generateNotification(context, message);
		    }
		    
		    /**
		     * Extracts the key-value pairs from the given Intent extras and returns
		     * them in a string.
		     */
		    private String intentExtrasToString(Bundle extras) {
		        StringBuilder sb = new StringBuilder();
		        sb.append("{ ");
		        
		        for (String key : extras.keySet()) {
		            sb.append(sb.length() <= 2 ? "" : ", ");
		            sb.append(key).append("=").append(extras.get(key));
		        }
		        
		        sb.append(" }");
		        return sb.toString();
		    }
		
		    /**
		     * Issues a notification to inform the user that server has sent a message.
		     */
		    private void generateNotification(Context context, String message)
		    {
		    	mNotificationManager = (NotificationManager)
		                context.getSystemService(Context.NOTIFICATION_SERVICE);
		
		        PendingIntent contentIntent = PendingIntent.getActivity(context, 0,
		            new Intent(context, MainActivity.class), 0);
		
		        NotificationCompat.Builder mBuilder =
		            new NotificationCompat.Builder(context)
		            .setSmallIcon(R.drawable.ic_launcher)
		            .setContentTitle("Notification Hub Demo")
		            .setStyle(new NotificationCompat.BigTextStyle()
		                     .bigText(message))
		            .setContentText(message);
		
		        mBuilder.setContentIntent(contentIntent);
		        mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		    }
		
			@Override
			protected void onError(Context arg0, String errorId) {
		        Log.i(TAG, "Received error:  " + errorId);
			}
		
			@Override
			protected void onUnregistered(Context arg0, String errorId) {
		        Log.i(TAG, "Received recoverable error: " + errorId);
			}
		}

##<a name="send"></a>Envoi d'une notification vers votre application

Vous pouvez envoyer des notifications en utilisant Notification Hubs à partir de tous les serveurs principaux qui utilisent l'<a href="http://msdn.microsoft.com/fr-fr/library/windowsazure/dn223264.aspx">interface REST</a>. Dans ce didacticiel, nous expliquons la procédure en utilisant une application console .NET. 

1. Créez une application console Visual C# :

	![][4]

2. Ajoutez une référence au Kit de développement logiciel (SDK) Azure Service Bus à l'aide du <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">package NuGet WindowsAzure.ServiceBus</a>. Dans le menu principal de Visual Studio, cliquez sur **Outils**, sur **Library Package Manager**, puis sur **Console du Gestionnaire de package**. Ensuite, dans la fenêtre de la console, tapez le code suivant et appuyez sur Entrée :

        Install-Package WindowsAzure.ServiceBus

3. Ouvrez le fichier Program.cs et ajoutez l'instruction using suivante :

        using Microsoft.ServiceBus.Notifications;

4. Dans votre classe `Program`, ajoutez la méthode suivante :

		private static async void SendNotificationAsync()
		{
			NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<DefaultFullSharedAccessSignatureSASConnectionString>", "<hub name>");
			await hub.SendNokiaXNativeNotificationAsync("{\"data\" : {\"payload\":\"" + "Hello from Azure" + "\"}}");
		}

5. Ajoutez ensuite les lignes suivantes dans votre méthode Main :

         SendNotificationAsync();
		 Console.ReadLine();

##<a name="test-app"></a>Test de votre application

Pour tester cette application avec un téléphone réel, connectez-le à votre ordinateur avec un câble USB.

Pour tester cette application avec l'émulateur :

1. Dans la barre d'outils supérieure d'Eclipse, cliquez sur Exécuter, puis sélectionnez votre application. 

2. Cette opération charge votre application sur le téléphone attaché ou lance l'émulateur et charge et exécute l'application.

3. L'application extrait le registrationId du service de notification Nokia et s'inscrit auprès du concentrateur de notification.

    <div class="dev-callout"><b>Remarque</b>
	<p>
	Si l'application Android parvient à s'inscrire correctement auprès du concentrateur de notification, un message similaire au suivant apparaît dans vos journaux Eclipse Logcatlogs :
	'Registered with Notification Hub - '<yourNotificationHubName>' with RegistrationID - '<RegistrationIdReturnedByNokiaNotificationService''
	</p>
    </div>
	
4.	Pour envoyer un test de notification lorsque vous utilisez l'application console .Net, appuyez simplement sur la touche F5 dans Visual Studio afin d'exécuter l'application : celle-ci envoie alors une notification qui apparaît dans la zone de notification supérieure de votre appareil ou émulateur. 

<!-- Images. -->
[1]: ./media/notification-hubs-nokia-x-get-started/AndroidAppProperties.png
[2]: ./media/notification-hubs-nokia-x-get-started/AzureManagementCreateNH.png
[3]: ./media/notification-hubs-nokia-x-get-started/AzureManagementPortal.png
[4]: ./media/notification-hubs-nokia-x-get-started/ConsoleProject.png
[5]: ./media/notification-hubs-nokia-x-get-started/NewAndroidApp.png
[6]: ./media/notification-hubs-nokia-x-get-started/NewJavaClass.png
[7]: ./media/notification-hubs-nokia-x-get-started/NHConfigure.png
[8]: ./media/notification-hubs-nokia-x-get-started/NHConfigureNokiaX.png
[9]: ./media/notification-hubs-nokia-x-get-started/NHConfigureTopItem.png
[10]: ./media/notification-hubs-nokia-x-get-started/NHDashboard.png
[11]: ./media/notification-hubs-nokia-x-get-started/NokiaConsole.png
[12]: ./media/notification-hubs-nokia-x-get-started/NokiaConsoleService.png
[13]: ./media/notification-hubs-nokia-x-get-started/AndroidBuildTarget.png

<!-- URLs. -->
[Portail de gestion Azure]: https://manage.windowsazure.com/


