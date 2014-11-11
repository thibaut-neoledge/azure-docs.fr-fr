<properties linkid="develop-mobile-tutorials-push-notifications-to-users-android" urlDisplayName="" pageTitle="Push notifications to users (Android ) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Push notifications to users by using Mobile Services" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Notifications Push adressées aux utilisateurs à l'aide de Mobile Services

<div class="dev-center-tutorial-selector sublanding">
    <a href="/fr-fr/develop/mobile/tutorials/push-notifications-to-users-wp8" title="Windows Phone">Windows Phone</a><a href="/fr-fr/develop/mobile/tutorials/push-notifications-to-users-ios" title="iOS">iOS</a><a href="/fr-fr/develop/mobile/tutorials/push-notifications-to-users-android" title="Android" class="current">Android</a>
</div>

<div class="dev-onpage-left-content">
<p>Cette rubrique s'inscrit dans le prolongement du <a href="/fr-fr/develop/mobile/tutorials/get-started-with-push-android">didacticiel pr&eacute;c&eacute;dent sur les notifications Push</a> en ajoutant une nouvelle table pour le stockage des URI d'inscription &agrave; Google Cloud Messaging (GCM), lesquels peuvent alors &ecirc;tre utilis&eacute;s pour envoyer des notifications Push &agrave; plusieurs utilisateurs de l'application Android. Dans ce didacticiel, une seule mise &agrave; jour g&eacute;n&eacute;rera des notifications Push sur tous les appareils inscrits chaque fois que des insertions sont effectu&eacute;es dans la table ToDoList. Dans le didacticiel pr&eacute;c&eacute;dent, une notification n'&eacute;tait envoy&eacute;e qu'&agrave; l'appareil effectuant l'insertion.</p>
</div>

Ce didacticiel vous guide tout au long de la procédure de mise à jour des notifications Push dans votre application :

1.  [Création de la table Registration][Création de la table Registration]
2.  [Mise à jour de votre application][Mise à jour de votre application]
3.  [Mise à jour des scripts serveur][Mise à jour des scripts serveur]
4.  [Vérification du comportement des notifications Push][Vérification du comportement des notifications Push]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services et s'appuie sur le didacticiel précédent [Prise en main des notifications Push][didacticiel précédent sur les notifications Push]. Avant de commencer ce didacticiel, vous devez suivre le didacticiel [Prise en main des notifications Push][didacticiel précédent sur les notifications Push].

## <a name="create-table"></a>Création d'une table

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure], cliquez sur **Mobile Services**, puis sur l'application.

    ![][0]

2.  Cliquez sur l'onglet **Data**, puis sur **Create**.

    ![][1]

    La boîte de dialogue **Create new table** s’affiche.

3.  Conservez le paramètre **Anybody with the application key** par défaut pour toutes les autorisations, saisissez *Registration* dans **Nom de la table**, puis cliquez sur le bouton de vérification.

    ![][2]

    La table **Registration** est alors créée, dans laquelle sont stockés les URI d'inscription utilisés pour envoyer des notifications Push indépendamment des données d'élément.

La prochaine étape consiste à modifier l'application de notifications Push pour stocker les données d'inscription dans cette nouvelle table, et non dans la table **TodoItem**.

## <a name="update-app"></a>Mise à jour de votre application

1.  Dans Eclipse, dans l'Explorateur de package, cliquez avec le bouton droit sur le package (sous le nœud `src`), cliquez sur **Nouveau**, puis sur **Classe**.

2.  Dans **Nom** tapez `Registration`, puis cliquez sur **Terminer**

    ![][3]

    Cela a permis de créer la classe d'inscription.

3.  Ouvrez le fichier ToDoItem.java, puis coupez le code suivant :

        @com.google.gson.annotations.SerializedName("handle")
        private String mHandle;

        public String getHandle() {
            return mHandle;
        }

        public final void setHandle(String handle) {
            mHandle = handle;
        }

4.  Collez le code, coupé dans l'étape précédente, dans le corps de la classe **Registration** précédemment créée.

5.  Ajoutez le code suivant à la classe **Registration** :

        @com.google.gson.annotations.SerializedName("id")
        private int mId;

        /**
         * Returns the item id
         */
        public int getId() {
            return mId;
        }

        /**
         * Sets the item id
         * 
         * @param id : id to set
         */
        public final void setId(int id) {
            mId = id;
        }

6.  Ouvrez le fichier **ToDoActivity.java**, et dans la méthode `addItem`, supprimez les lignes suivantes :

        item.setHandle(MyHandler.getHandle());

7.  Recherchez la propriété `mClient`, puis remplacez-la par le code suivant :

        /**
         * Mobile Service Client reference
         */
        private static MobileServiceClient mClient;

        /**
         * Returns the client reference
         */
        public static MobileServiceClient getClient() {
            return mClient;
        }

8.  Dans le fichier **MyHandler**, ajoutez les instructions import suivantes :

        import android.util.Log;

        import com.microsoft.windowsazure.notifications.NotificationsHandler;

        import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
        import com.microsoft.windowsazure.mobileservices.MobileServiceTable;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterResponse;
        import com.microsoft.windowsazure.mobileservices.TableOperationCallback;

9.  Ajoutez le code suivant à la fin de la méthode `onRegistered` :

        MobileServiceClient client = ToDoActivity.getClient();
        MobileServiceTable<Registration> registrations = client.getTable(Registration.class);

        // Create a new Registration
        Registration registration = new Registration();
        registration.setHandle(gcmRegistrationId);

        // Insert the new Registration
        registrations.insert(registration, new TableOperationCallback<Registration>() {

            public void onCompleted(Registration entity, Exception exception, ServiceFilterResponse response) {

                if (exception != null) {
                    Log.e("MyHandler", exception.getMessage());
                } else {
                    Log.e("MyHandler", "Registration OK");
                }
            }
        });

Votre application est à présent mise à jour et prend en charge l'envoi de notifications Push aux utilisateurs.

## <a name="update-scripts"></a>Mise à jour des scripts serveur

1.  Dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **Registration**.

    ![][4]

2.  Dans **Registration**, cliquez sur l'onglet **Script**, puis sélectionnez **Insérer**.

    ![][5]

    La fonction appelée lors d’une insertion dans la table **Registration** s’affiche.

3.  Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

        function insert(item, user, request) {
            var registrationTable = tables.getTable('Registration');
            registrationTable
                .where({ handle: item.handle })
                .read({ success: insertRegistrationIfNotFound });
            function insertRegistrationIfNotFound(existingRegistrations) {
                if (existingRegistrations.length > 0) {
                    request.respond(200, existingRegistrations[0]);
                } else {
                    request.execute();
                }
            }
        }

    Ce script vérifie dans la table **Registration** s'il existe une inscription avec le même URI. L'insertion n'a lieu que si aucune inscription correspondante n'est trouvée. Cela évite que des enregistrements d'inscription en double soient créés.

4.  Cliquez sur **TodoItem**, puis sur **Script** et sélectionnez **Insérer**.

    ![][6]

5.  Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

        function insert(item, user, request) {
            request.execute({
                success: function() {
                    // Write to the response and then send the notification in the background
                    request.respond();
                    sendNotifications(item.text);

                }
            });

        function sendNotifications(item_text) {
            var registrationTable = tables.getTable('Registration');
            registrationTable.read({
                success: function(registrations) {
                    registrations.forEach(function(registration) {
                        push.gcm.send(registration.handle, item_text, {
                            success: function(response) {
                                console.log('Push notification sent: ', response);
                            }, error: function(error) {
                                console.log('Error sending push notification: ', error);
                            }
                        });
                    });
                }
            });
        }

    }

    Ce script d'insertion envoie une notification Push (avec le texte de l'élément inséré) à toutes les inscriptions stockées dans la table **Registration**.

## <a name="test-app"></a>Test de l'application

1.  Dans Eclipse, dans le menu **Exécuter**, cliquez sur **Exécuter** pour démarrer l'application.

2.  Dans l'application, tapez un texte explicite, comme *Nouvelle tâche Mobile Services* puis cliquez sur le bouton **Ajouter**.

3.  Une boîte de notification noire apparaît brièvement dans la partie inférieure de l'écran.

    ![][7]

<!--7. Tap on the icon and swipe down to display the notification, which appears in the graphic below.      ![][27]-->

Vous avez terminé ce didacticiel.

## Étapes suivantes

Cela met fin à la série de didacticiels établissant les principes de base de l'utilisation de notifications Push. Pour plus d'informations sur les rubriques Mobile Services suivantes :

-   [Prise en main des données][Prise en main des données]

    En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main de l'authentification][Prise en main de l'authentification]

    En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.

-   [Référence de script serveur Mobile Services][Référence de script serveur Mobile Services]

    En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.

-   [Utilisation de la bibliothèque cliente Android pour Mobile Services][Utilisation de la bibliothèque cliente Android pour Mobile Services]

    En savoir plus sur l'utilisation de Mobile Services avec .NET.

  [didacticiel précédent sur les notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-android
  [Création de la table Registration]: #create-table
  [Mise à jour de votre application]: #update-app
  [Mise à jour des scripts serveur]: #update-scripts
  [Vérification du comportement des notifications Push]: #test-app
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-android-push-notifications-app-users/mobile-services-selection.png
  [1]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-table.png
  [2]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-table.png
  [3]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-class.png
  [4]: ./media/mobile-services-android-push-notifications-app-users/mobile-portal-data-tables-registration.png
  [5]: ./media/mobile-services-android-push-notifications-app-users/mobile-insert-script-registration.png
  [6]: ./media/mobile-services-android-push-notifications-app-users/mobile-insert-script-push2.png
  [7]: ./media/mobile-services-android-push-notifications-app-users/mobile-push-icon.png
  [Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-android
  [Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-android
  [Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Utilisation de la bibliothèque cliente Android pour Mobile Services]: /fr-fr/develop/mobile/how-to-guides/work-with-android-client-library
