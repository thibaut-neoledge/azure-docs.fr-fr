<properties
	pageTitle="Version préliminaire d’Azure Active Directory B2C : appel d’une API web depuis une application Android | Microsoft Azure" 
	description="Cet article décrit comment créer une application Android de type « liste de tâches » qui appelle une API web Node.js en utilisant les jetons du porteur OAuth 2.0. L’application Android et l’API web utilisent toutes les deux Azure Active Directory B2C pour gérer les identités des utilisateurs et les authentifier." 
	services="active-active-b2c"
	documentationCenter="android"
	authors="brandwe"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="brandwe"/>

# Version préliminaire d’Azure AD B2C : appel d’une API web depuis une application Android

Avec Azure Active Directory (Azure AD) B2C, vous pouvez ajouter de puissantes fonctionnalités de gestion des identités en libre-service à vos applications Android et API web, en seulement quelques étapes. Cet article vous explique comment créer une application Android de type « liste de tâches » qui appelle une API web node.js en utilisant les jetons du porteur OAuth 2.0. L’application Android et l’API web utilisent toutes les deux Azure AD B2C pour gérer les identités des utilisateurs et authentifier ces derniers.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Pour remplir pleinement sa fonction, ce guide de démarrage rapide nécessite que vous disposiez d’une API web protégée par Azure AD B2C. Nous en avons créé une pour .NET et Node.js à votre intention. Cette procédure pas à pas suppose que l’exemple d’API web Node.js est configuré. Pour plus d’informations, consultez le [didacticiel API web Azure AD B2C pour Node.js](active-directory-b2c-devquickstarts-api-node.md).

Pour les clients Android qui doivent accéder à des ressources protégées, Azure AD fournit la bibliothèque d’authentification Active Directory (ADAL). Cette bibliothèque a pour seule fonction de simplifier l’obtention des jetons d’accès pour votre application. Pour illustrer sa facilité d’utilisation, nous allons créer une application de liste de tâches Android qui effectue les actions suivantes :

- obtention de jetons d’accès qui appellent une API de liste de tâches à l’aide du [protocole d’authentification OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx) ;
- obtention des listes de tâches des utilisateurs ;
- déconnexion des utilisateurs.

> [AZURE.NOTE] Cet article ne couvre pas l’implémentation de la connexion, de l’inscription et de la gestion de profil avec Azure AD B2C. Il porte sur le mode d’appel des API web après l’authentification de l’utilisateur. Si ce n’est pas déjà fait, commencez par consulter le [didacticiel de prise en main des applications web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) pour en savoir plus sur les principes de base d’Azure AD B2C.

## Obtention d'un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire ou un client. Un répertoire est un conteneur destiné à recevoir tous vos utilisateurs, applications, groupes, etc. Si vous ne possédez pas encore de répertoire B2C, [créez-en un](active-directory-b2c-get-started.md) avant d’aller plus loin dans ce guide.

## Création d'une application

Vous devez maintenant créer dans votre répertoire B2C une application fournissant à Azure AD les informations nécessaires pour communiquer avec votre application en toute sécurité. L’application et l’API web sont alors toutes les deux représentées par un seul **ID d’application**, car elles constituent une application logique. Pour créer une application, suivez [ces instructions](active-directory-b2c-app-registration.md). Veillez à effectuer les opérations suivantes :

- Insérez une **application web**/**API web** dans l’application.
- Entrez `urn:ietf:wg:oauth:2.0:oob` comme **URL de réponse**. Il s’agit de l’URL par défaut pour cet exemple de code.
- Créez une **clé secrète d’application** pour votre application et copiez-la. Vous en aurez besoin ultérieurement. Cette valeur doit être [placée dans une séquence d’échappement XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) avant son utilisation.
- Copiez l’**ID d’application** affecté à votre application. Vous en aurez besoin ultérieurement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Création de vos stratégies

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dans Azure AD B2C, chaque expérience utilisateur est définie par une [stratégie](active-directory-b2c-reference-policies.md). Cette application contient trois expériences liées à l’identité : l’inscription, la connexion et la connexion avec Facebook. Vous devez créer une stratégie de chaque type, comme décrit dans l’[article de référence sur les stratégies](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Lors de la création de vos 3 stratégies, assurez-vous de :

- Choisir le **nom d’affichage** et d’autres attributs d’inscription dans votre stratégie d’inscription.
- Choisir le **nom d’affichage** et l’**ID objet** comme revendications d’application pour chaque stratégie. Vous pouvez aussi choisir d'autres revendications.
- Copier le **nom** de chaque stratégie après sa création. Il doit porter le préfixe `b2c_1_`. Vous aurez besoin des noms de ces stratégies ultérieurement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Une fois les trois stratégies créées, vous pouvez générer votre application.

Remarque : cet article n’explique pas comment utiliser les stratégies que vous venez de créer. Pour en savoir plus sur le fonctionnement des stratégies dans Azure AD B2C, commencez par consulter le [didacticiel de prise en main des applications web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Téléchargement du code

Le code associé à ce didacticiel est [stocké sur GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android). Pour générer l’exemple à mesure que vous avancez, vous pouvez [télécharger la structure de projet sous la forme d’un fichier zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip). Vous pouvez également cloner la structure :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE] **Vous devez télécharger la structure pour suivre ce didacticiel.** En raison de la complexité de l’implémentation d’une application Android entièrement fonctionnelle, la structure dispose d’un code d’expérience utilisateur qui est exécuté une fois ce didacticiel terminé. Cette mesure fait gagner du temps aux développeurs. Le code de l’expérience utilisateur est sans rapport avec la rubrique d’ajout de B2C à une application Android.

L’application terminée est également [disponible en tant que fichier .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip) ou sur la branche `complete` du même référentiel.

Pour générer l’application avec Maven, vous pouvez utiliser `pom.xml` au niveau supérieur.

  1. Suivez les étapes de la [section sur la configuration requise pour configurer Maven pour Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android).
  2. Installez l’émulateur avec le Kit de développement logiciel (SDK) 21.
  3. Accédez au dossier racine où vous avez cloné le référentiel.
  4. Exécutez la commande `mvn clean install`.
  5. Remplacez le répertoire par celui de l’exemple de démarrage rapide : `cd samples\hello`.
  6. Exécutez la commande `mvn android:deploy android:run`.

L’application doit normalement se lancer. Entrez les informations d’identification de l’utilisateur de test pour essayer l’application.

Les packages d’archive Java (JAR) sont également envoyés avec le package d’archive Android (AAR).

## Téléchargement de la bibliothèque ADAL Android et ajout de cette bibliothèque à votre espace de travail Android Studio

Vous disposez de plusieurs options pour utiliser cette bibliothèque dans votre projet Android :

* Vous pouvez utiliser le code source pour importer la bibliothèque dans Eclipse et la lier à votre application.
* Si vous utilisez Android Studio, vous pouvez utiliser le format de package AAR et référencer les fichiers binaires.

### Option 1 : fichiers binaires via Gradle (recommandé)

Vous pouvez obtenir les fichiers binaires à partir du référentiel central Maven. Le package AAR peut être inclus dans votre projet dans Android Studio (par exemple, dans `build.gradle`) de la manière suivante :

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

### Option 2 : AAR via Maven

Si vous utilisez le plug-in `m2e` dans Eclipse, vous pouvez spécifier la dépendance dans votre fichier `pom.xml` :

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0.1-alpha</version>
    <type>aar</type>
</dependency>
```

### Option 3 : code Source via Git (dernier recours)

Pour obtenir le code source du Kit de développement logiciel (SDK) via Git, saisissez :

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

Utilisez la branche **convergence**.

## Préparation de votre fichier de configuration

Utilisez la configuration que vous avez définie précédemment dans le portail B2C pour configurer le projet Android.

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
- `SCOPES` : étendues transmises au serveur que vous souhaitez demander à ce dernier lorsqu’un utilisateur se connecte. Pour la version préliminaire de B2C, vous transmettez `client_id`. Toutefois, cette étendue devrait être remplacée par `read scopes` ultérieurement. Ce document sera alors mis à jour.
- `ADDITIONAL_SCOPES` : étendues supplémentaires que vous pourriez vouloir utiliser pour votre application. Elles devraient être utilisées ultérieurement.
- `CLIENT_ID` : ID d’application obtenu depuis le portail.
- `REDIRECT_URL` : URL de redirection sur lequel le jeton devrait être publié.
- `EXTRA_QP` : toute information supplémentaire que vous souhaitez transmettre au serveur sous un format URL.
- `FB_POLICY` : stratégie que vous appelez. Il s’agit de l’élément le plus important de cette procédure pas à pas.
- `EMAIL_SIGNIN_POLICY` : stratégie que vous appelez. Il s’agit de l’élément le plus important de cette procédure pas à pas.
- `EMAIL_SIGNUP_POLICY` : stratégie que vous appelez. Il s’agit de l’élément le plus important de cette procédure pas à pas.

## Ajout des références à la bibliothèque ADAL Android dans votre projet

> [AZURE.NOTE]	La bibliothèque ADAL pour Android utilise un modèle basé sur une intention pour appeler l’authentification. Les intentions se « superposent » à l’application afin d’effectuer des tâches. Cet exemple, comme à chaque fois que la bibliothèque ADAL pour Android est utilisée, est centré sur la gestion des intentions et la transmission d’informations entre elles.

Tout d’abord, indiquez à Android la disposition de votre application, ainsi que les intentions que vous souhaitez utiliser. Ces intentions seront explicitées plus loin dans ce didacticiel.

Mettez à jour le fichier `AndroidManifest.xml` du projet pour y inclure toutes vos intentions :

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

Comme vous pouvez le voir, vous définissez cinq activités. Celles-ci seront toutes utilisées.

- `AuthenticationActivity` : provenant de la bibliothèque ADAL, cette activité fournit la vue web de connexion.
- `LoginActivity` : affiche vos stratégies de connexion et les boutons pour chaque stratégie.
- `SettingsActivity` : sert à modifier les paramètres de l’application lors de l’exécution.
- `AddTaskActivity` : sert à ajouter des tâches protégées par Azure AD à votre API REST.
- `ToDoActivity` : activité principale qui affiche les tâches.

## Création de l’activité Login

Créez une activité principale et nommez-la `LoginActivity`.

Créez un fichier appelé `LoginActivity.java`.

Vous devez initialiser l’activité et ajouter des boutons qui contrôleront votre interface utilisateur. Cela devrait vous être familier si vous avez déjà écrit du code Android.

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
Vous avez créé des boutons qui appellent votre intention `ToDoActivity` (qui appelle à son tour la bibliothèque ADAL quand vous avez besoin d’un jeton). Pour ce faire, ils utilisent votre activité comme référence et un paramètre supplémentaire. Ce paramètre supplémentaire est transmis par la méthode `intent.putExtra()`. Vous définissez `"thePolicy"` à l’aide des éléments spécifiés dans `Constants.java`. Cela indique à l’intention quelle stratégie appeler lors de l’authentification.

## Création de l’activité Settings

Cette activité sert à remplir votre interface utilisateur de paramètres.

Créez un fichier appelé `SettingsActivity.java` 
pour les opérations de création, lecture, mise à jour et suppression (CRUD) simples.

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

## Création de l’activité AddTask

Cette activité permet d’ajouter une tâche au point de terminaison de votre API REST.

Créez un fichier appelé `AddTaskActivity.java` 
et écrivez le code suivant.

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

## Création de l’activité ToDo

Il s’agit de l’activité la plus importante. Vous pouvez l’employer afin d’obtenir un jeton auprès d’Azure AD pour une stratégie et utiliser ensuite ce jeton pour appeler le serveur de tâches de l’API REST.

Créez un fichier appelé `ToDoActivity.java` et écrivez le code suivant. (Les appels seront expliqués plus loin.)

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


 Vous avez peut-être remarqué que ce code repose sur des méthodes qui n’ont pas encore été écrites. Celles-ci incluent `updateLoggedInUser()`, `clearSessionCookie()` et `getTasks()`. Vous écrirez ces méthodes plus loin dans ce guide. Vous pouvez pour l’instant ignorer les erreurs dans Android Studio.

Explication des paramètres :

  - `SCOPES` : ce paramètre obligatoire correspond aux étendues faisant l’objet de votre demande d’accès. Pour la version préliminaire de B2C, il équivaut à `client_id`, mais il devrait être modifié ultérieurement.
  - `POLICY` : stratégie à utiliser pour l’authentification de l’utilisateur.
  - `CLIENT_ID` : ce paramètre obligatoire provient du portail Azure AD.
  - `redirectUri` : ce paramètre peut être défini sur le nom de votre package. Il n’est pas obligatoire pour l’appel à `acquireToken`.
  - `getUserInfo()` : ce paramètre permet de vérifier si un utilisateur est déjà présent dans le cache. Il détermine également le type d’invite à présenter à l’utilisateur s’il est introuvable ou si son jeton d’accès n’est pas valide. Cette méthode sera écrite plus loin dans ce guide.
  - `PromptBehavior.always` : permet de demander des informations d’identification pour ignorer le cache et le cookie.
  - `Callback` : paramètre appelé après qu’un code d’autorisation a été échangé contre un jeton. Il possède un objet `AuthenticationResult`, qui contient le jeton d’accès, la date d’expiration et les informations de jeton d’ID.

> [AZURE.NOTE]	L’application de portail d’entreprise Microsoft Intune fournit le composant de service Broker et peut être installée sur l’appareil de l’utilisateur. Cette application offre un accès par authentification unique (SSO) pour toutes les applications de l’appareil. Les développeurs doivent être prêts à autoriser l’utilisation d’Intune. La bibliothèque ADAL pour Android utilise le compte de service Broker si un compte d’utilisateur est créé dans l’authentificateur. Pour utiliser le service Broker, le développeur doit inscrire un `redirectUri` spécial. `redirectUri` est au format msauth://packagename/Base64UrlencodedSignature. Vous pouvez obtenir `redirectUri` pour votre application à l’aide du script `brokerRedirectPrint.ps1` ou de l’appel d’API `mContext.getBrokerRedirectUri()`. La signature est liée à vos certificats de signature de la boutique Google Play.

 Vous pouvez ignorer l’utilisateur du service Broker avec :

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.NOTE] Pour réduire la complexité de ce guide de démarrage rapide B2C, nous avons décidé dans notre exemple d’ignorer le service Broker.

Ensuite, créez les méthodes d’assistance permettant d’obtenir le jeton au cours des appels d’authentification à l’API de tâche.

Dans le même fichier `ToDoActivity.java`, écrivez le code suivant :

```
    private void getToken(final AuthenticationCallback callback) {

        String policy = getIntent().getStringExtra("thePolicy");

        // one of the acquireToken overloads
        mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES,
                policy, Constants.CLIENT_ID, Constants.REDIRECT_URL, getUserInfo(),
                PromptBehavior.Always, "nux=1&" + Constants.EXTRA_QP, callback);
    }
```

Ajoutez également des méthodes qui définiront (set) et obtiendront (get) `AuthenticationResult` (qui contient votre jeton) dans l’élément `Constants` global. Bien que `ToDoActivity.java` utilise `sResult` dans ses flux, vous devez ajouter ces méthodes. Si vous ne le faites pas, vos autres activités n’auront pas accès au jeton pour effectuer des tâches (telles que l’ajout d’une tâche dans `AddTaskActivity.java`).

```

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {


        Constants.CURRENT_RESULT = newToken;
    }


```
## Création d’une méthode pour retourner un identificateur d’utilisateur

La bibliothèque ADAL pour Android représente l’utilisateur sous la forme d’un objet `UserIdentifier` afin de gérer l’utilisateur. Vous pouvez utiliser cet objet pour déterminer si le même utilisateur est utilisé dans les appels. À l’aide de ces informations, vous pouvez vous appuyer sur le cache au lieu d’effectuer un nouvel appel au serveur. Pour faciliter cette opération, nous avons créé une méthode `getUserInfo()` qui retourne un objet `UserIdentifier`. Vous pouvez utiliser cette méthode avec `acquireToken()`. Nous avons également créé une méthode `getUniqueId()` qui retourne l’ID de l’objet `UserIdentifier` dans le cache.

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

### Écriture des méthodes d’assistance

À présent, écrivez des méthodes d’assistance pour vous aider à effacer les cookies et à fournir l’objet `AuthenticationCallback`. Ces méthodes sont utilisées exclusivement pour l’exemple afin de s’assurer que l’état est correct lors de l’appel de votre activité `ToDo`.

Dans le même fichier `ToDoActivity.java`, écrivez le code suivant :

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

## Appel de l’API de tâche

Une fois votre activité prête à récupérer les jetons, vous pouvez écrire votre API de sorte qu’elle accède au serveur de tâches.

`getTasks` fournit un tableau qui répertorie les tâches présentes sur votre serveur.

Commencez avec `getTasks`.

Dans le même fichier `ToDoActivity.java`, écrivez le code suivant :

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

Écrivez également une méthode qui initialisera vos tables lors de la première exécution.

Dans le même fichier `ToDoActivity.java`, écrivez le code suivant :

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

Pour fonctionner, ce code nécessite des méthodes supplémentaires. Écrivez-les après.

### Création d’un générateur d’URL de point de terminaison

Vous devez générer l’URL de point de terminaison à laquelle vous vous connecterez. Effectuez cette opération dans le même fichier de classe.

**Dans le même fichier** `ToDoActivity.java`, écrivez le code suivant :

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


Notez que le jeton d’accès est ajouté à la requête dans le code présenté dans la section suivante.

## Écriture de méthodes d’expérience utilisateur

Android vous oblige à gérer certains rappels pour que l’application fonctionne. Il s’agit des rappels `createAndShowDialog` et `onResume()`. Cela devrait vous être familier si vous avez déjà écrit du code Android.

Dans le même fichier `ToDoActivity.java`, écrivez le code suivant :

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

Ensuite, gérez vos rappels de boîte de dialogue.

Dans le même fichier `ToDoActivity.java`, écrivez le code suivant :

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

Vous devriez maintenant disposer d’un fichier `ToDoActivity.java` compilé. L’ensemble du projet devrait également être compilé à ce stade.

## Exécution de l'exemple d'application

Enfin, générez et exécutez l’application dans Android Studio ou Eclipse. Inscrivez-vous ou connectez-vous à l’application. Créez des tâches pour l’utilisateur connecté. Déconnectez-vous et reconnectez-vous avec les identifiants d’un autre utilisateur, puis créez des tâches pour cet utilisateur.

Notez que les tâches sont stockées pour chaque utilisateur sur l’API, dans la mesure où l’API extrait l’identité de l’utilisateur à partir du jeton d’accès reçu.

Pour référence, l’exemple terminé [est fourni en tant que fichier .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip). Vous pouvez également le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```


## Informations importantes


### Chiffrement

La bibliothèque ADAL chiffre les jetons et les stocke dans `SharedPreferences` par défaut. Vous pouvez consulter la classe `StorageHelper` pour afficher les détails. Android a introduit **AndroidKeyStore** pour le stockage sécurisé des clés privées de la version 4.3 (API18). La bibliothèque ADAL utilise ces informations pour API18 et les versions ultérieures. Si vous souhaitez utiliser la bibliothèque ADAL pour les versions inférieures du Kit de développement logiciel (SDK), vous devez fournir une clé secrète à `AuthenticationSettings.INSTANCE.setSecretKey`.

### Cookies de session dans l’affichage web

La vue web d’Android n’efface pas les cookies de session après la fermeture de l’application. Vous pouvez gérer cela à l’aide de l’exemple de code suivant.
```
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
[En savoir plus sur les cookies](http://developer.android.com/reference/android/webkit/CookieSyncManager.html).

<!---HONumber=AcomDC_0302_2016-->