<properties 
	pageTitle="Prise en main des notifications Push (Appcelerator) | Centre de développement mobile" 
	description="Découvrez comment utiliser Azure Mobile Services pour envoyer des notifications Push à votre application Appcelerator." 
	services="mobile-services" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-appcelerator" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="mahender"/>

# Prise en main des notifications Push dans Mobile Services (Push hérité)
<div class="dev-center-tutorial-selector sublanding">
	<a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#">Windows Store C#</a>
	<a href="/fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows Store JavaScript</a>
	<a href="/fr-fr/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
	<a href="/fr-fr/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
	<a href="/fr-fr/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
	<a href="/fr-fr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator" class="current">Appcelerator</a>
</div>

Cette rubrique montre comment utiliser Microsoft Azure Mobile Services pour envoyer des notifications Push à des applications iOS et Android développées par l'intermédiaire d'Appcelerator Titanium Studio. Dans ce didacticiel, vous allez ajouter des notifications Push à l'aide du service de notifications Push Apple (APNS) et de Google Cloud Messaging au projet de démarrage rapide. Une fois la procédure terminée, votre service mobile envoie une notification Push chaque fois qu'un enregistrement est inséré.

>[AZURE.NOTE] Mobile Services s'intègre à Azure Notification Hubs pour prendre en charge une fonctionnalité de notification Push supplémentaire, comme les modèles, les plateformes multiples et la mise à l'échelle améliorée. Cette rubrique prend en charge les services mobiles existants qui n'ont pas encore été mis à niveau pour utiliser l'intégration à Notification Hubs. Lorsque vous créez un service mobile, cette fonctionnalité intégrée est automatiquement activée. Vous devez mettre à niveau votre service pour utiliser Notification Hubs lorsque cela est possible. **Un didacticiel consacré aux notifications Push envoyées par Notification Hubs avec Appcelerator sera prochainement disponible**

1.	[Génération de la demande de signature de certificat]
2.	[Inscription de votre application et activation des notifications Push]
3.	[Création d'un profil d'approvisionnement pour l'application]
4.	[Activation de Google Cloud Messaging]
5.  [Création du module GCM pour Titanium]
6.	[Configuration de Mobile Services]
7.	[Ajout de notifications Push à l'application]
8.	[Mise à jour des scripts pour l'envoi de notifications Push]
9.	[Insertion de données pour recevoir des notifications]

Ce didacticiel requiert les éléments suivants :

* Module Appcelerator d'Azure Mobile Services
* Appcelerator Titanium Studio 3.2.1 ou version ultérieure
* Un appareil compatible avec iOS 7.0 (ou une version ultérieure) et Android 4.3 (ou une version ultérieure)
* Un abonnement au programme pour développeurs iOS
* Un compte Google actif

> [AZURE.NOTE] En raison de la configuration requise pour les notifications Push, vous devez déployer et tester les notifications Push sur un appareil compatible iOS (iPhone ou iPad) au lieu d'un émulateur.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez effectuer le didacticiel [Prise en main de Mobile Services].

[AZURE.INCLUDE [Activation des notifications Pusj Apple](../includes/enable-apple-push-notifications.md)]

## <a name="register-gcm"></a>Activation de Google Cloud Messaging

>[AZURE.NOTE]Pour effectuer cette procédure, vous devez disposer d'un compte Google avec une adresse électronique vérifiée. Pour créer un nouveau compte Google, accédez à <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

[AZURE.INCLUDE [Activation GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]


##  <a name="gcm-module"></a>Création du module GCM pour Titanium

### Préparation d'Appcelerator Titanium Studio pour la création du module

Pour créer des modules Android, vous avez besoin d'installer le support Java au sein d'Appcelerator Titanium Studio. Si vous ne l'avez pas déjà fait, consultez la page [Installation des outils de développement Java] d'Appcelerator pour une description de la procédure.

Vous devez installer le NDK Android. Téléchargez le fichier .zip approprié sur le site [http://developer.android.com/sdk/ndk/index.html](http://developer.android.com/sdk/ndk/index.html) et décompressez-le sur le disque. Mémorisez l'emplacement choisi. 

### Création d'un module

1. Ouvrez Appcelerator Titanium Studio.

2. Cliquez sur File (Fichier) -> New (Nouveau) -> Mobile Module Project (Projet de module mobile).

    ![][0]

3. Dans la fenêtre suivante, entrez les données des paramètres du projet : 

    * **Nom du projet :** dans le cas présent, nous utilisons &quot;notificationhub&quot; (peut être la même).

    * **Id du module :** dans le cas présent, nous utilisons &quot;com.winwire.notificationhub&quot;. Il doit également correspondre avec notre " Id d'application ".

    * **Cibles de déploiement :** dans ce cas, nous allons sélectionner Android.

    > [AZURE.NOTE] Il est important que le nom de notre espace de travail ne contienne pas d'espaces, sinon nous aurons des problèmes lors de la création de la compilation.

    ![][1]

4. Appuyez ensuite sur Next (Suivant) et fournissez les informations pour le module.

    ![][2]

5. Enfin, appuyez sur Finish (Terminer).

6. Ouvrez le fichier timodule.xml et ajoutez les modifications suivantes dans la balise Android.


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




> [AZURE.NOTE] Dans le code ci-dessus, vous devez remplacer les instances du texte *com.winwire.notificationhub* par le nom de package de votre application (module Id).

7. Dans votre module Notification Hub, cliquez avec le bouton droit sur le dossier &quot;src&quot;, accédez à &quot;New&quot;(Nouveau) et sélectionnez &quot;folder&quot; (dossier). Attribuez au dossier le nom com.google.android.gcm.

> [AZURE.NOTE] Si &quot;folder&quot; (Dossier) n'apparaît pas dans les options sous &quot;New&quot; (Nouveau), sélectionnez &quot;Other&quot; (Autres) et développez General (Général) puis sélectionnez &quot;Folder&quot; (Dossier).

8. Téléchargez maintenant les fichiers &quot;.java&quot; (gcm.zip) depuis cet emplacement et copiez-les dans le dossier nouvellement créé(com.google.android.gcm).

9. Recherchez à présent le dossier nommé d'après l'ID du module et développez-le. Ce dossier contient la liste de fichiers " .java ". Dans ces fichiers, ouvrez celui qui a le nom du projet+module.java (par exemple, si le nom de votre projet est notificationhub, le fichier aura un nom du type &quot;NotificationhubModule.java&quot;) et ajoutez les lignes de code ci-dessous au début du fichier..

        private static NotificationhubModule _THIS;
        private KrollFunction successCallback;
        private KrollFunction errorCallback;
        private KrollFunction messageCallback;

10. Dans le même fichier, recherchez le constructeur et remplacez-le par le code ci-dessous.

        public NotificationhubModule() {
	        super();
	        _THIS = this;
        }

11. À la fin du même fichier, ajoutez les lignes de code suivantes.

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

12. Téléchargez à présent le fichier .zip du module et copiez les fichiers dans un dossier dont le nom correspond à l'ID du module.

> [AZURE.NOTE] Dans les fichiers ci-dessus, vous devez remplacer les instances du texte *com.winwire.notificationhub* par le nom de package de votre application (module Id). Remplacez également &quot;NotificationhubModule&quot; par NomProjet+Module (par exemple, &quot;NotificationhubModule&quot;).

### Création/empaquetage d'un module

Choisissez **Deploy > Package - Android Module** ((Déployer > Package - Module Android). Vous ne pouvez pas créer un module BlackBerry avec Studio. Utilisez soit les outils de l'interface de ligne de commande du NDK BlackBerry soit l'environnement de développement intégré (IDE) Momentics. 

![][3]


Vous pouvez alors choisir de déployer le module pour tous les projets ou pour un projet particulier, en suivant les règles d'installation indiquées à la page [Utilisation de modules Titanium] (Utilisation des modules Titanium). Pour résumer :

- Pour tous les projets : le fichier .zip du module est déposé à la racine de l'emplacement d'installation du Kit de développement logiciel (SDK) Titanium. 

- Pour un projet particulier : le fichier .zip du module est déposé à la racine de votre projet. 


## <a name="configure"></a>Configuration de Mobiles Services pour l'envoi de requête Push


[AZURE.INCLUDE [mobile-services-apns-configuration-push](../includes/mobile-services-apns-configure-push.md)]

5.	Entrez la valeur de la clé API fournie par GCM au cours de la procédure précédente, puis cliquez sur Save (Enregistrer).

    ![][4]

Votre service mobile est désormais configuré pour utiliser APNS et GCM.

## <a name="add-push"></a>Ajout de notifications Push à votre application

1.	Dans tiapp.xml, sélectionnez l'onglet tiapp.xml (qui se trouve en bas, à côté de l'onglet &quot;Overview&quot;, (Vue d'ensemble)) et recherchez la balise `<android>`. Sous la balise, ajoutez le code suivant :

        <modules>
            <module platform="android">ModuleId</module>
        </modules>
        <property name="ApplicationId.sender_id" type="string">Provide your GCM sender ID</property>
        <property name="ApplicationId.icon type="int">2130837504</property>
        <property name="ApplicationId.component" type="string">ApplicationId/ApplicationId.AppNameActivity</property>

>[AZURE.NOTE] Dans le code ci-dessus, vous devez remplacer **ModuleId**, **ApplicationId**. L'ID du module, que vous avez défini à la création du module GCM, et l'ID de l'application, qui est entré au moment de la création du projet.  Assurez-vous également que les valeurs de **ModuleId** et **ApplicationId** sont identiques. Vous avez également demandé la modification de **ApplicationId.AppNameActivity**. qui devrait avoir l'aspect suivant : com.winwire.notificationhub/ com.winwire.notificationhub.NotificationhubActivity.


2.	Copiez le module GCM que vous avez créé précédemment et placez-le à l'emplacement ci-dessous.	

    <table><tr>
    <td>OSX
    </td>
    <td>/Library/Application Support/Titanium ou  ~/Library/Application Support/Titanium
    </td>
    </tr>
    <td>Windows 7
    </td>
    <td>C:\Users\username\AppData\Roaming (ou C:\ProgramData\Titanium sur Titanium Studio 1.0.1 et les versions antérieures)
    </td>
    </tr><td>Windows XP
    </td>
    <td>C:\Documents and Settings\nom_utilisateur\Application Data (ou C:\Documents and Settings\All Users\Application Data\Titanium sur Titanium Studio 1.0.1 et les versions antérieures)
    </td>
    </tr><td>Linux
    </td>
    <td>~/.titanium
    </td>
    </tr>
    </tr>
    
    </table>

>[AZURE.NOTE]  Dans Mac OS, Library est un dossier masqué. Pour le rendre visible, vous devez exécuter la commande suivante, puis relancer le Finder : `**defaults write com.apple.finder AppleShowAllFiles TRUE**`

3.	Dans Appcelerator Titanium Studio, ouvrez le fichier index.js et ajoutez le code suivant à la fin du fichier. Ce code inscrira votre appareil aux notifications Push.

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
   

4.	Ouvrez maintenant le fichier TableData.js et recherchez les lignes suivantes dans la fonction **addOrUpdateDataFromAndroid**

        var request = {
    	    'text' : alertTextField.getValue(),
    	    'complete' : false
        };
  

    Remplacez le code ci-dessus dans la condition else (pour l'insertion d'un nouvel élément) uniquement.

5.	Pour Android, remplacez le code ci-dessus par le code suivant :

       var request = {
    	'text' : alertTextField.getValue(),
    	'complete' : false,
    	'handle' : Alloy.Globals.tempRegId
    }; 
    
    

6.	Recherchez maintenant les lignes dans la fonction suivantes **updateOrAddData**

           var request = {
    	    'text' : alertTextField.getValue(),
    	    'complete' : false
        };
    
    Remplacez le code ci-dessus dans la condition else (pour l'insertion d'un nouvel élément) uniquement.

7.	Pour iOS, remplacez le code ci-dessus par le code suivant :

       var request = {
    	'text' : alertTextField.getValue(),
    	'complete' : false,
    	'deviceToken' : Alloy.Globals.tempRegId
    };
    
 
Votre application a été mise à jour pour prendre en charge les notifications Push sur les plateformes iOS et Android.


## <a name="update-scripts"></a>Mise à jour du script d'insertion inscrit dans le portail de gestion

1.	Dans le portail de gestion, cliquez sur l'onglet Données, puis cliquez sur la table TodoItem.

    ![][5]

2.	Dans todoitem, cliquez sur l'onglet Script et sélectionnez Insérer.

    ![][6]

    La fonction appelée lors d'une insertion dans la table TodoItem s'affiche.

3.	Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

**Pour iOS :**

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
  
   	> [AZURE.NOTE] Ce script reporte l'envoi de la notification pour vous laisser le temps de fermer l'application pour recevoir une notification Push.  

**Pour Android :**

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



Ce code inscrit un nouveau script d'insertion qui utilise [Objet Push de Mobile Services] pour envoyer une notification Push (le texte inséré) à l'appareil indiqué dans la demande d'insertion.


<!-- Images. -->
[0]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0011.png
[1]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0031.png
[2]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0041.png
[3]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0061.png
[4]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image062.png
[5]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image064.png
[6]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image066.png

<!-- Anchors. -->
[Génération de la demande de signature de certificat]: #certificates
[Inscription de votre application et activation des notifications Push]: #register
[Création d'un profil d'approvisionnement pour l'application]: #profile
[Activation de Google Cloud Messaging]: #register-gcm
[Création du module GCM pour Titanium]: #gcm-module
[Configuration de Mobile Services]: #configure
[Ajout de notifications Push à l'application]: #add-push
[Mise à jour des scripts pour l'envoi de notifications Push]: #update-scripts
[Insertion de données pour recevoir des notifications]: #test

<!-- URLs. -->
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started
[Utilisation de modules Titanium]: http://docs.appcelerator.com/titanium/latest/#!/guide/Using_Titanium_Modules
[Portail de gestion Windows Azure]: https://manage.windowsazure.com/
[Objet Push de Mobile Services]: http://go.microsoft.com/fwlink/p/?linkid=272333&clcid=0x409
[Installation des outils de développement Java]: http://docs.appcelerator.com/titanium/latest/#!/guide/Installing_the_Java_Development_Tools



<!--HONumber=42-->
