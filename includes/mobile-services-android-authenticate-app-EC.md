
1. Dans l'Explorateur de package d'Eclipse, ouvrez le fichier ToDoActivity.java, puis ajoutez les instructions import suivantes.
   
        import java.util.concurrent.ExecutionException;
        import java.util.concurrent.atomic.AtomicBoolean;
   
        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;
   
        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;
2. Ajoutez la méthode suivante à la classe **ToDoActivity** :
   
    private void authenticate() { // Login using the Google provider.
   
        ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
   
        Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
            @Override
            public void onFailure(Throwable exc) {
                createAndShowDialog((Exception) exc, "Error");
            }           
            @Override
            public void onSuccess(MobileServiceUser user) {
                createAndShowDialog(String.format(
                        "You are now logged in - %1$2s",
                        user.getUserId()), "Success");
                createTable();    
            }
        });       
    }

    Cela crée une méthode pour gérer le processus d'authentification. L'utilisateur est authentifié à l'aide d'un nom d'utilisateur Google. Une boîte de dialogue affiche l'identifiant de l'utilisateur authentifié. Vous ne pouvez pas poursuivre sans authentification positive.

    > [AZURE.NOTE]Si vous utilisez un autre fournisseur d'identité que Google, remplacez la valeur passée à la méthode **login** ci-dessus par l'une des valeurs suivantes : _MicrosoftAccount_, _Facebook_, _Twitter_ ou _windowsazureactivedirectory_.

1. Dans la méthode **onCreate**, ajoutez la ligne de code suivante après le code qui permet d'instancier l'objet `MobileServiceClient`.
   
        authenticate();
   
    Cet appel lance le processus d'authentification.
2. Déplacez le code restant après `authenticate();` dans la méthode **onCreate** vers une nouvelle méthode **createTable** qui ressemble à ceci :
   
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
3. À partir du menu **Exécuter**, cliquez sur **Exécuter** pour démarrer l'application et vous connecter avec le fournisseur d'identité choisi.
   
       Lorsque vous êtes connecté, l'application doit s'exécuter sans erreur et vous devez pouvoir exécuter des requêtes Mobile Services et mettre à jour les données.

<!---HONumber=August15_HO6-->