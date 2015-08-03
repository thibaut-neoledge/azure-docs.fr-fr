<properties 
	pageTitle="Notification des utilisateurs via Azure Notification Hubs" 
	description="Découvrez comment envoyer des notifications Push aux utilisateurs dans Azure. Exemples de code écrits en Java pour Android" 
	documentationCenter="android" 
	services="notification-hubs" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="05/31/2015" 
	ms.author="wesmc"/>

#Notification des utilisateurs via Azure Notification Hubs


[AZURE.INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

##Vue d'ensemble

La prise en charge des notifications Push dans Azure vous permet d’accéder à une infrastructure Push conviviale, multi-plateforme et avec montée en charge qui simplifie fortement l’implémentation des notifications Push pour les applications consommateur et entreprise pour les plateformes mobiles. Ce didacticiel explique comment utiliser Azure Notification Hubs pour envoyer des notifications Push à un utilisateur particulier d'une application sur un appareil spécifique. Un serveur principal WebAPI ASP.NET est utilisé pour authentifier les clients et pour générer les notifications, comme présenté dans la rubrique de conseils [Inscription auprès du serveur principal de votre application](http://msdn.microsoft.com/library/dn743807.aspx). Ce didacticiel s'appuie sur le hub de notification que vous avez créé dans le didacticiel intitulé [Prise en main de Notification Hubs (Android)](notification-hubs-android-get-started.md).

> [AZURE.NOTE]Ce didacticiel part du principe que vous avez créé et configuré votre hub de notification, comme décrit dans [Prise en main de Notification Hubs (Android)](notification-hubs-android-get-started.md). Si vous utilisez Mobile Services comme service principal, consultez la [version consacrée à Mobile Services](../mobile-services-javascript-backend-android-push-notifications-app-users.md) de ce didacticiel.

[AZURE.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Création du projet Android

L'étape suivante consiste à créer l'application Android.

1. Suivez le didacticiel [Prise en main de Notification Hubs (Android)](notification-hubs-android-get-started.md) pour créer et configurer votre application afin de recevoir des notifications Push de GCM.

2. Ouvrez votre fichier **res/layout/activity_main.xml** et remplacez le contenu par les définitions de contenu suivantes.
 
    Cette opération ajoute de nouveaux contrôles EditText pour la connexion en tant qu'utilisateur. Un champ est également ajouté pour une balise de nom d'utilisateur qui fera partie des notifications que vous enverrez :
			
		<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
            android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
            android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">
        
        <EditText
            android:id="@+id/usernameText"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:ems="10"
            android:hint="@string/usernameHint"
            android:layout_above="@+id/passwordText"
            android:layout_alignParentEnd="true" />
        <EditText
            android:id="@+id/passwordText"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:ems="10"
            android:hint="@string/passwordHint"
            android:inputType="textPassword"
            android:layout_above="@+id/buttonLogin"
            android:layout_alignParentEnd="true" />
        <Button
            android:id="@+id/buttonLogin"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/loginButton"
            android:onClick="login"
            android:layout_above="@+id/toggleButtonGCM"
            android:layout_centerHorizontal="true"
            android:layout_marginBottom="24dp" />
        <ToggleButton
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textOn="WNS on"
            android:textOff="WNS off"
            android:id="@+id/toggleButtonWNS"
            android:layout_toLeftOf="@id/toggleButtonGCM"
            android:layout_centerVertical="true" />
        <ToggleButton
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textOn="GCM on"
            android:textOff="GCM off"
            android:id="@+id/toggleButtonGCM"
            android:checked="true"
            android:layout_centerHorizontal="true"
            android:layout_centerVertical="true" />
        <ToggleButton
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textOn="APNS on"
            android:textOff="APNS off"
            android:id="@+id/toggleButtonAPNS"
            android:layout_toRightOf="@id/toggleButtonGCM"
            android:layout_centerVertical="true" />
        <EditText
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:id="@+id/editTextNotificationMessageTag"
            android:layout_below="@id/toggleButtonGCM"
            android:layout_centerHorizontal="true"
            android:hint="@string/notification_message_tag_hint" />
        <EditText
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:id="@+id/editTextNotificationMessage"
            android:layout_below="@+id/editTextNotificationMessageTag"
            android:layout_centerHorizontal="true"
            android:hint="@string/notification_message_hint" />
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/send_button"
            android:id="@+id/sendbutton"
            android:onClick="sendNotificationButtonOnClick"
            android:layout_below="@+id/editTextNotificationMessage"
            android:layout_centerHorizontal="true" />
        </RelativeLayout>



3. Ouvrez votre fichier **res/values/strings.xml** et remplacez la définition `send_button` par les lignes suivantes, qui redéfinissent la chaîne du `send_button` et ajoutent des chaînes pour les autres contrôles :

        <string name="usernameHint">Username</string>
        <string name="passwordHint">Password</string>
        <string name="loginButton">1. Log in</string>
        <string name="send_button">2. Send Notification</string>
        <string name="notification_message_tag_hint">
			Recipient username tag
		</string>

	La présentation graphique de votre fichier main_activity.xml doit ressembler à ceci :

	![][A1]

4. Créez une classe nommée **RegisterClient** dans le même package que votre classe `MainActivity`. Utilisez le code ci-dessous pour le nouveau fichier de classe.
 
		import java.io.IOException;
        import java.io.UnsupportedEncodingException;
        import java.util.Set;
        
        import org.apache.http.HttpResponse;
        import org.apache.http.HttpStatus;
        import org.apache.http.client.ClientProtocolException;
        import org.apache.http.client.HttpClient;
        import org.apache.http.client.methods.HttpPost;
        import org.apache.http.client.methods.HttpPut;
        import org.apache.http.client.methods.HttpUriRequest;
        import org.apache.http.entity.StringEntity;
        import org.apache.http.impl.client.DefaultHttpClient;
        import org.apache.http.util.EntityUtils;
        import org.json.JSONArray;
        import org.json.JSONException;
        import org.json.JSONObject;
        
        import android.content.Context;
        import android.content.SharedPreferences;
        import android.util.Log;
        
        public class RegisterClient {
            private static final String PREFS_NAME = "ANHSettings";
            private static final String REGID_SETTING_NAME = "ANHRegistrationId";
            private String Backend_Endpoint;
            SharedPreferences settings;
            protected HttpClient httpClient;
            private String authorizationHeader;
        
            public RegisterClient(Context context, String backendEnpoint) {
                super();
                this.settings = context.getSharedPreferences(PREFS_NAME, 0);
                httpClient =  new DefaultHttpClient();
                Backend_Endpoint = backendEnpoint + "/api/register";
            }
        
            public String getAuthorizationHeader() {
                return authorizationHeader;
            }
        
            public void setAuthorizationHeader(String authorizationHeader) {
                this.authorizationHeader = authorizationHeader;
            }
        
            public void register(String handle, Set<String> tags) throws ClientProtocolException, IOException, JSONException {
                String registrationId = retrieveRegistrationIdOrRequestNewOne(handle);
        
                JSONObject deviceInfo = new JSONObject();
                deviceInfo.put("Platform", "gcm");
                deviceInfo.put("Handle", handle);
                deviceInfo.put("Tags", new JSONArray(tags));
        
                int statusCode = upsertRegistration(registrationId, deviceInfo);
        
                if (statusCode == HttpStatus.SC_OK) {
                    return;
                } else if (statusCode == HttpStatus.SC_GONE){
                    settings.edit().remove(REGID_SETTING_NAME).commit();
                    registrationId = retrieveRegistrationIdOrRequestNewOne(handle);
                    statusCode = upsertRegistration(registrationId, deviceInfo);
                    if (statusCode != HttpStatus.SC_OK) {
                        Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                        throw new RuntimeException("Error upserting registration");
                    }
                } else {
                    Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                    throw new RuntimeException("Error upserting registration");
                }
            }
        
            private int upsertRegistration(String registrationId, JSONObject deviceInfo)
                    throws UnsupportedEncodingException, IOException,
                    ClientProtocolException {
                HttpPut request = new HttpPut(Backend_Endpoint+"/"+registrationId);
                request.setEntity(new StringEntity(deviceInfo.toString()));
                request.addHeader("Authorization", "Basic "+authorizationHeader);
                request.addHeader("Content-Type", "application/json");
                HttpResponse response = httpClient.execute(request);
                int statusCode = response.getStatusLine().getStatusCode();
                return statusCode;
            }
        
            private String retrieveRegistrationIdOrRequestNewOne(String handle) throws ClientProtocolException, IOException {
                if (settings.contains(REGID_SETTING_NAME))
                    return settings.getString(REGID_SETTING_NAME, null);
        
                HttpUriRequest request = new HttpPost(Backend_Endpoint+"?handle="+handle);
                request.addHeader("Authorization", "Basic "+authorizationHeader);
                HttpResponse response = httpClient.execute(request);
                if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                    Log.e("RegisterClient", "Error creating registrationId: " + response.getStatusLine().getStatusCode());
                    throw new RuntimeException("Error creating Notification Hubs registrationId");
                }
                String registrationId = EntityUtils.toString(response.getEntity());
                registrationId = registrationId.substring(1, registrationId.length()-1);
        
                settings.edit().putString(REGID_SETTING_NAME, registrationId).commit();
        
                return registrationId;
            }
        }

	Ce composant met en œuvre les appels REST nécessaires pour contacter le service principal de l'application et inscrire cette dernière pour les notifications Push. Il enregistre également en local les informations *registrationIds* créées par le hub de notification, comme expliqué dans la rubrique [Inscription auprès du serveur principal de votre application](http://msdn.microsoft.com/library/dn743807.aspx). Notez qu'il utilise un jeton d'autorisation qui se trouve dans le stockage local quand vous cliquez sur le bouton **Log in**.

5. Dans votre classe `MainActivity`, supprimez ou commentez votre champ privé pour `NotificationHub`, puis ajoutez un champ pour la classe `RegisterClient` et une chaîne pour le point de terminaison de votre serveur principal ASP.NET. Remplacez bien `<Enter Your Backend Endpoint>` par le point de terminaison réel de votre serveur principal, obtenu précédemment. Par exemple : `http://mybackend.azurewebsites.net`.


		//private NotificationHub hub;
		private RegisterClient registerClient;
	    private static final String BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";

 
6. Dans votre classe `MainActivity`, dans la méthode `onCreate`, supprimez ou commentez l'initialisation du champ `hub` et l'appel à la méthode `registerWithNotificationHubs`. Ensuite, ajoutez du code pour initialiser une instance de la classe `RegisterClient`. La méthode doit contenir les lignes qui suivent :

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
    
            MyHandler.mainActivity = this;
            NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
            gcm = GoogleCloudMessaging.getInstance(this);
    
            //hub = new NotificationHub(HubName, HubListenConnectionString, this);
            //registerWithNotificationHubs();
    
	        registerClient = new RegisterClient(this, BACKEND_ENDPOINT);

            setContentView(R.layout.activity_main);
        }

7. Dans votre classe `MainActivity`, supprimez ou commentez l'intégralité de la méthode `registerWithNotificationHubs`. Celle-ci n'est pas utilisée dans ce didacticiel.

8. Ajoutez les instructions `import` suivantes à votre fichier **MainActivity.java**.

		import android.widget.Button;
		import java.io.UnsupportedEncodingException;
		import android.content.Context;
		import java.util.HashSet;
		import android.widget.Toast;
		import org.apache.http.client.ClientProtocolException;
		import java.io.IOException;
		import org.apache.http.HttpStatus;


9. Ensuite, ajoutez les méthodes suivantes pour gérer l'événement de clic sur le bouton **Log in** et l'envoi de notifications Push.

	    @Override
	    protected void onStart() {
	    	super.onStart();
        	Button sendPush = (Button) findViewById(R.id.sendbutton);
	        sendPush.setEnabled(false);
	    }
	
		public void login(View view) throws UnsupportedEncodingException {
	    	this.registerClient.setAuthorizationHeader(getAuthorizationHeader());
	    	
	    	final Context context = this;
	    	new AsyncTask<Object, Object, Object>() {
				@Override
				protected Object doInBackground(Object... params) {
					try {
						String regid = gcm.register(SENDER_ID);
				        registerClient.register(regid, new HashSet<String>());
					} catch (Exception e) {
	                    DialogNotify("MainActivity - Failed to register", e.getMessage());
						return e;
					}
					return null;
				}
	
				protected void onPostExecute(Object result) {
                	Button sendPush = (Button) findViewById(R.id.sendbutton);
			        sendPush.setEnabled(true);
					Toast.makeText(context, "Logged in and registered.",
							Toast.LENGTH_LONG).show();
				}
			}.execute(null, null, null);
	    }
	    
		private String getAuthorizationHeader() throws UnsupportedEncodingException {
			EditText username = (EditText) findViewById(R.id.usernameText);
	    	EditText password = (EditText) findViewById(R.id.passwordText);
	    	String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
	    	basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
	    	return basicAuthHeader;
		}

        /**
         * This method calls the ASP.NET WebAPI backend to send the notification message
         * to the platform notification service based on the pns parameter.
         *
         * @param pns     The platform notification service to send the notification message to. Must
         *                be one of the following ("wns", "gcm", "apns").
         * @param userTag The tag for the user who will receive the notification message. This string
         *                must not contain spaces or special characters.
         * @param message The notification message string. This string must include the double quotes
         *                to be used as JSON content.
         */
        public void sendPush(final String pns, final String userTag, final String message)
                throws ClientProtocolException, IOException {
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
    
                        String uri = BACKEND_ENDPOINT + "/api/notifications";
                        uri += "?pns=" + pns;
                        uri += "&to_tag=" + userTag;
    
                        HttpPost request = new HttpPost(uri);
                        request.addHeader("Authorization", "Basic "+ getAuthorizationHeader());
                        request.setEntity(new StringEntity(message));
                        request.addHeader("Content-Type", "application/json");
    
                        HttpResponse response = new DefaultHttpClient().execute(request);
    
                        if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                            DialogNotify("MainActivity - Error sending " + pns + " notification", 
								response.getStatusLine().toString());
                            throw new RuntimeException("Error sending notification");
                        }
                    } catch (Exception e) {
                        DialogNotify("MainActivity - Failed to send " + pns + " notification ", e.getMessage());
                        return e;
                    }
    
                    return null;
                }
            }.execute(null, null, null);
        }


	Le gestionnaire `login` pour le bouton **Log in** génère un jeton d'authentification de base, à partir du nom d'utilisateur et du mot de passe entrés (notez que cela représente n'importe quel jeton utilisé par votre système d'authentification), puis utilise `RegisterClient` pour appeler le serveur principal en vue de l'inscription.

	La méthode `sendPush` appelle le serveur principal pour déclencher une notification sécurisée pour l'utilisateur basée sur la balise d'utilisateur. Le service de notification de la plateforme ciblé par `sendPush` varie selon la chaîne `pns` passée.

10. Dans votre classe `MainActivity`, mettez à jour la méthode `sendNotificationButtonOnClick` pour appeler la méthode `sendPush` avec les services de notification de plateforme sélectionnés par l'utilisateur, comme suit.

        /**
         * Send Notification button click handler. This method sends the push notification
         * message to each platform selected.
         *
         * @param v The view
         */
        public void sendNotificationButtonOnClick(View v)
                throws ClientProtocolException, IOException {
    
            String nhMessageTag = ((EditText) findViewById(R.id.editTextNotificationMessageTag))
                    .getText().toString();
            String nhMessage = ((EditText) findViewById(R.id.editTextNotificationMessage))
                    .getText().toString();
    
            // JSON String
            nhMessage = """ + nhMessage + """;
    
            if (((ToggleButton)findViewById(R.id.toggleButtonWNS)).isChecked())
            {
                sendPush("wns", nhMessageTag, nhMessage);
            }
            if (((ToggleButton)findViewById(R.id.toggleButtonGCM)).isChecked())
            {
                sendPush("gcm", nhMessageTag, nhMessage);
            }
            if (((ToggleButton)findViewById(R.id.toggleButtonAPNS)).isChecked())
            {
                sendPush("apns", nhMessageTag, nhMessage);
            }
        }



## Exécution de l’application


1. Exécutez l'application sur un appareil ou un émulateur à l'aide d'Android Studio.

2. Dans l'application Android, entrez un nom d'utilisateur et un mot de passe. Ils doivent représenter la même valeur de chaîne et ne pas contenir d'espaces ou de caractères spéciaux.

3. Dans l'application Android, cliquez sur **Log in**. Attendez que s'affiche un message indiquant **Logged in and registered**. Le bouton **Send Notification** est alors activé.

	![][A2]

4. Cliquez sur les boutons bascule pour activer toutes les plateformes où vous avez exécuté l'application et inscrit un utilisateur.
5. Entrez le nom de l'utilisateur qui recevra le message de notification. Cet utilisateur doit être inscrit pour les notifications sur les appareils cibles.

6. Entrez le message que l'utilisateur recevra sous la forme d'un message de notification Push.
7. Cliquez sur **Send Notification**. Chaque appareil doté d'une inscription avec la balise de nom d'utilisateur correspondante reçoit la notification Push.


[A1]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users.png
[A2]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users-enter-password.png

 

<!---HONumber=July15_HO4-->