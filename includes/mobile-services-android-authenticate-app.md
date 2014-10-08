1.  Dans l'Explorateur de package d'Eclipse, ouvrez le fichier ToDoActivity.java, puis ajoutez les instructions import suivantes.

        import com.microsoft.windowsazure.mobileservices.MobileServiceUser;
        import com.microsoft.windowsazure.mobileservices.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.UserAuthenticationCallback;

2.  Ajoutez la méthode suivante à la classe **ToDoActivity** :

        private void authenticate() {

            // Login using the Google provider.
            mClient.login(MobileServiceAuthenticationProvider.Google,
                    new UserAuthenticationCallback() {

                        @Override
                        public void onCompleted(MobileServiceUser user,
                                Exception exception, ServiceFilterResponse response) {

                            if (exception == null) {
                                createAndShowDialog(String.format(
                                                "You are now logged in - %1$2s",
                                                user.getUserId()), "Success");
                                createTable();
                            } else {
                                createAndShowDialog("You must log in. Login Required", "Error");
                            }
                        }
                    });
        }

    Cela crée une méthode pour gérer le processus d'authentification. L'utilisateur est authentifié à l'aide d'un nom d'utilisateur Google. Une boîte de dialogue affiche l'identifiant de l'utilisateur authentifié. Vous ne pouvez pas poursuivre sans authentification positive.

    <div class="dev-callout"><b>Remarque</b>
<p>Si vous utilisez un fournisseur d'identit&eacute; diff&eacute;rent de Google, remplacez la valeur transmise &agrave; la m&eacute;thode <strong>login</strong> ci-dessus par l'une des valeurs suivantes&nbsp;: <em>MicrosoftAccount</em>, <em>Facebook</em>, <em>Twitter</em> ou <em>WindowsAzureActiveDirectory</em>.</p>
</div>

3.  Dans la méthode **onCreate**, ajoutez la ligne de code suivante après le code qui permet d'instancier l'objet `MobileServiceClient` .

        authenticate();

    Cet appel lance le processus d'authentification.

4.  Déplacez le code restant après `authenticate();` dans la méthode **onCreate** vers une nouvelle méthode **createTable** qui ressemble à ceci :

        private void createTable() {

            // Get the Mobile Service Table instance to use
            mToDoTable = mClient.getTable(ToDoItem.class);

            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);

            // Create an adapter to bind the items with the view
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

            // Load the items from the Mobile Service
            refreshItemsFromTable();
        }

5.  À partir du menu **Exécuter**, cliquez sur **Exécuter** pour démarrer l'application et vous connecter avec le fournisseur d'identité choisi.

    Lorsque vous êtes connecté, l'application doit s'exécuter sans erreur et vous devez pouvoir exécuter des requêtes Mobile Services et mettre à jour les données.


