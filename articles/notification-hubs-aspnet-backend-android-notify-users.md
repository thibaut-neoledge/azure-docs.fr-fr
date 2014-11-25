<properties title="Azure Notification Hubs Notify Users" pageTitle="Azure Notification Hubs Notify Users" metaKeywords="Azure push notifications, Azure notification hubs" description="Learn how to send secure push notifications in Azure. Code samples written in C# using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="elioda" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="elioda" />

# Notification des utilisateurs via Azure Notification Hubs

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/fr-fr/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal">Windows Universal</a><a href="/fr-fr/documentation/articles/notification-hubs-aspnet-backend-ios-notify-users/" title="iOS">iOS</a>
        <a href="/fr-fr/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android" class="current">Android</a>
</div>

La prise en charge des notifications Push dans Azure vous permet d'accéder à une infrastructure Push conviviale, multi-plateforme et avec montée en charge qui simplifie fortement l'implémentation des notifications Push pour les applications consommateur et entreprise pour les plateformes mobiles. Ce didacticiel explique comment utiliser Azure Notification Hubs pour envoyer des notifications Push à un utilisateur particulier d'une application sur un appareil spécifique. Un code WebAPI principal ASP.NET est utilisé pour authentifier les clients et pour générer les notifications, comme présenté dans la rubrique de conseils [Inscription auprès du serveur principal de votre application][Inscription auprès du serveur principal de votre application]. Ce didacticiel fait intervenir le concentrateur de notification que vous avez créé dans le didacticiel intitulé **Prise en main de Notification Hubs**.

> [AZURE.NOTE] Ce didacticiel part du principe que vous avez créé et configuré votre concentrateur de notification comme décrit dans la rubrique [Prise en main de Notification Hubs (Android)][Prise en main de Notification Hubs (Android)].

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Création du projet Android

L'étape suivante consiste à créer l'application Android.

1.  Suivez le didacticiel [Prise en main de Notification Hubs (Android)][Prise en main de Notification Hubs (Android)] pour créer et configurer votre application afin de recevoir des notifications Push de GCM.

2.  Ouvrez votre fichier res/layout/activity\_main.xml et remplacez le contenu par ce qui suit :

        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:tools="http://schemas.android.com/tools"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:paddingBottom="@dimen/activity_vertical_margin"
            android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
            tools:context="com.example.notifyusers.MainActivity"
            android:orientation="vertical">
            <EditText
                android:id="@+id/usernameText"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:ems="10"
                android:hint="@string/usernameHint" >
            </EditText>
            <EditText
                android:id="@+id/passwordText"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:ems="10"
                android:hint="@string/passwordHint"
                android:inputType="textPassword" />
            <Button
                android:id="@+id/buttonLogin"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/loginButton"
                android:onClick="login" />
            <Button
                android:id="@+id/buttonSend"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/sendButton"
                android:onClick="sendPush" />
        </LinearLayout>

3.  Ouvrez votre fichier res\\values\\string.xml et ajoutez les lignes suivantes :

        <string name="usernameHint">Username</string>
        <string name="passwordHint">Password</string>
        <string name="loginButton">1. Log in</string>
        <string name="sendButton">2. Send push</string>

    La présentation graphique de votre fichier main\_activity.xml doit ressembler à ceci :

    ![][0]

4.  Créez maintenant une classe **RegisterClient** dans le même package que votre classe **MainActivity**. Remplacez bien `{backend endpoint}` par le point de terminaison de votre serveur principal, obtenu dans la section précédente.

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
            private static final String BACKEND_ENDPOINT = "{backend endpoint}/api/register";
            SharedPreferences settings;
            protected HttpClient httpClient;
            private String authorizationHeader;

            public RegisterClient(Context context) {
                super();
                this.settings = context.getSharedPreferences(PREFS_NAME, 0);
                httpClient =  new DefaultHttpClient();
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
                HttpPut request = new HttpPut(BACKEND_ENDPOINT+"/"+registrationId);
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

                HttpUriRequest request = new HttpPost(BACKEND_ENDPOINT+"?handle="+handle);
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

    Ce composant met en œuvre les appels REST nécessaires pour contacter le serveur principal de l'application, afin de l'inscrire aux notifications Push. Il enregistre également en local les informations *registrationIds* créées par le concentrateur de notification, comme expliqué dans la rubrique [Inscription auprès du serveur principal de votre application][Inscription auprès du serveur principal de votre application]. Notez qu'il utilise un jeton d'autorisation qui se trouve dans le stockage local lorsque vous cliquez sur le bouton **Se connecter et s'inscrire**.

5.  Dans la classe **MainActivity**, supprimez vos champs privés pour **NotificationHub** et ajoutez un champ pour **RegisterClient** :

        //private NotificationHub hub;
        private RegisterClient registerClient;

6.  Ensuite, dans la méthode **onCreate**, supprimez l'initialisation du champ **hub** et de la méthode **registerWithNotificationHubs**. Ajoutez ensuite les lignes suivantes, qui initialisent une instance de la classe **RegisterClient**. La méthode doit contenir les lignes qui suivent :

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);

            NotificationsManager.handleNotifications(this, SENDER_ID,
                    MyHandler.class);
            gcm = GoogleCloudMessaging.getInstance(this);

            registerClient = new RegisterClient(this);

            setContentView(R.layout.activity_main);
        }

7.  Ensuite, ajoutez les méthodes suivantes, en remplaçant bien `{backend endpoint}` par votre point de terminaison principal, obtenu dans la section précédente.

        @Override
        protected void onStart() {
            super.onStart();
            Button sendPush = (Button) findViewById(R.id.buttonSend);
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
                        Log.e("MainActivity", "Failed to register - " + e.getMessage());
                        return e;
                    }
                    return null;
                }

                protected void onPostExecute(Object result) {
                    Button sendPush = (Button) findViewById(R.id.buttonSend);
                    sendPush.setEnabled(true);
                    Toast.makeText(context, "Logged in and registered.",
                            Toast.LENGTH_LONG).show();
                }
            }.execute(null, null, null);
        }

        public void sendPush(View view) throws ClientProtocolException, IOException {
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        HttpUriRequest request = new HttpPost("{backend endpoint}/api/notifications");
                        request.addHeader("Authorization", "Basic "+getAuthorizationHeader());
                        HttpResponse response = new DefaultHttpClient().execute(request);
                        if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                            Log.e("MainActivity", "Error sending notification" + response.getStatusLine().getStatusCode());
                            throw new RuntimeException("Error sending notification");
                        }
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to send notification - " + e.getMessage());
                        return e;
                    }
                    return null;
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

    Le rappel de **Log in** génère un jeton d'authentification de base, basé sur le nom d'utilisateur et le mot de passe entrés (notez que cela représente n'importe quel jeton utilisé par votre système d'authentification), puis utilise `RegisterClient` pour appeler le serveur principal. Le rappel de **Send push** appelle le serveur principal pour déclencher une notification sécurisée vers tous les appareils de cet utilisateur.

## Exécution de l'application

Pour exécuter l'application, procédez comme suit :

1.  Assurez-vous que **AppBackend** est déployé dans Azure. Si vous utilisez Visual Studio, exécutez l'application API web **AppBackend**. Une page web ASP.NET s'affiche.

2.  Dans Eclipse, exécutez l'application sur un appareil Android physique ou dans l'émulateur.

3.  Dans l'interface utilisateur de l'application Android, entrez un nom d'utilisateur et un mot de passe. La valeur peut être une chaîne quelconque, mais elle doit être identique pour les deux.

4.  Dans l'interface utilisateur de l'application Android, cliquez sur **Log in**. Cliquez ensuite sur **Send push**.

  [Inscription auprès du serveur principal de votre application]: http://msdn.microsoft.com/fr-fr/library/dn743807.aspx
  [0]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users1.PNG
