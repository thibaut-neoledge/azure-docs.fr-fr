<properties pageTitle="Get started with push notifications (Appcelerator) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Appcelerator app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="Appcelerator team;mahender" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-appcelerator" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="Appcelerator team;mahender"></tags>

# Prise en main des notifications Push dans Mobile Services (transmission de type push héritée)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store&nbsp;C#">Windows Store&nbsp;C#</a>
    <a href="/fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/fr-fr/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/fr-fr/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/fr-fr/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>     <a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
    <a href="/fr-fr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator" class="current">Appcelerator</a>
</div>

Cette rubrique montre comment utiliser Microsoft Azure Mobile Services pour envoyer des notifications Push à des applications iOS et Android développées par l'intermédiaire d'Appcelerator Titanium Studio. Dans ce didacticiel, vous allez ajouter des notifications Push à l'aide du service de notifications Push Apple (APNS) et de Google Cloud Messaging au projet de démarrage rapide. Une fois la procédure terminée, votre service mobile envoie une notification Push chaque fois qu'un enregistrement est inséré.

> [WACOM.NOTE] Mobile Services s'intègre à Azure Notification Hubs pour prendre en charge une fonctionnalité de notification Push supplémentaire, comme les modèles, les plateformes multiples et une mise à l'échelle améliorée. Cette rubrique prend en charge les services mobiles existants qui n'ont pas encore été mis à niveau pour utiliser l'intégration à Notification Hubs. Lorsque vous créez un service mobile, cette fonctionnalité intégrée est automatiquement activée. Vous devez mettre à niveau votre service pour utiliser Notification Hubs lorsque cela est possible. **Un didacticiel consacré aux notifications Push envoyées par Notification Hubs avec Appcelerator sera prochainement disponible.**

1.  [Génération de la demande de signature de certificat][Génération de la demande de signature de certificat]
2.  [Inscription de votre application et activation des notifications Push][Inscription de votre application et activation des notifications Push]
3.  [Création d'un profil de mise en service pour l'application][Création d'un profil de mise en service pour l'application]
4.  [Activation de Google Cloud Messaging][Activation de Google Cloud Messaging]
5.  [Création du module GCM pour Titanium][Création du module GCM pour Titanium]
6.  [Configuration de Mobile Services][Configuration de Mobile Services]
7.  [Ajout de notifications Push à l'application][Ajout de notifications Push à l'application]
8.  [Mise à jour des scripts pour l'envoi de notifications Push][Mise à jour des scripts pour l'envoi de notifications Push]
9.  [Insertion de données pour recevoir des notifications][Insertion de données pour recevoir des notifications]

Ce didacticiel requiert les éléments suivants :

-   Module Appcelerator d'Azure Mobile Services
-   Appcelerator Titanium Studio 3.2.1 ou version ultérieure
-   Un appareil compatible avec iOS 7.0 (ou une version ultérieure) et Android 4.3 (ou une version ultérieure)
-   Un abonnement au programme pour développeurs iOS
-   Un compte Google actif

> [WACOM.NOTE] En raison de la configuration requise pour les notifications Push, vous devez déployer et tester les notifications Push sur un appareil compatible iOS (iPhone ou iPad) au lieu d'un émulateur.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez effectuer le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services].

[WACOM.INCLUDE [Enable Apple Push Notifications][Enable Apple Push Notifications]]

## <a name="register-gcm"></a>Activation de Google Cloud Messaging

> [WACOM.NOTE]Pour exécuter cette procédure, vous devez disposer d'un compte Google avec une adresse électronique vérifiée. Pour créer un compte Google, consultez la page [accounts.google.com][accounts.google.com].

[WACOM.INCLUDE [Enable GCM][Enable GCM]]

## <a name="gcm-module"></a>Création du module GCM pour Titanium

### Préparation d'Appcelerator Titanium Studio pour la création du module

Pour créer des modules Android, vous avez besoin d'installer le support Java au sein d'Appcelerator Titanium Studio. Si vous ne l'avez pas déjà fait, consultez la page [Installation des outils de développement Java][Installation des outils de développement Java] d'Appcelerator pour une description de la procédure.

Vous devez installer le NDK Android. Téléchargez le fichier .zip approprié sur le site [][]<http://developer.android.com/sdk/ndk/index.html></a> et décompressez-le sur le disque. Mémorisez l'emplacement choisi.

### Création d'un module

1.  Ouvrez Appcelerator Titanium Studio.

2.  Cliquez sur File (Fichier) -\> New (Nouveau) -\> Mobile Module Project (Projet de module mobile).

    ![][]

3.  Dans la fenêtre suivante, entrez les données des paramètres du projet :

    -   **Project name (Nom du projet) :** dans le cas présent, nous utilisons le nom de projet « notificationhub » (vous pouvez utiliser le même nom).

    -   **Module Id (ID du module) :** dans le cas présent, nous utilisons l'ID de module « com.winwire.notificationhub ». Ce dernier doit correspondre à l'ID d'application.

    -   **Deployment Targets (Cibles de déploiement) :** dans le cas présent, nous sélectionnons Android.

    > [WACOM.NOTE] Il est important que le nom de notre espace de travail ne contienne pas d'espaces, sinon nous aurons des problèmes lors de la création de la compilation.

    ![][1]

4.  Appuyez ensuite sur Next (Suivant) et fournissez les informations pour le module.

    ![][2]

5.  Enfin, appuyez sur Finish (Terminer).

6.  Ouvrez le fichier timodule.xml et ajoutez les modifications suivantes dans la balise Android.

        <manifest package="com.winwire.notificationhub" android:versionCode="1" android:versionName="1.0">
        <supports-screens android:anyDensity="true"/>
        <uses-sdk android:minSdkVersion="8"/>
        <uses-permission android:name= "com.google.android.c2dm.permission.RECEIVE"/>
        <permission android:name= "${tiapp.properties['id']}.permission.C2D_MESSAGE" android:protectionLevel="signature"/>
        <uses-permission android:name= "${tiapp.properties['id']}.permission.C2D_MESSAGE"/>
        <uses-permission android:name= "android.permission.WAKE_LOCK"/>
        <uses-permission android:name= "android.permission.VIBRATE"/>
        <uses-permission android:name= "android.permission.INTERNET"/>
        <uses-permission android:name= "android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name= "android.permission.USE_CREDENTIALS"/>
        <uses-permission android:name= "android.permission.ACCESS_NETWORK_STATE"/>
        <application>
        <service android:name= "com.winwire.notificationhub.GCMIntentService" />
        <receiver android:name= "com.google.android.gcm.GCMBroadcastReceiver" android:permission= "com.google.android.c2dm.permission.SEND">
        <!-- Start receiver on boot -->
        <intent-filter>
        <action android:name= "android.intent.action.BOOT_COMPLETED"/>
        <category android:name= "android.intent.category.HOME"/>
        </intent-filter>
        <!-- Receive the actual message -->
        <intent-filter>
        <action android:name= "com.google.android.c2dm.intent.RECEIVE" />
        <category android:name= "${tiapp.properties['id']}" />
        </intent-filter>
        <!-- Receive the registration id -->
        <intent-filter>
        <action android:name= "com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name= "${tiapp.properties['id']}" />
        </intent-filter>
        </receiver>
        </application>
        </manifest>

> [WACOM.NOTE] Dans le code ci-dessus, remplacez toutes les instances du texte *com.winwire.notificationhub* par le nom du package de votre application (ID du module).

1.  Dans votre module Notification Hub, cliquez avec le bouton droit sur le dossier « src », accédez à « New » (Nouveau) et sélectionnez « folder » (dossier). Attribuez au dossier le nom com.google.android.gcm.

> [WACOM.NOTE] Si « folder » n'apparaît pas dans les options sous « New » (Nouveau), sélectionnez « Other » (Autres) et développez General (Général), puis sélectionnez « Folder » (Dossier).

1.  Téléchargez maintenant les fichiers « .java » (gcm.zip) depuis cet emplacement et copiez-les dans le dossier nouvellement créé (com.google.android.gcm).

2.  Recherchez à présent le dossier nommé d'après l'ID du module et développez-le. Ce dossier contient la liste de fichiers « .java ». Dans ces fichiers, ouvrez celui qui a le nom du projet+module.java (par exemple, si le nom de votre projet est notificationhub, le fichier aura un nom du type « NotificationhubModule.java ») et ajoutez les lignes de code ci-dessous au début du fichier.

        private static NotificationhubModule _THIS;
        private KrollFunction successCallback;
        private KrollFunction errorCallback;
        private KrollFunction messageCallback;

3.  Dans le même fichier, recherchez le constructeur et remplacez-le par le code ci-dessous.

        public NotificationhubModule() {
            super();
            _THIS = this;
        }

4.  À la fin du même fichier, ajoutez les lignes de code suivantes.

        @Kroll.method
        public void registerC2dm(HashMap options) {
            successCallback = (KrollFunction) options.get("success");
            errorCallback = (KrollFunction) options.get("error");
            messageCallback = (KrollFunction) options.get("callback");
            String registrationId = getRegistrationId();
            if (registrationId != null && registrationId.length() > 0) {
                sendSuccess(registrationId);
            } else {
                GCMRegistrar.register(TiApplication.getInstance(), getSenderId());
            }
        }
        @Kroll.method
        public void unregister() {
            GCMRegistrar.unregister(TiApplication.getInstance());
        }
        @Kroll.method
        public String getRegistrationId() {
            return GCMRegistrar.getRegistrationId(TiApplication.getInstance());
        }
        @Kroll.method
        public String getSenderId() {
            return TiApplication.getInstance().getAppProperties()
            .getString("com.winwire.notificationhub.sender_id", "");
        }

        public void sendSuccess(String registrationId) {
            if (successCallback != null) {
                HashMap data = new HashMap();
                data.put("registrationId", registrationId);
                successCallback.callAsync(getKrollObject(), data);
            }
        }

        public void sendError(String error) {
            if (errorCallback != null) {
                HashMap data = new HashMap();
                data.put("error", error);
                errorCallback.callAsync(getKrollObject(), data);
            }
        }

        public void sendMessage(HashMap messageData) {
            if (messageCallback != null) {
                HashMap data = new HashMap();
                data.put("data", messageData);
                messageCallback.call(getKrollObject(), data);
            }
        }
        public static NotificationhubModule getInstance() {
            return _THIS;
        }

5.  Téléchargez à présent le fichier .zip du module et copiez les fichiers dans un dossier dont le nom correspond à l'ID du module.

> [WACOM.NOTE] Dans les fichiers ci-dessus, vous devez remplacer toutes les instances du texte *com.winwire.notificationhub* par le nom du package de votre application (ID du module). Remplacez également « NotificationhubModule » par NomProjet+Module (par exemple, « NotificationhubModule »).

### Création/empaquetage d'un module

Choisissez **Deploy \> Package - Android Module** (Déployer \> Package - Module Android). Vous ne pouvez pas créer un module BlackBerry avec Studio. Utilisez soit les outils de l'interface de ligne de commande du NDK BlackBerry soit l'environnement de développement intégré (IDE) Momentics.

![][3]

Vous pouvez alors choisir de déployer le module pour tous les projets ou pour un projet particulier, en suivant les règles d'installation indiquées à la page [Using Titanium Modules][Using Titanium Modules] (Utilisation des modules Titanium). Pour résumer :

-   Pour tous les projets : le fichier .zip du module est déposé à la racine de l'emplacement d'installation du Kit de développement logiciel (SDK) Titanium.

-   Pour un projet particulier : le fichier .zip du module est déposé à la racine de votre projet.

## <a name="configure"></a>Configuration de Mobile Services pour l'envoi de demandes push

[WACOM.INCLUDE [mobile-services-apns-configure-push][mobile-services-apns-configure-push]]

1.  Entrez la valeur de la clé API fournie par GCM au cours de la procédure précédente, puis cliquez sur Save (Enregistrer).

    ![][4]

Votre service mobile est désormais configuré pour utiliser APNS et GCM.

## <a name="add-push"></a>Ajout de notifications push à votre application

1.  Dans tiapp.xml, sélectionnez l'onglet tiapp.xml (qui se trouve en bas, à côté de l'onglet « Vue d'ensemble ») et recherchez la balise `<android>`. Sous la balise, ajoutez le code suivant :

        <modules>
            <module platform="android">ModuleId</module>
        </modules>
        <property name="ApplicationId.sender_id" type="string">Provide your GCM sender ID</property>
        <property name="ApplicationId.icon type="int">2130837504</property>
        <property name="ApplicationId.component" type="string">ApplicationId/ApplicationId.AppNameActivity</property>

> [WACOM.NOTE] Dans le code ci-dessus, vous devez remplacer **ModuleId** et **ApplicationId** : l'ID du module, que vous avez défini à la création du module GCM, et l'ID de l'application, qui est entré au moment de la création du projet. Assurez-vous également que les valeurs **ModuleId** et **ApplicationId** sont identiques. Il faut également modifier **ApplicationId.AppNameActivity**, qui devrait avoir l'aspect suivant : com.winwire.notificationhub/ com.winwire.notificationhub.NotificationhubActivity.

1.  Copiez le module GCM que vous avez créé précédemment et placez-le à l'emplacement ci-dessous.

    <table>
    <tr><td>
    OSX
    </td><td>
    /Library/Application Support/Titanium ou ~/Library/Application Support/Titanium
    </td></tr>
    <tr><td>
    Windows 7
    </td><td>
    C:\\Users\\username\\AppData\\Roaming (ou C:\\ProgramData\\Titanium sur Titanium Studio 1.0.1 et les versions antérieures)
    </td></tr>
    <tr><td>
    Windows XP
    </td><td>
    C:\\Documents and Settings\\nom\_utilisateur\\Application Data (ou C:\\Documents and Settings\\All Users\\Application Data\\Titanium sur Titanium Studio 1.0.1 et les versions antérieures)
    </td></tr>
    <tr><td>
    Linux
    </td><td>
    ~/.titanium
    </td></tr>
    </table>

> [WACOM.NOTE] Dans Mac OS, Library est un dossier masqué. Pour le rendre visible, vous devez exécuter la commande suivante, puis relancer le Finder : `**defaults write com.apple.finder AppleShowAllFiles TRUE**`

1.  Dans Appcelerator Titanium Studio, ouvrez le fichier index.js et ajoutez le code suivant à la fin du fichier. Ce code inscrira votre appareil aux notifications Push.

            Alloy.Globals.tempRegId = '';
                if (OS_ANDROID) {
                var gcm = require('com.winwire.notificationhub');
            gcm.registerC2dm({
            success : function(e) {
            var regId = e.registrationId;
            Alloy.Globals.tempRegId = regId;
            },
            error : function(e) {
            alert("Error during registration : " + e.error);
            var message;
            if (e.error == "ACCOUNT_MISSING") {
            message = "No Google account found; you will need to add on in order to activate notifications";
            }
            Titanium.UI.createAlertDialog({
            title : 'Push Notification Setup',
            message : message,
            buttonNames : ['OK']
            }).show();
            },
            callback : function(e)// called when a push notification is received
            {
            var data = JSON.stringify(e.data);
            var intent = Ti.Android.createIntent({
            action : Ti.Android.ACTION_MAIN,
            className : 'com.winwire.notificationhub. WindowsAzureActivity',
            flags : Ti.Android.FLAG_ACTIVITY_RESET_TASK_IF_NEEDED | Ti.Android.FLAG_ACTIVITY_SINGLE_TOP
            });
            intent.addCategory(Titanium.Android.CATEGORY_LAUNCHER);
            var NotificationClickAction = Ti.Android.createPendingIntent({
            activity : Ti.Android.currentActivity,
            intent : intent,
            flags : Ti.Android.FLAG_UPDATE_CURRENT,
            type : Ti.Android.PENDING_INTENT_FOR_ACTIVITY
            });
            var NotificationMembers = {
            contentTitle : 'Notification Hub Demo',
            contentText : e.data.message,
            defaults : Titanium.Android.NotificationManager.DEFAULT_SOUND,
            tickerText : 'Notification Hub Demo',
            icon : Ti.App.Android.R.drawable.appicon,
            when : new Date(),
            flags : Ti.Android.FLAG_AUTO_CANCEL,
            contentIntent : NotificationClickAction
            };
            Ti.Android.NotificationManager.notify(1, .Android.createNotification(NotificationMembers));
            var toast = Titanium.UI.createNotification({
            duration : 2000,
            message : e.data.message
            });
            toast.show();
            alert(e.data.message);
            }
            });
            } else  if (OS_IOS) {
            Titanium.Network.registerForPushNotifications({
            types : [Titanium.Network.NOTIFICATION_TYPE_BADGE, .Network.NOTIFICATION_TYPE_ALERT, .Network.NOTIFICATION_TYPE_SOUND],
            success : function(e) {
            deviceToken = e.deviceToken;
            Alloy.Globals.tempRegId = deviceToken;
            },
            error : function(e) {
            Ti.API.info("Error during registration: " + e.error);
            },
            callback : function(e) {
            var data = e.data.inAppMessage;
            if (data != null && data != '') {
            alert(data);
            }
            }
            });
            }

2.  Ouvrez maintenant le fichier TableData.js et recherchez les lignes suivantes dans la fonction **addOrUpdateDataFromAndroid** :

        var request = {
            'text' : alertTextField.getValue(),
            'complete' : false
        };

    Remplacez le code ci-dessus dans la condition else (pour l'insertion d'un nouvel élément) uniquement.

3.  Pour Android, remplacez le code ci-dessus par le code suivant :

	    var request = {
	     'text' : alertTextField.getValue(),
	     'complete' : false,
	     'handle' : Alloy.Globals.tempRegId
	    };

4.  À présent, recherchez les lignes suivantes dans la fonction **updateOrAddData** :

           var request = {
            'text' : alertTextField.getValue(),
            'complete' : false
        };

    Remplacez le code ci-dessus dans la condition else (pour l'insertion d'un nouvel élément) uniquement.

5.  Pour iOS, remplacez le code ci-dessus par le code suivant :

	    var request = {
	     'text' : alertTextField.getValue(),
	     'complete' : false,
	     'deviceToken' : Alloy.Globals.tempRegId
	    };

Votre application a été mise à jour pour prendre en charge les notifications Push sur les plateformes iOS et Android.

## <a name="update-scripts"></a>Mise à jour du script d'insertion inscrit dans le portail de gestion

1.  Dans le portail de gestion, cliquez sur l'onglet Données, puis sur la table TodoItem.

    ![][5]

2.  Dans TodoItem, cliquez sur l'onglet Script et sélectionnez Insérer.

    ![][6]

    La fonction appelée lors d'une insertion dans la table TodoItem s'affiche.

3.  Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

**Pour iOS :**

        function insert(item, user, request) { 
            request.execute();
            // Set timeout to delay the notification, to provide time for the  
            // app to be closed on the device to demonstrate toast notifications 
            setTimeout(function() { 
                push.apns.send(item.deviceToken, { 
                    alert: "Toast: " + item.text, 
                    payload: { 
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'" 
                    }
                 }); 
            }, 2500);
        }

    > [WACOM.NOTE] This script delays sending the notification to give you time to close the app to receive a push notification.  

**Pour Android :**

          function insert(item, user, request) {
            request.execute({ 
                success: function() {  
                    // Write to the response and then send the notification in the background 
                    request.respond();  
                    push.gcm.send(item.handle, item.text, {
                        success: function(response) { 
                            console.log('Push notification sent: ', response);
                        }, error: function(error)   { 
                            console.log('Error sending push notification: ', error);      
                            }      
                   }); 
                }  
          });  
        }

Ce code inscrit un nouveau script d'insertion, qui utilise l'[objet Push Mobile Services][objet Push Mobile Services] pour envoyer une notification Push (le texte inséré) à l'appareil fourni dans la demande d'insertion.

<!-- Images. -->
<!-- Anchors. -->
<!-- URLs. -->

  [Windows Store C\#]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-push "Windows Store C#"
  [Windows Store JavaScript]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-push "Windows Store JavaScript"
  [Windows Phone]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started-push "Windows Phone"
  [iOS]: /fr-fr/documentation/articles/mobile-services-ios-get-started-push "iOS"
  [Android]: /fr-fr/documentation/articles/mobile-services-android-get-started-push "Android"
  [Appcelerator]: /fr-fr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push "Appcelerator"
  [Génération de la demande de signature de certificat]: #certificates
  [Inscription de votre application et activation des notifications Push]: #register
  [Création d'un profil de mise en service pour l'application]: #profile
  [Activation de Google Cloud Messaging]: #register-gcm
  [Création du module GCM pour Titanium]: #gcm-module
  [Configuration de Mobile Services]: #configure
  [Ajout de notifications Push à l'application]: #add-push
  [Mise à jour des scripts pour l'envoi de notifications Push]: #update-scripts
  [Insertion de données pour recevoir des notifications]: #test
  [Prise en main de Mobile Services]: /fr-fr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started
  [Enable Apple Push Notifications]: ../includes/enable-apple-push-notifications.md
  [accounts.google.com]: http://go.microsoft.com/fwlink/p/?LinkId=268302
  [Enable GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [Installation des outils de développement Java]: http://docs.appcelerator.com/titanium/latest/#!/guide/Installing_the_Java_Development_Tools
  []: http://developer.android.com/sdk/ndk/index.html
  []: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0011.png
  [1]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0031.png
  [2]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0041.png
  [3]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0061.png
  [Using Titanium Modules]: http://docs.appcelerator.com/titanium/latest/#!/guide/Using_Titanium_Modules
  [mobile-services-apns-configure-push]: ../includes/mobile-services-apns-configure-push.md
  [4]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image062.png
  [5]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image064.png
  [6]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image066.png
  [objet Push Mobile Services]: http://go.microsoft.com/fwlink/p/?linkid=272333&clcid=0x409
