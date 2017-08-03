
### <a name="create-an-application-express"></a>Créer une application (Express)
Maintenant, vous devez inscrire votre application dans le portail d’inscription des applications Microsoft :

1.  Inscrivez votre application via le [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure).
2.  Entrez un nom pour votre application, ainsi que votre adresse e-mail.
3.  Vérifiez que l’option *Guided Setup* (Installation guidée) est bien cochée.
4.  Suivez les instructions à l’écran pour obtenir l’ID de l’application et collez-le dans votre code.

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Ajouter les informations d’inscription de l’application à votre solution (Avancé)

1. Accédez au [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app) pour inscrire une application.
2. Entrez un nom pour votre application, ainsi que votre adresse e-mail. 
3. Vérifiez que l’option *Guided Setup* (Installation guidée) n’est pas cochée.
4.  Cliquez sur `Add Platform`, puis sélectionnez `Web`.
5. Ajoutez une URL de redirection à votre application. L’URL de redirection est l’URL de votre page `index.html` en fonction de votre serveur web.
6. Cliquez sur *Enregistrer*.

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url-using-ssl"></a>Instructions Visual Studio pour obtenir l’URL de redirection à l’aide de SSL
> Si vous utilisez Visual Studio, configurez votre projet de manière à utiliser SSL, puis utilisez l’URL SSL pour configurer les informations d’inscription de votre application en suivant les instructions ci-dessous :
> 1.    Dans l’Explorateur de solutions, sélectionnez le projet et examinez la fenêtre `Properties` (si vous ne voyez pas une fenêtre de propriétés, appuyez sur F4).
> 2.    Remplacez `SSL Enabled` par `True` :
> 3.    Copiez la valeur de `SSL URL` dans le Presse-papiers :<br/> ![Propriétés du projet](media/active-directory-singlepageapp-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 4.    Sélectionnez le menu `Project`, puis sélectionnez `{Project} Properties...` (où {Project} correspond au nom de votre projet)
> 5.    Ouvrez l’onglet `Web`.
> 6.    Collez la valeur de `SSL URL` dans le champ `Project Url`.
> 7.    Basculez vers le portail d’inscription des applications, collez la valeur d’URL de redirection dans `Redirect URL`, puis cliquez sur *Enregistrer*.


#### <a name="configure-your-javascript-spa-application"></a>Configurer votre application SPA JavaScript

1.  Créez un fichier nommé `msalconfig.js` contenant les informations d’inscription de l’application. Si vous utilisez Visual Studio, sélectionnez le projet (dossier racine du projet), cliquez avec le bouton droit, puis sélectionnez : `Add` > `New Item` > `JavaScript File`. Nommez-le `msalconfig.js`.
2.  Ajoutez le code suivant à votre fichier `msalconfig.js` :

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
<ol start="3">
<li>
Remplacez <code>Enter_the_Application_Id_here</code> par l’ID d’application que vous venez d’enregistrer
</li>
</ol>