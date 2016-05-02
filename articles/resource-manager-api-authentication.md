<properties 
   pageTitle="Gestion des autorisations via les API Resource Manager | Microsoft Azure"
   description="Guide de développement portant sur la gestion des autorisations via l’API Azure Resource Manager et Active Directory, à des fins d’intégration d’une application dans Azure."
   services="azure-resource-manager,active-directory"
   documentationCenter="na"
   authors="dushyantgill"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/18/2016"
   ms.author="dugill;tomfitz" />


# Guide du développeur pour l’authentification avec l’API Azure Resource Manager

Si vous êtes un développeur de logiciels souhaitant intégrer une application dans Microsoft Azure, ou gérer les ressources Azure de votre client, cette rubrique vous explique comment effectuer l’authentification avec les API Azure Resource Manager.

Votre application peut accéder à ces API de différentes manières :

1. **Accès de l’utilisateur et de l’application** : utilisez cette méthode pour les applications accédant aux ressources pour le compte d’un utilisateur connecté. Elle fonctionne pour les applications telles que les applications web et les outils en ligne de commande, qui gèrent uniquement l’« administration interactive » des ressources Azure.
1. **Accès de l’application seule** : utilisez cette méthode lorsque l’identité de l’application doit bénéficier d’un accès direct aux ressources. Elle fonctionne pour les services de démon et les tâches planifiées requérant un « accès hors connexion » longue durée à Microsoft Azure.

Cette rubrique explique pas à pas comment créer une application qui utilise ces deux méthodes d’autorisation.

Vous allez créer une application web qui :

1. Connecte un utilisateur Azure.
2. Interroge Resource Manager pour le compte de l’utilisateur (« accès de l’utilisateur et de l’application ») afin d’obtenir une liste des abonnements de l’utilisateur à Azure.
3. Permet à l’utilisateur de « connecter » les abonnements à l’application, octroyant ainsi un accès direct à ces abonnements.
4. Accède à Resource Manager pour le compte de l’application afin d’effectuer des opérations hors connexion (« accès de l’application seulement »).

Voici le flux de bout en bout correspondant à l’application web que vous écrivez.

![Authentification ARM - Inscription d’une application 1](./media/resource-manager-api-authentication/ARM-Auth-Swim-Lane.png)

L’ensemble du code de cette rubrique est exécuté en tant qu’application web, que vous pouvez essayer en accédant au site suivant : [http://vipswapper.azurewebsites.net/cloudsense](http://vipswapper.azurewebsites.net/cloudsense).

En tant qu’utilisateur, vous sélectionnez le type de compte à utiliser pour la connexion :

![Sélectionner la connexion](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-1.png)

Indiquez vos informations d’identification.

![Fournir des informations d’identification](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-2.png)

Accordez à l’application un accès à vos abonnements Azure :
 
 ![Accorder l’accès](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-3.png)
 
Connectez vos abonnements à l’application, à des fins de surveillance :

![Connecter un abonnement](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-4.png)

Déconnectez ou réparez la connexion à l’application :

![Déconnecter un abonnement](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-5.png)

## Inscription de votre application auprès d’Azure Active Directory

Commencez par inscrire votre application web auprès de Microsoft Azure Active Directory (AD). L’inscription de l’application entraîne la création d’une identité centrale pour votre application dans Azure AD. Elle contient des informations de base sur votre application, par exemple l’ID client OAuth, les informations d’identification et URL de réponse que votre application utilise pour s’authentifier et accéder aux API Azure Resource Manager. L’inscription de l’application enregistre également les différentes autorisations que requièrent vos applications pour accéder aux API Microsoft pour le compte de l’utilisateur.

La rubrique [Création de l’application Active Directory et du principal du service à l’aide du portail](resource-group-create-service-principal-portal.md) indique toutes les étapes requises pour configurer votre application. Suivez la procédure de cette rubrique pour créer une application présentant les propriétés décrites ci-après :

- application web appelée **CloudSense** ;
- URL de connexion et URI ID de l’application, au format **http://{domain_name_of_your_directory}/{name_of_the_app}** ;
- clé d’authentification pour la signature de l’application ;
- autorisation déléguée **Accéder à la gestion des services Azure** pour l’**API de gestion des services Azure**. Laissez la valeur par défaut **Activer l’authentification unique et lire le profil de l’utilisateur** pour **Azure Active Directory** ;
- applications multi-locataires.

### Configuration facultative : informations d’identification des certificats

Azure AD prend également en charge les informations d’identification des certificats pour les applications : vous créez un certificat auto-signé, conservez la clé privée et ajoutez la clé publique lors de l’inscription de votre application Azure AD. Dans le cadre de l’authentification, votre application envoie une petite charge utile à Azure AD, signée à l’aide de votre clé privée ; ensuite, Azure AD valide la signature à l’aide de la clé publique que vous avez inscrite.

Pour en savoir plus sur la configuration du certificat, voir [Build service and daemon apps in Office 365 (Créer des applications de démon et de service dans Office 365)](https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365). La section portant sur la configuration d’un certificat X.509 public pour l’application propose des instructions pas à pas, qui expliquent comment configurer ce certificat. Vous pouvez également consulter la rubrique [Authentification d’un principal du service à l’aide d’Azure Resource Manager](resource-group-authenticate-service-principal.md) pour obtenir des exemples de configuration d’un certificat via Azure PowerShell ou l’interface Azure CLI.

## Authentifier l’utilisateur et obtenir un jeton d’accès

Vous avez maintenant tout ce dont vous avez besoin pour commencer à coder l’application. Cette rubrique fournit des exemples d’API REST pour chaque étape du flux de bout en bout, ainsi que des liens vers le code C# approprié pour chaque étape. L’intégralité de l’exemple d’application ASP.NET MVC est disponible à l’adresse suivante : [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

Vous commencez au moment où l’utilisateur décide de connecter son abonnement Azure à votre application.

Vous devez lui demander deux éléments :

1. **Nom de domaine Active Directory**: nom de domaine de l’instance Azure Active Directory associée à l’abonnement Azure de l’utilisateur. La demande d’autorisation OAuth 2.0 doit être envoyée à cette instance Azure AD. L’utilisateur peut rechercher le nom de domaine de son instance Azure AD en accédant au portail Azure et en sélectionnant le compte dans l’angle supérieur droit de la fenêtre. Vous pouvez fournir des instructions visuelles à l’utilisateur, par exemple : 

     ![](./media/resource-manager-api-authentication/show-directory.png)
   
1. **Compte Microsoft ou compte professionnel**: déterminez si l’utilisateur gère son abonnement Azure avec un compte Microsoft (également appelé « ID Live ») ou un compte professionnel (ou « compte d’organisation »). S’il utilise un compte Microsoft, votre application redirige l’utilisateur vers la page de connexion Azure Active Directory avec un paramètre de chaîne de requête (& domain\_hint=live.com), qui indique au logiciel Azure AD qu’il doit diriger l’utilisateur directement vers la page de connexion du compte Microsoft. Le code d’autorisation et les jetons que vous recevez pour chaque type de compte sont traités de la même façon.

Votre application redirige ensuite l’utilisateur vers Azure AD avec une requête d’autorisation OAuth 2.0, afin d’authentifier les informations d’identification de l’utilisateur et d’obtenir un code d’autorisation. Votre application utilise le code d’autorisation pour obtenir un jeton d’accès à Resource Manager.

### Demande d’autorisation (OAuth 2.0)

Envoyez une demande d’autorisation Open ID Connect/OAuth2.0 au point de terminaison d’autorisation Azure AD :

    http://login.microsoftonline.com/{directory_domain_name}/OAuth2/Authorize

Les paramètres de chaîne de requête qui sont disponibles pour cette requête sont décrits dans la rubrique [Flux d’octroi d’un code d’autorisation](https://msdn.microsoft.com/library/azure/dn645542.aspx).

L’exemple suivant indique comment demander l’autorisation OAuth2.0 :

    https://login.windows.net/dushyantgill.com/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Le logiciel Azure AD authentifie l’utilisateur et, si nécessaire, lui demande d’accorder des autorisations à l’application. Il renvoie le code d’autorisation à l’URL de réponse de votre application. Selon le paramètre response\_mode demandé, Azure AD renvoie les données dans la chaîne de requête ou sous la forme de données de publication.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### Demande d’autorisation (Open ID Connect)

Si vous souhaitez accéder à Azure Resource Manager pour le compte de l’utilisateur, mais également permettre à ce dernier de se connecter à votre application via son compte Azure AD, envoyez une demande d’autorisation Open ID Connect. Grâce à Open ID Connect, votre application reçoit également un paramètre id\_token, envoyé par Azure AD, que votre application peut utiliser pour connecter l’utilisateur.

Les paramètres de chaîne de requête de la demande d’autorisation OAuth2.0 sont les suivants :

| Paramètre QS | Valeur
|----|----
| client\_id | ID client de votre application
| response\_mode | **form\_post** ou **query**
| response\_type | **code+id\_token**
| redirect\_uri | URL de réponse codée URL de votre application. Exemple : http://www.vipswapper.com/cloudsense/Account/SignIn |
| resource | Identificateur codé URL des API de gestion des services Azure : https://management.core.windows.net/ |
| scope | openid+profile
| nonce | Élément de données permettant de lier la demande d’autorisation au paramètre id\_token renvoyé pour garantir que la demande d’autorisation est demandée et n’est pas réexécutée.
| domain\_hint | live.com <br />**Remarque** : utilisez uniquement le paramètre domain\_hint si l’utilisateur gère son abonnement Azure via un compte Microsoft.
| state | Si vous le souhaitez, spécifiez toutes les données d’état que le logiciel Azure AD doit renvoyer avec la réponse.

Exemple de demande Open ID Connect :

     https://login.windows.net/dushyantgill.com/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Le logiciel Azure AD authentifie l’utilisateur et, si nécessaire, lui demande d’accorder des autorisations à l’application. Il renvoie le code d’autorisation à l’URL de réponse de votre application. Selon le paramètre response\_mode demandé, Azure AD renvoie les données dans la chaîne de requête ou sous la forme de données de publication.

Exemple de réponse Open ID Connect :

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### Valider le paramètre id\_token

Avant de connecter l’utilisateur, l’application doit valider le paramètre id\_token. La validation du jeton est un sujet important. Vous êtes invité à utiliser la bibliothèque de gestionnaire de jetons web JSON standard de votre plateforme de développement (consultez le [code source du gestionnaire JWT .NET Azure AD](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/System.IdentityModel.Tokens.Jwt/JwtSecurityTokenHandler.cs)). Ceci dit, la sécurité de votre application est votre responsabilité. Assurez-vous que la bibliothèque que vous utilisez pour gérer correctement le paramètre id\_token valide correctement les aspects suivants du jeton :

- **Minutage du jeton** : vérifiez les revendications nbf et exp pour vous assurer que le jeton n’est ni trop récent, ni obsolète. En général, une période de 5 minutes est prévue pour la prise en compte du décalage horaire éventuel.
- **Émetteur** : vérifiez la revendication iss, afin de vous assurer que l’émetteur du jeton est bien Azure Active Directory : https://sts.windows.net/{tenant_id_of_the_directory}
- **Public** : vérifiez la revendication aud pour vous assurer que le jeton a été a été réalisé conformément à votre application. La valeur doit correspondre à l’ID client de votre application.
- **Nonce** : vérifiez la revendication nonce par rapport aux données nonce envoyée dans la demande d’autorisation, afin de vous assurer que la réponse a été demandée par votre application et que le jeton n’est pas en cours de réexécution.
- **Signature** : votre application doit vérifier que le jeton a été signé par Azure Active Directory. Les clés de signature AD Azure sont modifiées fréquemment. De ce fait, votre application doit interroger le système pour obtenir les clés actualisées quotidiennement ou renvoyer une erreur concernant les clés actualisées en cas d’échec de la validation de la signature. Pour en savoir plus, voir [Informations importantes sur la substitution des clés de signature dans Azure AD](https://msdn.microsoft.com/library/azure/dn641920.aspx).

Une fois le paramètre **id\_token** validé, utilisez la valeur de revendication oid en tant qu’identificateur non modifiable et non réutilisable de l’utilisateur. Utilisez la revendication **unique\_name** ou upn/email comme nom d’affichage contrôlable de visu de l’utilisateur. Vous pouvez également utiliser les revendications given\_name/family\_name facultatives à des fins d’affichage.

### Demande de jeton (flux d’octroi d’un code d’autorisation OAuth2.0)

Maintenant que votre application a reçu le code d’autorisation d’Azure AD, il est temps d’obtenir le jeton d’accès pour Azure Resource Manager. Publiez une demande de jeton d’octroi de code OAuth2.0 au point de terminaison de jeton Azure AD :

    http://login.microsoftonline.com/{directory_domain_name}/OAuth2/Token

Les paramètres de chaîne de requête qui sont disponibles pour cette requête sont décrits dans la rubrique [Flux d’octroi d’un code d’autorisation](https://msdn.microsoft.com/library/azure/dn645542.aspx).

L’exemple suivant illustre une demande de jeton d’octroi de code avec les informations d’identification de mot de passe :

    POST https://login.windows.net/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Lorsque vous utilisez des informations d’identification de certificat, créez une clé d’authentification web JSON (JWT) et effectuez la signature (RSA SHA256) à l’aide de la clé privée des informations d’identification du certificat de votre application. Les types de revendication du jeton sont affichés dans la rubrique [Flux d’octroi d’un code d’autorisation](https://msdn.microsoft.com/library/azure/dn645542.aspx). Référez-vous également au code [Active Directory Authentication Library (.NET)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/master/src/ADAL.NET/CryptographyHelper.cs) pour la signature des jetons JWT d’assertion du client.

Pour en savoir plus sur l’authentification du client, reportez-vous aux [spécifications d’Open ID Connect](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication). Voici une [exemple de jeton JWT d’assertion du client](https://www.authnauthz.com/OAuth/ParseJWTToken?token=eyJhbGciOiJSUzI1NiIsIng1dCI6IlFwcXdKZnJNZ003ekJ4M1hkM2NSSFdkYVFsTSJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ud2luZG93cy5uZXRcL2FhbHRlc3RzLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQyODk2Mjk5MSwiaXNzIjoiOTA4M2NjYjgtOGE0Ni00M2U3LTg0MzktMWQ2OTZkZjk4NGFlIiwianRpIjoiMmYyMjczMzQtZGQ3YS00NzZkLWFlOTYtYzg4NDQ4YTkxZGM0IiwibmJmIjoxNDI4OTYyMzkxLCJzdWIiOiI5MDgzY2NiOC04YTQ2LTQzZTctODQzOS0xZDY5NmRmOTg0YWUifQ.UXQE9H-FlwxYQmRVG0-p7pAX9TFgiRXcYr7GhbcC7ndIPHKpZ5tfHWPEgBl3ZVRvF2l8uA7HEV86T7t2w7OHhHwLBoW7XTgj-17hnV1CY21MwjrebPjaPIVITiilekKiBASfW2pmss3MjeOYcnBV2MuUnIgt4A_iUbF_-opRivgI4TFT4n17_3VPlChcU8zJqAMpt3TcAxC3EXXfh10Mw0qFfdZKqQOQxKHjnL8y7Of9xeB9BBD_b22JNRv0m7s0cYRx2Cz0cUUHw-ipHhWaW7YwhVRMfK6BMkaDUgaie4zFkcgHb7rm1z0rM1CvzIqP-Mwu3oEqYpY9cYo8nEjMyA).

L’exemple suivant illustre une demande de jeton d’octroi de code avec les informations d’identification de certificat :

	POST https://login.windows.net/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1
	
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 1012
	
	grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Exemple de réponse de jeton d’octroi de code :

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### Gérer la réponse de jeton d’octroi de code

Une réponse de jeton correcte contient le jeton d’accès (user + app) associé à Azure Resource Manager. Votre application utilise ce jeton d’accès pour accéder à Resource Manager pour le compte de l’utilisateur. La durée de vie des jetons d’accès émis par Azure AD est d’une heure. Il est peu probable que votre application web ait besoin de renouveler le jeton d’accès (user + app). Cependant, si tel est le cas, vous pouvez utiliser le jeton d’actualisation que votre application reçoit dans la réponse du jeton. Publiez une demande de jeton OAuth2.0 au point de terminaison du jeton Azure AD :

    http://login.microsoftonline.com/{directory_domain_name}/OAuth2/Token

Les paramètres à utiliser avec la demande d’actualisation sont décrits dans la rubrique [Flux d’octroi d’un code d’autorisation](https://msdn.microsoft.com/library/azure/dn645542.aspx).

L’exemple suivant indique comment utiliser le jeton d’actualisation :

    POST https://login.windows.net/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Remarque : bien que les jetons d’actualisation puissent être utilisés pour obtenir de nouveaux jetons d’accès pour Azure Resource Manager, ils ne sont pas adaptés à un accès hors connexion par votre application. La durée de vie des jetons d’actualisation est limitée ; ils sont liés à l’utilisateur. Si ce dernier quitte l’organisation, l’application utilisant le jeton d’actualisation ne bénéficie plus d’un accès. Cette approche n’est pas adaptée aux applications qui sont utilisées par les équipes pour gérer leurs ressources Azure.

## Liste des abonnements disponibles

Votre application bénéficie désormais d’un jeton permettant d’accéder à Microsoft Azure Resource Manager pour le compte de l’utilisateur.

L’étape suivante consiste à autoriser l’utilisateur à connecter son abonnement Azure à votre application, afin que cette dernière puisse gérer cet abonnement même lorsqu’il n’est pas présent (accès hors connexion à long terme). À l’attention de l’utilisateur, affichez une liste des abonnements Azure pour lesquels il peut gérer l’accès et autorisez-le à affecter directement un rôle RBAC à l’identité de votre application.

![Authentification ARM - Exemple d’application Ux 4](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-4-full.png)

### Liste des abonnements auxquels l’utilisateur a accès

Nous allons commencer par appeler l’API [de la liste des abonnements Resource Manager](https://msdn.microsoft.com/library/azure/dn790531.aspx) pour répertorier tous les abonnements auxquels l’utilisateur a accès, quelle que soit la nature de cet accès. Ensuite, nous identifierons ceux dont l’utilisateur peut gérer les accès.

La méthode [GetUserSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L79) de l’exemple d’application MVC ASP.net implémente cet appel.

Voici un exemple de demande de liste d’abonnements :

    GET https://management.azure.com/subscriptions?api-version=2014-04-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Voici un exemple de réponse à une demande de liste d’abonnements :

    HTTP/1.1 200 OK

    {"value":[{"id":"/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2","subscriptionId":"34370e90-ac4a-4bf9-821f-85eeedeae1a2","displayName":"Sandbox","state":"Enabled","subscriptionPolicies":{"locationPlacementId":"Public_2014-09-01","quotaId":"PayAsYouGo_2014-09-01"}},{"id":"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e","subscriptionId":"c276fc76-9cd4-44c9-99a7-4fd71546436e","displayName":"Production","state":"Enabled","subscriptionPolicies":{"locationPlacementId":"Public_2014-09-01","quotaId":"PayAsYouGo_2014-09-01"}}]}

### Obtenir les autorisations de l’utilisateur concernant les abonnements

L’action de connexion/déconnexion doit être affichée uniquement pour les abonnements pour lesquels l’utilisateur peut gérer les accès. Pour chaque abonnement, vous allez appeler l’API des [autorisations de liste Resource Manager](https://msdn.microsoft.com/library/azure/dn906889.aspx), afin de déterminer si l’utilisateur bénéficie de droits de gestion des accès concernant l’abonnement.

La méthode [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L132) de l’exemple d’application MVC ASP.net implémente cet appel.

L’exemple suivant indique comment demander les autorisations d’un utilisateur concernant un abonnement. L’ID de cet abonnement est le suivant : 83cfe939-2402-4581-b761-4f59b0a041e4.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2014-07-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Voici un exemple de réponse à une demande d’obtention des autorisations d’un utilisateur concernant un abonnement :

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

L’API d’autorisations renvoie plusieurs autorisations. Chaque autorisation est constituée d’actions autorisées (paramètre Actions) et interdites (paramètre NotActions). Si une action est présente dans la liste d’actions autorisées de n’importe quelle autorisation, mais ne figure pas dans sa liste d’actions interdites, cela signifie que l’utilisateur est autorisé à effectuer cette action. L’action permettant d’octroyer des droits de gestion des accès est la suivante : **microsoft.authorization/roleassignments/write**. Votre application doit analyser le résultat des autorisations, afin de rechercher une correspondance d’expression régulière sur cette chaîne d’action dans les actions autorisées et interdites de chaque autorisation.

### Facultatif : liste des répertoires dans laquelle le compte d’utilisateur est présent

Un compte d’utilisateur peut être présent dans plusieurs annuaires Azure Active Directory. Il se peut que l’utilisateur n’ait pas spécifié le nom de répertoire correct au départ. Dans ce cas, l’abonnement désiré n’apparaît pas dans la liste.

L’API de [liste des locataires Resource Manager](https://msdn.microsoft.com/library/azure/dn790536.aspx) répertorie les ID de l’ensemble des répertoires dans lesquels figure le compte d’utilisateur. Vous pouvez appeler l’API pour déterminer si le compte d’utilisateur se trouve dans plusieurs répertoires et, éventuellement, afficher à l’utilisateur un message du type : « Vous n’avez pas trouvé l’abonnement que vous recherchiez ? Peut-être se trouve-t-il dans l’autre répertoire Azure Active Directory auquel vous appartenez. Cliquez ici pour changer de répertoire. »

La méthode [GetUserOrganizations](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) de l’exemple d’application MVC ASP.NET implémente cet appel.

Voici un exemple de demande de liste de répertoires :

    GET https://management.azure.com/tenants?api-version=2014-04-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1Qi****8DJf1UO4a-ZZ_TJmWFlwO1mM7Cw6JWtfY2lGc5A

Voici un exemple de réponse à une demande de liste de répertoires :

    HTTP/1.1 200 OK

    {"value":[{"id":"/tenants/7fe877e6-a150-4992-bbfe-f517e304dfa0","tenantId":"7fe877e6-a150-4992-bbfe-f517e304dfa0"},{"id":"/tenants/62e173e9-301e-423e-bcd4-29121ec1aa24","tenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24"}]}

## Connecter l’abonnement à l’application

Vous disposez à présent d’une liste des abonnements Azure que l’utilisateur peut connecter à votre application. L’étape suivante consiste à donner à l’utilisateur une commande permettant de créer la connexion. Lorsque l’utilisateur sélectionne **connect**, votre application :

1. Attribue le rôle RBAC approprié à l’identité de votre application sur l’abonnement.
2. Valide l’attribution d’accès en demandant l’autorisation de l’application associée à l’abonnement, ou en accédant à Resource Manager via le jeton d’application uniquement.
1. Enregistre la connexion dans la structure de données des abonnements connectés associée à vos applications, en assurant la persistance de l’ID de l’abonnement.

Étudions de plus près la première étape. Pour affecter le rôle RBAC approprié à l’identité de l’application, vous devez déterminer :

- l’ID objet de l’identité de votre application dans l’annuaire Azure Active Directory de l’utilisateur ;
- l’identificateur du rôle RBAC requis par votre application sur l’abonnement.

Penchons-nous plus avant sur la première partie : la première fois que votre application authentifie un utilisateur à partir d’une instance de Microsoft Azure AD, un objet de principal de service est créé pour votre application dans cette instance. Microsoft Azure permet d’affecter des rôles RBAC à des principaux de service, afin d’accorder un accès direct aux applications correspondantes sur des ressources Azure. C’est exactement ce que nous souhaitons faire. Donc, nous commençons par interroger l’API Microsoft Azure AD Graph pour déterminer l’identificateur du principal du service de votre application dans l’instance Azure AD de l’utilisateur connecté.

Vous disposez d’un jeton d’accès pour Azure Resource Manager uniquement. Il vous faut donc un nouveau jeton d’accès pour appeler l’API Microsoft Azure AD Graph. Dans Azure AD, chaque application est autorisée à interroger son propre objet de principal de service. Nous n’avons donc pas besoin d’un jeton d’accès utilisateur + application pour cela : un jeton d’accès d’application uniquement suffit.

<a id="app-azure-ad-graph">
### Obtenir un jeton d’accès d’application uniquement pour l’API Microsoft Azure AD Graph

Pour authentifier votre application et obtenir un jeton à l’API Microsoft Azure AD Graph, envoyez une demande de jeton de flux OAuth2.0 pour l’octroi d’informations d’identification du client au point de terminaison de jeton Azure AD (**http://login.microsoftonline.com/{nom\_domaine\_répertoire}/OAuth2/Token**).

Les lignes 73-77 de la méthode [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#L73) de l’exemple d’application MVC ASP.net permettent d’obtenir un jeton d’accès d’application uniquement pour l’API Graph, via la bibliothèque d’authentification Active Directory Authentication Library (ADAL) pour .NET.

Les données de la demande de jeton d’octroi d’informations d’identification du client sont les suivantes :

| Élément | Valeur
|----|----
| grant\_type | **client\_credentials**
| client\_id | ID client de votre application
| resource | Identificateur codé URL de la ressource pour laquelle le jeton d’accès est demandé. Dans ce cas, l’identificateur de l’API Azure AD Graph est le suivant : **https://graph.windows.net/** | clé\_secrète\_client or type\_assertion\_client + assertion\_client | Si votre application utilise des informations d’identification de mot de passe, utilisez la valeur clé\_secrète\_client. Si votre application utilise des informations d’identification de certificat, utilisez la valeur assertion\_client.

Voici un exemple de demande de jeton d’octroi d’informations d’identification du client :

    POST https://login.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Voici un exemple de réponse à une demande de jeton d’octroi d’informations d’identification du client :

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### Obtenir le principal de service d’application ObjectId dans l’instance Azure AD de l’utilisateur

À présent, utilisez le jeton d’accès d’application uniquement pour interroger l’API des [principaux du service Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#ServicePrincipalEntity), afin de déterminer l’ID objet du principal du service de l’application dans le répertoire.

La méthode [GetObjectIdOfServicePrincipalInOrganiation](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#L66) de l’exemple d’application MVC ASP.net implémente cet appel.

L’exemple suivant indique comment demander un principal du service d’une application. L’ID client de l’application est le suivant : a0448380-c346-4f9f-b897-c18733de9394.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

L’exemple suivant inclut une réponse à la demande d’obtention du principal de service d’une application.

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### Obtenir l’identificateur du rôle Azure RBAC

Vous devez attribuer le rôle RBAC approprié au principal du service de votre application sur l’abonnement sélectionné. Pour cela, vous devez déterminer l’identificateur de ce rôle.

Le rôle RBAC approprié pour votre application :

- Si votre application surveille uniquement l’abonnement sans apporter de modifications, elle requiert des autorisations de lecture sur l’abonnement uniquement. Affectez le rôle **Lecteur**.
- Si votre application gère l’abonnement dans Azure, ainsi que la création, la modification et la suppression d’entités, elle nécessite l’une des autorisations de collaborateur.
  - Pour gérer un type particulier de ressource, affectez des rôles de collaborateur spécifiques de la ressource (de type contributeur de machines virtuelles, collaborateur de réseau virtuel, collaborateur de compte de stockage, etc.).
  - Pour gérer tout type de ressource, vous devez attribuer le rôle **Contributeur**.

L’attribution d’un rôle à votre application est visible pour les utilisateurs. Pour cette raison, vous devez sélectionner le privilège présentant le niveau requis le moins élevé.

Appelez l’[API de définition de rôle Resource Manager](https://msdn.microsoft.com/library/azure/dn906879.aspx) pour répertorier tous les rôles RBAC Azure et recherchez, puis itérez le résultat, afin de rechercher la définition du rôle souhaité par son nom.

La méthode [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L354) de l’exemple d’application MVC ASP.net implémente cet appel.

L’exemple de demande suivant indique comment obtenir l’identificateur de rôle RBAC Azure. L’ID de l’abonnement est le suivant : 09cbd307-aa71-4aca-b346-5f253e6e3ebb.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2014-07-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

La réponse présente le format suivant :

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Remarque : il est inutile d’appeler cette API en continu. Une fois que vous avez déterminé le GUID réservé de la définition de rôle, vous pouvez construire l’ID de définition de rôle ainsi :

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Voici les GUID réservés des rôles intégrés couramment utilisés :

| Rôle | Guid |
| ----- | ------ |
| Lecteur | acdd72a7-3385-48ef-bd42-f606fba81ae7
| Collaborateur | b24988ac-6180-42a0-ab88-20f7382dd24c
| Collaborateur de machine virtuelle | d73bb868-a0df-4d4d-bd69-98a00b01fccb
| Collaborateur de réseau virtuel | b34d265f-36f7-4a0d-a4d4-e158ca92e90f
| Collaborateur de compte de stockage | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25
| Collaborateur de site web | de139f84-1756-47ae-9be6-808fbbe84772
| Collaborateur de plan web | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b
| Collaborateur SQL Server | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437
| Collaborateur de base de données SQL | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec

### Attribuer un rôle RBAC à l’application

Vous disposez à présent de tous les éléments requis pour attribuer le rôle RBAC approprié au principal de service de votre application sur l’abonnement sélectionné, à l’aide de l’[API d’attribution de rôle Resource Manager](https://msdn.microsoft.com/library/azure/dn906887.aspx).

La méthode [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L269) de l’exemple d’application MVC ASP.net implémente cet appel.

Voici un exemple de demande d’attribution d’un rôle RBAC à l’application :

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2014-10-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

Dans cette demande, les valeurs suivantes sont utilisées :

| Guid | Description |
| ------ | --------- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb | ID de l’abonnement
| c3097b31-7309-4c59-b4e3-770f8406bad2 | ID objet du principal du service de l’application
| acdd72a7-3385-48ef-bd42-f606fba81ae7 | GUID réservé du rôle RBAC Lecteur
| 4f87261d-2816-465d-8311-70a27558df4c | Nouveau GUID créé pour l’attribution du nouveau rôle

La réponse présente le format suivant :

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### Obtenir un jeton d’accès d’application uniquement pour Azure Resource Manager

L’étape suivante consiste à vérifier que l’application bénéficier du type d’accès requis sur l’abonnement. Pour ce faire, vous devez exécuter une tâche de test sur l’abonnement à l’aide d’un jeton d’application uniquement pour Azure Resource Manager. La tâche de test doit vérifier que votre application bénéficie bien de l’accès souhaité à l’abonnement, afin de pouvoir effectuer la gestion/surveillance hors connexion.

Pour obtenir un jeton d’accès d’application uniquement pour Azure Resource Manager, suivez la procédure de la section [Obtenir un jeton d’accès d’application uniquement pour l’API Microsoft Azure AD Graph](#app-azure-ad-graph), en utilisant une valeur différente pour le paramètre de ressource :

    https://management.core.windows.net/

Les lignes 210-214 de la méthode [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L203) de l’exemple d’application MVC ASP.net permettent d’obtenir un jeton d’accès d’application uniquement pour Azure Resource Manager, via la bibliothèque d’authentification ADAL pour .NET.

#### Obtenir les autorisations de l’application concernant l’abonnement

Pour vérifier que votre application dispose de l’accès désiré à un abonnement Azure, vous pouvez également appeler l’API [d’autorisations Resource Manager](https://msdn.microsoft.com/library/azure/dn906889.aspx), de la même manière que vous avez déterminé si l’utilisateur bénéficiait de droits de gestion des accès pour l’abonnement. Cette fois, cependant, appelez l’API d’autorisations avec le jeton d’accès d’application uniquement que vous avez reçu au cours de l’étape précédente.

La méthode [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L203) de l’exemple d’application MVC ASP.NET implémente cet appel.

## Gérer les abonnements connectés

Lorsque le rôle RBAC approprié est affecté au principal de service de votre application sur l’abonnement, cette dernière peut continuer à la surveiller/la gérer via des jetons d’accès d’application uniquement pour Azure Resource Manager.

Si le propriétaire d’un abonnement supprime l’attribution de rôle associée à votre application via des outils de ligne de commande ou le portail de gestion Azure, votre application ne peut plus accéder à cet abonnement. Dans ce cas, informez l’utilisateur que la connexion à l’abonnement a été supprimée depuis un emplacement externe à l’application et donnez-lui la possibilité de « réparer » cette connexion. Dans ce cas, la « réparation » consiste simplement à recréer l’attribution de rôle supprimée hors connexion.


## Déconnecter des abonnements

Tout comme vous avez permis à l’utilisateur de connecter ses abonnements à votre application, vous devez l’autoriser à les déconnecter. Dans le contexte de la gestion des accès, le terme « déconnexion » signifie que vous supprimez l’attribution de rôle associée au principal de service de l’application sur l’abonnement. Si vous le souhaitez, vous pouvez supprimer n’importe quel état de l’application pour l’abonnement. Seuls les utilisateurs dotés d’une autorisation de gestion des accès sur l’abonnement pourront déconnecter l’abonnement.

La méthode [RevokeRoleFromServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L303) de l’exemple d’application MVC ASP.net implémente cet appel.

Ça y est, les utilisateurs peuvent désormais se connecter et gérer facilement leurs abonnements Azure avec votre application.

<!---HONumber=AcomDC_0420_2016-->