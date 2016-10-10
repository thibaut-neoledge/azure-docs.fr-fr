<properties 
	pageTitle="Connexion à un compte Media Services à l’aide de .NET" 
	description="Cette rubrique montre comment se connecter à Media Services avec .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/26/2016"
	ms.author="juliako"/>


# Connexion à un compte Media Services à l’aide du Kit de développement logiciel (SDK) Media Services pour .NET

> [AZURE.SELECTOR]
- [REST](media-services-rest-connect-programmatically.md)
- [.NET](media-services-dotnet-connect_programmatically.md)


Cette rubrique décrit comment obtenir une connexion à Microsoft Azure Media Services par programme lorsque vous programmez avec le Kit de développement logiciel (SDK) Media Services pour .NET.


## Connexion à Media Services

Pour vous connecter à Media Services par programme, vous devez avoir précédemment configuré un compte Azure et Media Services sur ce compte, puis configurer un projet Visual Studio pour le développement avec le Kit de développement logiciel (SDK) Media Services pour .NET. Pour plus d'informations, consultez la page Configuration du développement avec le Kit de développement logiciel (SDK) Media Services pour .NET.

À la fin du processus de configuration de compte Media Services, vous avez obtenu les valeurs de connexion requises suivantes. Utilisez-les pour établir les connexions par programme avec Media Services.

- Le nom de votre compte Media Services.

- Votre clé de compte Media Services.

Pour obtenir ces valeurs, accédez au portail de gestion Azure, sélectionnez votre compte Media Service, puis cliquez sur l'icône **« GÉRER LES CLÉS en bas »** de la fenêtre du portail. Cliquer sur l'icône en regard de chaque zone de texte copie la valeur dans le Presse-papiers du système.


## Création d’une instance CloudMediaContext

Pour commencer à programmer sur Media Services, vous devez créer une instance **CloudMediaContext** qui représente le contexte du serveur. **CloudMediaContext** comprend des références à des collections importantes comme les travaux, les éléments multimédias, les fichiers, les stratégies d'accès et les localisateurs.

>[AZURE.NOTE] La classe **CloudMediaContext** n'est pas thread-safe. Vous devez créer un nouveau CloudMediaContext par thread ou par ensemble d’opérations.


CloudMediaContext a cinq surcharges de constructeur. Il est recommandé d'utiliser des constructeurs qui acceptent **MediaServicesCredentials** en tant que paramètre. Pour plus d'informations, consultez la rubrique **Réutilisation des jetons Access Control Service** qui suit.

L’exemple suivant utilise le constructeur public CloudMediaContext(informations d’identification MediaServicesCredentials) :

	// _cachedCredentials and _context are class member variables. 
	_cachedCredentials = new MediaServicesCredentials(
	                _mediaServicesAccountName,
	                _mediaServicesAccountKey);
	
	_context = new CloudMediaContext(_cachedCredentials);


## Réutilisation des jetons du service de contrôle d’accès

Cette section montre comment réutiliser les jetons du Service de contrôle d’accès à l’aide de constructeurs CloudMediaContext acceptant MediaServicesCredentials en tant que paramètre.


[Azure Active Directory Access Control](https://msdn.microsoft.com/library/hh147631.aspx) (également appelé Access Control Service ou ACS) est un service cloud qui offre un moyen facile d'authentifier les utilisateurs et d'autoriser l'accès à leurs applications web. Microsoft Azure Media Services contrôle l’accès à ses services via le protocole OAuth, qui requiert un jeton ACS. Media Services reçoit les jetons ACS d’un serveur d’autorisation.

Lorsque vous développez avec le Kit de développement logiciel (SDK) Media Services, vous pouvez choisir de ne pas traiter les jetons, étant donné que le code du Kit de développement (SDK) les gère pour vous. Toutefois, laisser le Kit de développement logiciel (SDK) gérer entièrement les jetons ACS entraîne des demandes de jetons inutiles. Les demandes de jetons prennent du temps et consomment les ressources client et serveur. En outre, le serveur ACS limite les demandes si le taux est trop élevé. La limite est de 30 demandes par seconde. Consultez [Limitations du Service ACS](https://msdn.microsoft.com/library/gg185909.aspx) pour plus de détails.

À compter de la version 3.0.0.0 du Kit de développement logiciel (SDK) Media Services, vous pouvez réutiliser les jetons ACS. Les constructeurs **CloudMediaContext** qui acceptent **MediaServicesCredentials** comme paramètre autorisent le partage des jetons ACS entre plusieurs contextes. La classe MediaServicesCredentials encapsule les informations d’identification de Media Services. Si un jeton ACS est disponible et que son heure d’expiration est connue, vous pouvez créer une instance de MediaServicesCredentials avec le jeton et le transmettre au constructeur de CloudMediaContext. Notez que le Kit de développement logiciel (SDK) Media Services actualise automatiquement les jetons chaque fois qu’ils arrivent à expiration. Il existe deux façons de réutiliser les jetons ACS, comme le montrent les exemples ci-dessous.

- Vous pouvez mettre en cache l'objet **MediaServicesCredentials** en mémoire (par exemple, dans une variable de classe statique). Puis, passez l’objet mis en cache au constructeur CloudMediaContext. L’objet MediaServicesCredentials contient un jeton ACS qui peut être réutilisé s’il est toujours valide. Si le jeton n’est pas valide, il est actualisé par le Kit de développement logiciel (SDK) Media Services en utilisant les informations d’identification fournies au constructeur MediaServicesCredentials.

	Notez que l'objet **MediaServicesCredentials** fournit un jeton valide après l'appel de RefreshToken. **CloudMediaContext** appelle la méthode **RefreshToken** dans le constructeur. Si vous envisagez d’enregistrer les valeurs de jeton sur un stockage externe, veillez à vérifier si la valeur de TokenExpiration est valide avant d’enregistrer les données du jeton. S’il n’est pas valide, appelez RefreshToken avant la mise en cache.

		// Create and cache the Media Services credentials in a static class variable.
		_cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);

		
		// Use the cached credentials to create a new CloudMediaContext object.
		if(_cachedCredentials == null)
		{
		    _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);
		}
		
		CloudMediaContext context = new CloudMediaContext(_cachedCredentials);

- Vous pouvez également mettre en cache la chaîne AccessToken et les valeurs de TokenExpiration. Les valeurs peuvent être utilisées ultérieurement pour créer un nouvel objet MediaServicesCredentials avec les données du jeton mises en cache. Ceci est particulièrement utile pour les scénarios où le jeton peut être partagé en toute sécurité entre plusieurs processus ou ordinateurs.

	Les extraits de code suivants appellent les méthodes SaveTokenDataToExternalStorage, GetTokenDataFromExternalStorage et UpdateTokenDataInExternalStorageIfNeeded qui ne sont pas définies dans cet exemple. Vous pouvez définir ces méthodes pour stocker, extraire et mettre à jour des données de jeton dans un stockage externe.

		CloudMediaContext context1 = new CloudMediaContext(_mediaServicesAccountName, _mediaServicesAccountKey);
		
		// Get token values from the context.
		var accessToken = context1.Credentials.AccessToken;
		var tokenExpiration = context1.Credentials.TokenExpiration;
		
		// Save token values for later use. 
		// The SaveTokenDataToExternalStorage method should check 
		// whether the TokenExpiration value is valid before saving the token data. 
		// If it is not valid, call MediaServicesCredentials’s RefreshToken before caching.
		SaveTokenDataToExternalStorage(accessToken, tokenExpiration);
		
	Utilisez les valeurs de jeton enregistrées pour créer MediaServicesCredentials.


		var accessToken = "";
		var tokenExpiration = DateTime.UtcNow;
		
		// Retrieve saved token values.
		GetTokenDataFromExternalStorage(out accessToken, out tokenExpiration);
		
		// Create a new MediaServicesCredentials object using saved token values.
		MediaServicesCredentials credentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey)
		{
		    AccessToken = accessToken,
		    TokenExpiration = tokenExpiration
		};
		
		CloudMediaContext context2 = new CloudMediaContext(credentials);

	Mettez à jour la copie du jeton dans le cas où celui-ci a été mis à jour par le kit SDK Media Services.
	
		if(tokenExpiration != context2.Credentials.TokenExpiration)
		{
		    UpdateTokenDataInExternalStorageIfNeeded(accessToken, context2.Credentials.TokenExpiration);
		}
		

- Si vous possédez plusieurs comptes Media Services (par exemple, pour le partage de charge ou la distribution géographique) vous pouvez mettre en cache les objets de MediaServicesCredentials à l’aide de la collection System.Collections.Concurrent.ConcurrentDictionary (la collection ConcurrentDictionary représente une collection thread-safe de paires clé/valeur accessible par plusieurs threads simultanément). Vous pouvez ensuite utiliser la méthode GetOrAdd pour obtenir les informations d’identification mises en cache.

		// Declare a static class variable of the ConcurrentDictionary type in which the Media Services credentials will be cached.  
		private static readonly ConcurrentDictionary<string, MediaServicesCredentials> mediaServicesCredentialsCache = 
		    new ConcurrentDictionary<string, MediaServicesCredentials>();
		

		// Cache (or get already cached) Media Services credentials. Use these credentials to create a new CloudMediaContext object.
		static public CloudMediaContext CreateMediaServicesContext(string accountName, string accountKey)
		{
		    CloudMediaContext cloudMediaContext;
		    MediaServicesCredentials mediaServicesCredentials;
		
		    mediaServicesCredentials = mediaServicesCredentialsCache.GetOrAdd(
		        accountName,
		        valueFactory => new MediaServicesCredentials(accountName, accountKey));
		
		    cloudMediaContext = new CloudMediaContext(mediaServicesCredentials);
		
		    return cloudMediaContext;
		}
		
## Connexion à un compte Media Services situé dans le nord de la Chine

Si votre compte se trouve dans le nord de la Chine, utilisez le constructeur suivant :

	public CloudMediaContext(Uri apiServer, string accountName, string accountKey, string scope, string acsBaseAddress)

Par exemple :


	_context = new CloudMediaContext(
	    new Uri("https://wamsbjbclus001rest-hs.chinacloudapp.cn/API/"),
	    _mediaServicesAccountName,
	    _mediaServicesAccountKey,
	    "urn:WindowsAzureMediaServices",
	    "https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn");


## Stockage des valeurs de connexion dans la configuration

Il est fortement recommandé de stocker des valeurs de connexion, en particulier les valeurs sensibles comme votre nom de compte et le mot de passe, dans la configuration. En outre, il est fortement recommandé de chiffrer les données de configuration sensibles. Vous pouvez chiffrer le fichier de configuration en utilisant le système de fichiers EFS Windows. Pour activer EFS sur un fichier, cliquez avec le bouton droit sur le fichier, sélectionnez **Propriétés** et activez le chiffrement sous l'onglet de paramètres **Avancé**. Vous pouvez également créer une solution personnalisée pour le chiffrement des parties sélectionnées d’un fichier de configuration à l’aide de la configuration protégée. Consultez la page [Chiffrement des informations de configuration à l'aide de la configuration protégée](https://msdn.microsoft.com/library/53tyfkaw.aspx).

Le fichier App.config suivant contient les valeurs de connexion requises. Les valeurs de l’élément <appSettings> sont les valeurs requises que vous avez obtenues lors du processus d’installation du compte Media Services.

	<configuration>
	  <appSettings>
	    <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
	    <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
	  </appSettings>
	</configuration>


Pour récupérer les valeurs de connexion de configuration, vous pouvez utiliser la classe **ConfigurationManager**, puis affectez les valeurs aux champs de votre code :
	
	private static readonly string _accountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
	private static readonly string _accountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];



##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0928_2016-->