<properties pageTitle="Prise en main des notifications Push (Android) | Centre de développement mobile" description="Découvrez comment utiliser Azure Mobile Services pour envoyer des notifications Push à votre application Android." services="mobile-services" documentationCenter="android" authors="RickSaling" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="11/21/2014" ms.author="ricksal"/>

# Prise en main des notifications Push dans Mobile Services (Push hérité)

<div class="dev-center-tutorial-selector sublanding"> <a href="/fr-fr/develop/mobile/tutorials/get-started-with-push-dotnet" title="Windows Store C#">Windows Store C#</a> <a href="/fr-fr/develop/mobile/tutorials/get-started-with-push-js" title="Windows Store JavaScript">Windows Store JavaScript</a> <a href="/fr-fr/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a> <a href="/fr-fr/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a> <a href="/fr-fr/develop/mobile/tutorials/get-started-with-push-android" title="Android" class="current">Android</a>
<!--    <a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a> <a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
	<a href="/fr-fr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title=".NET backend">Service principal .NET</a> | <a href="/fr-fr/documentation/articles/mobile-services-android-get-started-push/"  title="JavaScript backend" class="current">Service principal JavaScript</a></div>

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push vers une application Android. Dans ce didacticiel, vous ajoutez des notifications Push à l'aide du service Google Cloud Messaging (GCM) au projet de démarrage rapide. Une fois la procédure terminée, votre service mobile envoie une notification Push chaque fois qu'un enregistrement est inséré.

>[AZURE.NOTE]Cette rubrique prend en charge les services mobiles <em>existants</em> qui n'ont pas encore été <em>mis à niveau</em> pour utiliser l'intégration de Notification Hubs. Lorsque vous créez un <em>nouveau</em> service mobile, cette fonctionnalité intégrée est automatiquement activée. Pour les nouveaux services mobiles, consultez la page [Prise en main des notifications Push](/fr-fr/documentation/articles/mobile-services-javascript-backend-android-get-started-push/).
>
>Mobile Services s'intègre à Azure Notification Hubs pour prendre en charge une fonctionnalité de notification Push supplémentaire, comme les modèles, les plateformes multiples et la mise à l'échelle améliorée. <em>Vous devez mettre à niveau vos services mobiles existants pour utiliser Notification Hubs lorsque cela est possible</em>. Une fois la mise à niveau effectuée, consultez cette version de la [Prise en main des notifications Push](/fr-fr/documentation/articles/mobile-services-javascript-backend-android-get-started-push/).

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

* [Activation de Google Cloud Messaging](#register)
* [Configuration de Mobile Services](#configure)
* [Ajout de notifications Push à votre application](#add-push)
* [Mise à jour des scripts pour l'envoi de notifications Push](#update-scripts)
* [Insertion de données pour recevoir des notifications](#test)

Ce didacticiel requiert les éléments suivants :

+ [Kit de développement logiciel (SDK) Mobile Services pour Android]
+ Un compte Google actif

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer ce didacticiel, vous devez suivre le didacticiel [Prise en main de Mobile Services]. 

##<a id="register"></a>Activation de Google Cloud Messaging


[AZURE.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Ensuite, vous allez utiliser cette valeur de clé API afin d'activer Mobile Services pour l'authentification avec GCM et l'envoi des notifications Push au nom de votre application.

##<a id="configure"></a>Configuration de Mobile Services pour l'envoi de demandes push

1. Connectez-vous au [portail de gestion Azure], puis cliquez sur **Mobile Services** et sur l'application.

   	![][18]

2. Cliquez sur l'onglet **Push**, entrez la valeur **Clé API** fournie par GCM dans la procédure précédente, puis cliquez sur **Enregistrer**.

   	![][19]

Votre service mobile est désormais configuré pour utiliser GCM pour l'envoi de notifications Push.

##<a id="add-push"></a>Ajout de notifications Push à votre application


###Ajout de services Google Play au projet

[AZURE.INCLUDE [Ajout de services Play](../includes/mobile-services-add-Google-play-services.md)]

###Ajout de code

1. Ouvrez le fichier projet **AndroidManifest.xml**. Google Cloud Messaging a des spécifications requises d'API minimales pour le développement et les tests, auxquelles la propriété **minSdkVersion** du manifeste doit se conformer. Consultez la page [Configuration du Kit de développement logiciel (SDK) des services Google Play] pour déterminer comment définir cette valeur si vous devez la définir en dessous de 16 parce que vous utilisez un appareil plus ancien. Définissez la propriété comme il convient.

2. Assurez-vous que dans l'élément  `uses-sdk`, **targetSdkVersion** est défini sur le numéro d'une plateforme de Kit de développement logiciel (SDK) qui a été installée (étape 1). Il est préférable de le définir sur la dernière version disponible. 

3. La balise **uses-sdk** peut ressembler à ceci, en fonction des choix que vous avez effectués aux étapes précédentes :

	    <uses-sdk
	        android:minSdkVersion="17"
	        android:targetSdkVersion="19" />
	
4. Dans le code des deux prochaines étapes, remplacez _`**my_app_package**`_ par le nom du package de l'application de votre projet, qui est la valeur de l'attribut  `package` de la balise  `manifest`. 

5. Ajoutez les nouvelles autorisations suivantes après l'élément `uses-permission` existant :
     <permission android:name="**my_app_package**.permission.C2D_MESSAGE"          android:protectionLevel="signature" />     <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" />      <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />     <uses-permission android:name="android.permission.GET_ACCOUNTS" />     <uses-permission android:name="android.permission.WAKE_LOCK" />

6. Ajoutez le code suivant après la balise d'ouverture `application` : 
     <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"         android:permission="com.google.android.c2dm.permission.SEND">         <intent-filter>             <action android:name="com.google.android.c2dm.intent.RECEIVE" />             <category android:name="**my_app_package**" />         </intent-filter>     </receiver>



7. Ouvrez le fichier ToDoItem.java, puis ajoutez le code suivant à la classe **TodoItem** :

			@com.google.gson.annotations.SerializedName("handle")
			private String mHandle;
		
			public String getHandle() {
				return mHandle;
			}
		
			public final void setHandle(String handle) {
				mHandle = handle;
			}
		
	Ce code crée une propriété qui contient l'ID d'inscription.
 > [AZURE.NOTE] Lorsque le schéma dynamique est activé sur votre service mobile, une nouvelle colonne **handle** est automatiquement ajoutée à la table **TodoItem** lorsqu'un nouvel élément contenant cette propriété est inséré.

8. Téléchargez et décompressez le [Kit de développement logiciel (SDK) Mobile Services pour Android], ouvrez le dossier **notificationhubs**, copiez le fichier **notification-hubs-x.y.jar** dans le dossier *libs* de votre projet Eclipse, puis actualisez le dossier *libs*.
 > [AZURE.NOTE] Les numéros à la fin du nom du fichier peuvent changer dans les versions ultérieures du Kit de développement logiciel (SDK).

9.  Ouvrez le fichier *ToDoItemActivity.java*, puis ajoutez l'instruction import suivante :

		import com.microsoft.windowsazure.notifications.NotificationsManager;

10. Ajoutez la variable privée suivante à la classe, où _`<PROJECT_NUMBER>`_ est le numéro de projet attribué par Google à votre application dans la procédure précédente :

		public static final String SENDER_ID = "<PROJECT_NUMBER>";

11. Dans la méthode **onCreate**, avant l'instanciation de MobileServiceClient, ajoutez ce code qui inscrit le gestionnaire de notification pour l'appareil :

		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);



12. Ajoutez la ligne de code suivante à la méthode **addItem** avant que l'élément ne soit inséré dans la table :

		item.setHandle(MyHandler.getHandle());

	Ce code définit la propriété `handle` de l'élément sur l'ID d'inscription de l'appareil.

13. Dans l'Explorateur de package, cliquez avec le bouton droit sur le package (sous le nœud `src`), cliquez sur **Nouveau**, puis sur **Classe**.

14. Tapez `MyHandler` dans **Nom**, tapez `com.microsoft.windowsazure.notifications.NotificationsHandler` dans **Superclasse**, puis cliquez sur **terminer**

	![][6]

	Cela a permis de créer la classe MyHandler.

15. Ajoutez les instructions import suivantes :

		import android.content.Context;
		
		import com.microsoft.windowsazure.notifications.NotificationsHandler;
		

16. Ajoutez le code suivant :

		@com.google.gson.annotations.SerializedName("handle")
		private static String mHandle;
	
		public static String getHandle() {
			return mHandle;
		}
	
		public static final void setHandle(String handle) {
			mHandle = handle;
		}
	

17. Remplacez la méthode à substituer **onRegistered** existante par le code suivant :

		@Override
		public void onRegistered(Context context, String gcmRegistrationId) {
			super.onRegistered(context, gcmRegistrationId);
			
			setHandle(gcmRegistrationId);
		}
		

L'application est mise à jour et prend en charge les notifications Push.


##<a id="update-scripts"></a>Mise à jour du script d'insertion inscrit dans le portail de gestion

1. Dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItem**. 

   	![][21]

2. Dans **todoitem**, cliquez sur l'onglet **Script** et sélectionnez **Insérer**.
   
  	![][22]

   	La fonction appelée lors d'une insertion dans la table **TodoItem** s'affiche.

3. Remplacez la fonction d'insertion par le code suivant, puis cliquez sur **Enregistrer** :

		function insert(item, user, request) {
			request.execute({
				success: function() {
					// Write to the response and then send the notification in the background
					request.respond();
					push.gcm.send(item.handle, item.text, {
						success: function(response) {
							console.log('Push notification sent: ', response);
						}, error: function(error) {
							console.log('Error sending push notification: ', error);
						}
					});
				}
			});
		}

   	Ceci inscrit un nouveau script d'insertion, qui utilise l'[objet gcm] pour envoyer une notification Push (le texte inséré) à l'appareil fourni dans la demande d'insertion. 

##<a id="test"></a>Test des notifications Push dans votre application

> [AZURE.NOTE] Lorsque vous exécutez cette application dans l'émulateur, veillez à utiliser un AVD (appareil virtuel Android) qui prend en charge les API Google.

1. Redémarrez Eclipse, puis, dans l'Explorateur de package, cliquez avec le bouton droit sur le projet, cliquez sur **Propriétés**, cliquez sur **Android**, cochez **API Google**, puis cliquez sur **OK**.

	![][23]

  	Cela permet de cibler le projet pour les API Google.

2. Dans **Fenêtre**, sélectionnez **Gestionnaire d'appareil virtuel Android**, choisissez votre appareil, puis cliquez sur **Modifier**.

	![][24]

3. Sélectionnez **API Google** dans **Cible**, puis cliquez sur OK.

   	![][25]

	Cela permet de cibler l'AVD pour utiliser les API Google.

4. Dans le menu **Exécuter**, cliquez sur **Exécuter** pour démarrer l'application.

5. Dans l'application, tapez un texte explicite, comme _Nouvelle tâche Mobile Services_, et cliquez sur le bouton **Ajouter**.

  	![][26]

6. Une boîte de notification noire apparaît brièvement dans la partie inférieure de l'écran. 

  	![][28]

<!--7. Tap on the icon and swipe down to display the notification, which appears in the graphic below.

  ![][27]-->

Vous avez terminé ce didacticiel.

## <a name="next-steps"></a>Étapes suivantes

Dans cet exemple simple, un utilisateur reçoit une notification Push avec les données venant d'être insérées. Le jeton de l'appareil utilisé par GCM est fourni au service mobile par le client dans la requête. Dans le didacticiel suivant, [Notifications Push pour les utilisateurs d'application], vous allez créer une table Devices distincte dans laquelle stocker les jetons d'appareil et envoyer une notification Push à tous les canaux stockés lorsqu'une insertion a lieu. 


<!-- Images. -->

[1]: ./media/mobile-services-android-get-started-push/mobile-services-google-developers.png
[2]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server.png
[3]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server2.png
[4]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server3.png
[5]: ./media/mobile-services-android-get-started-push/mobile-services-android-sdk-manager.png
[6]: ./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png
[7]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-new-project.png
[8]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-sms-verify.png
[9]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-project-created.png
[10]: ./media/mobile-services-android-get-started-push/mobile-google-choose-play-api.png
[18]: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
[21]: ./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png
[24]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
[25]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
[26]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
[27]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push2-android.png
[28]: ./media/mobile-services-android-get-started-push/mobile-push-icon.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

<!-- URLs. -->
[Console Cloud Google]: https://cloud.google.com/console
[API Google]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Portail d'approvisionnement Android]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Configuration du Kit de développement logiciel (SDK) des services Google Play]: http://go.microsoft.com/fwlink/?LinkId=389801
[Référence à un projet de bibliothèque]: http://go.microsoft.com/fwlink/?LinkId=389800
[Kit de développement logiciel (SDK) Mobile Services pour Android]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-android
[Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-android
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-android
[Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-android
[Notifications Push aux utilisateurs de l'application]: /fr-fr/develop/mobile/tutorials/push-notifications-to-users-android
[Autorisation des utilisateurs avec des scripts]: /fr-fr/develop/mobile/tutorials/authorize-users-android

[Portail de gestion Azure]: https://manage.windowsazure.com/
[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
[Mobile Services Android conceptuel]: /fr-fr/develop/mobile/how-to-guides/work-with-android-client-library/
[gcm object]: http://go.microsoft.com/fwlink/p/?LinkId=282645



<!--HONumber=42-->
