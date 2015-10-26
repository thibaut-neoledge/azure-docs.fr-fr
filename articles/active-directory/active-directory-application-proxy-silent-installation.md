<properties
	pageTitle="Comment installer silencieusement le connecteur du Proxy d'application Azure AD"
	description="Explique comment effectuer une installation silencieuse du connecteur du Proxy d’application Azure AD pour offrir un accès à distance sécurisé à vos applications locales."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="steven.powell"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/01/2015"
	ms.author="rkarlin"/>

# Comment installer silencieusement le connecteur du Proxy d'application Azure AD

Vous souhaitez pouvoir envoyer un script d'installation vers plusieurs serveurs ou des serveurs Windows sans interface utilisateur. Cette rubrique explique comment créer un script Windows PowerShell permettant l'installation sans assistance et l’inscription de votre connecteur de Proxy d'application Azure AD.

## Activation de l’accès
Le Proxy d’application fonctionne avec un service Windows Server léger appelé connecteur, à l’intérieur de votre réseau. Pour que le connecteur du Proxy d'application puisse fonctionner, il doit être inscrit auprès de votre annuaire Azure AD à l'aide d'un identifiant d’administrateur global et d’un mot de passe. Généralement, ces informations sont saisies pendant l'installation du connecteur dans une boîte de dialogue contextuelle. Sinon, vous pouvez utiliser Windows PowerShell pour créer un objet d'informations d'identification et utiliser le jeton ainsi créé pour saisir vos informations d'inscription, ou vous pouvez créer votre propre jeton à utiliser pour entrer vos informations d'inscription.

## Étape 1 : Installation du connecteur sans inscription


Pour installer les MSI du connecteur sans inscrire le connecteur, procédez comme suit :


1. Ouvrez une invite de commandes.
2. Exécutez la commande suivante dans laquelle /q signifie une installation silencieuse - l'installation ne vous demande pas d’accepter le Contrat de Licence Utilisateur Final.

        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## Étape 2 : Sélection du connecteur avec Azure Active Directory
Cette étape peut être effectuée avec une des méthodes suivantes : - Inscription du connecteur à l'aide d'un objet d'informations d'identification Windows PowerShell - Inscription du connecteur à l'aide d'un jeton créé hors connexion

### Inscription du connecteur à l’aide d’un objet d’informations d’identification Windows PowerShell


1. Créez l'objet d'informations d'identification de Windows PowerShell en exécutant la commande suivante, où les valeurs <username> et <password> doivent être remplacées par le nom d'utilisateur et le mot de passe pour accéder à votre annuaire :

        $User = "<username>" 
        $PlainPassword = '<password>' 
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force 
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword 
    
2. 	Accédez à **C:\\Program Files\\Microsoft AAD App Proxy Connector** puis exécutez le fichier **Register Connector.PS1** dans Windows PowerShell.
3. Utilisez l'objet d'informations d'identification PowerShell créé afin d’entrer le nom d'utilisateur et le mot de passe pour l’inscription du connecteur en exécutant la commande suivante, où $cred est le nom de l'objet d'informations d'identification PowerShell créé :

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred 


### Inscription du connecteur à l’aide d’un jeton créé hors connexion

1. Créez un jeton hors connexion à l'aide de la classe AuthenticationContext, par exemple :

        #region constants /// /// The AAD authentication endpoint uri /// static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.windows.net/common/oauth2/token?api-version=1.0");
        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
		static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");
		
		/// <summary>
		/// The AppIdUri of the registration service in AAD
		/// </summary>
		static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

		#endregion


		public static void GetAuthenticationToken()
		{
    		AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);
	    AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);


	        if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
    	    {
          Trace.TraceError("Authentication result, token or tenant id returned are null");
          throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
    	}

    	string token = authResult.AccessToken;
    	string tenantID = authResult.TenantId;
		}

2. Une fois que vous disposez du jeton, créez une chaîne SecureString en utilisant le jeton : <br> `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`
3. Exécutez la commande Windows PowerShell suivante, où SecureToken est le nom du jeton que vous avez créé précédemment : <br> `RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`



## Et ensuite ?
Vous pouvez faire bien d’autres choses encore avec le Proxy d’application :


- [Publier des applications avec votre propre nom de domaine](active-directory-application-proxy-custom-domains.md)
- [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
- [Utiliser des applications utilisant les revendications](active-directory-application-proxy-claims-aware-apps.md)
- [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)


### En savoir plus sur le Proxy d’application
- [Consultez notre aide en ligne](active-directory-application-proxy-enable.md)
- [Consultez le blog sur le Proxy d’application](http://blogs.technet.com/b/applicationproxyblog/)
- [Regardez nos vidéos sur Channel 9](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Ressources supplémentaires
* [Inscription à Azure en tant qu’organisation](../sign-up-organization.md)
* [Identité Azure](../fundamentals-identity.md)

<!---HONumber=Oct15_HO3-->