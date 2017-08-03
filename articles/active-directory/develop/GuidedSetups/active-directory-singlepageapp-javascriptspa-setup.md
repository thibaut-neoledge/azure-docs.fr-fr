
## <a name="setting-up-your-web-server-or-project"></a>Configuration du serveur web ou du projet

> Vous préférez télécharger le projet Visual Studio de cet exemple ? [Téléchargez un projet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/master.zip) et passez à l’étape [Configuration](#create-an-application-express) pour configurer l’exemple de code avant l’exécution.

## <a name="prerequisites"></a>Composants requis
Pour exécuter cette installation guidée, vous avez besoin d’un serveur web local tel que [http-server](https://www.npmjs.com/package/http-server/), [Node.js](https://nodejs.org/en/download/) ou [.NET Core](https://www.microsoft.com/net/core), ou de l’intégration d’IIS Express à [Visual Studio 2017](https://www.visualstudio.com/downloads/). 

Les instructions de ce guide sont basées sur Visual Studio 2017. Cependant, vous pouvez utiliser n’importe quel environnement de développement ou éditeur HTML/JavaScript.


## <a name="create-your-project-visual-studio-only"></a>Créer votre projet (Visual Studio uniquement)

Si vous utilisez Visual Studio pour créer un projet, suivez les étapes ci-dessous pour créer une solution Visual Studio :
1.  Dans Visual Studio : `File` > `New` > `Project`
2.  Sous `Visual C#\Web`, sélectionnez `ASP.NET Web Application (.NET Framework)`
3.  Donnez un nom à votre application et cliquez sur *OK*.
4.  Sous `New ASP.NET Web Application`, sélectionnez `Empty`


## <a name="create-your-single-page-applications-ui"></a>Créer l’interface utilisateur de votre application à page unique (SPA)
1.  Créez un fichier index.html pour votre application SPA JavaScript. Si vous utilisez Visual Studio, sélectionnez le projet (dossier racine du projet), cliquez avec le bouton droit, sélectionnez `Add` > `New Item` > `HTML page`, puis nommez-le index.html.
2.  Ajoutez le code suivant à votre page :
```html
<!DOCTYPE html>
<html>
    <head>
        <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
        <title>JavaScript SPA Guided Setup</title>
    </head>
    <body style="margin: 40px">
        <button id="callGraphButton" type="button" class="btn btn-primary" onclick="callGraphAPI()">Call Microsoft Graph API</button>
        <div id="errorMessage" class="text-danger"></div>
        <div class="hidden">
            <h3>Graph API Call Response</h3>
            <pre class="well" id="graphResponse"></pre>
        </div>
        <div class="hidden">
            <h3>Access Token</h3>
            <pre class="well" id="accessToken"></pre>
        </div>
        <div class="hidden">
            <h3>ID Token Claims</h3>
            <pre class="well" id="userInfo"></pre>
        </div>
        <button id="signOutButton" type="button" class="btn btn-primary hidden" onclick="signOut()">Sign out</button>

        <script src="//secure.aadcdn.microsoftonline-p.com/lib/0.1.1/js/msal.min.js"></script>
        <script type="text/javascript" src="msalconfig.js"></script>
    
        <!-- The 'bluebird' and 'fetch' references below are required if you need to run this application on Internet Explorer -->
        <script src="//cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
        <script src="//cdnjs.cloudflare.com/ajax/libs/fetch/2.0.3/fetch.min.js"></script>
    </body>
</html>
````
