En général, les erreurs d’authentification résultent de paramètres de configuration incorrects ou incohérents. Voici quelques suggestions spécifiques des éléments à vérifier :

* Assurez-vous que vous n’avez pas oublié de cliquer sur le bouton **Enregistrer**. C’est souvent le cas. Il en résulte que les valeurs sur une page de portail sont correctes mais qu’elles n’ont pas été enregistrées dans l’environnement Azure ou l’application Azure AD.
* Pour les paramètres configurés dans le panneau **Paramètres de l’application** du portail Azure, vérifiez que vous avez bien sélectionné l’application API ou web appropriée lors de la saisie des paramètres.  Vérifiez également que les paramètres ont été entrés en tant que **paramètres de l’application** et non en tant que **chaînes de connexion**, car le format des deux sections est similaire.
* Pour l’authentification sur un serveur frontal JavaScript, téléchargez le manifeste à nouveau pour vérifier que `oauth2AllowImplicitFlow` a bien été remplacé par `true`.
* Vérifiez que vous avez utilisé HTTPS à chaque fois que vous avez configuré les URL :
  
  * Dans le code de projet
  * Dans CORS
  * Dans les paramètres de l’application de l’environnement Azure pour chaque application API et application web
  * Dans les paramètres de l’application Azure AD.
    
    Notez que si vous copiez l’URL d’une application API à partir du portail, elle contient souvent `http://` que vous devez modifier manuellement en `https://`.
* Vérifiez que les modifications de code ont été déployées avec succès. Par exemple, dans une solution à plusieurs projets, il est possible de modifier le code d’un projet et de choisir accidentellement une des autres versions lorsque vous envisager de déployer la version modifiée.
* Assurez-vous que vous allez accéder aux URL HTTPS et non à vos URL HTTP dans votre navigateur. Par défaut, Visual Studio crée les profils de publication avec des URL HTTP. C’est ce qui s’ouvre dans le navigateur après le déploiement d’un projet.
* Pour l’authentification à un serveur frontal JavaScript, vérifiez que CORS est correctement configuré sur l’application API que le code JavaScript appelle. En cas de doute sur la nature du problème(s’il est lié ou non à CORS), essayez « * » en tant qu’URL d’origine autorisée. 
* Pour un serveur frontal JavaScript, ouvrez l’onglet de la console des outils de développement de votre navigateur pour obtenir plus d’informations sur l’erreur, et examinez les requêtes HTTP sur le réseau. Toutefois, les messages d’erreur de la console peuvent être trompeurs. Si vous obtenez un message indiquant une erreur CORS, le problème réel peut être l’authentification. Vous pouvez vérifier si c’est le cas en exécutant l’application avec l’authentification temporairement désactivée.
* Pour une application API .NET, assurez-vous d’obtenir le plus d’informations possible dans les messages d’erreur en définissant [customErrors mode sur Off](../articles/app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview).
* Pour une application API .NET, démarrez une [session de débogage à distance](../articles/app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)et examinez les valeurs des variables qui sont transmises au code qui utilise ADAL pour acquérir un jeton de porteur ou le code qui vérifie les revendications selon l’ID du principal du service attendu. Notez que, dans la mesure où votre code peut prendre des valeurs de configuration de nombreuses sources différentes, cette approche peut produire des résultats surprenants. Par exemple, si `ida:ClientId` est nommé par erreur `ida:ClientID` lors de la configuration des paramètres de l’environnement Azure App Service, le code peut obtenir la valeur `ida:ClientId` qu’il recherche dans le fichier Web.config, en ignorant le paramètre d’Azure App Service. 
* Si les choses ne fonctionnent pas dans une fenêtre Internet Explorer normale, il est possible qu’une connexion existante soit la cause du problème. Essayez InPrivate et Chrome ou Firefox.

