<properties linkid="" urlDisplayName="" pageTitle="Push notifications to users (Android ) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Push notifications to users by using Mobile Services" authors="ricksal" solutions="" manager="" editor="" />

Notifications Push adressées aux utilisateurs à l'aide de Mobile Services
=========================================================================

[Windows Phone](/en-us/develop/mobile/tutorials/push-notifications-to-users-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/push-notifications-to-users-ios "iOS")[Android](/en-us/develop/mobile/tutorials/push-notifications-to-users-android "Android")

Cette rubrique s'inscrit dans le prolongement du [didacticiel précédent sur les notifications Push](/en-us/develop/mobile/tutorials/get-started-with-push-android) en ajoutant une nouvelle table pour le stockage des URI d'inscription à Google Cloud Messaging (GCM), lesquels peuvent alors être utilisés pour envoyer des notifications Push à plusieurs utilisateurs de l'application Android. Dans ce didacticiel, une seule mise à jour générera des notifications Push sur tous les appareils inscrits chaque fois que des insertions sont effectuées dans la table ToDoList. Dans le didacticiel précédent, une notification n'était envoyée qu'à l'appareil effectuant l'insertion.

Ce didacticiel vous guide tout au long de la procédure de mise à jour des notifications Push dans votre application :

1.  [Création de la table Registration](#create-table)
2.  [Mise à jour de votre application](#update-app)
3.  [Mise à jour des scripts serveur](#update-scripts)
4.  [Vérification du comportement des notifications Push](#test-app)

Ce didacticiel est basé sur le démarrage rapide de Mobile Services et s'appuie sur le didacticiel précédent [Prise en main des notifications Push](/en-us/develop/mobile/tutorials/get-started-with-push-android). Avant de commencer ce didacticiel, vous devez suivre le didacticiel [Prise en main des notifications Push](/en-us/develop/mobile/tutorials/get-started-with-push-android).

Création d'une table
--------------------

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur l'application.

     ![](./media/mobile-services-android-push-notifications-app-users/mobile-services-selection.png)

2.  Cliquez sur l'onglet **Data**, puis sur **Create**.

     ![](./media/mobile-services-android-push-notifications-app-users/mobile-create-table.png)

   La boîte de dialogue **Create new table** s'affiche.

3.  Conservez le paramètre **Anybody with the application key** par défaut pour toutes les autorisations, saisissez *Registration* dans **Nom de la table**, puis cliquez sur le bouton de vérification.

     ![](./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-table.png)

  La table **Registration** est alors créée, dans laquelle sont stockés les URI d'inscription utilisés pour envoyer des notifications Push indépendamment des données d'élément.

La prochaine étape consiste à modifier l'application de notifications Push pour stocker les données d'inscription dans cette nouvelle table, et non dans la table **TodoItem**.

Mise à jour de votre application
--------------------------------

1.  Dans Eclipse, dans l'Explorateur de package, cliquez avec le bouton droit sur le package (sous le nœud `src`), cliquez sur **Nouveau**, puis sur **Classe**.

2.  Dans **Nom**, tapez `Registration`, puis cliquez sur **Terminer**.

    ![](./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-class.png)

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
          * Renvoie l'ID de l'élément
          */
         public int getId() {
             return mId;
         }

         /**
          * Définit l'ID de l'élément
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
          * Référence du client Mobile Service
          */
         private static MobileServiceClient mClient;

         /**
          * Renvoie la référence du client
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

         // Créez une inscription.
         Registration registration = new Registration();
         registration.setHandle(gcmRegistrationId);

         // Insérez la nouvelle inscription.
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

Mise à jour des scripts serveur
-------------------------------

1.  Dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **Registration**.

    ![](./media/mobile-services-android-push-notifications-app-users/mobile-portal-data-tables-registration.png)

2.  Dans **Registration**, cliquez sur l'onglet **Script**, puis sélectionnez **Insérer**.

     ![](./media/mobile-services-android-push-notifications-app-users/mobile-insert-script-registration.png)

     La fonction appelée lors d'une insertion dans la table **Registration** s'affiche.

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

    ![](./media/mobile-services-android-push-notifications-app-users/mobile-insert-script-push2.png)

5.  Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

         function insert(item, user, request) {
             request.execute({
                 success: function() {
                     // Complétez la réponse, puis envoyez la notification en arrière-plan
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

Test de l'application
---------------------

1.  Dans Eclipse, dans le menu **Exécuter**, cliquez sur **Exécuter** pour démarrer l'application.

2.  Dans l'application, tapez un texte explicite, comme *Nouvelle tâche Mobile Services*, puis cliquez sur le bouton **Ajouter**.

3.  Une boîte de notification noire apparaît brièvement dans la partie inférieure de l'écran.

 ![](./media/mobile-services-android-push-notifications-app-users/mobile-push-icon.png)

Vous avez terminé ce didacticiel.

Étapes suivantes
----------------

Cela met fin à la série de didacticiels établissant les principes de base de l'utilisation de notifications Push. Pour plus d'informations sur les rubriques Mobile Services suivantes :

-   [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-android)
    <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-android)
    <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.

-   [Référence de script serveur Mobile Services](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.

-   [Utilisation de la bibliothèque cliente Android pour Mobile Services](/en-us/develop/mobile/how-to-guides/work-with-android-client-library)
    <br/>En savoir plus sur l'utilisation de Mobile Services avec .NET.


