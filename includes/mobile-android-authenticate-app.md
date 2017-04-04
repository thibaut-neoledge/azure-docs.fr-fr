
1. Dans l’ **Explorateur de projets** dans Android Studio, ouvrez le fichier ToDoActivity.java, puis ajoutez les instructions import suivantes.

        import java.util.concurrent.ExecutionException;
        import java.util.concurrent.atomic.AtomicBoolean;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;
2. Ajoutez la méthode suivante à la classe **ToDoActivity** :

        // You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
        public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;
 
        private void authenticate() {
            // Login using the Google provider.
            mClient.login("Google", "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
        }
         
        @Override
        protected void onActivityResult(int requestCode, int resultCode, Intent data) {
            // When request completes
            if (resultCode == RESULT_OK) {
                // Check the request code matches the one we send in the login request
                if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                    MobileServiceActivityResult result = mClient.onActivityResult(data);
                    if (result.isLoggedIn()) {
                        // login succeeded
                        createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                        createTable();
                    } else {
                        // login failed, check the error message
                        String errorMessage = result.getErrorMessage();
                        createAndShowDialog(errorMessage, "Error");
                    }
                }
            }
        }

    Cela crée une méthode pour gérer le processus d'authentification. L’utilisateur est authentifié à l’aide d’infos d’identification Google. Une boîte de dialogue affiche l’identificateur de l’utilisateur authentifié. Vous ne pouvez pas poursuivre sans authentification positive.

    > [!NOTE]
    > Si vous utilisez un autre fournisseur d’identité que Google, remplacez la valeur passée à la méthode **login** ci-dessus par l’une des valeurs suivantes : _MicrosoftAccount_, _Facebook_, _Twitter_ ou _windowsazureactivedirectory_.

3. Dans la méthode **onCreate**, ajoutez la ligne de code suivante après le code qui permet d’instancier l’objet `MobileServiceClient`.

        authenticate();

    Cet appel lance le processus d'authentification.
4. Déplacez le code restant après `authenticate();` dans la méthode **onCreate** vers une nouvelle méthode **createTable**. Le résultat ressemble à ceci :

        private void createTable() {

            // Get the table instance to use.
            mToDoTable = mClient.getTable(ToDoItem.class);

            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);

            // Create an adapter to bind the items with the view.
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

            // Load the items from Azure.
            refreshItemsFromTable();
        }

5. Ajoutez l’extrait de code suivant de _RedirectUrlActivity_ à _AndroidManifest.xml_ pour garantir la redirection.
 
        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="{url_scheme_of_your_app}"
                    android:host="easyauth.callback"/>
            </intent-filter>
        </activity>

6.  Ajoutez redirectUriScheme au _build.gradle_ de votre application Android.
 
        android {
            buildTypes {
                release {
                    // … …
                    manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
                }
                debug {
                    // … …
                    manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
                }
            }
        }

7. Ajoutez com.android.support:customtabs:23.0.1 aux dépendances de votre build.gradle :

      dependencies {        // ...        compile ’com.android.support:customtabs:23.0.1’    }

8. Dans le menu **Exécuter**, cliquez sur **Exécuter l’application** pour démarrer l’application, puis connectez-vous avec le fournisseur d’identité choisi.

Une fois que vous êtes connecté, l’application doit s’exécuter sans erreur et vous devez être en mesure d’interroger le service principal et de mettre à jour les données.
