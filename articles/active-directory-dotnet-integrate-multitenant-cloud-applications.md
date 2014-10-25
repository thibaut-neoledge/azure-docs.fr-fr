<properties urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Integrating Multi-Tenant Cloud Applications with Azure Active Directory" authors="terrylan" solutions="" manager="terrylan" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="terrylan"></tags>

# Intégration d'une application cloud mutualisée avec Azure Active Directory

## <a name="introduction"></a>Introduction

Azure Active Directory (Azure AD) est un service moderne, basé sur le protocole REST, qui offre des capacités de contrôle d'accès et de gestion des identités pour vos applications dans le cloud. Azure AD s'intègre aisément aux services cloud, ainsi qu'à Azure, Microsoft Office 365, Dynamics CRM Online et Windows Intune. Les déploiements Active Directory locaux existants peuvent également tirer pleinement parti d'Azure AD. Pour en savoir plus, consultez la [page Identité][] sur [windowsazure.com][].

Cette procédure pas à pas s'adresse aux développeurs .NET qui souhaitent intégrer une application mutualisée à Azure AD. Vous apprendrez à :

-   permettre aux clients de s'inscrire à votre application avec Azure AD ;
-   activer l'authentification unique avec Azure AD ;
-   interroger les données d'annuaire d'un client en utilisant l'API Azure AD Graph.

L'exemple d'application associé à cette procédure pas à pas peut être [téléchargé ici][]. Vous pouvez exécuter cet exemple sans aucune modification, mais il peut s'avérer nécessaire de changer l'[attribution des ports dans Visual Studio][] pour utiliser HTTPS. Cliquez sur le lien et suivez les instructions indiquées, mais définissez le protocole de liaison sur « https » dans la section bindings du fichier ApplicationHost.config. Tous les extraits de code des étapes suivantes proviennent de l'exemple.

> [WACOM.NOTE]
> L'exemple d'application d'annuaire mutualisée est fourni aux fins d'illustration uniquement. Cet exemple (y compris les classes de la bibliothèque d'assistance) ne doit pas être utilisé en production.

### Conditions préalables

Les éléments de développement suivants sont requis pour cette procédure pas à pas :

-   [Visual Studio 2012][]
-   [Services de données WCF pour OData][]

### Sommaire

-   [Introduction][]
-   [Partie 1 : obtention d'un ID client pour l'accès à Azure AD][]
-   [Partie 2 : activation de l'inscription des clients à l'aide d'Azure AD][]
-   [Partie 3 : activation de l'authentification unique][]
-   [Partie 4 : accès à Azure AD Graph][]
-   [Partie 5 : publication de votre application][]
-   [Résumé][]

## <a name="getclientid"></a>Partie 1 : Obtention d'un ID client pour l'accès à Azure AD

Cette section explique comment obtenir un ID client et une clé secrète client après avoir créé un compte Tableau de bord du vendeur Microsoft. L'ID client est l'identificateur unique de votre application et la clé secrète client est la clé associée requise lorsque vous effectuez des demandes avec l'ID client. Tous deux sont nécessaires pour intégrer votre application à Azure AD.

### Étape 1 : création d'un compte avec le Tableau de bord du vendeur Microsoft

Pour développer et publier des applications qui s'intègrent à Azure AD, vous devez créer un compte [Tableau de bord du vendeur Microsoft][]. Vous êtes ensuite invité à [créer un profil de compte][] en tant que société ou individu. Ce profil est utilisé pour publier des applications sur Azure Marketplace ou d'autres marchés, et il est requis pour générer un ID client et une clé secrète client.

Les nouveaux comptes sont placés dans un état « Compte en attente d'approbation ». Cet état ne vous empêche pas de commencer le développement ; vous pouvez toujours créer des ID client ainsi que des brouillons de description d'application. Mais une description d'application ne peut être soumise pour approbation qu'après l'approbation du compte proprement dit. Elle n'est alors visible qu'aux clients dans Azure Marketplace une fois qu'elle a été approuvée.

### Étape 2 : obtention d'un ID client pour votre application

Vous avez besoin d'un ID client et d'une clé secrète client pour intégrer votre application à Azure AD. Identificateur unique de votre application, l'ID client sert principalement à identifier une application pour l'authentification unique ou pour authentifier des appels sur Azure AD Graph. Pour plus d'informations sur l'obtention d'un ID client et d'une clé secrète client, consultez la page [Création d'ID et de clés secrètes client dans le Tableau de bord du vendeur Microsoft][].

> [WACOM.NOTE]
> Vous aurez besoin de votre ID client et de votre clé secrète client plus tard dans cette procédure pas à pas. Aussi, veillez à les enregistrer.

Pour générer un ID client et une clé secrète client, vous devez entrer les propriétés suivantes dans le Tableau de bord du vendeur Microsoft :

**Domaine d'application** : nom d'hôte de votre application, « contoso.com », par exemple. Cette propriété ne doit contenir aucun numéro de port. Pendant le développement, cette propriété doit être définie sur « localhost ».

**App Redirect URL** : URL de redirection à laquelle Azure AD envoie une réponse après la connexion de l'utilisateur et lorsqu'une organisation a autorisé votre application, par exemple : « <https://contoso.com/> ». Pendant le développement, cette propriété doit être définie sur « https://localhost:\<numéro de port\> ».

### Étape 3 : configuration de votre application pour l'utilisation de l'ID client et de la clé secrète client

Cette étape exige l'ID client et la clé secrète client qui ont été générés pendant l'inscription sur le tableau de bord du vendeur. L'ID client est utilisé pour l'authentification unique, et l'ID client et la clé secrète client seront tous deux utilisés ultérieurement pour obtenir un jeton d'accès afin d'appeler l'API Azure AD Graph.

L'exemple d'application a été préconfiguré pour utiliser Azure AD, et charge l'ID client et la clé secrète client à partir de la configuration. Dans le fichier **Web.config** de l'exemple d'application, effectuez les modifications suivantes :

1.  Dans le nœud **appSettings**, remplacez les valeurs de « clientId » et « SymmetricKey » par vos ID client, clé secrète client et domaine d'application :

        <appSettings>
            <add key="clientId" value="(Your Client ID value)"/>
            <add key="SymmetricKey" value="(Your Client Secret value)"/>
            <add key="AppHostname" value="(Your App Domain)"/>
        </appSettings>

2.  Dans le nœud **audienceUris** de **system.identityModel**, insérez votre ID client après « spn: » :

        <system.identityModel>
            <audienceUris>
                <add value="spn:(Your Client ID value)" />
            </audienceUris>

## <a name="enablesignup"></a>Partie 2 : activation de l'inscription des clients à l'aide d'Azure AD

Cette section montre comment activer l'inscription des clients à votre application à l'aide d'Azure AD. Pour qu'un client puisse utiliser une application intégrée à Azure AD, un administrateur client doit d'abord autoriser l'application. Ce processus d'autorisation commence par une demande de consentement envoyée par votre application à Azure, lequel génère une réponse qui doit être traitée par votre application. Les étapes suivantes expliquent comment générer une demande de consentement et traiter la réponse.

Les étapes décrites dans cette section utilisent les classes d'assistance de l'exemple d'application. Ces classes sont contenues dans la bibliothèque *Microsoft.IdentityModel.WAAD.Preview* de l'exemple et permettent de se concentrer sur le code de l'application plutôt que sur les détails des identités et des protocoles.

### Étape 1 : demande de consentement pour votre application

L'exemple d'interaction suivant présente le processus de demande de consentement pour votre application :

1.  Le client clique sur un lien d'inscription à l'aide d'Azure AD sur une page web de votre application.
2.  Vous redirigez le client vers la page d'autorisation Azure AD en ajoutant les informations de votre application à la demande.
3.  Le client donne ou refuse son consentement pour votre application.
4.  Azure AD redirige le client vers l'URL de redirection d'application spécifiée. Vous avez spécifié cette URL lorsque vous avez généré un ID client et une clé secrète client sur le Tableau de bord du vendeur Microsoft. La demande de redirection indique le résultat de la demande de consentement, en incluant des informations sur le locataire si le consentement a été accordé.

Pour générer une demande de redirection à l'étape 2 ci-dessus, vous devez ajouter des paramètres de chaîne de requête à l'URL suivante de la page d'autorisation Azure AD : *<http://activedirectory.windowsazure.com/Consent/AuthorizeApplication.aspx>*

Les paramètres de chaîne de requête sont décrits ci-après :

**ApplicationID** : (obligatoire) valeur **ClientID** que vous avez reçue dans le Tableau de bord du vendeur.

**RequestedPermissions** : (facultatif) autorisations qui doivent être accordées à votre application par le locataire.
Pendant le développement, ces autorisations sont utilisées pour tester les applications non publiées. Pour les applications publiées, ce paramètre est ignoré et les autorisations demandées de votre description d'application sont utilisées à la place. Pour plus d'informations sur cette description, reportez-vous à la partie 5.
Ce paramètre peut prendre trois valeurs :

**DirectoryReader** : accorde l'autorisation de lire les données d'annuaire, par exemple, les comptes utilisateur, les groupes et les informations sur votre organisation. Active l'authentification unique.

**UserAccountAdministrator** : accorde l'autorisation de lire et d'écrire des données, par exemple, les utilisateurs, les groupes et les informations sur votre organisation. Active l'authentification unique.

**None** : permet l'authentification unique, mais désactive l'accès aux données d'annuaire.

La valeur par défaut est « None » si le paramètre n'est pas spécifié ou est spécifié de manière incorrecte.

Voici un exemple d'URL de demande de consentement correcte :
*<https://activedirectory.windowsazure.com/Consent/AuthorizeApplication.aspx?ApplicationId=33E48BD5-1C3E-4862-BA79-1C0D2B51FB26&RequestedPermissions=DirectoryReader>*

Dans l'exemple d'application, le lien « Register » contient une URL similaire pour la demande de consentement, comme indiqué ci-dessous :

![login][]

> [WACOM.NOTE]
> Lorsque vous testez votre application non publiée, vous êtes confronté, pour ce qui touche au consentement, à une expérience similaire à celle de vos clients. La page d'autorisation pour une application non publiée, cependant, est différente de la page d'autorisation pour une application publiée. Cette dernière, contrairement à une application non publiée, affiche le nom de votre application, votre logo et les détails de l'éditeur.

### Étape 2 : traitement de la réponse à la demande de consentement

Lorsqu'un client a accordé ou refusé son consentement pour votre application, Azure AD envoie une réponse à l'URL de redirection de votre application. Cette réponse contient les paramètres de chaîne de requête suivants :

**TenantId** : GUID unique du locataire Azure AD qui a autorisé votre application. Ce paramètre est spécifié uniquement si le client a autorisé votre application.

**Consent** : la valeur est définie sur « Granted » si l'application a été autorisée ou sur « Denied » si la demande a été refusée.

L'exemple suivant présente une réponse correcte à la demande de consentement indiquant que l'application a été autorisée :
*<https://app.litware.com/redirect.aspx&TenantId=7F3CE253-66DB-4AEF-980A-D8312D76FDC2&Consent=Granted>*

Votre application a besoin de conserver le contexte de telle sorte que la demande envoyée à la page d'autorisation Azure AD soit attachée à la réponse (et rejettera toute réponse qui n'est pas associée à une demande).

<div class="dev-callout"><strong>Remarque</strong><p>Une fois le consentement accord&eacute;, il faut un certain temps &agrave; Azure&nbsp;AD pour approvisionner l'authentification unique et l'acc&egrave;s &agrave; Graph. Le premier utilisateur de chaque organisation qui s'inscrit &agrave; votre application pourra &ecirc;tre confront&eacute; &agrave; des erreurs de connexion jusqu'&agrave; ce que l'approvisionnement soit termin&eacute;.</p></div>

Lorsqu'un client a accordé son consentement à votre application, il est important d'associer et de stocker le locataire nouvellement créé dans votre application avec l'ID de locataire (TenantId) renvoyé par la réponse à la demande de consentement. L'exemple d'application contient, dans l'espace de noms *Microsoft.IdentityModel.WAAD.Preview.Consent*, un *HttpModule* qui enregistre automatiquement l'ID de locataire dans un « magasin de données » client/ID de locataire sur toutes les réponses correctes. Le code utilisé à cet effet est inclus ci-après et l'enregistrement de l'ID de locataire dans un « magasin de données » client/ID de locataire est effectué par la méthode *TrustedIssuers.Add* :

    private void Application_BeginRequest(Object source,
             EventArgs e)
    {
        HttpApplication application = (HttpApplication)source;
        HttpRequest req = application.Context.Request;             

        if((!string.IsNullOrEmpty(req.QueryString["TenantId"]) && (!string.IsNullOrEmpty(req.QueryString["Consent"]))))
        { 
            if(req.QueryString["Consent"].Equals("Granted",StringComparison.InvariantCultureIgnoreCase))
            {
                // For this sample we store the consenting tenants in
                // an XML file. We strongly recommend that you change
                // this to use your DataStore
                TrustedIssuers.Add(req.QueryString["TenantId"]; 
            }
        }            
    }

Pour pouvoir tester le code de demande de consentement/réponse pour votre application, vous devez d'abord obtenir un locataire d'annuaire Azure AD.

### Étape 3 : obtention d'un locataire Azure AD pour tester votre application

Pour tester la capacité d'intégration à Azure AD de votre application, vous avez besoin d'un locataire Azure AD. Si vous disposez déjà d'un locataire pour tester une autre application, vous pouvez le réutiliser. Nous vous recommandons d'obtenir au moins deux locataires pour vous assurer que votre application peut être testée et utilisée par plusieurs locataires. Nous vous déconseillons d'utiliser un locataire de production à cet effet. [Obtention d'un locataire Azure AD][].

Une fois que vous avez obtenu un locataire Azure AD, vous pouvez générer et exécuter l'application en appuyant sur **F5**. En outre, vous pouvez essayer de vous inscrire à votre application en utilisant le nouveau locataire.

<div class="dev-callout"><strong>Remarque</strong><p>Si vos clients s'inscrivent &agrave; un nouveau locataire Azure&nbsp;AD, un certain d&eacute;lai peut &ecirc;tre n&eacute;cessaire pour que ce dernier soit enti&egrave;rement approvisionn&eacute;. Des erreurs peuvent appara&icirc;tre sur la page de consentement jusqu'&agrave; ce que l'approvisionnement soit termin&eacute;.</p></div>

## <a name="enablesso"></a>Partie 3 : activation de l'authentification unique

Cette section montre comment activer l'authentification unique. Le processus commence par la construction d'une demande de connexion à Azure AD qui authentifie un utilisateur sur votre application, puis vérifie dans la réponse de connexion que le client appartient à un locataire qui a autorisé votre application. La demande de connexion requiert votre ID client du Tableau de bord du vendeur et l'ID de locataire de l'organisation du client.

La demande de connexion est propre à un locataire d'annuaire et doit inclure un ID de locataire. Ce dernier peut être déterminé à partir du nom de domaine d'un locataire d'annuaire Azure AD. Il existe deux moyens courants d'obtenir ce nom de domaine de l'utilisateur final lorsqu'il se connecte :

-   Si l'URL de l'application est *<https://contoso.myapp.com>* ou *<https://myapp.com/contoso.com>*, *contoso* et *contoso.com* représentent le nom de domaine Azure AD et *myapp.com* l'URL de votre application.
-   Votre application peut inviter l'utilisateur à fournir son adresse électronique ou son nom de domaine Azure AD. Cette approche est utilisée dans l'exemple d'application, où l'utilisateur doit entrer son nom de domaine Azure AD, comme indiqué ci-après :

![login][]

### Étape 1 : recherche de l'ID de locataire

À l'aide du nom de domaine Azure AD fourni par le client, vous pouvez rechercher son ID de locataire en analysant les métadonnées de fédération Azure AD. Dans l'exemple d'application, ce processus est traité par la méthode *Domain2TenantId* de la classe *Microsoft.IdentityModel.WAAD.Preview.TenantUtils.Globals*.

Pour présenter ce processus, les étapes suivantes utilisent le nom de domaine contoso.com.

1.  Obtenez le fichier **FederationMetadata.xml** pour le locataire Azure AD. Par exemple :
    *<https://accounts.accesscontrol.windows.net/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml>*
2.  Dans le fichier **FederationMetadata.xml**, recherchez l'entrée **Entity Descriptor**. L'ID de locataire est inclus dans la propriété **entityID** qui suit « <https://sts.windows.net> », comme indiqué ci-dessous :

         <EntityDescriptor xmlns="urn:oasis:names:tc:SAML:2.0:metadata" entityID="https://sts.windows.net/a7456b11-6fe2-4e5b-bc83-67508c201e4b/" ID="_cba45203-f8f4-4fc3-a3bb-0b136a2bafa5"> 

    Dans le cas présent, la valeur de l'ID de locataire (TenantID) est **a7456b11-6fe2-4e5b-bc83-67508c201e4b**.

3.  Dans le « magasin de données » client/ID de locataire de votre application, vous devez stocker le domaine et l'ID de locataire qui lui est associé. Ces deux valeurs peuvent être utilisées ensemble pour les futures demandes de connexion et vous évitent de devoir obtenir le **FederationMetadata.xml** à chaque fois. L'exemple d'application n'inclut pas cette optimisation.

### Étape 2 : génération de la demande de connexion

Lorsqu'un client se connecte à votre application, par exemple, en cliquant sur un bouton de connexion, la demande de connexion doit être générée avec l'ID de locataire du client et l'ID client de votre application. Dans l'exemple d'application, cette requête est générée par la méthode *GenerateSignInMessage* de la classe *Microsoft.IdentityModel.WAAD.Preview.WebSSO.URLUtils*. Cette méthode vérifie que l'ID de locataire du client représente une organisation qui a autorisé votre application et elle génère l'URL de destination du bouton de connexion, comme indiqué ci-dessous :

![login][]

Un clic sur le bouton permet d'accéder à une page de connexion à Azure AD dans le navigateur de l'utilisateur. Une fois la connexion établie, Azure AD renvoie une réponse de connexion à l'application.

### Étape 3 : validation de l'émetteur du jeton entrant dans la réponse de connexion

Lorsqu'un client se connecte à votre application, vous devez valider le fait que son locataire a autorisé votre application. La réponse de connexion contient un jeton et celui-ci contient des propriétés et des revendications qui peuvent être inspectées par votre application.

Pour effectuer cette validation, vous devez obtenir l'ID de locataire de la propriété Issuer dans le jeton et vous assurer qu'il existe dans le « magasin de données » client/ID de locataire. Dans l'exemple d'application, cette validation est réalisée en créant une classe de gestionnaire de jetons personnalisé qui étend le *Saml2SecurityTokenHandler* de Windows Identity Foundation. Le gestionnaire de jetons personnalisé vérifie le jeton de sécurité entrant et rend ses revendications et ses propriétés disponibles pour l'application de telle sorte que l'ID de locataire puisse être validé. L'extrait de code correspondant à cette classe est présenté ci-dessous.

Dans l'exemple d'application, le code d'origine se trouve sous l'espace de noms *Microsoft.IdentityModel.WAAD.Preview.WebSSO*. Le gestionnaire de jetons utilise également la méthode Contains de la classe *Microsoft.IdentityModel.WAAD.Preview.WebSSO.TrustedIssuers*, qui vérifie que l'ID de locataire est conservé dans le « magasin de données » client/ID de locataire.

    /// <summary>
    /// Extends the out of the box SAML2 token handler by ensuring
    /// that incoming tokens have been issued by registered tenants 
    /// </summary>
    public class ConfigurationBasedSaml2SecurityTokenHandler : Saml2SecurityTokenHandler
    {
        public override ReadOnlyCollection<System.Security.Claims.ClaimsIdentity> ValidateToken(SecurityToken token)
        {
            ReadOnlyCollection<System.Security.Claims.ClaimsIdentity> aa = base.ValidateToken(token);
            Saml2SecurityToken ss = token as Saml2SecurityToken;
            string tenant = ss.Assertion.Issuer.Value.Split('/')[3];
            if (!TrustedIssuers.Contains(tenant))
            {
                throw new SecurityTokenValidationException(string.Format("The tenant {0} is not registered with the application", tenant));
            }
            return aa;
        }
    }

Une fois que le jeton est validé, l'utilisateur est connecté à l'application. Exécutez l'application et essayez de vous connecter en utilisant un compte Azure AD dans le locataire consenti que vous avez créé au préalable.

## <a name="accessgraph"></a>Partie 4 : accès à Azure AD Graph

Cette section montre comment obtenir un jeton d'accès et appeler l'API Azure AD Graph pour accéder aux données d'annuaire d'un locataire. Par exemple, bien que le jeton obtenu pendant la connexion contienne des informations utilisateur telles qu'un nom et une adresse électronique, votre application peut avoir besoin d'informations telles que les appartenances à des groupes ou le nom du responsable de l'utilisateur. Il est possible d'obtenir ces informations de l'annuaire du locataire à l'aide de l'API Graph. Pour plus d'informations sur l'API Graph, consultez [cette rubrique][].

Pour que votre application puisse appeler Azure AD Graph, elle doit s'authentifier et obtenir un jeton d'accès. Vous pouvez obtenir les jetons d'accès en authentifiant votre application avec son ID client et sa clé secrète client. Les étapes suivantes vous montrent comment :

1.  utiliser une classe proxy générée pour appeler Azure AD Graph ;
2.  acquérir un jeton d'accès avec Azure Authentication Library ;
3.  appeler Azure AD Graph pour obtenir une liste d'utilisateurs pour un locataire.

<div class="dev-callout"><strong>Remarque</strong><p>La biblioth&egrave;que d'assistance de l'exemple d'application Microsoft.IdentityModel.WAAD.Preview contient d&eacute;j&agrave; une classe proxy g&eacute;n&eacute;r&eacute;e automatiquement (cr&eacute;&eacute;e par l'ajout d'une r&eacute;f&eacute;rence de service &agrave; https://graph.windows.net/your-domain-name appel&eacute;e GraphService). L'application utilisera cette classe proxy pour appeler le service Azure&nbsp;AD&nbsp;Graph.</p></div>

### Étape 1 : utilisation de la classe proxy pour appeler Azure AD Graph

Au cours de cette étape, nous allons utiliser l'exemple d'application pour vous montrer comment :

1.  créer un point de terminaison Azure AD Graph propre au locataire ;
2.  utiliser le point de terminaison afin d'instancier le proxy pour appeler Graph ;
3.  ajouter l'en-tête Authorization à la demande et acquérir le jeton.

Dans l'exemple d'application, ces appels à l'API sont traités par la méthode GraphInterface dans la classe *Microsoft.IdentityModel.WAAD.Preview.Graph.GraphInterface*, comme indiqué dans le code suivant.

    public GraphInterface()
    {
        // 1a: When the customer was signed in, we get a security token 
        // that contains a tenant id. Extract that here
        TenantDomainName = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/ws/2012/10/identity/claims/tenantid").Value;

        // 1b: We generate a URL (https://graph.windows.net/<CustomerDomainName>)
        // to access the Azure AD Graph API endpoint for the tenant 
        connectionUri = new Uri(string.Format(@"https://{0}/{1}", TenantUtils.Globals.endpoint, TenantDomainName));

        // 2: create an instance of the AzureAD Service proxy with the connection URL
        dataService = new DirectoryDataService(connectionUri);

        // This flags ignores the resource not found exception
        // If AzureAD Service throws this exception, it returns null
        dataService.IgnoreResourceNotFoundException = true;
        dataService.MergeOption = MergeOption.OverwriteChanges;
        dataService.AddAndUpdateResponsePreference = DataServiceResponsePreference.IncludeContent;

        // 3: This adds the default required headers to each request
        AddHeaders(GetAuthorizationHeader());
    }

### Étape 2 : acquisition d'un jeton d'accès à l'aide d'Azure Authentication Library

L'exemple d'application utilise Azure Authentication Library (AAL) pour acquérir des jetons permettant d'accéder à l'API Graph. Le processus d'acquisition des jetons illustré ci-dessous est géré par la méthode *GetAuthorizationHeader* dans la classe *Microsoft.IdentityModel.WAAD.Preview.Graph.GraphInterface*.

<div class="dev-callout"><strong>Remarque</strong><p>AAL est disponible sous la forme d'un package NuGet et peut &ecirc;tre install&eacute; dans Visual Studio.</p></div>

    /// <summary>
    /// Method to get the Oauth2 Authorization header from ACS
    /// </summary>
    /// <returns>AOauth2 Authorization header</returns>
    private string GetAuthorizationHeader()
    {
        // AAL values
        string fullTenantName = TenantUtils.Globals.StsUrl + TenantDomainName;
        string serviceRealm = string.Format("{0}/{1}@{2}", TenantUtils.Globals.GraphServicePrincipalId, TenantUtils.Globals.GraphServiceHost, TenantDomainName);
        string issuingResource = string.Format("{0}@{1}", Globals.ClientId, TenantDomainName);
        string clientResource = string.Format("{0}/{1}@{2}", Globals.ClientId, Globals.AppHostname, TenantDomainName);

        string authzHeader = null;
        AuthenticationContext _authContext = new AuthenticationContext(fullTenantName);

        try
        {
            ClientCredential credential = new ClientCredential(issuingResource, clientResource, Globals.ServicePrincipalKey);
            AssertionCredential _assertionCredential = _authContext.AcquireToken(serviceRealm, credential);
            authzHeader = _assertionCredential.CreateAuthorizationHeader();
        }
        catch (Exception ex)
        {
            AALException aex = ex as AALException;
            string a = aex.InnerException.Message;
        }

        return authzHeader;
    }

Les informations suivantes sont utilisées pour acquérir le jeton d'accès, comme décrit dans le code ci-dessus :

1.  Informations de l'application (ClientID, ServicePrincipalKey et AppHostname)
2.  Informations sur la cible, à savoir Graph, qui est nommé ServiceRealm ci-dessus
3.  TenantDomainName acquis précédemment

### Étape 3 : appel d'Azure AD Graph pour l'obtention d'une liste d'utilisateurs

La méthode suivante dans la classe *Microsoft.IdentityModel.WAAD.Preview.Graph.GraphInterface* permet d'obtenir une liste de tous les utilisateurs pour votre locataire à l'aide du proxy *DataService* généré précédemment.

    public List<User> GetUsers()
    {
        // Add the page size using top
        var users = dataService.Users.AddQueryOption("$top", 20);

        // Execute the Query
        var userQuery = users.Execute();

        // Get the return users list
        return userQuery.ToList();
    }

Cette méthode est appelée depuis le fichier **HomeController.cs** pour afficher la liste d'utilisateurs sous l'onglet Utilisateurs dans l'application web.

## <a name="publish"></a>Partie 5 : Publication de votre application

Une fois que vous avez testé de manière approfondie votre application, vous pouvez créer une description d'application et la publier sur Azure Marketplace. Ces opérations sont effectuées sur le Tableau de bord du vendeur Microsoft.

<div class="dev-callout"><strong>Remarque</strong><p>Votre application assure la gestion des relations avec vos clients relatives &agrave; la tarification. Azure Marketplace fournit uniquement des liens vers le site web de votre application et des informations sur cette derni&egrave;re.</p></div>

### Étape 1 : création d'un manifeste d'application et d'une description d'application

Avant de créer une description d'application, vous devez générer un nouvel ID client et une nouvelle clé secrète client pour la version de production de votre application. Dans la première partie de cette procédure pas à pas, vous avez généré un ID client et une clé secrète client destinés à une version test de votre application. Répétez ces étapes et configurez votre application pour utiliser les nouvelles valeurs, en veillant à définir un domaine d'application et une URL de redirection d'application de production.

Vous devez ensuite créer un manifeste d'application qui répertorie les autorisations que votre application demandera pour le consentement du client. Ce manifeste est écrit dans un format XML régi par un fichier XSD. Le manifeste doit être téléchargé dans le cadre de la description d'application que vous créez.

Il existe trois niveaux d'autorisation, comme décrit dans la première partie de cette procédure pas à pas :

**DirectoryReader** : accorde l'autorisation de lire les données d'annuaire, par exemple, les comptes utilisateur, les groupes et les informations sur votre organisation. Active l'authentification unique.

**UserAccountAdministrator** : accorde l'autorisation de lire et d'écrire des données, par exemple, les utilisateurs, les groupes et les informations sur votre organisation. Active l'authentification unique.

**None** : permet l'authentification unique, mais désactive l'accès aux données d'annuaire.

Deux exemples de manifeste d'application sont inclus ci-après. Le premier présente les autorisations pour une application à authentification unique exclusivement et le second les autorisations pour une application en lecture seule :

    <?xml version="1.0" encoding="utf-16"?>
    <AppRequiredPermissions>
      <AppPermissionRequests Policy="AppOnly">
        <AppPermissionRequest Right="None" Scope="http://directory" />
      </AppPermissionRequests>
    </AppRequiredPermissions>


    <?xml version="1.0" encoding="utf-16"?>
    <AppRequiredPermissions>
      <AppPermissionRequests Policy="AppOnly">
        <AppPermissionRequest Right="Directory Reader" Scope="http://directory">
          <Reason culture="fr-fr" value="Needs to read the app"/>
        </AppPermissionRequest>
      </AppPermissionRequests>
    </AppRequiredPermissions>

L'attribut *Policy* dans les exemples ci-dessus décrit le type d'autorisation d'application demandé. Actuellement, seul l'attribut d'autorisation « AppOnly » est pris en charge. Ce type d'autorisation indique que l'application accède uniquement à l'annuaire en tant que tel.

L'élément facultatif *Reason* vous permet de spécifier (dans plusieurs cultures) votre justification pour le niveau d'autorisation requis. Ce texte est affiché sur la page de consentement pour aider le client lorsqu'il approuve ou refuse votre application.

À l'aide de votre ID client et de votre manifeste d'application, vous pouvez créer une description d'application en suivant les instructions de la page [Ajout d'applications dans le Tableau de bord du vendeur Microsoft][]. Lors de la création d'une description d'application, veillez à sélectionner le type d'application Azure AD. Une fois la création de votre description d'application terminée, cliquez sur « submit » pour publier votre application sur Azure Marketplace. Vous devez attendre que votre application soit approuvée pour que la publication soit effective.

<div class="dev-callout"><strong>Remarque</strong><p>Si vous &ecirc;tes invit&eacute; &agrave; &laquo;&nbsp;ajouter des informations sur les taxes et les paiements&nbsp;&raquo;, vous pouvez ignorer cette &eacute;tape, car vous vendez votre application directement au client, et pas par l'interm&eacute;diaire de Microsoft.</p></div>

### Étape 2 : finalisation du test et publication de votre application

Une fois que votre description d'application est approuvée, vous devez retester votre application de bout en bout. Par exemple, assurez-vous que votre application a été mise à jour avec l'ID client et la clé secrète client de l'environnement de production. Parcourez la liste de contrôle de test une dernière fois, en vous assurant que la page de consentement affiche à présent les informations que vous avez incluses dans votre description d'application.

## <a name="summary"></a>Résumé

Dans cette procédure pas à pas, vous avez appris à intégrer votre application mutualisée à Azure AD. Cette procédure comporte trois étapes :

-   permettre aux clients de s'inscrire à votre application avec Azure AD ;
-   activer l'authentification unique avec Azure AD ;
-   interroger les données d'annuaire d'un client en utilisant l'API Azure AD Graph.

L'intégration à Azure AD permet à vos clients de s'inscrire et de se connecter à votre application avec un système de gestion des identités dont ils assurent déjà la maintenance, ce qui réduit, voire élimine, le besoin d'effectuer des tâches de gestion des identités distinctes avec votre application. Cette fonctionnalité offre à vos clients une expérience plus transparente lorsqu'ils utilisent votre application et leur permet d'économiser du temps sur les tâches de gestion.

  [page Identité]: http://www.windowsazure.com/fr-fr/home/features/identity/
  [téléchargé ici]: http://go.microsoft.com/fwlink/?LinkId=271213
  [attribution des ports dans Visual Studio]: http://msdn.microsoft.com/fr-fr/library/ms178109(v=vs.100).aspx
  [Visual Studio 2012]: http://www.microsoft.com/visualstudio/eng/downloads
  [Services de données WCF pour OData]: http://www.microsoft.com/download/en/details.aspx?id=29306
  [Introduction]: #introduction
  [Partie 1 : obtention d'un ID client pour l'accès à Azure AD]: #getclientid
  [Partie 2 : activation de l'inscription des clients à l'aide d'Azure AD]: #enablesignup
  [Partie 3 : activation de l'authentification unique]: #enablesso
  [Partie 4 : accès à Azure AD Graph]: #accessgraph
  [Partie 5 : publication de votre application]: #publish
  [Résumé]: #summary
  [Tableau de bord du vendeur Microsoft]: https://sellerdashboard.microsoft.com/
  [créer un profil de compte]: http://msdn.microsoft.com/fr-fr/library/jj552460.aspx
  [Création d'ID et de clés secrètes client dans le Tableau de bord du vendeur Microsoft]: http://msdn.microsoft.com/fr-fr/library/jj552461.aspx
  [login]: ./media/active-directory-dotnet-integrate-multitent-cloud-applications/login.png
  [Obtention d'un locataire Azure AD]: http://g.microsoftonline.com/0AX00en/5
  [contoso]: https://contoso.myapp.com
  [cette rubrique]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh974476.aspx
  [Ajout d'applications dans le Tableau de bord du vendeur Microsoft]: http://msdn.microsoft.com/fr-fr/library/jj552465.aspx
