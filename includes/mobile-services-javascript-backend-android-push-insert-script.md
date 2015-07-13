
1. Dans le portail de gestion, cliquez sur l’onglet **Données**, puis sur la table **TodoItem**. 
 
2. Dans **todoitem**, cliquez sur l'onglet **Script** et sélectionnez **Insérer**.
   
   	La fonction appelée lors d'une insertion dans la table **TodoItem** s'affiche.

3. Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

		function insert(item, user, request) {
		// Define a simple payload for a GCM notification.
	    var payload = {
	        "data": {
	            "message": item.text
	        }
	    };		
		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		        push.gcm.send(null, payload, {
		            success: function(pushResponse) {
		                console.log("Sent push:", pushResponse, payload);
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
		}

   	Cela inscrit un nouveau script d’insertion qui utilise l’[objet gcm](http://go.microsoft.com/fwlink/p/?LinkId=282645) pour envoyer une notification Push à tous les appareils inscrits après l’insertion.

<!---HONumber=62-->