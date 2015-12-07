<properties pageTitle="Version préliminaire d’Azure AD B2C : appel d'une API Web à partir d'une application Android | Microsoft Azure" description="Cet article décrit comment créer une application Android de type « liste des tâches » qui appelle une API Web node.js utilisant des jetons du porteur OAuth 2.0. L’application Android et l’API Web utilisent toutes les deux Azure AD B2C pour gérer les identités des utilisateurs et authentifier les utilisateurs." services="active-active-b2c" documentationCenter="android" authors="brandwe" manager="msmbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="11/19/2015"
	ms.author="brandwe"/>

# Version préliminaire d’Azure AD B2C : appel d'une API Web à partir d'une application Android

Avec Azure AD B2C, vous pouvez ajouter des fonctionnalités de gestion des identités en libre-service puissantes à vos applications Android et API Web en quelques étapes seulement. Cet article vous explique comment créer une application Android de type « liste des tâches », qui appelle une API Web node.js utilisant des jetons du porteur OAuth 2.0. L’application Android et l’API Web utilisent toutes les deux Azure AD B2C pour gérer les identités des utilisateurs et authentifier les utilisateurs.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

 	
> [AZURE.NOTE]Ce guide de démarrage rapide nécessite que vous disposiez d'une API Web protégée par Azure AD avec B2C. Nous en avons créé une pour .Net et node.js afin que vous puissiez l’utiliser. Cette procédure pas à pas suppose que l'exemple d’API Web node.js est configuré. Reportez-vous au [didacticiel API Web Azure AD B2C pour Node.js](active-directory-b2c-devquickstarts-api-node.md).

 
> [AZURE.NOTE]Cet article ne couvre pas l'implémentation de connexion, d'inscription, ni de gestion de profil avec Azure AD B2C. Il s'intéresse principalement à l'appel d'API web après que l'utilisateur s'est authentifié. Si ce n’est pas déjà fait, vous devriez commencer par lire le [didacticiel sur la prise en main de l’application Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) pour en savoir plus sur les principes de base d’Azure AD B2C.


Pour les clients Android qui doivent accéder à des ressources protégées, Azure AD fournit la bibliothèque d’authentification Active Directory (bibliothèque ADAL). Le seul objectif de cette bibliothèque ADAL est de faciliter l’obtention des jetons d'accès pour votre application. Pour illustrer sa facilité d’utilisation, nous allons créer une application To-Do List Android qui effectue les actions suivantes :

-	obtention de jetons d’accès pour appeler l’API To-Do List à l’aide du [protocole d’authentification OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx) ;
-	obtention de la liste des tâches d’un utilisateur ;
-	déconnexion des utilisateurs.



### Étape 1 : obtention d’un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire ou un client. Un répertoire est un conteneur destiné à recevoir tous vos utilisateurs, applications, groupes et autres. Si vous n’en avez pas déjà un, reportez-vous à [créer un répertoire B2C](active-directory-b2c-get-started.md) avant de continuer.

### Étape 2 : création d’une application

Vous devez maintenant créer une application dans votre répertoire B2C, qui fournit à Azure AD certaines informations nécessaires pour communiquer de manière sécurisée avec votre application. L'application et l’API Web seront alors toutes les deux représentées par un seul **ID d'application**, car elles constituent une application logique. Pour créer une application, suivez [ces instructions](active-directory-b2c-app-registration.md). Assurez-vous de

- Inclure une **application Web/API Web** dans l'application
- Saisir `urn:ietf:wg:oauth:2.0:oob` en tant qu’**URL de réponse** ; il s’agit de l’URL par défaut correspondant à cet exemple de code.
- Créer un **secret d'application** pour votre application et notez-le quelque part. Vous en aurez besoin rapidement.
- Noter également l’**ID d’application** affecté à votre application. Vous en aurez aussi besoin rapidement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

### Étape 3 : création de vos stratégies

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dans Azure AD B2C, chaque expérience utilisateur est définie par une [**stratégie**](active-directory-b2c-reference-policies.md). Cette application contient trois expériences liées à l'identité : l'inscription, la connexion et la connexion avec Facebook. Vous devez créer une stratégie de chaque type, comme décrit dans l’[article de référence de stratégie](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Lors de la création de vos trois stratégies, assurez-vous de :

- Choisir le **Nom d’affichage** et quelques autres attributs d’inscription dans votre stratégie d’inscription.
- Choisir les revendications d’application **Nom d’affichage** et **ID objet** dans chaque stratégie. Vous pouvez aussi choisir d'autres revendications.
- Noter le **nom** de chaque stratégie après sa création. Il doit porter le préfixe `b2c_1_`. Vous aurez besoin des noms de ces stratégies rapidement. 

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Une fois vos trois stratégies créées, vous pouvez générer votre application.

Remarque : cet article n'explique pas comment utiliser les stratégies que vous venez de créer. Pour en savoir plus sur la façon dont les stratégies fonctionnent dans Azure AD B2C, nous vous recommandons de commencer par lire le [didacticiel sur la prise en main de l’application web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

### Étape 4 : téléchargement du code

Le code associé à ce didacticiel est stocké [sur GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android). Pour générer l’exemple à mesure que vous avancez, vous pouvez [télécharger une structure de projet sous la forme d’un fichier zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip) ou cloner la structure :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE]**Le téléchargement de la structure est nécessaire pour suivre ce didacticiel.** En raison de la complexité de l’implémentation d’une application entièrement fonctionnelle sur Android, la **structure** dispose d’un code d’expérience utilisateur qui s’exécutera lorsque vous aurez terminé le didacticiel ci-dessous. Il s'agit d'une mesure visant à gagner du temps pour le développeur. Le code de l'expérience utilisateur n'est pas associé à la rubrique d’ajout de B2C à une application Android.

L’application terminée est également [disponible en tant que fichier .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip) ou sur la branche `complete` du même référentiel.


Pour générer avec Maven, vous pouvez utiliser le pom.xml au niveau supérieur.


  * Suivez les étapes de la section [Configuration requise pour configurer maven pour Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android).
  * Installez l’émulateur avec le kit de développement (SDK) 21.
  * Accédez au dossier racine où vous avez cloné le référentiel.
  * Exécutez la commande : mvn clean install
  * Accédez au répertoire de l’exemple de démarrage rapide : cd samples\\hello
  * Exécutez la commande : mvn android:deploy android:run
  * L’application doit normalement se lancer
  * Entrez les informations d’identification utilisateur de test pour faire un essai.

Les packages JAR sont également envoyés à côté du package aar.

### Étape 5 : téléchargement de la bibliothèque ADAL Android et ajout de cette bibliothèque à votre espace de travail Android Studio

Nous avons fait en sorte que vous puissiez facilement disposer de plusieurs options pour utiliser cette bibliothèque dans votre projet Android :

* Vous pouvez utiliser le code source pour importer cette bibliothèque dans Eclipse et la lier à votre application.
* Si vous utilisez Android Studio, vous pouvez utiliser le format de package *aar* et référencer les fichiers binaires.



####Option 1 : fichiers binaires via Gradle (recommandé)

Vous pouvez obtenir les fichiers binaires à partir du référentiel central Maven. Le package AAR peut être inclus dans votre projet dans AndroidStudio (exemple : dans `build.gradle`) :

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
    compile('com.microsoft.aad:adal:2.0.1-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0.1-alpha
}
```

####Option 2 : aar via Maven

Si vous utilisez le plug-in m2e dans Eclipse, vous pouvez spécifier la dépendance dans votre fichier `pom.xml` :

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0.1-alpha</version>
    <type>aar</type>
</dependency>
```

####Option 3 : code Source via Git (dernier recours)

Pour obtenir le code source du Kit de développement (SDK) via git, tapez simplement :

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src
    
    use the branch "convergence"


### Étape 6 : préparation de votre fichier configuration

Nous utiliserons la configuration que vous avez définie dans le portail B2C ci-dessus pour configurer le projet Android.

Ouvrez `helpes/Constants.java` et renseignez les valeurs pour les éléments suivants :

```

package com.microsoft.aad.taskapplication.helpers;

import com.microsoft.aad.adal.AuthenticationResult;

public class Constants {

    public static final String SDK_VERSION = "1.0";
    public static final String UTF8_ENCODING = "UTF-8";
    public static final String HEADER_AUTHORIZATION = "Authorization";
    public static final String HEADER_AUTHORIZATION_VALUE_PREFIX = "Bearer ";

    // -------------------------------AAD
    // PARAMETERS----------------------------------
    public static String AUTHORITY_URL = "https://login.microsoftonline.com/hypercubeb2c.onmicrosoft.com/";
    public static String CLIENT_ID = "<your application id>";
    public static String[] SCOPES = {"<your application id>"};
    public static String[] ADDITIONAL_SCOPES = {""};
    public static String REDIRECT_URL = "<redirect uri>";
    public static String CORRELATION_ID = "";
    public static String USER_HINT = "";
    public static String EXTRA_QP = "";
    public static String FB_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNIN_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNUP_POLICY = "B2C_1_<your policy>";
    public static boolean FULL_SCREEN = true;
    public static AuthenticationResult CURRENT_RESULT = null;
    // Endpoint we are targeting for the deployed WebAPI service
    public static String SERVICE_URL = "http://localhost:3000/tasks";

    // ------------------------------------------------------------------------------------------

    static final String TABLE_WORKITEM = "WorkItem";
    public static final String SHARED_PREFERENCE_NAME = "com.example.com.test.settings";


}


```
**scopes** : étendues que nous transmettons au serveur, que nous souhaitons demander au serveur, pour l’utilisateur qui se connecte. Pour la version préliminaire de B2C, nous transmettons la valeur client\_id. Toutefois, ceci sera modifié pour la lecture des étendues à l'avenir. Ce document sera alors mis à jour. **ADDITIONAL\_SCOPES** : étendues supplémentaires que vous pourriez vouloir utiliser pour votre application. Il sera utilisé dans l’ID d’application **CLIENT\_ID** que vous avez obtenu du portail **REDIRECT\_URL** - la redirection où le jeton devrait être publié. **EXTRA\_QP** - toute information supplémentaire que vous souhaitez transmettre au serveur sous un format URL. **FB\_POLICY** - la stratégie que vous appelez. La partie la plus importante de cette procédure pas à pas. **EMAIL\_SIGNIN\_POLICY** - la stratégie que vous appelez. La partie la plus importante de cette procédure pas à pas. **EMAIL\_SIGNUP\_POLICY** - la stratégie que vous appelez. La partie la plus importante de cette procédure pas à pas.

### Étape 7 : ajout des références à la bibliothèque ADAL Android dans votre projet


> [AZURE.NOTE]ADAL pour Android utilise un modèle basé sur une intention pour appeler l'authentification. Les intentions se superposent à l’app pour effectuer le travail. Cet exemple, comme tous ceux qui utilisent ADAL pour Android, gère les intentions et échange des informations entre eux.


La première chose à faire consiste à indiquer à Android la mise en page, y compris les éléments Intents() que nous souhaitons utiliser. J’expliquerai ces intentions plus en détail ultérieurement.

Mettez à jour le fichier AndroidManifest.xml du projet pour y inclure toutes vos intentions :

```
   <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.microsoft.aad.taskapplication"
    android:versionCode="1"
    android:versionName="1.0" >

    <uses-sdk
        android:minSdkVersion="11"
        android:targetSdkVersion="19" />

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        <activity
            android:name="com.microsoft.aad.adal.AuthenticationActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:exported="true"
            android:label="@string/title_login_hello_app" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.LoginActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.UsersListActivity"
            android:label="@string/title_activity_feed" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.SettingsActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.AddTaskActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.ToDoActivity"
            android:label="@string/app_name" >
        </activity>
    </application>

</manifest>    
```

Comme vous pouvez le voir, nous définissons les 5 activités que nous utiliserons.

**AuthenticationActivity** - provient d’ADAL et fournit la vue web de connexion

**LoginActivity** : affiche les stratégies d’authentification et les boutons pour chaque stratégie.

**SettingsActivity** : permet de modifier les paramètres de l’application lors de l’exécution.

**AddTaskActivity** : permet d’ajouter des tâches à nos API REST protégées par Azure AD

**ToDoActivity** : l’activité principale qui affiche les tâches.



### Étape 8: Création de l'activité de connexion

Nous allons créer une activité principale et l’appeler `LoginActivity`.

Création d’un fichier appelé `LoginActivity.java`

Nous devons initialiser l'activité et ajouter des boutons qui contrôleront notre interface utilisateur. Là encore, c'est assez simple et très basique si vous avez déjà écrit du code Android auparavant :

```
import android.app.Activity;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;

/**
 * Created by brwerner on 9/17/15.
 */
public class LoginActivity extends Activity {

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

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_signin);
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        Button button = (Button) findViewById(R.id.signin_facebook);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.FB_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signin_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNIN_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signup_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNUP_POLICY);
                startActivity(intent);

            }
        });

    }

}


```
Nous avons créé des boutons qui appellent notre intention ToDoActivity (qui appellera ADAL lorsque nous avons besoin d'un jeton) avec notre propre activité comme référence et un paramètre supplémentaire. Ce paramètre supplémentaire est transmis par la méthode `intent.putExtra()`. Vous voyez ici que nous définissons « thePolicy » comme spécifié dans `Constants.java`. Cela permet à l'intention de savoir quelle stratégie appeler lors de l'authentification.

### Étape 9: Création de l'activité Settings

Il s’agit simplement d’une activité qui remplit notre interface utilisateur de paramètres.

Création d’un fichier appelé `SettingsActivity.java`

Ici, un élément CRUD assez simple :

```
 package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.content.SharedPreferences;
import android.content.SharedPreferences.Editor;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.CheckBox;
import android.widget.Switch;
import android.widget.TextView;

import com.microsoft.aad.taskapplication.helpers.Constants;

/**
 * Settings page to try broker by options
 */
public class SettingsActivity extends Activity {

	//private CheckBox checkboxAskBroker, checkboxCheckBroker;
    private Switch fullScreenSwitch;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_settings);

        loadSettings();
//		checkboxAskBroker = (CheckBox) findViewById(R.id.askInstall);
//		checkboxCheckBroker = (CheckBox) findViewById(R.id.useBroker);

        Button save = (Button) findViewById(R.id.settingsSave);

        save.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                TextView textView = (TextView)findViewById(R.id.authority);
                Constants.AUTHORITY_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.resource);
            //    Constants.SCOPES = textView.getText().toString();

                textView = (TextView)findViewById(R.id.clientId);
                Constants.CLIENT_ID = textView.getText().toString();

                textView = (TextView)findViewById(R.id.extraQueryParameters);
                Constants.EXTRA_QP = textView.getText().toString();

                textView = (TextView)findViewById(R.id.redirectUri);
                Constants.REDIRECT_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                Constants.SERVICE_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                textView.setText(Constants.SERVICE_URL);

                textView = (TextView)findViewById(R.id.fb_signin);
                textView.setText(Constants.FB_POLICY);

                textView = (TextView)findViewById(R.id.email_signin);
                textView.setText(Constants.EMAIL_SIGNIN_POLICY);

                textView = (TextView)findViewById(R.id.email_signup);
                textView.setText(Constants.EMAIL_SIGNUP_POLICY);

                textView = (TextView)findViewById(R.id.correlationId);
                textView.setText(Constants.CORRELATION_ID);

                fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
                Constants.FULL_SCREEN = fullScreenSwitch.isChecked();

                finish();
            }
        });


	}

    private void loadSettings() {
        TextView textView = (TextView)findViewById(R.id.authority);
        textView.setText(Constants.AUTHORITY_URL);
        textView = (TextView)findViewById(R.id.resource);
        textView.setText(Constants.SCOPES[0]);
        textView = (TextView)findViewById(R.id.clientId);
        textView.setText(Constants.CLIENT_ID);
        textView = (TextView)findViewById(R.id.extraQueryParameters);
        textView.setText(Constants.EXTRA_QP);
        textView = (TextView)findViewById(R.id.redirectUri);
        textView.setText(Constants.REDIRECT_URL);
        textView = (TextView)findViewById(R.id.serviceUrl);
        textView.setText(Constants.SERVICE_URL);
        textView = (TextView)findViewById(R.id.fb_signin);
        textView.setText(Constants.FB_POLICY);
        textView = (TextView)findViewById(R.id.email_signin);
        textView.setText(Constants.EMAIL_SIGNIN_POLICY);
        textView = (TextView)findViewById(R.id.email_signup);
        textView.setText(Constants.EMAIL_SIGNUP_POLICY);
        textView = (TextView)findViewById(R.id.correlationId);
        textView.setText(Constants.CORRELATION_ID);

        fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
        fullScreenSwitch.setChecked(Constants.FULL_SCREEN);
    }

	private void saveSettings(String key, boolean value) {
		SharedPreferences prefs = SettingsActivity.this.getSharedPreferences(
				Constants.SHARED_PREFERENCE_NAME, Activity.MODE_PRIVATE);
		Editor prefsEditor = prefs.edit();
		prefsEditor.putBoolean(key, value);
		prefsEditor.commit();
	}
}
```

### Étape 10 : Création de l'activité AddTask

Cela nous permettra d'ajouter une tâche à notre point de terminaison d’API REST. Ici aussi, c’est plutôt simple.

Création d’un fichier appelé `AddTaskActivity.java`

Et écrivez :

```
package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;

public class AddTaskActivity extends Activity {

    EditText textField;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_add_task);
        textField = (EditText) findViewById(R.id.taskToAdd);
        Button button = (Button) findViewById(R.id.postTaskbutton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (textField.getText().toString() != null
                        && !textField.getText().toString().trim().isEmpty()
                        && Constants.CURRENT_RESULT != null) {

                    TodoListHttpService service = new TodoListHttpService();
                    try {
                        service.addItem(textField.getText().toString(), Constants.CURRENT_RESULT.getAccessToken());
                    } catch (Exception e) {
                        SimpleAlertDialog.showAlertDialog(getApplicationContext(), "Exception caught", e.getMessage());
                    }
                    finish();
                }
            }
        });
    }

}

```

### Étape 11 : Création de l'activité ToDoList

Voici l'activité la plus importante, celle qui permet d'obtenir un jeton d'Azure AD pour la stratégie. Nous utilisons ce jeton pour appeler le serveur de l'API REST de la tâche.

Création d’un fichier appelé `ToDoActivity.java`

Écrivez les éléments suivants (j'expliquerai les appels par la suite) :

```

package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.app.AlertDialog;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Build;
import android.os.Bundle;
import android.view.View;
import android.view.Window;
import android.webkit.CookieManager;
import android.webkit.CookieSyncManager;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.ListView;
import android.widget.TextView;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationCallback;
import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.adal.AuthenticationResult;
import com.microsoft.aad.adal.AuthenticationSettings;
import com.microsoft.aad.adal.UserIdentifier;
import com.microsoft.aad.adal.PromptBehavior;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.InMemoryCacheStore;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;
import com.microsoft.aad.taskapplication.helpers.Utils;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;


import java.io.UnsupportedEncodingException;
import java.net.MalformedURLException;
import java.net.URL;
import java.security.NoSuchAlgorithmException;
import java.security.spec.InvalidKeySpecException;
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

public class ToDoActivity extends Activity {



    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;
    private static AuthenticationResult sResult;

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
        CookieSyncManager.createInstance(getApplicationContext());
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        // Clear previous sessions
        clearSessionCookie();
        try {
            // Provide key info for Encryption
            if (Build.VERSION.SDK_INT < 18) {
                Utils.setupKeyForSample();
            }

        Button button = (Button) findViewById(R.id.addTaskButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, AddTaskActivity.class);
                startActivity(intent);
            }
        });

        button = (Button) findViewById(R.id.appSettingsButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.switchUserButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, LoginActivity.class);
                startActivity(intent);

            }
        });


        final TextView name = (TextView)findViewById(R.id.userLoggedIn);


        mLoginProgressDialog = new ProgressDialog(this);
        mLoginProgressDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        mLoginProgressDialog.setMessage("Login in progress...");
        mLoginProgressDialog.show();
        // Ask for token and provide callback
        try {
            mAuthContext = new AuthenticationContext(ToDoActivity.this, Constants.AUTHORITY_URL,
                    false);
            String policy = getIntent().getStringExtra("thePolicy");

            if(Constants.CORRELATION_ID != null &&
                    Constants.CORRELATION_ID.trim().length() !=0){
                mAuthContext.setRequestCorrelationId(UUID.fromString(Constants.CORRELATION_ID));
            }

            AuthenticationSettings.INSTANCE.setSkipBroker(true);

            mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES, policy, Constants.CLIENT_ID,
                    Constants.REDIRECT_URL, getUserInfo(), PromptBehavior.Always,
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

                            if (result != null && !result.getToken().isEmpty()) {
                                setLocalToken(result);
                                updateLoggedInUser();
                                getTasks();
                                ToDoActivity.sResult = result;
                                Toast.makeText(getApplicationContext(), "Token is returned", Toast.LENGTH_SHORT)
                                        .show();

                                if (sResult.getUserInfo() != null) {
                                    name.setText(result.getUserInfo().getDisplayableId());
                                    Toast.makeText(getApplicationContext(),
                                            "User:" + sResult.getUserInfo().getDisplayableId(), Toast.LENGTH_SHORT)
                                            .show();
                                }
                            } else {
                                //TODO: popup error alert
                            }
                        }
                    });
        } catch (Exception e) {
            SimpleAlertDialog.showAlertDialog(ToDoActivity.this, "Exception caught", e.getMessage());
        }
        Toast.makeText(ToDoActivity.this, TAG + "done", Toast.LENGTH_SHORT).show();
    } catch (InvalidKeySpecException e) {
            e.printStackTrace();
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }}
   
```

        
 Vous remarquerez peut-être qu’il s’appuie sur des méthodes que nous n’avons pas encore écrites, comme `updateLoggedInUser()`, `clearSessionCookie()` et `getTasks()`. Ces méthodes sont écrites ci-après. Vous pouvez pour l’instant ignorer les erreurs dans Android Studio.

Explication des paramètres :

  * ***SCOPES*** est requis et correspond aux étendues faisant l’objet de votre demande d’accès. Pour la version préliminaire de B2C, cela équivaut à Clientid, mais il n’en sera pas de même à l’avenir.
  * ***POLICY*** est la stratégie pour laquelle vous souhaitez authentifier l’utilisateur. 
  * ***CLIENT\_ID*** est obligatoire et provient du portail Azure AD.
  * Vous pouvez configurer redirectUri comme votre packagename. Il n’est pas obligatoire pour l’appel à acquireToken.
  * ***getUserInfo()*** permet de déterminer si l’utilisateur est déjà dans le cache et de notifier l’utilisateur s’il est introuvable ou si le jeton d’accès n’est pas valide. Nous écrivons cette méthode ci-dessous.
  * ***PromptBehavior.always*** permet de demander des informations d’identification pour ignorer le cache et le cookie.
  * ***Callback*** sera appelé une fois que le code d’autorisation aura été échangé contre un jeton.

  Callback aura un objet AuthenticationResult avec jeton d’accès, date d’expiration et info idtoken.

> [AZURE.NOTE]L’application Portail d’entreprise de Microsoft Intune fournit le composant de service broker et peut être installée sur l’appareil de l’utilisateur. Les développeurs doivent être prêts à autoriser son utilisation, car elle fournit l’authentification unique pour toutes les applications de l’appareil. La bibliothèque ADAL pour Android utilise le compte de service broker si un compte d’utilisateur est créé dans Authenticator. Pour utiliser le service broker, le développeur doit inscrire un redirectUri spécial. RedirectUri est au format msauth://packagename/Base64UrlencodedSignature. Vous pouvez obtenir le redirectUri de votre application à l’aide du script `brokerRedirectPrint.ps1` ou de l’appel d’API `mContext.getBrokerRedirectUri()`. La signature est liée à vos certificats de signature de la boutique Google Play.

 Un développeur peut ignorer l’utilisateur de service broker avec :

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.NOTE]Pour réduire la complexité de ce démarrage rapide B2C, nous avons décidé dans notre exemple d’ignorer le service broker.

Ensuite, passons à la création de méthodes d’assistance qui obtiendront le jeton au cours de nos appels d’authentification à l’API de tâche :

**Dans le même fichier** appelé `ToDoActivity.java`

```
    private void getToken(final AuthenticationCallback callback) {

        String policy = getIntent().getStringExtra("thePolicy");

        // one of the acquireToken overloads
        mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES,
                policy, Constants.CLIENT_ID, Constants.REDIRECT_URL, getUserInfo(),
                PromptBehavior.Always, "nux=1&" + Constants.EXTRA_QP, callback);
    }
```

Nous allons également ajouter des méthodes qui « définiront » et « obtiendront » notre AuthenticationResult (qui contient notre jeton) dans les CONSTANTES globales. Cela est nécessaire parce que bien que `ToDoActivity.java` utilise **sResult** dont son le flux, nos autres activités risquent de ne pas avoir accès au jeton (par exemple pour ajouter une tâche à notre `AddTaskActivity.java`)

```

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {


        Constants.CURRENT_RESULT = newToken;
    }

    
```
### Étape 12 : Création d’une méthode pour retourner un objet UserIdentifier

ADAL pour Android représente l’utilisateur sous la forme d’un objet **UserIdentifier**. Cela gère l'utilisateur et nous permet de savoir si le même utilisateur est utilisé dans nos appels, et donc si nous pouvons nous appuyer sur le cache au lieu de créer un appel sur le serveur. Pour faciliter cette opération, nous créons une méthode `getUserInfo()` qui retournera un objet UserIdentifier que nous pouvons utiliser pour `acquireToken()`. Nous créons également une méthode getUniqueId() qui nous renverra rapidement l’ID de l’objet UserIdentifier dans le cache.

```
  private String getUniqueId() {
        if (sResult != null && sResult.getUserInfo() != null
                && sResult.getUserInfo().getUniqueId() != null) {
            return sResult.getUserInfo().getUniqueId();
        }

        return null;
    }

    private UserIdentifier getUserInfo() {

        final TextView names = (TextView)findViewById(R.id.userLoggedIn);
        String name = names.getText().toString();
        return new UserIdentifier(name, UserIdentifier.UserIdentifierType.OptionalDisplayableId);
    }
    
```
 
### Étape 13 : Écriture de certaines méthodes d'assistance

Nous devons écrire certaines méthodes d’assistance qui nous aident à supprimer les cookies et à fournir un objet AuthenticationCallback. Ces éléments sont utilisés exclusivement pour l'exemple afin de s'assurer que nous sommes dans un état correct lors de l'appel de notre activité ToDo.

**Dans le même fichier** appelé `ToDoActivity.java`

```

    private void clearSessionCookie() {

        CookieManager cookieManager = CookieManager.getInstance();
        cookieManager.removeSessionCookie();
        CookieSyncManager.getInstance().sync();
    }
``` 

```
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        mAuthContext.onActivityResult(requestCode, resultCode, data);
    }
    
```   

### Étape 14: Appel de l’API de la tâche

À présent que notre activité est configurée et prête à récupérer des jetons, écrivons notre API pour accéder au serveur de tâches.

Notre `getTasks` fournit un tableau qui représente les tâches sur notre serveur

Tout d’abord, écrivons notre `getTask` :

**Dans le même fichier** appelé `ToDoActivity.java`

```
    private void getTasks() {
        if (sResult == null || sResult.getToken().isEmpty())
            return;

        List<String> items = new ArrayList<>();
        try {
            items = new TodoListHttpService().getAllItems(sResult.getToken());
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
 
 **Dans le même fichier** appelé `ToDoActivity.java`
 
```
    private void initAppTables() {
        try {
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

        } catch (Exception e) {
            createAndShowDialog(new Exception(
                    "There was an error creating the Mobile Service. Verify the URL"), "Error");
        }
    }

```
 
 Pour que ce code fonctionne, celui-ci nécessite des méthodes supplémentaires. Écrivons-les maintenant.
 
### Créer le générateur d’URL de point de terminaison
 
 Nous devons générer l’URL de point de terminaison à laquelle nous allons nous connecter. Effectuons cette opération dans le même fichier de classe :
 
 **Dans le même fichier** appelé `ToDoActivity.java`
 
 ``` private URL getEndpointUrl() { URL endpoint = null; try { endpoint = new URL(Constants.SERVICE\_URL); } catch (MalformedURLException e) { e.printStackTrace(); } return endpoint; }

 ```


Notez que nous ajoutons le jeton d’accès à la demande dans le code suivant :

### Étape 15 : écriture de méthodes d’expérience utilisateur

Android nous oblige à gérer certains rappels pour que l’application fonctionne. Il s’agit des rappels `createAndShowDialog` et `onResume()`. C’est assez simple et très basique si vous avez déjà écrit du code Android :

Écrivons-les maintenant :

**Dans le même fichier** appelé `ToDoActivity.java`

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

Et maintenant, gérons nos rappels de boîte de dialogue :

**Dans le même fichier** appelé `ToDoActivity.java`


```
    /**
     * Creates a dialog and shows it
     *
     * @param exception The exception to show in the dialog
     * @param title     The dialog title
     */
    private void createAndShowDialog(Exception exception, String title) {
        createAndShowDialog(exception.toString(), title);
    }

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

Et voilà ! Vous devriez avoir un fichier `ToDoActivity.java` compilé. L'ensemble du projet devrait également être compilé à ce stade.
    


### Étape 16 : exécution de l'exemple d'application

Enfin, générez et exécutez l'application dans Android Studio ou Eclipse. Inscrivez-vous ou connectez-vous à l'application et créez des tâches pour l'utilisateur connecté. Déconnectez-vous et reconnectez-vous par le biais d'un autre utilisateur, ce qui a pour effet de créer des tâches pour cet utilisateur.

Notez la façon dont les tâches sont stockées par utilisateur sur l'API, dans la mesure où l'API extrait l'identité de l'utilisateur à partir du jeton d'accès qu'il reçoit.

Pour référence, l’exemple terminé [est fourni au format .zip ici](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip). Vous pouvez également le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```


### Informations importantes


#### Chiffrement

ADAL chiffre les jetons et les stocke dans SharedPreferences par défaut. Vous pouvez consulter la classe StorageHelper pour afficher les détails. Android a introduit AndroidKeyStore pour le stockage sécurisé des clés privées de la version 4.3(API18). La bibliothèque ADAL utilise ces informations pour API18 et les versions ultérieures. Si vous souhaitez utiliser la bibliothèque ADAL pour les versions inférieures du Kit de développement (SDK), vous devez fournir une clé secrète à AuthenticationSettings.INSTANCE.setSecretKey.

#### Cookies de session dans Webview

Android Webview n’efface pas les cookies de session après la fermeture de l’application. Vous gérez cela avec l’exemple de code suivant : ```
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
``` Pour en savoir plus sur les cookies : http://developer.android.com/reference/android/webkit/CookieSyncManager.html
 

<!---HONumber=AcomDC_1125_2015-->