<properties urlDisplayName="Get Started" pageTitle="Prise en main d'Azure Notification Hubs" metaKeywords="" description="Découvrez comment utiliser Azure Notification Hubs pour envoyer des notifications Push." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="piyushjo" solutions="" manager="dwrede" editor="" />

<tags ms.service="" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="10/03/2014" ms.author="piyushjo" />

# Prise en main de Notification Hubs

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Store C#">Windows Store C#</a><a href="/fr-fr/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/fr-fr/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/fr-fr/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/fr-fr/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/fr-fr/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu" class="current">Baidu</a><a href="/fr-fr/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/fr-fr/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Le service de transmission Push dans le cloud de Baidu est un service cloud chinois que vous pouvez utiliser pour envoyer des notifications Push à des appareils mobiles. Ce service est particulièrement utile en Chine où la remise de notifications Push vers Android est complexe en raison de la présence de différents magasins d'applications et services de transmission Push, et de la disponibilité de périphériques Android généralement non connectés à la messagerie cloud de Google (GCM). 

Ce didacticiel requiert les éléments suivants :

+ Kit de développement logiciel (SDK) Android (nous présumons que vous utiliserez Eclipse), que vous pouvez télécharger <a href="http://go.microsoft.com/fwlink/?LinkId=389797">ici</a>
+ [Kit de développement logiciel (SDK) Android pour Mobile Services]
+ [Kit de développement logiciel (SDK) Android pour transmissions Push Baidu]

>[WACOM.NOTE] Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">version d'évaluation gratuite Azure</a>.

Ce didacticiel vous familiarise avec les étapes de base pour activer les notifications Push :

* [Création d'un compte Baidu](#createBaiduAccount)
* [Inscription en tant que développeur Baidu](#registerBaiduDeveloper)
* [Création d'un projet Baidu de transmission Push dans le cloud](#createBaiduPushProject)
* [Configuration de votre concentrateur de notification](#configure-hub)
* [Connexion de votre application au concentrateur de notification](#connecting-app)
* [Envoi de notifications à votre application](#send)

##<a id="createBaiduAccount"></a>Création d'un compte Baidu

Pour utiliser Baidu, vous devez créer un compte. Si vous en possédez déjà un, connectez-vous au [portail Baidu] avec votre compte Baidu et passez à l'étape suivante. Sinon, consultez les instructions ci-dessous pour créer un nouveau compte Baidu.  

1. Accédez au [portail Baidu] et cliquez sur le lien 登录 (Connexion). Cliquez sur立即注册 pour démarrer le processus d'inscription d'un nouveau compte. 

   	![][1]

2. Entrez les informations requises (téléphone/adresse de messagerie, mot de passe et code de vérification), puis cliquez sur Inscription.

   	![][2]

3. Vous recevrez un courrier électronique à l'adresse de messagerie que vous avez entrée, avec un lien pour activer votre compte Baidu. 

   	![][3]

4. Connectez-vous à votre compte de messagerie, ouvrez le message d'activation Baidu, puis cliquez sur le lien d'activation pour activer votre compte Baidu. 

   	![][4]

Une fois que vous possédez un compte Baidu activé, connectez-vous au [portail Baidu] avec ce compte. 

##<a id="registerBaiduDeveloper"></a>Inscription en tant que développeur Baidu

1. Une fois que vous vous êtes connecté au [portail Baidu], cliquez sur **更多>> (plus)**.

  	![][5]

2. Faites défiler la section **站长与开发者服务 (Administrateur Web et services de développement)** et cliquez sur **百度开放云平台 (Plateforme de cloud ouvert Baidu)**. 

  	![][6]

3. Dans la page suivante, cliquez sur **开发者服务 (Services de développement)** dans le coin supérieur droit. 

  	![][7]

4. Dans la page suivante, cliquez sur **注册开发者 (Développeurs inscrits)** dans le menu en haut à droite. 

  	![][8]

5. Entrez votre nom, description et numéro de téléphone mobile pour recevoir un message texte de vérification, puis cliquez sur **送验证码 (Envoyer un code de vérification)**. Notez que pour les numéros de téléphone internationaux, vous devez placer le code de pays entre parenthèses. Voici un exemple de numéro en France : **(33)1234567890**.

  	![][9]

6. Vous recevrez ensuite un message texte comportant un numéro de vérification, comme illustré dans l'exemple suivant :

  	![][10] 

7. Entrez le numéro de vérification figurant dans le message dans **验证码 (Code de confirmation)**. 

8. Enfin, terminez l'inscription en tant que développeur en acceptant le contrat d'utilisation Baidu et en cliquant sur **提交(Envoyer)**. La page suivante s'affiche en cas de réussite de l'inscription :

  	![][11] 

##<a id="createBaiduPushProject"></a>Création d'un projet Baidu de transmission Push dans le cloud

Quand vous créez un projet Baidu de transmission Push dans le cloud, vous recevez un ID d'application, une clé d'API et une clé secrète.

1. Une fois que vous vous êtes connecté au [portail Baidu], cliquez sur **更多>> (plus)**.

  	![][5]

2. Faites défiler la section **站长与开发者服务 (Administrateur Web et services de développement)** et cliquez sur **百度开放云平台 (Plateforme de cloud ouvert Baidu)**. 

  	![][6]

3. Dans la page suivante, cliquez sur **开发者服务 (Services de développement)** dans le coin supérieur droit. 

  	![][7]

4. Dans la page suivante, cliquez sur **云推送(Cloud Push)** dans la section **云服务(Services cloud)**. 

  	![][12]

5. Une fois que vous êtes inscrit comme développeur, vous voyez **管理控制台 (Console de gestion)** dans le menu supérieur. Cliquez sur **开发者服务管理 (Gestion du service de développement)**. 

  	![][13]

6. Dans la page suivante, cliquez sur **创建工程 (Créer un projet)**.

  	![][14]

7. Entrez un nom d'application et cliquez sur **创建 (Créer)**.

  	![][15]

8. Une fois la création réussie, une page s'affiche présentant l'**ID d'application**, la **clé API** et la **clé secrète**. Prenez note de la **clé API** et de la **clé secrète** que nous utiliserons ultérieurement. 

  	![][16]

9. Configurez le projet pour les notifications Push en cliquant sur **云推送 (Cloud Push)** dans le volet gauche. 

  	![][31]

10. Dans la page suivante, cliquez sur le bouton **推送设置 (Paramètres de transmission Push)**.

	![][32]  

11. On the configuration page, add the package name that you will be using in your Android project in the **应用包名 (Application package)** field and click **保存设置 (Save)**  

	![][33]

Le message **保存成功！ (Enregistrement réussi !)** s'affiche.

##<a id="configure-hub"></a>Configuration de votre concentrateur de notification

1. Connectez-vous au [portail de gestion Azure], puis cliquez sur **+NOUVEAU** en bas de l'écran.

2. Cliquez sur **Services d'application**, sur **Service Bus**, sur **Concentrateur de notification**, puis sur **Création rapide**.
 
3. Fournissez un nom pour le **concentrateur de notification**, sélectionnez la **région** et l'**espace de noms** où ce concentrateur de notification sera créé, puis cliquez sur **Créer un concentrateur de notification**.  

  	![][17]

4. Cliquez sur l'espace de noms dans lequel vous avez créé votre concentrateur de notification, puis cliquez sur **Concentrateurs de notification** en haut. 

  	![][18]

5. Sélectionnez le concentrateur de notification que vous avez créé et cliquez sur **Configurer** dans le menu supérieur.

  	![][19]

6. Faites défiler l'affichage jusqu'à la section **Paramètres de notification baidu** et entrez la **clé API** et la **clé secrète** obtenues précédemment dans la console Baidu pour votre projet Baidu de transmission Push dans le cloud. Cliquez sur **Enregistrer** après avoir entré ces valeurs. 

  	![][20]

7. Cliquez sur l'onglet **Tableau de bord** en haut pour le concentrateur de notification, puis cliquez sur **Afficher la chaîne de connexion**.

  	![][21]

8. Prenez note des valeurs **DefaultListenSharedAccessSignature** et **DefaultFullSharedAccessSignature** dans la fenêtre Accès aux informations de connexion. 

    ![][22]

##<a id="connecting-app"></a>Connexion de votre application au concentrateur de notification

1. Dans Eclipse ADT, créez un projet Android en sélectionnant File (Fichier) -> New (Nouveau) -> Android Application (Application Android).

    ![][23]

2. Entrez un **nom d'application** et assurez-vous que la version du **Kit de développement logiciel (SDK) minimum requis** est définie sur **API 16 : Android 4.1**.

    ![][24]

3. Cliquez sur **Suivant** et continuez à suivre l'Assistant jusqu'à la fenêtre **Create Activity** (Créer une activité). Assurez-vous que l'option **Blank Activity** (Activité vide) soit sélectionnée et sélectionnez **Terminer** pour créer une nouvelle application Android. 

    ![][25]

4. Assurez-vous que la **cible de la génération du projet** est définie correctement.

    ![][26]

5. Téléchargez et décompressez le [Kit de développement logiciel (SDK) Mobile Services pour Android], ouvrez le dossier **notificationhubs**, copiez le fichier **notification-hubs-x.y.jar** dans le dossier *libs* de votre projet Eclipse, puis actualisez le dossier *libs*.

6. Téléchargez et décompressez le [Kit de développement logiciel (SDK) Android pour transmissions Push Baidu], ouvrez le dossier **libs** et copiez le fichier jar *pushservice-x.y.z* et les dossiers *armeabi* & *mips* dans le dossier **libs** de votre application Android. 

7. Ouvrez le fichier **AndroidManifest.xml** de votre projet Android et ajoutez les autorisations requises par le Kit de développement logiciel (SDK) Baidu.

	    <uses-permission android:name="android.permission.INTERNET" />
	    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
	    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
	    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
	    <uses-permission android:name="android.permission.WRITE_SETTINGS" />
	    <uses-permission android:name="android.permission.VIBRATE" />
	    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
	    <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
	    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
	    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
	    <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
	    <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />

8. Ajoutez la propriété *android:name* à votre élément *application* dans le fichier **AndroidManifest.xml** en remplaçant *yourprojectname*, par exemple **com.example.BaiduTest**. Assurez-vous que ce nom de projet correspond à celui que vous avez configuré dans la console Baidu. 

		<application android:name="yourprojectname.DemoApplication"

9. Ajoutez la configuration suivante dans l'élément d'application après l'élément d'activité .MainActivity en remplaçant *yourprojectname*, par exemple **com.example.BaiduTest** :

		<receiver android:name="yourprojectname.MyPushMessageReceiver">
		    <intent-filter>
		        <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
		        <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
		        <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
		    </intent-filter>
		</receiver>
		
		<receiver android:name="com.baidu.android.pushservice.PushServiceReceiver"
		    android:process=":bdservice_v1">
		    <intent-filter>
		        <action android:name="android.intent.action.BOOT_COMPLETED" />
		        <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
				<action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
		    </intent-filter>
		</receiver>
        
        <receiver android:name="com.baidu.android.pushservice.RegistrationReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.METHOD" />
                <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package" />
            </intent-filter>                   
        </receiver>
        
        <service
            android:name="com.baidu.android.pushservice.PushService"
            android:exported="true"
            android:process=":bdservice_v1"  >
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
            </intent-filter>
        </service>

9. Ajoutez une nouvelle classe appelée **ConfigurationSettings.java** au projet. 

    ![][28]

    ![][29]

10. Ajoutez-lui le code suivant :

		public class ConfigurationSettings {
		        public static String API_KEY = "...";
				public static String NotificationHubName = "...";
				public static String NotificationHubConnectionString = "...";
			}
	
	Définissez la valeur de *API_KEY* avec ce que vous avez récupéré précédemment à partir du projet de cloud Baidu, *NotificationHubName* avec votre nom de concentrateur de notification à partir du portail Azure et *NotificationHubConnectionString* avec DefaultListenSharedAccessSignature à partir du portail Azure. 

11. Ajoutez une nouvelle classe appelée **DemoApplication.java** et ajoutez-lui le code suivant :

		import com.baidu.frontia.FrontiaApplication;
		
		public class DemoApplication extends FrontiaApplication {
		    @Override
		    public void onCreate() {
		        super.onCreate();
		    }
		}

12. Ajoutez une autre classe nouvelle appelée **MyPushMessageReceiver.java** et ajoutez-lui le code ci-dessous. Il s'agit de la classe qui gère les notifications Push reçues à partir du serveur émetteur Baidu :

		import java.util.List;
		import android.content.Context;
		import android.os.AsyncTask;
		import android.util.Log;
		import com.baidu.frontia.api.FrontiaPushMessageReceiver;
		import com.microsoft.windowsazure.messaging.NotificationHub;
		
		public class MyPushMessageReceiver extends FrontiaPushMessageReceiver {
		    /** TAG to Log */
			public static NotificationHub hub = null;
			public static String mChannelId, mUserId;
		    public static final String TAG = MyPushMessageReceiver.class
		            .getSimpleName();
		    
			@Override
		    public void onBind(Context context, int errorCode, String appid,
		            String userId, String channelId, String requestId) {
		        String responseString = "onBind errorCode=" + errorCode + " appid="
		                + appid + " userId=" + userId + " channelId=" + channelId
		                + " requestId=" + requestId;
		        Log.d(TAG, responseString);
		        mChannelId = channelId;
		        mUserId = userId;
		        
		        try {
		       	 if (hub == null) {
		                hub = new NotificationHub(
		                		ConfigurationSettings.NotificationHubName, 
		                		ConfigurationSettings.NotificationHubConnectionString, 
		                		context);
		                Log.i(TAG, "Notification hub initialized");
		            }
		        } catch (Exception e) {
		           Log.e(TAG, e.getMessage());
		        }
		        
		        registerWithNotificationHubs();
			}
		    
		    private void registerWithNotificationHubs() {
		       new AsyncTask<Void, Void, Void>() {
		          @Override
		          protected Void doInBackground(Void... params) {
		             try {
		            	 hub.registerBaidu(mUserId, mChannelId);
		            	 Log.i(TAG, "Registered with Notification Hub - '" 
		     	    			+ ConfigurationSettings.NotificationHubName + "'" 
		     	    			+ " with UserId - '"
		     	    			+ mUserId + "' and Channel Id - '"
		     	    			+ mChannelId + "'");
		             } catch (Exception e) {
		            	 Log.e(TAG, e.getMessage());
		             }
		             return null;
		         }
		       }.execute(null, null, null);
		    }
		    
		    @Override
		    public void onSetTags(Context context, int errorCode,
		            List<String> sucessTags, List<String> failTags, String requestId) {
		        String responseString = "onSetTags errorCode=" + errorCode
		                + " sucessTags=" + sucessTags + " failTags=" + failTags
		                + " requestId=" + requestId;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onDelTags(Context context, int errorCode,
		            List<String> sucessTags, List<String> failTags, String requestId) {
		        String responseString = "onDelTags errorCode=" + errorCode
		                + " sucessTags=" + sucessTags + " failTags=" + failTags
		                + " requestId=" + requestId;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onListTags(Context context, int errorCode, List<String> tags,
		            String requestId) {
		        String responseString = "onListTags errorCode=" + errorCode + " tags="
		                + tags;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onUnbind(Context context, int errorCode, String requestId) {
		        String responseString = "onUnbind errorCode=" + errorCode
		                + " requestId = " + requestId;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onNotificationClicked(Context context, String title,
		            String description, String customContentString) {
		        String notifyString = "title=\"" + title + "\" description=\""
		                + description + "\" customContent=" + customContentString;
		        Log.d(TAG, notifyString);
		    }
		
		    @Override
		    public void onMessage(Context context, String message,
		            String customContentString) {
		        String messageString = "message=\"" + message + "\" customContentString=" + customContentString;
		        Log.d(TAG, messageString);
		    }
		}

13. Ouvrez **MainActivity.java** et ajoutez le code suivant à la méthode **onCreate** :

	        PushManager.startWork(getApplicationContext(),
	                PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);

et ajoutez les instructions import suivantes en haut :
			import com.baidu.android.pushservice.PushConstants;
			import com.baidu.android.pushservice.PushManager;

##<a id="send"></a>Envoi de notifications à votre application

Vous pouvez envoyer des notifications en utilisant Notification Hubs à partir de tous les serveurs principaux qui utilisent notre <a href="http://msdn.microsoft.com/fr-fr/library/windowsazure/dn223264.aspx">interface REST</a>. Dans ce didacticiel, nous expliquons la procédure en utilisant une application console .NET. 

1. Créez une application console Visual C# :

	![][30]

2. Ajoutez une référence au Kit de développement logiciel (SDK) Azure Service Bus à l'aide du <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">package NuGet WindowsAzure.ServiceBus</a>. Dans le menu principal de Visual Studio, cliquez sur **Outils**, sur **Library Package Manager**, puis sur **Console du gestionnaire de package**. Puis, dans la fenêtre de la console, tapez le code suivant et appuyez sur Entrée :

        Install-Package WindowsAzure.ServiceBus

3. Ouvrez le fichier Program.cs et ajoutez l'instruction using suivante :

        using Microsoft.ServiceBus.Notifications;

4. Dans votre classe " Program ", ajoutez la méthode suivante et remplacez *DefaultFullSharedAccessSignatureSASConnectionString* et *NotificationHubName* par les valeurs que vous avez. 

		private static async void SendNotificationAsync()
		{
			NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
			string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
			var result = await hub.SendBaiduNativeNotificationAsync(message);
		}

5. Ajoutez ensuite les lignes suivantes dans votre méthode Main :

         SendNotificationAsync();
		 Console.ReadLine();

##<a name="test-app"></a>Test de votre application

Pour tester cette application avec un téléphone réel, connectez-le à votre ordinateur avec un câble USB.

Pour tester cette application avec l'émulateur :

1. Dans la barre d'outils supérieure d'Eclipse, cliquez sur Run (Exécuter), puis sélectionnez votre application. 

2. Cette opération charge votre application sur le téléphone attaché ou lance l'émulateur, puis charge et exécute l'application.

3. L'application récupère les paramètres " userId " et " channelId " à partir du service de notification Push Baidu et s'inscrit auprès du concentrateur de notification.
	
4.	Pour envoyer un test de notification lorsque vous utilisez l'application console .Net, appuyez simplement sur la touche F5 dans Visual Studio afin d'exécuter l'application : celle-ci envoie alors une notification qui apparaît dans la zone de notification supérieure de votre appareil ou émulateur. 


<!-- Images. -->
[1]: ./media/notification-hubs-baidu-get-started/BaiduRegistration.png
[2]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationInput.png
[3]: ./media/notification-hubs-baidu-get-started/BaiduConfirmation.png
[4]: ./media/notification-hubs-baidu-get-started/BaiduActivationEmail.png
[5]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationMore.png
[6]: ./media/notification-hubs-baidu-get-started/BaiduOpenCloudPlatform.png
[7]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperServices.png
[8]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperRegistration.png
[9]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationInput.png
[10]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationConfirmation.png
[11]: ./media/notification-hubs-baidu-get-started/BaiduDevConfirmationFinal.png
[12]: ./media/notification-hubs-baidu-get-started/BaiduCloudPush.png
[13]: ./media/notification-hubs-baidu-get-started/BaiduDevSvcMgmt.png
[14]: ./media/notification-hubs-baidu-get-started/BaiduCreateProject.png
[15]: ./media/notification-hubs-baidu-get-started/BaiduCreateProjectInput.png
[16]: ./media/notification-hubs-baidu-get-started/BaiduProjectKeys.png
[17]: ./media/notification-hubs-baidu-get-started/AzureNHCreation.png
[18]: ./media/notification-hubs-baidu-get-started/NotificationHubs.png
[19]: ./media/notification-hubs-baidu-get-started/NotificationHubsConfigure.png
[20]: ./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png
[21]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionStringView.png
[22]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionString.png
[23]: ./media/notification-hubs-baidu-get-started/EclipseNewProject.png
[24]: ./media/notification-hubs-baidu-get-started/EclipseProjectCreation.png
[25]: ./media/notification-hubs-baidu-get-started/EclipseBlankActivity.png
[26]: ./media/notification-hubs-baidu-get-started/EclipseProjectBuildProperty.png
[27]: ./media/notification-hubs-baidu-get-started/EclipseBaiduReferences.png
[28]: ./media/notification-hubs-baidu-get-started/EclipseNewClass.png
[29]: ./media/notification-hubs-baidu-get-started/EclipseConfigSettingsClass.png
[30]: ./media/notification-hubs-baidu-get-started/ConsoleProject.png
[31]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig1.png
[32]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig2.png
[33]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig3.png

<!-- URLs. -->
[Kit de développement logiciel (SDK) Mobile Services pour Android] : https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Kit de développement logiciel (SDK) Android pour transmissions Push Baidu] http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[Portail de gestion Azure] : https://manage.windowsazure.com/
[Portail Baidu] : http://www.baidu.com/


<!--HONumber=35.2-->
