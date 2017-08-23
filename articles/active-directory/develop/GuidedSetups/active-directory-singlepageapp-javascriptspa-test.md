
## <a name="test-your-code"></a>Test de votre code

Si vous utilisez Visual Studio, appuyez sur `F5` pour exécuter votre projet. Le navigateur s’ouvre et vous redirige vers *http://localhost:{port}* où vous pouvez voir le bouton *Call Microsoft Graph API* (Appeler l’API Microsoft Graph).

Si vous n’utilisez pas Visual Studio, vérifiez que votre serveur web est démarré et que le dossier contenant votre application web JavaScript est configuré dans votre serveur web. Ouvrez le navigateur et tapez *http://localhost:{port}/chemin*, où *port* correspond au port qu’écoute votre serveur web, et *chemin* au chemin de votre fichier index.html.

Cliquez sur le bouton *Call Microsoft Graph API* (Appeler l’API Microsoft Graph). S’il s’agit de la première fois, une fenêtre contextuelle invite l’utilisateur à se connecter.
 
![Exemple d’écran](media/active-directory-singlepageapp-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="consent"></a>Consentement
La première fois que vous vous connectez à votre application, un écran de consentement semblable à ce qui suit, où vous devez accepter explicitement ce qui est indiqué, s’affiche :

 ![Écran de consentement](media/active-directory-singlepageapp-javascriptspa-test/javascriptspaconsent.png)

Étant donné que vous interrogez l’API Microsoft Graph, il est possible que vous voyiez une autre page de consentement. Ceci est dû au *consentement dynamique*, qui nécessite un consentement pour chaque étendue demandée par l’application. Pour l’exemple d’application de ce guide, l’étendue *user.read* est fournie. Vous devez donc accorder votre consentement pour que cette application puisse lire le profil utilisateur.

> [!IMPORTANT]
> En raison d’un problème connu avec le javascript *msal*, vous devez désactiver le bloqueur de fenêtres publicitaires dans les navigateurs Chrome et Firefox pour que l’écran de *consentement dynamique* fonctionnent correctement. Vous devrez désactiver le bloqueur de fenêtres publicitaires si vous appelez l’API Microsoft Graph à l’aide de `acquireTokenPopup` pour la première fois.

### <a name="expected-results"></a>Résultats attendus
Vous devez voir les informations de profil utilisateur retournées par l’appel de l’API Microsoft Graph.
 
 ![Résultats](media/active-directory-singlepageapp-javascriptspa-test/javascriptsparesults.png)

Vous verrez également des informations de base sur le jeton acquis sous les sections *Access Token* (Jeton d’accès) et *ID Token Claims* (Revendications de jetons d’ID).

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Informations supplémentaires sur les étendues et les autorisations déléguées

L’API Microsoft Graph nécessite l’étendue `user.read` pour lire le profil de l’utilisateur. Par défaut, cette étendue est automatiquement ajoutée à toutes les applications inscrites dans notre portail d’inscription. D’autres API pour Microsoft Graph ainsi que des API personnalisées pour votre serveur principal peuvent nécessiter des étendues supplémentaires. Par exemple, pour Microsoft Graph, l’étendue `Calendars.Read` est nécessaire pour dresser la liste des calendriers de l’utilisateur. Pour accéder au calendrier de l’utilisateur dans le contexte d’une application, vous devez ajouter l’autorisation déléguée `Calendars.Read` aux informations d’inscription de l’application, puis ajouter l’étendue `Calendars.Read` à l’appel `acquireTokenSilent`. L’utilisateur peut être invité à donner des consentements supplémentaires à mesure que vous augmentez le nombre d’étendues.

Si une API principale ne nécessite pas d’étendue (non recommandé), vous pouvez utiliser le `clientId` en tant qu’étendue dans les appels `acquireTokenSilent` et/ou `acquireTokenPopup`.

<!--end-collapse-->
