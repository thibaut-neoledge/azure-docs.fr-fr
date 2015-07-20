

Enfin, vous devez mettre à jour le script inscrit dans l'opération d'insertion sur la table TodoItem pour envoyer les notifications.

1. Cliquez sur **TodoItem**, puis sur **Script** et sélectionnez **Insérer**. 

2. Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

		function insert(item, user, request) {
		// Define a payload for the Windows Store toast notification.
		var payload = '<?xml version="1.0" encoding="utf-8"?><toast><visual>' +    
		    '<binding template="ToastText01">  <text id="1">' +
		    item.text + '</text></binding></visual></toast>';
		
		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		    	push.wns.send(null, payload, 'wns/toast', {
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

	À l'issue de l'insertion, ce script d'insertion envoie une notification Push (avec le texte de l'élément inséré) à tous les enregistrements d'application Windows Store.

<!---HONumber=July15_HO2-->