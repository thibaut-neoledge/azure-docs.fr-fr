<properties linkid="develop-net-tutorials-push-notifications-to-users-wp8" urlDisplayName="Push Notifications to Users (WP8)" pageTitle="Push notifications to users (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your Windows Phone app." metaCanonical="" services="" documentationCenter="" title="Push notifications to users by using Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Notifications Push adressées aux utilisateurs à l'aide de Mobile Services
=========================================================================

[Windows Phone](/en-us/develop/mobile/tutorials/push-notifications-to-users-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/push-notifications-to-users-ios "iOS")[Android](/en-us/develop/mobile/tutorials/push-notifications-to-users-android "Android")

Cette rubrique s'inscrit dans le prolongement du [didacticiel précédent sur les notifications Push](/en-us/develop/mobile/tutorials/get-started-with-push-wp8) en ajoutant une nouvelle table pour le stockage des URI des canaux du Service de notifications Push Microsoft (MPNS). Ceux-ci peuvent ensuite être utilisés pour envoyer des notifications Push aux utilisateurs de l'application Windows Phone 8.

Ce didacticiel vous guide tout au long de la procédure de mise à jour des notifications Push dans votre application :

1.  [Création de la table de canaux](#create-table)
2.  [Mise à jour de l'application](#update-app)
3.  [Mise à jour des scripts serveur](#update-scripts)
4.  [Vérification du comportement des notifications Push](#test-app)

Ce didacticiel est basé sur le démarrage rapide de Mobile Services et s'appuie sur le didacticiel précédent [Prise en main des notifications Push](/en-us/develop/mobile/tutorials/get-started-with-push-wp8). Avant de commencer ce didacticiel, vous devez suivre le didacticiel [Prise en main des notifications Push](/en-us/develop/mobile/tutorials/get-started-with-push-wp8).

Création d'une table
--------------------

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur l'application.

	![][0]

2.  Cliquez sur l'onglet **Data**, puis sur **Create**.
	
	![][1]

	La boîte de dialogue **Create new table** s'affiche.

3.  Conservez le paramètre **Anybody with the application key** par défaut pour toutes les autorisations, tapez *Channel* dans **Table name**, puis cliquez sur le bouton de vérification.

	![][2]

La table **Channel** est alors créée, dans laquelle sont stockés les URI des canaux utilisés pour envoyer des notifications Push indépendamment des données d'élément.

La prochaine étape consiste à modifier l'application de notifications Push pour stocker les données dans cette nouvelle table, et non dans la table **TodoItem**.

Mise à jour de votre application
--------------------------------

1.  Dans Visual Studio 2012 Express pour Windows Phone, ouvrez le projet à partir du didacticiel [Prise en main des notifications Push](/en-us/develop/mobile/tutorials/get-started-with-push-wp8), ouvrez le fichier MainPage.xaml.cs et supprimez la propriété **Channel** de la classe **TodoItem**. Le code doit se présenter comme suit :

         public class TodoItem
         {
            public int Id { get; set; }

                [JsonProperty(PropertyName = "text")]
             public string Text { get; set; }

             [JsonProperty(PropertyName = "complete")]
             public bool Complete { get; set; }
         }

2.  Remplacez la méthode de gestionnaire d'événements **ButtonSave\_Click** par la version originale de cette méthode, comme suit :

         private void ButtonSave_Click(object sender, RoutedEventArgs e)
         {
             var todoItem = new TodoItem { Text = TextInput.Text };
             InsertTodoItem(todoItem);
         }

3.  Ajoutez le code suivant qui crée une classe **Channel** :

         public class Channel
         {
             public int Id { get; set; }

             [JsonProperty(PropertyName = "uri")]
             public string Uri { get; set; }
         }

4.  Ouvrez le fichier App.xaml.cs et remplacez la méthode **AcquirePushChannel** par le code suivant :

         private void AcquirePushChannel()
         {
             CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

             if (CurrentChannel == null)
             {
                 CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                 CurrentChannel.Open();
                 CurrentChannel.BindToShellTile();
             }
                      
            IMobileServiceTable<Channel> channelTable = App.MobileService.GetTable<Channel>();
            var channel = new Channel { Uri = CurrentChannel.ChannelUri.ToString() };
            channelTable.InsertAsync(channel);
         }

    Ce code insère un canal dans la table Channel.

Mise à jour des scripts serveur
-------------------------------

1.  Dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **Channel**.

	![][3]

2.  Dans **channel**, cliquez sur l'onglet **Script**, puis sélectionnez **Insert**.

	![][4]
	
	La fonction appelée lors d'une insertion dans la table **Channel** s'affiche.

3.  Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

             function insert(item, user, request) {
                 var channelTable = tables.getTable('Channel');
                 channelTable
                     .where({ uri: item.uri })
                     .read({ success: insertChannelIfNotFound });
                 function insertChannelIfNotFound(existingChannels) {
                    if (existingChannels.length > 0) {
                        request.respond(200, existingChannels[0]);
                    } else {
                        request.execute();
                    }
                }
             }

	Ce script vérifie dans la table **Channel** s'il existe un canal avec le même URI. L'insertion n'a lieu que si aucun canal correspondant n'est trouvé. Cela évite que des enregistrements de canal en double soient créés.

4.  Cliquez sur **TodoItem**, puis sur **Script** et sélectionnez **Insérer**.

	![][5]

5.  Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

		function insert(item, user, request) {
    		request.execute({
                 success: function() {
                    request.respond();
                    sendNotifications();
                }
			});

             function sendNotifications() {
                var channelTable = tables.getTable('Channel');
                channelTable.read({
                    success: function(channels) {
                        channels.forEach(function(channel) {
                            push.mpns.sendFlipTile(channel.uri, {
                                title: item.text
                            }, {
                                success: function(pushResponse) {
                                    console.log("Sent push:", pushResponse);
                                }
                            });
                        });
                    }
				});
			}
		}

    Ce script de fonction insert envoie une notification Push (avec le texte de l'élément inséré) à tous les canaux stockés dans la table **Channel**.

Test de l'application
---------------------

1.  Dans Visual Studio, sélectionnez **Déployer la solution** dans le menu **Générer**.

2.  Appuyez sur la vignette nommée **TodoList** ou **hello push** pour lancer l'application.

	![](./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push4-wp8.png)

1.  Dans l'application, entrez le texte « hello push again » dans la zone de texte, puis cliquez sur **Enregistrer**.

	![][24]

	Cela envoie une requête insert au service mobile pour stocker l'élément ajouté.

1.  Appuyez sur le bouton **Démarrer** pour revenir au menu Démarrer.

	![](./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push6-wp8.png) Notez que l'application a reçu la notification Push et que le titre de la vignette est maintenant **hello push**.

1.  (Facultatif) Exécutez l'application sur les deux appareils en même temps et répétez l'étape précédente.

    La notification est envoyée à toutes les instances d'application en cours d'exécution.

Étapes suivantes
----------------

Cela met fin à la série de didacticiels établissant les principes de base de l'utilisation de notifications Push. Pour plus d'informations sur les rubriques Mobile Services suivantes :

-   [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-wp8)
    En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-wp8)
    En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.

-   [Référence de script serveur Mobile Services](http://go.microsoft.com/fwlink/p/?LinkId=262293)
    En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.

<!-- anchors -->
[Create the Channel table]: #create-table
[Update the app]: #update-app
[Update server scripts]: #update-scripts
[Verify the push notification behavior]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-services-selection.png
[1]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-create-table.png
[2]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-create-channel-table.png
[3]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-portal-data-tables-channel.png
[4]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-insert-script-channel.png
[5]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-insert-script-push2.png


[23]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push4-wp8.png
[24]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push5-wp8.png
[25]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push6-wp8.png

<!-- URLs. -->
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-wp8
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-wp8
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-wp8
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-wp8

[Azure Management Portal]: https://manage.windowsazure.com/

