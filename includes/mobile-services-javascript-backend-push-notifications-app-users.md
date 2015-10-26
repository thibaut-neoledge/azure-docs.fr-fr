
1. Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com), cliquez sur **Mobile Services**, puis sur votre service mobile.

2. Cliquez sur l'onglet **Push**, sélectionnez **Seuls les utilisateurs authentifiés** pour **Autorisations**, puis cliquez sur **Enregistrer** et **Modifier le script**.
	
	Ceci vous permet de personnaliser la fonction de rappel d'inscription aux notifications Push. Si vous utilisez Git pour modifier votre code source, cette même fonction d'inscription peut être trouvée dans `.\service\extensions\push.js`.

3. Remplacez la fonction **register** existante par le code suivant, puis cliquez sur **Enregistrer** :

		exports.register = function (registration, registrationContext, done) {   
		    // Get the ID of the logged-in user.
			var userId = registrationContext.user.userId;    
		    
			// Perform a check here for any disallowed tags.
			if (!validateTags(registration))
			{
				// Return a service error when the client tries 
		        // to set a user ID tag, which is not allowed.		
				done("You cannot supply a tag that is a user ID");		
			}
			else{
				// Add a new tag that is the user ID.
				registration.tags.push(userId);
				
				// Complete the callback as normal.
				done();
			}
		};
		
		function validateTags(registration){
		    for(var i = 0; i < registration.tags.length; i++) { 
		        console.log(registration.tags[i]);           
				if (registration.tags[i]
				.search(/facebook:|twitter:|google:|microsoft:/i) !== -1){
					return false;
				}
				return true;
			}
		}

	Ceci ajoute une balise à l'inscription, qui est l'ID de l'utilisateur connecté. Les balises fournies sont validées pour empêcher un utilisateur de s’inscrire avec l’ID d’un autre utilisateur Lorsqu’une notification est envoyée à cet utilisateur, elle est reçue sur ce périphérique et tout autre périphérique enregistré par l’utilisateur.

4. Cliquez sur la flèche Précédent. Cliquez sur l'onglet **Données**, puis sur **TodoItem** et **Script**, puis sélectionnez **Insérer**.

<!---HONumber=Oct15_HO3-->