

1. Ouvrez le fichier de projet default.js et dans la surcharge de la méthode **app.OnActivated**, remplacez l'appel à la méthode **refreshTodoItems** par le code suivant : 
	
        var userId = null;

        // Request authentication from Mobile Services using a Facebook login.
        var login = function () {
            return new WinJS.Promise(function (complete) {
                client.login("facebook").done(function (results) {
                    userId = results.userId;
                    refreshTodoItems();
                    var message = "You are now logged in as: " + userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message);
                    dialog.showAsync().done(complete);
                }, function (error) {
                    userId = null;
                    var dialog = new Windows.UI.Popups
                        .MessageDialog("An error occurred during login", "Login Required");
                    dialog.showAsync().done(complete);
                });
            });
        }            

        var authenticate = function () {
            login().then(function () {
                if (userId === null) {

                    // Authentication failed, try again.
                    authenticate();
                }
            });
        }

        authenticate();

    Cela crée une variable membre pour le stockage de l'utilisateur actuel et une méthode pour gérer le processus d'authentification. L'utilisateur est authentifié à l'aide d'une connexion Facebook. Si vous utilisez un autre fournisseur d'identité que Facebook, remplacez la valeur passée à la méthode <strong>login</strong> ci-dessus par l'une des valeurs suivantes : _microsoftaccount_, _twitter_, _google_ ou _windowsazureactivedirectory_.

    >[AZURE.NOTE]Si vous avez enregistré les informations du package de l'application Windows Store avec Mobile Services, vous devez appeler la méthode <a href="http://go.microsoft.com/fwlink/p/?LinkId=322050" target="_blank">login</a> en fournissant la valeur <strong>true</strong> pour le paramètre <em>useSingleSignOn</em>. Si vous ne le faites pas, vos utilisateurs seront toujours invités à se connecter à chaque appel de la méthode de connexion.

2. Appuyez sur la touche F5 pour exécuter l'application et vous connecter à l'application avec le fournisseur d'identité choisi.

   	Lorsque vous êtes connecté, l'application doit s'exécuter sans erreur et vous devez pouvoir exécuter des requêtes Mobile Services et mettre à jour les données.

<!---HONumber=July15_HO3-->