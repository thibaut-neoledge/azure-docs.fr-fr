<properties 
	pageTitle="Envoi de notifications Push aux utilisateurs authentifiés de l'application Windows universelle." 
	description="Découvrez comment utiliser Azure Mobile Services pour envoyer des notifications Push à des utilisateurs spécifiques de votre application Windows C# universelle." 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/05/2016" 
	ms.author="glenga"/>

# Envoi de notifications Push aux utilisateurs authentifiés

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Pour la version Mobile Apps équivalente de cette rubrique, consultez [How to: Send push notifications to an authenticated user using tags](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#push-user).

##Vue d'ensemble
Cette rubrique montre comment envoyer des notifications Push à un utilisateur authentifié sur un appareil inscrit. À l’inverse du précédent didacticiel [Ajout de notifications Push à votre application], celui-ci modifie votre service mobile pour exiger l’authentification des utilisateurs avant que le client puisse s’inscrire auprès du hub de notification pour les notifications Push. L'inscription est également modifiée pour ajouter une balise basée sur l'ID d'utilisateur affecté. Enfin, le script serveur est mis à jour afin de n'envoyer la notification qu'à l'utilisateur authentifié, au lieu de l'envoyer à toutes les inscriptions.

Ce didacticiel vous familiarise avec les procédures suivantes :

1. [Mise à jour du service pour demander l'authentification pour l'inscription]
2. [Mise à jour de l'application pour se connecter avant l'inscription]
3. [Test de l'application]
 
Ce didacticiel prend en charge les applications Windows Store et Windows Phone Store.

##Configuration requise 

Avant de commencer ce didacticiel, vous devez suivre les didacticiels Mobile Services suivants :

+ [Ajout de l’authentification à votre application]<br/>Ajoute une exigence de connexion à l’exemple d’application TodoList.

+ [Ajout de notifications Push à votre application]<br/>Configure l’exemple d’application TodoList pour les notifications Push à l’aide de Notification Hubs.

Une fois ces deux didacticiels terminés, vous saurez comment empêcher les utilisateurs non authentifiés de s'inscrire pour recevoir les notifications Push de votre service mobile.

##<a name="register"></a>Mise à jour du service pour demander l'authentification pour l'inscription

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

&nbsp;&nbsp;5. Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

	function insert(item, user, request) {
    // Define a payload for the Windows Store toast notification.
    var payload = '<?xml version="1.0" encoding="utf-8"?><toast><visual>' +    
    '<binding template="ToastText01"><text id="1">' +
    item.text + '</text></binding></visual></toast>';

    // Get the ID of the logged-in user.
    var userId = user.userId;		

    request.execute({
        success: function() {
            // If the insert succeeds, send a notification to all devices 
	    	// registered to the logged-in user as a tag.
            	push.wns.send(userId, payload, 'wns/toast', {
                success: function(pushResponse) {
                    console.log("Sent push:", pushResponse);
	    			request.respond();
                    },              
                    error: function (pushResponse) {
                            console.log("Error Sending push:", pushResponse);
	    				request.respond(500, { error: pushResponse });
                        }
                    });
                }
            });
	}

&nbsp;&nbsp;Ce script d'insertion utilise la balise ID utilisateur pour envoyer une notification Push (avec le texte de l'élément inséré) à toutes les inscriptions d'applications Windows Store créées par l'utilisateur connecté.

##<a name="update-app"></a>Mise à jour de l'application pour se connecter avant l'inscription

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-push-notifications-app-users](../../includes/mobile-services-windows-store-dotnet-push-notifications-app-users.md)]

##<a name="test"></a>Test de l'application

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../../includes/mobile-services-windows-test-push-users.md)]

<!-- Anchors. -->
[Mise à jour du service pour demander l'authentification pour l'inscription]: #register
[Mise à jour de l'application pour se connecter avant l'inscription]: #update-app
[Test de l'application]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Ajout de l’authentification à votre application]: ../mobile-services-windows-store-dotnet-get-started-users.md
[Ajout de notifications Push à votre application]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md

<!---------HONumber=AcomDC_0309_2016-->