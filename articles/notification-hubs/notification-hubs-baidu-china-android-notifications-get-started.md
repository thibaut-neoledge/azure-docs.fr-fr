---
title: "Prendre en main Azure Notification Hubs à l’aide de Baidu | Microsoft Docs"
description: "Dans ce didacticiel, vous découvrirez comment utiliser Azure Notification Hubs pour envoyer des notifications Push à un appareil Android à l’aide de Baidu."
services: notification-hubs
documentationcenter: android
author: kpiteira
manager: erikre
editor: 
ms.assetid: 23bde1ea-f978-43b2-9eeb-bfd7b9edc4c1
ms.service: notification-hubs
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: mobile-baidu
ms.workload: mobile
ms.date: 08/29/2017
ms.author: kapiteir
ms.openlocfilehash: 91f20a6e0ff6c2dd512879e9ab3c9369dab5d8ff
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2017
---
# <a name="get-started-with-notification-hubs-using-baidu"></a>Prendre en main Notification Hubs à l’aide de Baidu
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Le service de transmission Push dans le cloud de Baidu est un service cloud chinois que vous pouvez utiliser pour envoyer des notifications Push à des appareils mobiles. 

Comme Google Play et FCM (messagerie Cloud Firebase) ne sont pas disponibles en Chine, il est nécessaire d’utiliser des magasins d’applications différents et des services à transmission de type push. Baidu est l’un d’eux, et celui qui est actuellement utilisé par le Hub de notification.

## <a name="prerequisites"></a>Composants requis
Ce didacticiel requiert les éléments suivants :

* Kit de développement logiciel Android SDK (nous supposons que vous utilisez Android Studio), que vous pouvez télécharger à partir du <a href="http://go.microsoft.com/fwlink/?LinkId=389797">site Android</a>
* [Push Baidu Android SDK]

> [!NOTE]
> Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-baidu-get-started%2F).
> 
> 

## <a name="create-a-baidu-account"></a>Création d’un compte Baidu
Pour utiliser Baidu, vous devez disposer d’un compte Baidu. Si vous en avez déjà un, connectez-vous au [portail Baidu] et passez à l’étape suivante. Dans le cas contraire, consultez les instructions ci-après pour créer un compte Baidu.  

1. Accédez au [portail Baidu] et cliquez sur le lien **登录** (**Connexion**). Cliquez sur **立即注册** (**S’inscrire maintenant**) pour démarrer le processus d’inscription d’un compte.
   
    ![Inscription Baidu](./media/notification-hubs-baidu-get-started/BaiduRegistration.png)

2. Entrez les informations requises (téléphone/adresse de messagerie, mot de passe et code de vérification), puis cliquez sur 注册 (**Inscription**).
   
    ![Entrée d’inscription Baidu](./media/notification-hubs-baidu-get-started/BaiduRegistrationInput.png)

3. Vous recevez un courrier électronique à l’adresse e-mail que vous avez entrée avec un lien permettant d’activer votre compte Baidu.
   
    ![Confirmation de l’inscription Baidu](./media/notification-hubs-baidu-get-started/BaiduConfirmation.png)

4. Connectez-vous à votre compte de messagerie, ouvrez le courrier électronique d’activation Baidu, puis cliquez sur le lien d’activation pour activer votre compte Baidu.
   
    ![E-mail d’activation Baidu](./media/notification-hubs-baidu-get-started/BaiduActivationEmail.png)

Une fois que vous possédez un compte Baidu activé, connectez-vous au [portail Baidu].

## <a name="create-a-baidu-cloud-push-project"></a>Créer un projet Baidu de transmission Push dans le cloud
Quand vous créez un projet Baidu de transmission Push dans le cloud, vous recevez un ID d’application, une clé API et une clé secrète.

1. Une fois que êtes connecté au [portail Baidu], cliquez sur **更多>>** (**plus**).
   
    ![Inscription - Plus](./media/notification-hubs-baidu-get-started/BaiduRegistrationMore.png)

2. Faites défiler la section **站长与开发者服** (**Administrateur Web et Services de développement**) et cliquez sur **百度云推送** (**Plateforme cloud ouverte Baidu**).
   
    ![Plateforme cloud ouverte Baidu](./media/notification-hubs-baidu-get-started/BaiduOpenCloudPlatform.png)

3. Dans la page suivante, cliquez sur**登录**(**Connexion**) dans le coin supérieur droit.
   
    ![Connexion à Baidu](./media/notification-hubs-baidu-get-started/BaiduLogin.png)

4. Ensuite, cliquez sur**创建应用**(**Créer une application**) sur cette page.

    ![Créer une application sur Baidu](./media/notification-hubs-baidu-get-started/BaiduCreateApplication.png)

5. Sur la page suivante, cliquez sur 创建新应用 (**Créer une nouvelle application**).
   
    ![Créer une application sur Baidu](./media/notification-hubs-baidu-get-started/BaiduCreateNewApplication.png)

6. Entrez un nom d’application et cliquez sur 创建 (**Créer**).
   
    ![](./media/notification-hubs-baidu-get-started/BaiduCreateApplicationDoCreate.png)

7. Une fois qu’un projet push cloud Baidu a été créé, vous voyez apparaître une page présentant **l’ID d’application**, la **clé API** et la **clé secrète**. Prenez note de la clé API et de la clé secrète que nous utiliserons ultérieurement.
   
    ![Clés secrètes Baidu de type push](./media/notification-hubs-baidu-get-started/BaiduGetSecrets.png)

8. Configurez le projet pour les notifications push en cliquant sur 云推送 (**Créer une notification**) dans le volet gauche.
   
    ![](./media/notification-hubs-baidu-get-started/BaiduCreateNotification.png)


## <a name="configure-a-new-notification-hub"></a>Configurer un nouveau hub de notification
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


&emsp;&emsp;6. Dans le hub de notification, sélectionnez **Notification Services**, puis **Baidu (Android China)**.

&emsp;&emsp;![Azure Notification Hubs - Baidu](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

&emsp;&emsp;7. Faites défiler jusqu'à la sectiondes paramètres de notification Baidu. Entrez la clé API et la clé secrète que vous avez obtenues à partir de la console Baidu, dans le projet push cloud Baidu. Cliquez ensuite sur Enregistrer.

&emsp;&emsp;![Azure Notification Hubs - Clés secrètes Baidu](./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png)

Votre Notification Hub est désormais configuré pour fonctionner avec Baidu. Vous avez également les **paramètres de connexion** pour enregistrer votre application afin d’envoyer et de recevoir des notifications Push.

Prenez note de `DefaultListenSharedAccessSignature` et `DefaultFullSharedAccessSignature` à partir de la fenêtre d’informations de connexion Accès.

## <a name="connect-your-app-to-the-notification-hub"></a>Connexion de votre application au hub de notification
1. Dans Android Studio, créez un nouveau projet Android (Fichier > Nouveau > Nouveau projet).

    ![Azure Notification Hubs - Nouveau projet Baidu](./media/notification-hubs-baidu-get-started/AndroidNewProject.png)

2.  Entrez un nom d’application et vérifiez que la version du Kit de développement logiciel (SDK) minimum obligatoire est définie sur API 16: Android 4.1. **Assurez-vous également que votre nom de package (应用包名) est le même que dans le portail de push cloud Baidu**

    ![Azure Notification Hubs - SDK1 Baidu min](./media/notification-hubs-baidu-get-started/AndroidMinSDK.png)
    ![Azure Notification Hubs - SDK2 Baidu min](./media/notification-hubs-baidu-get-started/AndroidMinSDK2.png)

3.  Cliquez sur Next et continuez à suivre l’Assistant jusqu’à ce que la fenêtre Create Activity s’affiche. Assurez-vous que l’option Empty Activity est sélectionnée, puis cliquez sur Finish pour créer une nouvelle application Android.

    ![Azure Notification Hubs - Activité d’ajout Baidu](./media/notification-hubs-baidu-get-started/AndroidAddActivity.png)

4.  Assurez-vous que la cible de génération du projet est définie correctement.

5.  Ajoutez ensuite des bibliothèques Azure Notification Hubs. Dans le fichier `Build.Gradle` de l’application, dans la section dépendances, ajoutez les lignes suivantes.

    ```javascript
    compile 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
    compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

    Ajoutez le référentiel suivant après la section dépendances.

    ```javascript
    repositories {
        maven {
            url "http://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

    Afin d’éviter le conflit de List, nous devons ajouter le code suivant dans **Manifest.xml**.

    ```xml
    <manifest package="YOUR.PACKAGE.NAME"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:android="http://schemas.android.com/apk/res/android">
    ```

    puis, dans la balise `<application/>` :

    ```xml
    <application
        tools:replace="android:allowBackup,icon,theme,label">
    ```

6.  Téléchargez et décompressez le [Push Baidu Android SDK]. Copiez le fichier `pushservice-x.y.z jar` dans le dossier de bibliothèques. Puis copiez les fichiers `.so` dans les dossiers `src/main/jniLibs` (créer un nouveau dossier) de votre application Android.

    ![Azure Notification Hubs - Bibliothèques SDK Baidu](./media/notification-hubs-baidu-get-started/BaiduSDKLib.png)

7. Cliquez avec le bouton droit sur le fichier pushervice-x.y.z.jar dans le dossier de bibliothèques, cliquez sur Ajouter en tant que bibliothèque pour inclure cette bibliothèque dans le projet.

    ![Azure Notification Hubs - Ajouter en tant que bibliothèque Baidu](./media/notification-hubs-baidu-get-started/BaiduAddAsALib.jpg)

8. Ouvrez le fichier **AndroidManifest.xml** de votre projet Android et ajoutez les autorisations requises par le Kit de développement logiciel (SDK) Baidu. **Remplacez `YOURPACKAGENAME` avec le nom de votre package**.

    ```xml
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
    <uses-permission android:name="android.permission.EXPAND_STATUS_BAR" />
    !! <uses-permission android:name="baidu.push.permission.WRITE_PUSHINFOPROVIDER.YOURPACKAGENAME" />
    !!<permission android:name="baidu.push.permission.WRITE_PUSHINFOPROVIDER.YOURPACKAGENAME"android:protectionLevel="normal" />

    ```

9. Ajoutez la configuration ci-après dans l’élément application après l’élément d’activité `.MainActivity` en remplaçant *yourprojectname* (par exemple, `com.example.BaiduTest`) :

    ```xml
    <activity
        android:name="com.baidu.android.pushservice.richmedia.MediaViewActivity"
        android:configChanges="orientation|keyboardHidden"
        android:label="MediaViewActivity" />
    <activity
        android:name="com.baidu.android.pushservice.richmedia.MediaListActivity"
        android:configChanges="orientation|keyboardHidden"
        android:label="MediaListActivity"
        android:launchMode="singleTask" />
 
    <!-- Push application definition message -->
    <receiver android:name=".MyPushMessageReceiver">
        <intent-filter>

            <!-- receive push message-->
            <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
            <!-- receive bind,unbind,fetch,delete.. message-->
            <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
            <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
        </intent-filter>
    </receiver>

    <receiver
        android:name="com.baidu.android.pushservice.PushServiceReceiver"
        android:process=":bdservice_v1">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
            <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
            <action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
            <action android:name="com.baidu.android.pushservice.action.media.CLICK" />
            <action android:name="android.intent.action.MEDIA_MOUNTED" />
            <action android:name="android.intent.action.USER_PRESENT" />
            <action android:name="android.intent.action.ACTION_POWER_CONNECTED" />
            <action android:name="android.intent.action.ACTION_POWER_DISCONNECTED" />
        </intent-filter>
    </receiver>

    <receiver
        android:name="com.baidu.android.pushservice.RegistrationReceiver"
        android:process=":bdservice_v1">
        <intent-filter>
            <action android:name="com.baidu.android.pushservice.action.METHOD" />
            <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
        </intent-filter>
        <intent-filter>
            <action android:name="android.intent.action.PACKAGE_REMOVED" />

            <data android:scheme="package" />
        </intent-filter>
    </receiver>

    <service
        android:name="com.baidu.android.pushservice.PushService"
        android:exported="true"
        android:process=":bdservice_v1">
        <intent-filter>
            <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
        </intent-filter>
    </service>

    <service
        android:name="com.baidu.android.pushservice.CommandService"
        android:exported="true" />

    <!-- Adapt the ContentProvider declaration required for the Android N system, and the write permissions include the application package name-->
    <provider
        android:name="com.baidu.android.pushservice.PushInfoProvider"
        android:authorities="com.baidu.push.example.bdpush"
        android:exported="true"
        android:protectionLevel="signature"
        android:writePermission="baidu.push.permission.WRITE_PUSHINFOPROVIDER. yourprojectname  " />

    <!-- API Key of the Baidu application -->
    <meta-data
        android:name="api_key"
        !!   android:value="api_key" />
    </application>
    ```

10. Ajoutez une nouvelle classe nommée `ConfigurationSettings.java` au projet.

    ```java
    public class ConfigurationSettings {
        public static String API_KEY = "...";
        public static String NotificationHubName = "...";
        public static String NotificationHubConnectionString = "...";
    }
    ```
    
    Définissez la valeur de la chaîne `API_KEY` avec l’API_KEY à partir du projet cloud Baidu.
    
    Définissez la valeur de la chaîne `NotificationHubName` avec le nom de votre concentrateur de notification à partir du [portail Azure]; puis `NotificationHubConnectionString` avec `DefaultListenSharedAccessSignature` à partir du [portail Azure].

11. Ouvrez MainActivity.java et ajoutez le code suivant à la méthode onCreate :

    ```java
    PushManager.startWork(this, PushConstants.LOGIN_TYPE_API_KEY,  API_KEY );
    ```

12. Ajoutez une nouvelle classe appelée `MyPushMessageReceiver.java`DemoApplication.java et ajoutez-lui le code suivant. Il s’agit de la classe qui gère les notifications Push reçues à partir du serveur push Baidu.

    ```java
    package your.package.name;

    import android.content.Context;
    import android.content.Intent;
    import android.os.AsyncTask;
    import android.text.TextUtils;
    import android.util.Log;

    import com.baidu.android.pushservice.PushMessageReceiver;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    import org.json.JSONException;
    import org.json.JSONObject;

    import java.util.List;

    public class MyPushMessageReceiver extends PushMessageReceiver {

        public static final String TAG = MyPushMessageReceiver.class
                .getSimpleName();
        public static NotificationHub hub = null;
        public static String mChannelId, mUserId;

        @Override
        public void onBind(Context context, int errorCode, String appid,
                        String userId, String channelId, String requestId) {
            String responseString = "onBind errorCode=" + errorCode + " appid="
                    + appid + " userId=" + userId + " channelId=" + channelId
                    + " requestId=" + requestId;
            Log.d(TAG, responseString);

            if (errorCode == 0) {
                // Binding successful
                Log.d(TAG, " Binding successful");
            }
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
            mChannelId = channelId;
            mUserId = userId;

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
        public void onMessage(Context context, String message,
                            String customContentString) {
            String messageString = " onMessage=\"" + message
                    + "\" customContentString=" + customContentString;
            Log.d(TAG, messageString);
            if (!TextUtils.isEmpty(customContentString)) {
                JSONObject customJson = null;
                try {
                    customJson = new JSONObject(customContentString);
                    String myvalue = null;
                    if (!customJson.isNull("mykey")) {
                        myvalue = customJson.getString("mykey");
                    }
                } catch (JSONException e) {
                    e.printStackTrace();
                }
            }

        }

        @Override
        public void onNotificationArrived(Context context, String title, String description, String customContentString) {
            String notifyString = " Notice Arrives onNotificationArrived  title=\"" + title
                    + "\" description=\"" + description + "\" customContent="
                    + customContentString;
            Log.d(TAG, notifyString);
            if (!TextUtils.isEmpty(customContentString)) {
                JSONObject customJson = null;
                try {
                    customJson = new JSONObject(customContentString);
                    String myvalue = null;
                    if (!customJson.isNull("mykey")) {
                        myvalue = customJson.getString("mykey");
                    }
                } catch (JSONException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }
            }
        }

        @Override
        public void onNotificationClicked(Context context, String title, String description, String customContentString) {
            String notifyString = " onNotificationClicked title=\"" + title + "\" description=\""
                    + description + "\" customContent=" + customContentString;
            Log.d(TAG, notifyString);
            Intent intent = new Intent(context.getApplicationContext(),MainActivity.class);
            intent.putExtra("title",title);
            intent.putExtra("description",description);
            intent.putExtra("isFromNotify",true);
            intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
            context.getApplicationContext().startActivity(intent);

        }

        @Override
        public void onSetTags(Context context, int errorCode,
                            List<String> successTags, List<String> failTags, String requestId) {
            String responseString = "onSetTags errorCode=" + errorCode
                    + " successTags=" + successTags + " failTags=" + failTags
                    + " requestId=" + requestId;
            Log.d(TAG, responseString);

        }

        @Override
        public void onDelTags(Context context, int errorCode,
                            List<String> successTags, List<String> failTags, String requestId) {
            String responseString = "onDelTags errorCode=" + errorCode
                    + " successTags=" + successTags + " failTags=" + failTags
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

            if (errorCode == 0) {
                // Unbinding is successful
                Log.d(TAG, " Unbinding is successful ");
            }
        }
    }
    ```

## <a name="send-notifications-to-your-app"></a>Envoi de notifications à votre application

Vous pouvez tester rapidement la réception de notifications à partir du [portail Azure] : utilisez le bouton **Envoyer** sur l’écran du hub de notification, comme illustré ans les écrans ci-dessous :

![](./media/notification-hubs-baidu-get-started/BaiduTestSendButton.png)
![](./media/notification-hubs-baidu-get-started/BaiduTestSend.png)

Les notifications Push sont normalement envoyées dans un service principal tel que Mobile Services ou ASP.NET à l’aide d’une bibliothèque compatible. Si une bibliothèque n’est pas disponible pour votre serveur principal, vous pouvez utiliser l’API REST directement pour envoyer des messages de notification.

Par souci de simplicité, ce didacticiel utilise une application console pour la démonstration de l’envoi d’une notification avec le kit de développement .NET. Toutefois, nous vous recommandons de consulter le didacticiel [Utiliser Notification Hubs pour envoyer des notifications Push aux utilisateurs](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) comme prochaine étape pour envoyer des notifications à partir d’un serveur principal ASP.NET. 

Voici les différentes approches d’envoi de notifications :
* **Interface REST** : vous pouvez prendre en charge les notifications sur n’importe quel serveur principal à l’aide de [l’interface REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).
* **SDK .NET Microsoft Azure Notification Hubs**: dans le Gestionnaire de package Nuget pour Visual Studio, exécutez [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).
* **Node.js** : [Utilisation de Notification Hubs à partir de Node.js](notification-hubs-nodejs-push-notification-tutorial.md).
* **Applications mobiles Azure**: pour découvrir un exemple de la procédure d’envoi de notifications à partir d’une application mobile Azure intégrée à Notification Hubs, consultez l’article [Add push notifications for Mobile Apps](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md) (Ajouter des notifications Push pour Mobile Apps).
* **Java/PHP** : pour voir un exemple d’envoi de notifications au moyen des API REST, consultez « Utilisation de Notification Hubs depuis Java/PHP » ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).

## <a name="optional-send-notifications-from-a-net-console-app"></a>(Facultatif) Envoi de notifications à partir d’une application de console .NET.
Dans cette section, nous montrons comment envoyer une notification à l’aide d’une application console .NET.

1. Créez une application console Visual C# :
   
    ![](./media/notification-hubs-baidu-get-started/ConsoleProject.png)

2. Dans la fenêtre Console du gestionnaire de package, choisissez **Projet par défaut** comme nouveau projet d’application console, puis exécutez la commande suivante dans la fenêtre de console :
   
        Install-Package Microsoft.Azure.NotificationHubs
   
    Cette instruction ajoute une référence au Kit de développement logiciel (SDK) Azure Notification Hubs à l’aide du <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">package NuGet Microsoft.Azure.Notification Hubs</a>.
   
    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

3. Ouvrez le fichier `Program.cs` et ajoutez l’instruction suivante :
   
    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

4. Dans votre classe `Program`, ajoutez la méthode suivante et remplacez `DefaultFullSharedAccessSignatureSASConnectionString` et `NotificationHubName` avec les valeurs que vous avez.
   
    ```csharp
    private static async void SendNotificationAsync()
    {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
        string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
        var result = await hub.SendBaiduNativeNotificationAsync(message);
    }
    ```

5. Ajoutez ensuite les lignes suivantes dans votre méthode `Main` :

    ```csharp
    SendNotificationAsync();
    Console.ReadLine();
    ```

## <a name="test-your-app"></a>Test de l'application

Pour tester cette application avec un téléphone réel, connectez simplement le téléphone à votre ordinateur à l’aide d’un câble USB. Cette action charge votre application sur le téléphone attaché.

Pour tester cette application avec l’émulateur, dans la barre d’outils supérieure d’Android Studio, cliquez sur **Run**, puis sélectionnez votre application : elle démarre l’émulateur, puis charge et exécute l’application.

L’application récupère les paramètres `userId` et `channelId` à partir du service de notification push Baidu et s’inscrit auprès du hub de notification.

Pour envoyer une notification de test, vous pouvez utiliser l’onglet de débogage du [portail Azure]. Si vous avez intégré l’application de console .NET, appuyez sur la touche F5 dans Visual Studio pour exécuter l’application. L’application envoie une notification qui s’affiche dans la zone de notification supérieure de votre appareil ou de l’émulateur.

<!-- URLs. -->
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Push Baidu Android SDK]: http://push.baidu.com/sdk/push_client_sdk_for_android
[portail Azure]: https://portal.azure.com/
[portail Baidu]: http://www.baidu.com/
