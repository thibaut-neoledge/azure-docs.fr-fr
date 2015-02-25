<properties pageTitle="Envoi de notifications Push à des utilisateurs authentifiés" description="Découvrez comment envoyer des notifications Push à" services="mobile-services, notification-hubs" documentationCenter="android" authors="wesmc7777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="09/29/2014" ms.author="wesmc"/>

# Envoi de notifications Push à des utilisateurs authentifiés

[AZURE.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

Cette rubrique montre comment envoyer des notifications Push à un utilisateur authentifié sur un appareil inscrit. Contrairement au précédent didacticiel relatif aux [notifications Push][Prise en main des notifications Push], celui-ci modifie votre service mobile pour exiger l'authentification des utilisateurs avant que le client puisse s'inscrire auprès du concentrateur de notification pour les notifications Push. L'inscription est également modifiée pour ajouter une balise basée sur l'ID d'utilisateur attribué. Enfin, le script serveur est mis à jour afin de n'envoyer la notification qu'à l'utilisateur authentifié, au lieu de l'envoyer à toutes les inscriptions.

Ce didacticiel vous familiarise avec les procédures suivantes :

+ [Mise à jour du service pour exiger l'authentification pour l'inscription]
+ [Mise à jour de l'application pour se connecter avant l'inscription]
+ [Test de l'application]
 
Ce didacticiel prend en charge les applications Android.

##Conditions préalables 

Avant de commencer ce didacticiel, vous devez suivre les didacticiels Mobile Services suivants :

+ [Prise en main de l'authentification]<br/>Ajoute une exigence de connexion à l'exemple d'application TodoList.

+ [Prise en main des notifications Push]<br/>Configure l'exemple d'application TodoList pour utiliser les notifications Push avec Notification Hubs. 

Une fois ces deux didacticiels terminés, vous saurez comment empêcher les utilisateurs non authentifiés de s'inscrire pour recevoir les notifications Push de votre service mobile.

##<a name="register"></a>Mise à jour du service pour exiger l'authentification pour l'inscription

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../includes/mobile-services-javascript-backend-push-notifications-app-users.md)] 

<ol start="5"><li><p>Remplacez la fonction insert par le code suivant, puis cliquez sur <strong>Enregistrer</strong> :</p>
<pre><code>function insert(item, user, request) {

    // Define a payload for the Google Cloud Messaging toast notification.
    var payload = 
        '{"data":{"message" : "Hello from Mobile Services! An Item was inserted"}}';

    // Get the ID of the logged-in user.
    var userId = user.userId;		

    request.execute({
        success: function() {
            // If the insert succeeds, send a notification to all devices 
            // registered to the logged-in user as a tag.
            push.gcm.send(userId, payload, {
                success: function(pushResponse) {
                    console.log("Sent push with " + userId + " tag:", pushResponse, payload);
	    			request.respond();
                    },              
                    error: function (pushResponse) {
                            console.log("Error Sending push:", pushResponse);
	    				request.respond(500, { error: pushResponse });
                        }
                    });
                },
                error: function(err) {
                    console.log("request.execute error", err)
                    request.respond();
                }
            });
}</code></pre>

<p>Ce script d'insertion utilise la balise ID utilisateur pour envoyer une notification Push (avec le texte de l'élément inséré) à toutes les inscriptions Google Cloud Messaging créées par l'utilisateur connecté.</p></li></ol>

##<a name="update-app"></a>Mise à jour de l'application pour se connecter avant l'inscription

[AZURE.INCLUDE [mobile-services-android-push-notifications-app-users](../includes/mobile-services-android-push-notifications-app-users.md)] 

##<a name="test"></a>Test de l'application

[AZURE.INCLUDE [mobile-services-android-test-push-users](../includes/mobile-services-android-test-push-users.md)] 

<!---## <a name="next-steps"> </a>Étapes suivantes

Dans le didacticiel suivant, [Autorisation côté service des utilisateurs Mobile Services][Autorisation des utilisateurs avec des scripts], vous allez prendre la valeur d'ID utilisateur fournie par Mobile Services sur la base d'un utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services. Obtenez plus d'informations sur Mobile Services avec .NET dans le [guide de fonctionnement Mobile Services .NET]-->

<!-- Anchors. -->
[Mise à jour du service pour exiger l'authentification pour l'inscription]: #register
[Mise à jour de l'application pour se connecter avant l'inscription]: #update-app
[Test de l'application]: #test
[Étapes suivantes]:#next-steps


<!-- URLs. -->
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-android-get-started-users/
[Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-javascript-backend-android-get-started-push/

[Portail de gestion Azure]: https://manage.windowsazure.com/
[Guide de fonctionnement Mobile Services .NET]: /fr-fr/develop/mobile/how-to-guides/work-with-net-client-library


<!--HONumber=42-->
