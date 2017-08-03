
## <a name="add-the-applications-registration-information-to-your-app"></a>Ajouter les informations d’inscription de l’application à votre application

Pour cette étape, vous devez configurer l’URL de redirection des informations d’inscription de votre application, puis ajouter l’ID d’application à votre application SPA JavaScript.

### <a name="configure-redirect-url"></a>Configurer l’URL de redirection

Dans le champ `Redirect URL` ci-dessus, copiez l’URL de la page index.html de votre serveur web, puis cliquez sur *Mettre à jour*.

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url-using-ssl"></a>Instructions Visual Studio pour obtenir l’URL de redirection à l’aide de SSL
> Si vous utilisez Visual Studio, configurez votre projet de manière à utiliser SSL, puis utilisez l’URL SSL pour configurer les informations d’inscription de votre application en suivant les instructions ci-dessous :
> 1.    Dans l’Explorateur de solutions, sélectionnez le projet et examinez la fenêtre `Properties` (si vous ne voyez pas une fenêtre de propriétés, appuyez sur F4).
> 2.    Remplacez `SSL Enabled` par `True` :
> 3.    Copiez la valeur de `SSL URL` dans le Presse-papiers :<br/> ![Propriétés du projet](media/active-directory-singlepageapp-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 4.    Sélectionnez le menu `Project`, puis sélectionnez `{Project} Properties...` (où {Project} correspond au nom de votre projet)
> 5.    Ouvrez l’onglet `Web`.
> 6.    Collez la valeur de `SSL URL` dans le champ `Project Url`.
> 7.    Collez également la valeur dans le champ `Redirect URL` situé en haut de la page, puis cliquez sur *Mettre à jour*.


### <a name="configure-your-javascript-spa-application"></a>Configurer votre application SPA JavaScript

1.  Créez un fichier nommé `msalconfig.js` contenant les informations d’inscription de l’application. Si vous utilisez Visual Studio, sélectionnez le projet (dossier racine du projet), cliquez avec le bouton droit, puis sélectionnez : `Add` > `New Item` > `JavaScript File`. Nommez-le `msalconfig.js`.
2.  Ajoutez le code suivant à votre fichier `msalconfig.js` :

```javascript
var msalconfig = {
    clientID: "[Enter the application Id here]",
    redirectUri: location.origin
};
``` 
