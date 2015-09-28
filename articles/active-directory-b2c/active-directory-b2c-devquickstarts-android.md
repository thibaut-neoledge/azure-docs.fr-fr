<properties pageTitle="Version préliminaire d’Azure AD B2C : appel d'une API Web à partir d'une application Android | Microsoft Azure" description="Cet article décrit comment créer une application Android de type « liste des tâches » qui appelle une API Web node.js utilisant des jetons du porteur OAuth 2.0. L’application Android et l’API Web utilisent toutes les deux Azure AD B2C pour gérer les identités des utilisateurs et authentifier les utilisateurs." services="active-active-b2c" documentationCenter="android" authors="brandwe" manager="msmbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="brandwe"/>

# Version préliminaire d’Azure AD B2C : appel d'une API Web à partir d'une application Android

Avec Azure AD B2C, vous pouvez ajouter des fonctionnalités de gestion des identités en libre-service puissantes à vos applications Android et API Web en quelques étapes seulement. Cet article vous explique comment créer une application Android de type « liste des tâches », qui appelle une API Web node.js utilisant des jetons du porteur OAuth 2.0. L’application Android et l’API Web utilisent toutes les deux Azure AD B2C pour gérer les identités des utilisateurs et authentifier les utilisateurs.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]
	
> [AZURE.NOTE]
	Ce guide de démarrage rapide nécessite que vous disposiez d'une API Web protégée par Azure AD avec B2C. Nous en avons créé une pour .Net et node.js afin que vous puissiez l’utiliser. 
	Cette procédure pas à pas suppose que l'exemple d’API Web node.js est configuré. Reportez-vous au [didacticiel API Web Azure AD B2C pour Node.js](active-directory-b2c-devquickstarts-api-node.md).

> [AZURE.NOTE]
	Cet article ne couvre pas l'implémentation de connexion, d'inscription, ni de gestion de profil avec Azure AD B2C. Il se concentre sur l’appel d’API Web une fois que l'utilisateur est déjà authentifié. 
	Si ce n’est pas déjà fait, vous devriez commencer par lire le [didacticiel sur la prise en main de l’application Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) pour en savoir plus sur les principes de base d’Azure AD B2C.

Pour les clients Android qui doivent accéder à des ressources protégées, Azure AD fournit la bibliothèque d’authentification Active Directory (bibliothèque ADAL). Le seul objectif de cette bibliothèque ADAL est de faciliter l’obtention des jetons d'accès pour votre application. Pour illustrer sa facilité d’utilisation, nous allons créer une application To-Do List Android qui effectue les actions suivantes :

-	obtention de jetons d’accès pour appeler l’API To-Do List à l’aide du [protocole d’authentification OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx) ;
-	obtention de la liste des tâches d’un utilisateur ;
-	déconnexion des utilisateurs.



### Étape 1 : obtention d’un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire ou un client. Un répertoire est un conteneur pour tous vos utilisateurs, toutes vos applications, tous vos groupes et ainsi de suite. Si vous n’en avez pas déjà un, reportez-vous à [créer un répertoire B2C](active-directory-b2c-get-started.md) avant de continuer.

### Étape 2 : création d’une application

Vous devez maintenant créer une application dans votre répertoire B2C, qui fournit à Azure AD certaines informations nécessaires pour communiquer de manière sécurisée avec votre application. L'application et l’API Web seront alors toutes les deux représentées par un seul **ID d'application**, car elles constituent une application logique. Pour créer une application, suivez [ces instructions](active-directory-b2c-app-registration.md). Assurez-vous de

- Inclure une **application Web/API Web** dans l'application
- Saisir `http://localhost:3000/auth/openid/return` en tant qu’**URL de réponse** ; il s’agit de l'URL par défaut pour cet exemple de code.
- Créer un **secret d'application** pour votre application et notez-le quelque part. Vous en aurez besoin rapidement.
- Noter également l’**ID d’application** affecté à votre application. Vous en aurez aussi besoin rapidement.

### Étape 3 : création de vos stratégies

Dans Azure AD B2C, chaque expérience utilisateur est définie par une [**stratégie**](active-directory-b2c-reference-policies.md). Cette application contient trois expériences liées à l’identité : l’inscription, la connexion et la connexion avec Facebook. Vous devez créer une stratégie de chaque type, comme décrit dans l’[article de référence de stratégie](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Lors de la création de vos trois stratégies, assurez-vous de :

- Choisir le **nom d’affichage** et quelques autres attributs d'inscription dans votre stratégie d'inscription.
- Choisir les revendications d’application **nom d’affichage** et **ID objet** dans chaque stratégie. Vous pouvez choisir d'autres revendications.
- Noter le **nom** de chaque stratégie après sa création. Il doit présenter le préfixe `b2c_1_`. Vous aurez besoin des noms de ces stratégies rapidement. 

Une fois vos trois stratégies créées, vous pouvez concevoir votre application.

Remarque : cet article n’explique pas comment utiliser les stratégies que vous venez de créer. Si vous souhaitez en savoir plus sur la façon dont les stratégies fonctionnent dans Azure AD B2C, vous devriez commencer par lire le [didacticiel sur la prise en main de l’application Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

### Étape 4 : téléchargement du code

Le code associé à ce didacticiel est stocké [sur GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android). Pour générer l'exemple à mesure que vous avancez, vous pouvez [télécharger une structure de projet sous la forme d’un fichier zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip) ou cloner la structure :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE]**Le téléchargement de la structure est nécessaire pour suivre ce didacticiel.** En raison de la complexité de l'implémentation d'une application entièrement fonctionnelle sur Android, la **structure** dispose d’un code d’expérience utilisateur qui s'exécutera lorsque vous aurez terminé le didacticiel ci-dessous. Il s'agit d'une mesure visant à gagner du temps pour le développeur. Le code de l'expérience utilisateur n'est pas associé à la rubrique d’ajout de B2C à une application Android.

L'application terminée est également [disponible en tant que fichier zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip) ou sur la branche `complete` du même référentiel.


Pour générer avec Maven, vous pouvez utiliser le pom.xml au niveau supérieur.


  * Suivez les étapes de la section [Configuration requise pour configurer maven pour android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android).
  * Installez l’émulateur avec le kit de développement (SDK) 21.
  * Accédez au dossier racine où vous avez cloné le référentiel.
  * Exécutez la commande : mvn clean install
  * Accédez au répertoire de l’exemple de démarrage rapide : cd samples\\hello
  * Exécutez la commande : mvn android:deploy android:run
  * L’application doit normalement se lancer
  * Entrez les informations d’identification utilisateur de test pour faire un essai.

Les packages JAR sont également envoyés à côté du package aar.

### Étape 5 : téléchargement de la bibliothèque ADAL Android et ajout de cette bibliothèque à votre espace de travail Eclipse

Nous avons fait en sorte que vous puissiez facilement disposer de plusieurs options pour utiliser cette bibliothèque dans votre projet Android :

* Vous pouvez utiliser le code source pour importer cette bibliothèque dans Eclipse et la lier à votre application.
* Si vous utilisez Android Studio, vous pouvez utiliser le format de package *aar* et référencer les fichiers binaires.

####Option 1 : code Source via Git

Pour obtenir le code source du Kit de développement (SDK) via git, tapez simplement :

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src
    
    use the branch "convergence"

####Option 2 : fichiers binaires via Gradle

Vous pouvez obtenir les fichiers binaires à partir du référentiel central Maven. Le package AAR peut être inclus dans votre projet dans AndroidStudio comme suit :

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:2.0-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0-alpha
}
```

####Option 3 : aar via Maven

Si vous utilisez le plug-in m2e dans Eclipse, vous pouvez spécifier la dépendance dans votre fichier pom.xml :

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0-alpha</version>
    <type>aar</type>
</dependency>
```


####Option 5 : package jar à l'intérieur du dossier de bibliothèques
Vous pouvez obtenir le fichier jar du référentiel maven et le déposer dans le dossier *libs* de votre projet. Vous devez également copier les ressources nécessaires à votre projet dans la mesure où les packages jar ne les incluent pas.


### Étape 6 : ajout des références à la bibliothèque ADAL Android dans votre projet


2. Ajoutez une référence à votre projet et spécifiez qu’il s’agit d’une bibliothèque Android. Si vous ne savez pas comment procéder, [cliquez ici pour plus d'informations](http://developer.android.com/tools/projects/projects-eclipse.html).

3. Ajoutez la dépendance de projet pour le débogage dans vos paramètres de projet

4. Mettez à jour le fichier AndroidManifest.xml du projet pour y inclure le code suivant :

    ```Java
      <uses-permission android:name="android.permission.INTERNET" />
      <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
      <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
      ....
      <application/>
    ```

### Étape 7: ajout du code permettant d’appeler la bibliothèque ADAL pour Android

Nous allons créer une activité principale et l’appeler `ToDoActivity`.

Nous devons initialiser l'activité et ajouter des boutons qui contrôleront notre interface utilisateur. Là encore, c'est assez simple et très basique si vous avez déjà écrit du code Android auparavant :

```
public class ToDoActivity extends Activity {

    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;

    /**
     * Initializes the activity
     */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_list_todo_items);
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();
```

Ensuite, dans la même classe (ne fermez pas le crochet pour le moment), ajoutons nos boutons pour l'interface utilisateur. Comme vous pouvez le constater, nous ajoutons des boutons pour la connexion par le biais d’un compte Facebook ou d’une adresse e-mail. Ces ajouts utiliseront les stratégies que nous avons définies dans notre fichier `constants.java` :

```
        Button button = (Button) findViewById(R.id.signin_facebook);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signin_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signup_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

    ```
Create an instance of AuthenticationContext at your main Activity:

    ```Java
 		 mLoginProgressDialog = new ProgressDialog(this);
        mLoginProgressDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        mLoginProgressDialog.setMessage("Login in progress...");
        mLoginProgressDialog.show();
        // Ask for token and provide callback
        try {
            mAuthContext = new AuthenticationContext(ToDoActivity.this, Constants.AUTHORITY_URL,
                    false, InMemoryCacheStore.getInstance());
            mAuthContext.getCache().removeAll();

            if(Constants.CORRELATION_ID != null &&
                    Constants.CORRELATION_ID.trim().length() !=0){
                mAuthContext.setRequestCorrelationId(UUID.fromString(Constants.CORRELATION_ID));
    ```
  * NOTE: mContext is a field in your activity
 
 Let's also define our Result method `onActivityResult` that will be called when we have a result from the callback we'll write later:
 
 ```
 @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        mAuthContext.onActivityResult(requestCode, resultCode, data);
    }
 ```

Next, let's define our callback:

    ```Java
    mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES, Constants.POLICY, Constants.CLIENT_ID, Constants.REDIRECT_URL, Constants.USER_HINT, Constants.PROMPT,
                    "nux=1&" + Constants.EXTRA_QP,
                    new AuthenticationCallback<AuthenticationResult>() {

                        @Override
                        public void onError(Exception exc) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }
                            SimpleAlertDialog.showAlertDialog(ToDoActivity.this,
                                    "Failed to get token", exc.getMessage());
                        }

                        @Override
                        public void onSuccess(AuthenticationResult result) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }

                            if (result != null && !result.getAccessToken().isEmpty()) {
                                setLocalToken(result);
                                updateLoggedInUser();
                                getTasks();
                            } else {
                                //TODO: popup error alert
                            }
                        }
                    });
        } catch (Exception e) {
            SimpleAlertDialog.showAlertDialog(getApplicationContext(), "Exception caught", e.getMessage());
        }
        Toast.makeText(getApplicationContext(), TAG + "done", Toast.LENGTH_SHORT).show();
    }
        ```
        
 You may notice that this relies on methods we haven't written yet, such as `updateLoggedInUser()` and `getTasks()`. We'll write those below.	You can safely ignore the errors in Android Studio for now.

Explanation of the parameters:

  * ***SCOPES*** is required and is the scopes you are trying to reqeust access for. For the B2C preview this is the same as the Clientid but will change in the future.
  * ***POLICY*** is the policy for while you wish to authenticate the user. 
  * ***CLIENT_ID*** is required and comes from the AzureAD Portal.
  * You can setup redirectUri as your packagename. It is not required to be provided for the acquireToken call.
  * ***USER_HINT*** is the way we look up if the user is already in the cache and prompt the user if they are not found or the access token is invalid.
  * ***PROMPT*** helps to ask for credentials to skip cache and cookie.
  * ***Callback*** will be called after authorization code is exchanged for a token.

  The Callback will have an object of AuthenticationResult which has accesstoken, date expired, and idtoken info.

> [AZURE.NOTE]	Microsoft Intune's Company portal app provides the broker component and may be installed on the user's device. Developers should be prepared to allow for it's use as it provides SSO across all applications on the device. ADAL for Android will use the broker account if there is one user account created in the Authenticator. To use the broker, the developer needs to register a special redirectUri for broker usage. RedirectUri is in the format of msauth://packagename/Base64UrlencodedSignature. You can get your redirecturi for your app using the script `brokerRedirectPrint.ps1` or use API call `mContext.getBrokerRedirectUri()`. The signature is related to your signing certificates from the Google Play store.

 A Developer can skip the broker user with:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.WARNING] In order to reduce the complexity of this B2C Quickstart, we have opted in our sample to skip the broker. However, this is not recommended.

Next, let's create some helper methods that will get the token alone during our authentication calls to the Task API:

```
private void getToken(final AuthenticationCallback callback) {

        // one of the acquireToken overloads
        mAuthContext.acquireToken(onnstants.SCOPES, Constants.ADDITIONAL_SCOPES, Constants.POLICY,
                Constants.CLIENT_ID, Constants.REDIRECT_URL, Constants.USER_HINT, Constants.PROMPT,
                "nux=1&" + Constants.EXTRA_QP, callback);
    }

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {
        Constants.CURRENT_RESULT = newToken;
    }
    
```

Finally, Your app manifest should have permissions to use AccountManager accounts: http://developer.android.com/reference/android/accounts/AccountManager.html

 * GET_ACCOUNTS
 * USE_CREDENTIALS
 * MANAGE_ACCOUNTS


### Step 8. Call the Task API

Now that we have our Activity wired up and ready to go to do the heavy lifting of grabbing tokens, let's write our API to access the task server.

Our `getTasks` provides an array that represents the tasks in our server 
Our `addTask` and `deleteTask` do the subsequent action and return TRUE or FALSE if it was successful.

Let's write our `getTask` first:

```
private void getTasks() { if (Constants.CURRENT\_RESULT == null || Constants.CURRENT\_RESULT.getAccessToken().isEmpty()) return;

        List<String> items = new ArrayList<>();
        try {
            items = new TodoListHttpService().getAllItems(Constants.CURRENT_RESULT.getAccessToken());
        } catch (Exception e) {
            items = new ArrayList<>();
        }

        ListView listview = (ListView) findViewById(R.id.listViewToDo);
        ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
                android.R.layout.simple_list_item_1, android.R.id.text1, items);
        listview.setAdapter(adapter);
    }
 ```
 
 Nous allons également écrire une méthode qui initialisera nos tables lors de la première exécution :
 
 ```
     private void initAppTables() {
        try {
            // Get the Mobile Service Table instance to use
//            mToDoTable = mClient.getTable(WorkItem.class);
//            mToDoTable.TABvLES_URL = "/api/";
            //mTextNewToDo = (EditText)findViewById(R.id.listViewToDo);

            // Create an adapter to bind the items with the view
            //mAdapter = new WorkItemAdapter(ToDoActivity.this, R.layout.listViewToDo);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

        } catch (Exception e) {
            createAndShowDialog(new Exception(
                    "There was an error creating the Mobile Service. Verify the URL"), "Error");
        }
    }

```
 
 You'll see that this code requires some additional methods to do it's work. Let's write those now.
 
 ### Create endpoint URL generator
 
 We need to generate the endpoint URL that we'll be connecting to. Let's do that in the same class file:
 
 
 ```
     private URL getEndpointUrl() {
        URL endpoint = null;
        try {
            endpoint = new URL(Constants.SERVICE_URL);
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
        return endpoint;
    }

    ```


Note that we add the access token to the request in the following code:

### Step 9: Let's write some UX methods

Android requires us to handle some callbacks in order to operate the app. These are `createAndShowDialog` and `onResume()`. This is pretty simple and very familiar if you've written Android code before. 

Let's write those now:

```
    @Override
    public void onResume() {
        super.onResume(); // Always call the superclass method first

        updateLoggedInUser();
        // User can click logout, it will come back here
        // It should refresh list again
        getTasks();
    }
    
```

And now manage our dialog callbacks:


```
/** * Crée une boîte de dialogue et l’affiche * * Exception @param L’exception à afficher dans la boîte de dialogue * Titre @param Le titre de la boîte de dialogue */ private void createAndShowDialog(Exception exception, String title) { createAndShowDialog(exception.toString(), title); }

    /**
     * Creates a dialog and shows it
     *
     * @param message The dialog message
     * @param title   The dialog title
     */
    private void createAndShowDialog(String message, String title) {
        AlertDialog.Builder builder = new AlertDialog.Builder(this);

        builder.setMessage(message);
        builder.setTitle(title);
        builder.create().show();
    }
    
 ```
    


### Étape 10 : exécution de l'exemple d'application

Enfin, générez et exécutez l'application dans Android Studio ou Eclipse. Inscrivez-vous ou connectez-vous à l'application et créez des tâches pour l'utilisateur connecté. Déconnectez-vous et reconnectez-vous par le biais d’un autre utilisateur, ce qui a pour effet de créer des tâches pour cet utilisateur.

Notez la façon dont les tâches sont stockées par utilisateur sur l'API, dans la mesure où l’API extrait l’identité de l'utilisateur à partir du jeton d'accès qu'il reçoit.

Pour référence, l'exemple terminé [est fourni au format .zip ici](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip). Vous pouvez également le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```


<!--

### Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a node.js Web API from a node.js Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

### Important Information


#### Encryption

ADAL encrypts the tokens and store in SharedPreferences by default. You can look at the StorageHelper class to see the details. Android introduced AndroidKeyStore for 4.3(API18) secure storage of private keys. ADAL uses that for API18 and above. If you want to use ADAL for lower SDK versions, you need to provide secret key at AuthenticationSettings.INSTANCE.setSecretKey

#### Session cookies in Webview

Android webview does not clear session cookies after app is closed. You can handle this with sample code below:
```java
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
Pour en savoir plus sur les cookies, consultez la page suivante : http://developer.android.com/reference/android/webkit/CookieSyncManager.html
 

<!---HONumber=Sept15_HO3-->