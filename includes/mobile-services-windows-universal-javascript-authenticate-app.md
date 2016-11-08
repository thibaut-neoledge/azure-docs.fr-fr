

1. Ouvrez le fichier de projet default.js et dans la surcharge de la méthode **app.OnActivated**, remplacez l'appel à la méthode **refreshTodoItems** par le code suivant : 
   
        // Define a member variable for storing the signed-in user.
        var userId = null;
   
        // Request authentication from Mobile Services using a Facebook login.
        var authenticate = function () {
            return new WinJS.Promise(function (complete) {
                // Change 'client' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                client.login("facebook").done(function (results) {
                    userId = results.userId;
                    refreshTodoItems();
                    buttonLogin.disabled = true;
                    var message = "You are now signed in as: " + userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message);
                    dialog.showAsync().done(complete);
                }, function (error) {
                    userId = null;
                    var dialog = new Windows.UI.Popups
                    .MessageDialog(error);
                        //.MessageDialog("An error occurred during login", "Login Required");
                    dialog.showAsync().done(complete);
                });
            });
        };
   
        // Handle the sign in button click event.
        buttonLogin.addEventListener("click", function () {
            authenticate();
        });
   
    Si vous utilisez un autre fournisseur d'identité que Facebook, remplacez la valeur passée à la méthode <strong>login</strong> ci-dessus par l'une des valeurs suivantes : *microsoftaccount*, *twitter*, *google* ou *windowsazureactivedirectory*.
   
   > [!NOTE]
   > Si vous avez enregistré les informations du package de l'application Windows Store avec Mobile Services, vous devez appeler la méthode <a href="http://go.microsoft.com/fwlink/p/?LinkId=322050" target="_blank">login</a> en fournissant la valeur <strong>true</strong> pour le paramètre <em>useSingleSignOn</em>. Si vous ne le faites pas, vos utilisateurs seront toujours invités à se connecter à chaque appel de la méthode de connexion.
   > 
   > 
2. Dans le projet d'application Windows Store, ouvrez le fichier de projet default.html, puis ajoutez l'élément **Bouton** suivant juste avant l'élément définissant le bouton **Enregistrer** :
   
          <button id="buttonLogin" style="margin-left: 5px">Sign in</button>
3. Appuyez sur la touche F5 pour exécuter l'application et vous connecter à l'application avec le fournisseur d'identité choisi.
   
       Lorsque vous êtes connecté, l'application doit s'exécuter sans erreur et vous devez pouvoir exécuter des requêtes Mobile Services et mettre à jour les données.

<!---HONumber=Oct15_HO3-->