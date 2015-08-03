
L'exemple précédent montrait une connexion standard, qui nécessite que le client contacte le fournisseur d'identité et le service mobile à chaque démarrage de l'application. Cette méthode est non seulement inefficace, mais vous pouvez rencontrer des problèmes d'utilisation si de nombreux clients tentent de lancer votre application en même temps. Une meilleure approche consiste à mettre en cache le jeton d'autorisation renvoyé par Mobile Services et à l'utiliser en premier avant de faire appel à la connexion via un fournisseur.

>[AZURE.NOTE]Vous pouvez mettre en cache le jeton émis par Mobile Services, que vous utilisiez l'authentification gérée par un client ou gérée par un service. Ce didacticiel utilise cette dernière.

1. Dans le fichier de projet default.js, remplacez la fonction **Login** existante par le code suivant :

        var credential = null;
        var vault = new Windows.Security.Credentials.PasswordVault();

        // Request authentication from Mobile Services using a Facebook login.
        var login = function () {
            return new WinJS.Promise(function (complete) {
                client.login("facebook").done(function (results) {
                    // Create a credential for the returned user.
                    credential = new Windows.Security.Credentials
                        .PasswordCredential("Facebook", results.userId,
                        results.mobileServiceAuthenticationToken);
                    vault.add(credential);
                    userId = results.userId;

                    refreshTodoItems();
                    var message = "You are now logged in as: " + userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message);
                    dialog.showAsync().done(complete);
                }, function (error) {
                    var dialog = new Windows.UI.Popups
                        .MessageDialog("An error occurred during login", "Login Required");
                    dialog.showAsync().done(complete);
                });
            });
        }

2. Remplacez la fonction **authenticate** existante par le code suivant :

        var authenticate = function () {
            // Try to get a stored credential from the PasswordVault.                
            try{
                credential = vault.findAllByResource("Facebook").getAt(0);
            }
            catch (error) {
                // This is expected when there's no stored credential.
            }
            
            if (credential) {
                // Set the user from the returned credential.   
                credential.retrievePassword();
                client.currentUser = {
                    "userId": credential.userName,
                    "mobileServiceAuthenticationToken": credential.password
                };

                // Try to return an item now to determine if the cached credential has expired.
                todoTable.take(1).read()
                            .done(function () {
                                refreshTodoItems();
                            }, function (error) {
                                if (error.request.status === 401) {
                                    login(credential, vault).then(function () {
                                        if (!credential) {

                                            // Authentication failed, try again.
                                            authenticate();
                                        }
                                    });
                                }                                   
                            });
            } else {

                login().then(function () {
                    if (!credential) {
                        // Authentication failed, try again.
                        authenticate();
                    }
                });
            }
        }

	Dans cette version de **authenticate**, l'application essaye d'utiliser des informations d'identification stockées dans le **coffre de mots de passe** pour accéder au service mobile. Une requête simple est envoyée pour vérifier que le jeton stocké n'est pas expiré. Quand une erreur 401 est retournée, une connexion basée sur un fournisseur habituel est tentée. Une connexion normale est également effectuée quand il n'y a pas d'informations d'identification stockées.

3. Redémarrez l'application deux fois.

	Notez que lors du premier démarrage, la connexion avec le fournisseur est à nouveau requise. Cependant, lors du second redémarrage, les informations d'identification mises en cache sont utilisées et l'étape de connexion est ignorée.

<!---HONumber=July15_HO4-->