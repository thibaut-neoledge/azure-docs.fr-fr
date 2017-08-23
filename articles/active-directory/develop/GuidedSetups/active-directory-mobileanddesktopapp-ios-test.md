
## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>Tester l’interrogation de l’API Microsoft Graph à partir de votre application iOS

Appuyez sur `Command` + `R` pour exécuter le code dans le simulateur.

![Exemple d’écran](media/active-directory-mobileanddesktopapp-ios-test/iostestscreenshot.png)

Quand vous êtes prêt à tester, appuyez sur *Appeler l’API Microsoft Graph* et vous serez invité à taper votre nom d’utilisateur et un mot de passe.

### <a name="consent"></a>Consentement
La première fois que vous vous connectez à votre application, un écran de consentement semblable à ce qui suit, où vous devez accepter explicitement ce qui est indiqué, s’affiche :

![Écran de consentement](media/active-directory-mobileanddesktopapp-ios-test/iosconsentscreen.png)

### <a name="expected-results"></a>Résultats attendus
Vous devez voir les informations de profil utilisateur renvoyées par l’appel de l’API Microsoft Graph dans la section *Journalisation*.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Informations supplémentaires sur les étendues et les autorisations déléguées

L’API Microsoft Graph requiert l’étendue `user.read` pour lire le profil de l’utilisateur. Par défaut, cette étendue est automatiquement ajoutée à toutes les applications inscrites dans notre portail d’inscription. D’autres API pour Microsoft Graph ainsi que des API personnalisées pour votre serveur principal peuvent nécessiter des étendues supplémentaires. Par exemple, pour Microsoft Graph, l’étendue `Calendars.Read` est requise pour dresser la liste des calendriers de l’utilisateur. Pour accéder au calendrier de l’utilisateur dans le contexte d’une application, vous devez ajouter l’autorisation déléguée `Calendars.Read` aux informations d’inscription de l’application, puis ajouter l’étendue `Calendars.Read` à l’appel `acquireTokenSilent`. L’utilisateur peut être invité à donner des consentements supplémentaires à mesure que vous augmentez le nombre d’étendues.

<!--end-collapse-->



