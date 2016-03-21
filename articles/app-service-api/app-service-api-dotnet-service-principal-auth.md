<properties
	pageTitle="Authentification de principal du service pour API Apps dans Azure App Service | Microsoft Azure"
	description="Découvrez comment protéger une application API dans Azure App Service pour les scénarios de service à service."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/04/2016" 
	ms.author="tdykstra"/>

# Authentification de principal du service pour API Apps dans Azure App Service

[AZURE.INCLUDE [sélecteur](../../includes/app-service-api-auth-selector.md)]

## Vue d'ensemble

Cet article explique comment utiliser l’authentification App Service pour l’accès [interne](app-service-api-authentication.md#internal) aux applications API. Un scénario interne se présente lorsque vous souhaitez qu’une application API soit uniquement utilisable par votre propre code d’application. Le moyen le plus simple d’implémenter ce scénario dans App Service consiste à utiliser Azure AD pour protéger l’application API appelée. Vous appelez l’application API protégée avec un jeton de porteur que vous obtenez d’Azure AD en fournissant des informations d’identification de l’identité d’application (principal du service).

Cet article porte sur les points suivants :

* Utiliser Azure Active Directory (Azure AD) pour protéger une application API contre tout accès non authentifié
* Utiliser une application API protégée à partir d’une application API, web ou mobile à l’aide des informations d’identification du principal du service (identité de l’application) Azure AD. Pour savoir comment utiliser une application API protégée à partir d’une application logique, consultez [Utilisation de votre API personnalisée hébergée sur App Service avec les applications logiques](../app-service-logic/app-service-logic-custom-hosted-api.md).
* S’assurer que les utilisateurs connectés n’ont pas la possibilité d’appeler l’application API protégée à partir d’un navigateur
* S’assurer que seul un principal du service Azure AD spécifique a la possibilité d’appeler l’application API protégée

Cet article contient deux sections :

* La section intitulée [Procédure de configuration de l’authentification du principal du service dans Azure App Service](#authconfig) explique comment configurer l’authentification en général pour n’importe quelle application API et comment utiliser l’application API protégée. Cette section s’applique également à toutes les infrastructures prises en charge par App Service, y compris .NET, Node.js et Java.

* Commençant par la section [Suite des didacticiels dédiés à la mise en route de .NET](#tutorialstart), le didacticiel vous guide à travers la configuration d’un scénario « d’accès interne » pour un exemple d’application .NET exécuté dans App Service.

## <a id="authconfig"></a> Procédure de configuration de l’authentification du principal du service dans Azure App Service

Cette section fournit des instructions générales qui s’appliquent à n’importe quelle application API. Pour connaître les étapes applicables à l’exemple d’application .NET ToDoList, passez à la section [Suite des didacticiels dédiés à la mise en route de .NET](#tutorialstart).

1. Dans le [portail Azure](https://portal.azure.com/), accédez au panneau **Paramètres** de l’application API que vous souhaitez protéger, puis recherchez la section **Fonctionnalités** et cliquez sur **Authentification/Autorisation**.

	![Authentification/Autorisation dans le portail Azure](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Dans le panneau **Authentification/Autorisation**, cliquez sur **Activé**.

4. Dans la liste déroulante **Action à effectuer quand une demande n’est pas authentifiée**, sélectionnez **Se connecter avec Azure Active Directory**.

5. Sous **Fournisseurs d’authentification**, cliquez sur **Azure Active Directory**.

	![Panneau Authentification/Autorisation dans le portail Azure](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Configurez le panneau **Paramètres Active Directory Azure** pour créer une nouvelle application Azure AD ou utilisez une application Azure AD existante si vous en avez déjà une que vous souhaitez utiliser.

	Les scénarios internes impliquent généralement l’appel d’une application API à une autre. Vous pouvez utiliser soit des applications Azure AD distinctes pour chaque application API, soit une simple application Azure AD.

	Pour plus d’informations sur ce panneau, consultez l’article [Configurer votre application App Service pour utiliser la connexion Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

7. Une fois que vous en avez terminé avec le panneau de configuration du fournisseur d’authentification, cliquez sur **OK**.

7. Dans le panneau **Authentification/Autorisation**, cliquez sur **Enregistrer**.

	![Cliquez sur Enregistrer.](./media/app-service-api-dotnet-service-principal-auth/authsave.png)

À présent, App Service autorise uniquement les demandes provenant d’appelants dans le client Azure AD configuré. Aucun code d’authentification ou d’autorisation n’est requis dans l’application API protégée. Le jeton de porteur est transmis à l’application API avec les revendications couramment utilisées dans les en-têtes HTTP, et vous pouvez lire ces informations dans le code pour vérifier que les demandes proviennent d’un appelant particulier, par exemple un principal du service.

Cette fonctionnalité d’authentification fonctionne de la même manière pour tous les langages pris en charge par App Service, notamment .NET, Node.js et Java.

#### Comment utiliser l’application API protégée

L’appelant doit joindre aux appels API un jeton de porteur Azure AD. Pour obtenir un jeton de porteur à l’aide des informations d’identification du principal du service, l’appelant utilise la bibliothèque d’authentification Active Directory (ADAL pour [.NET](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory), [Node.js](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) ou [Java](https://github.com/AzureAD/azure-activedirectory-library-for-java)). Pour obtenir un jeton, le code qui appelle la bibliothèque ADAL lui fournit les informations suivantes :

* le nom de votre client Azure AD ;
* l’ID et la clé secrète du client (clé d’application) de l’application Azure AD associée à l’appelant ;
* l’ID client de l’application Azure AD associée à l’application API protégée (si une seule application Azure AD est utilisée, il s’agit du même ID client que celui de l’appelant).

Ces valeurs sont disponibles dans les pages Azure AD du [portail Azure Classic](https://manage.windowsazure.com/).

Une fois le jeton obtenu, l’appelant peut l’inclure avec les requêtes HTTP dans l’en-tête d’autorisation. App Service valide le jeton et autorise les demandes à atteindre l’application API protégée.

#### Comment protéger l’application API contre tout accès par les utilisateurs dans le même client

Les jetons de porteur des utilisateurs du même client sont considérés comme valides pour l’application API protégée. Si vous souhaitez faire en sorte que seul un principal du service puisse appeler l’application API protégée, ajoutez du code dans l’application API protégée pour valider les revendications suivantes dans le jeton :

* `appid` doit correspondre à l’ID client de l’application Azure AD associée à l’appelant. 
* `oid` (`objectidentifier`) doit être l’ID du principal du service de l’appelant. 

App Service fournit également la revendication `objectidentifier` dans l’en-tête X-MS-CLIENT-PRINCIPAL-ID.

### Comment protéger l’application API contre tout accès navigateur

Si vous ne validez pas les déclarations contenues dans le code de l’application API protégée, et si vous utilisez une application Azure AD distinctes pour l’application API protégée, assurez-vous que l’URL de réponse de l’application Azure AD est différente de l’URL de base de l’application API. Si l’URL de réponse pointe directement vers l’application API protégée, un utilisateur du même client Azure AD peut accéder à l’application API, se connecter et appeler l’API.

## <a id="tutorialstart"></a> Suite des didacticiels dédiés à la mise en route de .NET

Si vous suivez la série dédiée à la mise en route de Node.js ou Java pour les applications API, passez à la section [Étapes suivantes](#next-steps).

Le reste de cet article poursuit la série de mise en route de .NET pour les applications API. Vous devez avoir terminé le [didacticiel sur l’authentification utilisateur](app-service-api-dotnet-user-principal-auth.md) et lancé l’exécution de l’exemple d’application dans Azure avec l’authentification utilisateur activée.

## Configurer l’authentification dans Azure

Dans cette section, vous allez configurer App Service afin que les seules requêtes HTTP autorisées à atteindre l’application API de la couche de données soient celles qui disposent de jetons de porteur Azure AD valides.

Dans la section suivante, vous allez configurer l’application API de niveau intermédiaire pour qu’elle envoie les informations d’identification de l’application à Azure AD et qu’elle reçoive en retour un jeton de porteur qu’elle transmettra à l’application API de la couche de données. Ce processus est illustré dans le schéma suivant.

![Diagramme d’authentification du service](./media/app-service-api-dotnet-service-principal-auth/appdiagram.png)

Si vous rencontrez des problèmes en suivant les instructions du didacticiel, consultez la section [Résolution de problèmes](#troubleshooting) à la fin du didacticiel.

1. Dans le [portail Azure](https://portal.azure.com/), accédez au panneau **Paramètres** de l’application API que vous avez créée pour l’application API ToDoListDataAPI (de la couche de données), puis cliquez sur **Paramètres**.

2. Dans le panneau **Paramètres**, recherchez la section **Fonctionnalités**, puis cliquez sur **Authentification/Autorisation**.

	![Authentification/Autorisation dans le portail Azure](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Dans le panneau **Authentification/Autorisation**, cliquez sur **Activé**.

4. Dans la liste déroulante **Action à effectuer lorsque la demande n’est pas authentifiée**, sélectionnez **Se connecter avec Azure Active Directory**.

	Grâce à ce paramètre, APP Service veillera à ce que seules les demandes authentifiées atteignent l’application API. Pour les demandes qui comportent des jetons de porteur valides, App Service transmet les jetons à l’application API et remplit les en-têtes HTTP avec les déclarations courantes pour rendre ces informations plus facilement disponibles pour votre code.

5. Sous **Fournisseurs d’authentification**, cliquez sur **Azure Active Directory**.

	![Panneau Authentification/Autorisation dans le portail Azure](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Dans le panneau **Paramètres Azure Active Directory**, cliquez sur **Express**.

	L’option **Express** permet à Azure de créer automatiquement une application AAD dans le [client](https://msdn.microsoft.com/fr-FR/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) Azure AD.

	Vous n’avez pas à créer un client, car chaque compte Azure en possède un.

7. Sous **Mode d’administration**, cliquez sur **Créer une application AD**, si cette option n’est pas déjà sélectionnée.

	La zone de saisie **Créer une application** du portail comporte automatiquement une valeur par défaut. Par défaut, l’application Azure AD porte le même nom que celui de l’application API. Vous pouvez, si vous le souhaitez, choisir un autre nom.
	
	![Paramètres Azure AD](./media/app-service-api-dotnet-service-principal-auth/aadsettings.png)

	**Remarque** : vous pouvez également utiliser une seule application Azure AD pour l’application API appelante et l’application API protégée. Si vous choisissez cette solution, l’option **Créer une application AD** n’est pas utile car vous avez déjà créé une application Azure AD dans le didacticiel sur l’authentification utilisateur. Pour ce didacticiel, vous allez utiliser des applications Azure AD distinctes pour l’application API appelante et l’application API protégée.

8. Notez la valeur contenue dans la zone de saisie **Créer une application** ; vous rechercherez plus tard cette application AAD dans le portail Azure Classic.

7. Cliquez sur **OK**.

10. Dans le panneau **Authentification/Autorisation**, cliquez sur **Enregistrer**.

	![Cliquez sur Enregistrer.](./media/app-service-api-dotnet-service-principal-auth/saveauth.png)

	App Service crée une application Azure Active Directory en paramétrant automatiquement l’**URL de connexion** et l’**URL de réponse** sur l’URL de votre application API. Cette valeur permet aux utilisateurs de votre client AAD de se connecter et d’accéder à l’application API.

### Vérifier la protection de l’application API

1. Dans un navigateur, accédez à l’URL de l’application API : dans le panneau **Application API** du portail Azure, cliquez sur le lien situé sous **URL**. 

	Vous êtes redirigé vers un écran de connexion, car les demandes non authentifiées ne sont pas autorisées à atteindre l’application API.

	Si votre navigateur accède à l’interface utilisateur Swagger, cela signifie qu’il est peut-être déjà connecté. Dans ce cas, ouvrez une fenêtre InPrivate ou Incognito et accédez à l’URL de l’interface utilisateur Swagger.

18. Connectez-vous avec les informations d’identification d’un utilisateur de votre client AAD.

	Une fois connecté, la page signalant que la création a réussi s’affiche dans le navigateur.

## Configurer le projet ToDoListAPI pour acquérir et envoyer le jeton Azure AD

Dans cette section, vous effectuerez les tâches suivantes :

* Ajouter du code dans l’application API de niveau intermédiaire qui utilise les informations d’identification de l’application Azure AD pour acquérir un jeton et l’envoyer en même temps que les requêtes HTTP à l’application API de la couche de données.
* Obtenir auprès d’Azure AD les informations d’identification dont vous avez besoin.
* Saisir les informations d’identification dans les paramètres de l’environnement d’exécution Azure App Service de l’application API de niveau intermédiaire. 

### Configurer le projet ToDoListAPI pour acquérir et envoyer le jeton Azure AD

Dans Visual Studio, apportez les modifications suivantes au projet ToDoListAPI.

1. Dans le fichier *ServicePrincipal.cs*, supprimez les commentaires sur l’ensemble du code.

	Ce code utilise la bibliothèque ADAL pour .NET dans le but d’acquérir le jeton du porteur Azure AD. Il utilise plusieurs valeurs de configuration que vous définirez ultérieurement dans l’environnement d’exécution Azure. Voici le code :

		public static class ServicePrincipal
		{
		    static string authority = ConfigurationManager.AppSettings["ida:Authority"];
		    static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
		    static string clientSecret = ConfigurationManager.AppSettings["ida:ClientSecret"];
		    static string resource = ConfigurationManager.AppSettings["ida:Resource"];
		
		    public static AuthenticationResult GetS2SAccessTokenForProdMSA()
		    {
		        return GetS2SAccessToken(authority, resource, clientId, clientSecret);
		    }
		
		    static AuthenticationResult GetS2SAccessToken(string authority, string resource, string clientId, string clientSecret)
		    {
		        var clientCredential = new ClientCredential(clientId, clientSecret);
		        AuthenticationContext context = new AuthenticationContext(authority, false);
		        AuthenticationResult authenticationResult = context.AcquireToken(
		            resource,
		            clientCredential);
		        return authenticationResult;
		    }
		}

	**Remarque :** ce code nécessite la bibliothèque ADAL du package NuGet .NET (Microsoft.IdentityModel.Clients.ActiveDirectory), lequel est préinstallé dans le projet. Si vous avez créé entièrement ce projet, vous devrez installer ce package. Ce package n’est pas installé automatiquement par le modèle de nouveau projet d’application API.

2. Dans *Contrôleurs/ToDoListController*, supprimez les commentaires du code dans la méthode `NewDataAPIClient` qui ajoute un jeton aux requêtes HTTP dans l’en-tête d’autorisation.

		client.HttpClient.DefaultRequestHeaders.Authorization =
		    new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);

3. Déployez le projet ToDoListAPI (cliquez avec le bouton droit sur le projet, puis cliquez sur **Publier > Publier**).

	Visual Studio déploie le projet et ouvre un navigateur vers l’URL de base d’application Web. Cette commande affiche la page d’erreur 403, ce qui est normal pour une tentative d’accès à une URL de base d’API Web depuis un navigateur.

4. Fermez le navigateur.

### Obtenir les valeurs de configuration Azure AD

11. Dans le [portail Azure Classic](https://manage.windowsazure.com/), accédez à **Azure Active Directory**.

12. Sous l’onglet **Annuaire**, cliquez sur votre client AAD.

14. Cliquez sur **Applications > Applications que ma société possède**, puis cliquez sur la coche.

15. Dans la liste des applications, cliquez sur le nom de l’application qu’Azure a créée pour vous lorsque vous avez activé l’authentification pour l’application API ToDoListDataAPI (couche de données).

16. Cliquez sur l’onglet **Configurer**.

5. Copiez la valeur **ID client** et enregistrez-la à un emplacement où vous pourrez la récupérer par la suite.

8. Dans le portail Azure Classic, revenez à la liste **Applications que ma société possède**, puis cliquez sur l’application AAD que vous avez créée pour l’application API ToDoListAPI (celle que vous avez créée dans le précédent didacticiel, pas celle que vous avez créée dans ce didacticiel).

16. Cliquez sur l’onglet **Configurer**.

5. Copiez la valeur **ID client** et enregistrez-la à un emplacement où vous pourrez la récupérer par la suite.

6. Sous **Clés**, sélectionnez **1 an** dans la liste déroulante **Sélectionner une durée**.

6. Cliquez sur **Enregistrer**.

	![Générer la clé d’application](./media/app-service-api-dotnet-service-principal-auth/genkey.png)

7. Copiez la valeur de clé et enregistrez-la à un emplacement où vous pourrez la récupérer par la suite.

	![Copiez la nouvelle clé d’application](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

### Configurer les paramètres d’Azure AD dans l’environnement d’exécution de l’application API de niveau intermédiaire

1. Dans le [portail Azure](https://portal.azure.com/), accédez au panneau **Application API** de l’application API qui héberge le projet TodoListAPI (de niveau intermédiaire).

2. Cliquez sur **Paramètres > Paramètres de l'application**.

3. Dans la section **Paramètres de l’application**, ajoutez les clés et les valeurs suivantes :

	| **Clé** | ida:Authority |
	|---|---|
	| **Valeur** | https://login.microsoftonline.com/{your (nom du client Azure AD} |
	| **Exemple** | https://login.microsoftonline.com/contoso.onmicrosoft.com |

	| **Clé** | ida:ClientId |
	|---|---|
	| **Valeur** | ID client de l’application appelante (niveau intermédiaire : ToDoListAPI) |
	| **Exemple** | 960adec2-b74a-484a-960adec2-b74a-484a |

	| **Clé** | ida:ClientSecret |
	|---|---|
	| **Valeur** | Clé d’application de l’application appelante (niveau intermédiaire : ToDoListAPI) |
	| **Exemple** | e65e8fc9-5f6b-48e8-e65e8fc9-5f6b-48e8 |

	| **Clé** | ida:Resource |
	|---|---|
	| **Valeur** | ID client de l’application appelante (couche de données : ToDoListAPI) |
	| **Exemple** | e65e8fc9-5f6b-48e8-e65e8fc9-5f6b-48e8 |

	**Remarque**: pour `ida:Resource`, veillez à utiliser l’**ID client** de l’application appelée et non son **URI d’ID d’application**.

	`ida:ClientId` et `ida:Resource` représentent différentes valeurs pour ce didacticiel, car vous utilisez des applications Azure AD distinctes pour le niveau intermédiaire et la couche de données. Si vous utilisez une seule application Azure AD pour l’application API appelante et l’application API protégée, vous devez utiliser la même valeur pour `ida:ClientId` et pour `ida:Resource`.

	Le code utilise ConfigurationManager pour obtenir ces valeurs, afin de pouvoir les stocker dans le fichier Web.config du projet ou dans l’environnement d’exécution Azure. Pendant l’exécution d’une application ASP.NET dans Azure App Service, les paramètres d’environnement remplacent automatiquement les paramètres du fichier Web.config. Les paramètres d’environnement offrent généralement un [moyen plus sécurisé de stocker des informations sensibles qu’un fichier Web.config](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

6. Cliquez sur **Enregistrer**.

	![Cliquez sur Enregistrer.](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

### Test de l'application

1. Dans un navigateur, accédez à l’URL HTTPS de l’application web frontale AngularJS.

2. Cliquez sur l’onglet **To Do List** et connectez-vous avec les informations d’identification d’un utilisateur de votre client Azure AD.

4. Ajoutez des éléments de tâche pour vérifier que l’application fonctionne.

	![Page To do List](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)

	Si l’application ne fonctionne pas comme prévu, vérifiez à nouveau tous les paramètres que vous avez saisis dans le portail Azure. Si tous les paramètres semblent corrects, consultez la section [Résolution de problèmes](#troubleshooting) dans la suite de ce didacticiel.

## Protéger l’application API contre tout accès navigateur

Dans ce didacticiel, vous avez créé une application Azure AD distincte pour l’application API ToDoListDataAPI (couche de données). Comme vous avez pu le voir, lorsqu’App Service crée une application AAD, il la configure de telle sorte que l’utilisateur puisse accéder à l’URL de l’application API dans un navigateur et se connecter. Autrement dit, l’API est accessible non seulement au principal du service, mais également à n’importe quel utilisateur final de votre client Azure AD.

Si vous souhaitez interdire l’accès via un navigateur sans avoir à écrire du code dans l’application API protégée, vous pouvez modifier l’**URL de réponse** dans l’application AAD afin qu’elle soit différente de l’URL de base de l’application API.

### Désactiver l’accès du navigateur

1. Dans l’onglet **Configurer** du portail Classic correspondant à l’application AAD créée pour le projet ToDoListService, modifiez la valeur du champ **URL de réponse** pour qu’elle corresponde à une URL valide, sans utiliser l’URL de l’application API.
 
2. Cliquez sur **Enregistrer**.

### Vérifier que l’accès au navigateur ne fonctionne plus

Vous avez déjà vérifié qu’il était possible de revenir à l’URL de l’application API à partir d’un navigateur en vous connectant avec les informations d’identification d’un utilisateur donné. Dans cette section, vous allez vérifier que cet accès ne fonctionne plus.

1. Dans une nouvelle fenêtre de navigateur, accédez de nouveau à l’URL de l’application API.

2. Connectez-vous lorsque vous êtes invité à le faire.

3. La connexion aboutit, mais génère une page d’erreur.

	Vous avez configuré l’application AAD afin que les utilisateurs du client AAD ne puissent ni se connecter ni accéder à l’API à partir d’un navigateur. Vous pouvez toujours accéder à l’application API à l’aide d’un jeton du principal de service ; pour le vérifier, il vous suffit d’accéder à l’URL de l’application web et d’ajouter d’autres éléments de tâche.

## Restreindre l’accès à un principal de service spécifique  

À ce stade, tout appelant capable d’obtenir un jeton pour un utilisateur ou principal du service dans votre client Azure AD est en mesure d’appeler l’application API TodoListDataAPI (couche de données). Si vous le souhaitez, vous pouvez faire en sorte que l’application API de la couche de données accepte uniquement les appels en provenance de l’application API TodoListAPI (couche intermédiaire) et uniquement à partir d’un principal de service particulier.

Vous pouvez ajouter ces restrictions en ajoutant du code pour valider les revendications `appid` et `objectidentifier` sur les appels entrants.

Dans ce didacticiel, vous allez ajouter un code qui valide l’ID de l’application et l’ID du principal de service directement dans les actions du contrôleur. Vous pouvez également utiliser un attribut `Authorize` personnalisé ou effectuer cette validation dans vos séquences de démarrage (par exemple, le middleware OWIN). Pour obtenir un exemple de cette dernière option, consultez [cet exemple d’application](https://github.com/mohitsriv/EasyAuthMultiTierSample/blob/master/MyDashDataAPI/Startup.cs).

Apportez les modifications suivantes au projet ToDoListDataAPI.

2. Ouvrez le fichier *Controllers\\ToDoListController.cs*.

3. Supprimez les commentaires des lignes qui définissent `trustedCallerClientId` et `trustedCallerServicePrincipalId`.

		private static string trustedCallerClientId = ConfigurationManager.AppSettings["todo:TrustedCallerClientId"];
		private static string trustedCallerServicePrincipalId = ConfigurationManager.AppSettings["todo:TrustedCallerServicePrincipalId"];

4. Supprimez les commentaires du code dans la méthode CheckCallerId. Cette méthode est appelée au début de chaque méthode d’action du contrôleur.

		private static void CheckCallerId()
		{
		    string currentCallerClientId = ClaimsPrincipal.Current.FindFirst("appid").Value;
		    string currentCallerServicePrincipalId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		    if (currentCallerClientId != trustedCallerClientId || currentCallerServicePrincipalId != trustedCallerServicePrincipalId)
		    {
		        throw new HttpResponseException(new HttpResponseMessage { StatusCode = HttpStatusCode.Unauthorized, ReasonPhrase = "The appID or service principal ID is not the expected value." });
		    }
		}

5. Redéployez le projet ToDoListDataAPI dans Azure App Service.

6. Dans votre navigateur, accédez à l’URL HTTPS de l’application web frontale AngularJS, puis, sur la page d’accueil, cliquez sur l’onglet **To Do List**.

	L’application ne fonctionne pas en raison de l’échec des appels vers le serveur principal. Le nouveau code vérifie les attributs appid et objectidentifier, mais il ne dispose pas encore des valeurs correctes qui lui permettent de les vérifier. La Console des outils de développement du navigateur signale que le serveur renvoie une erreur HTTP 401.

	![Erreur dans la console des outils de développement](./media/app-service-api-dotnet-service-principal-auth/webapperror.png)

	Dans la procédure suivante, vous allez configurer les valeurs attendues.

8. À l’aide d’Azure AD PowerShell, obtenez la valeur du principal du service pour l’application Azure AD que vous avez créée pour le projet TodoListWebApp.

	a. Pour savoir comment installer Azure PowerShell et le connecter à votre abonnement, consultez l’article [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).

	b. Pour obtenir une liste des principaux du service, exécutez la commande `Login-AzureRmAccount`, puis la commande `Get-AzureRmADServicePrincipal`.

	c. Recherchez l’attribut objectid du principal du service de l’application TodoListAPI, et enregistrez-le dans un emplacement d’où vous pourrez le copier ultérieurement.

7. Dans le portail Azure, accédez au panneau Application web de l’application web où vous avez déployé le projet ToDoListAngular.

9. Cliquez sur **Paramètres > Paramètres de l’application**.

3. Dans la section **Paramètres de l’application**, ajoutez les clés et les valeurs suivantes :

	| **Clé** | todo:TrustedCallerServicePrincipalId |
	|---|---|
	| **Valeur** | ID du principal de service principal de l’application appelante |
	| **Exemple** | 4f4a94a4-6f0d-4072-4f4a94a4-6f0d-4072 |

	| **Clé** | todo:TrustedCallerClientId |
	|---|---|
	| **Valeur** | ID client de l’application appelante (copié à partir de l’application AD Azure TodoListAPI) |
	| **Exemple** | 960adec2-b74a-484a-960adec2-b74a-484a |

6. Cliquez sur **Enregistrer**.

	![Cliquez sur Enregistrer.](./media/app-service-api-dotnet-service-principal-auth/trustedcaller.png)

6. Dans votre navigateur, revenez à l’URL de l’application web, puis, sur la page d’accueil, cliquez sur l’onglet **To Do List**.

	Cette fois, l’application fonctionne comme prévu car l’ID de l’application appelante et l’ID du principal de service correspondent aux valeurs attendues.

	![Page To do List](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)

## Génération de projets de toutes pièces

Les deux projets d’API Web ont été créés à l’aide du modèle de projet **Azure API App** et par remplacement du contrôleur Values par défaut par un contrôleur ToDoList. Pour acquérir les jetons du principal du service Azure AD dans le projet ToDoListAPI, le package NuGet [Active Directory Authentication Library (ADAL) pour .NET](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) a été installé.
 
Pour plus d’informations sur la création d’une application à page unique AngularJS avec un back-end d’API Web telle que ToDoListAngular, consultez la page [Hands On Lab: Build a Single Page Application (SPA) with ASP.NET Web API and Angular.js](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs) (Exercice pratique : créer une application à page unique (SPA) avec l’API Web ASP.NET et Angular.js). Pour plus d’informations sur l’ajout de code d’authentification Azure AD, consultez [Sécurisation d’une application à page unique AngularJS avec Azure AD](../active-directory/active-directory-devquickstarts-angular.md).

## Résolution des problèmes

[AZURE.INCLUDE [résolution des problèmes](../../includes/app-service-api-auth-troubleshooting.md)]

* Assurez-vous de ne pas confondre ToDoListAPI (niveau intermédiaire) et ToDoListDataAPI (couche de données). Par exemple, dans ce didacticiel vous allez ajouter l’authentification à l’application API de la couche de données, **mais la clé d’application doit provenir de l’application Azure AD que vous avez créée pour l’application de niveau intermédiaire API**.

## Étapes suivantes

Il s’agit ici du dernier article de la série de didacticiels dédiés à la prise en main d’API Apps.

Pour plus d’informations sur Azure Active Directory, consultez les ressources suivantes :

* [Guide du développeur Azure AD](http://aka.ms/aaddev)
* [Scénarios Azure AD](http://aka.ms/aadscenarios)
* [Exemples Azure AD](http://aka.ms/aadsamples)

	L’exemple [WebApp-WebAPI-OAuth2-AppIdentity-DotNet](http://github.com/AzureADSamples/WebApp-WebAPI-OAuth2-AppIdentity-DotNet) est similaire à celui présenté dans ce didacticiel, à ceci près qu’il n’utilise pas l’authentification App Service.

Pour plus d’informations sur les autres méthodes de déploiement de projets Visual Studio dans des applications API, à l’aide de Visual Studio ou en [automatisant le déploiement](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) à partir d’un [système de contrôle de code source](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), consultez [Déploiement de votre application dans Azure App Service](../app-service-web/web-sites-deploy.md).

<!---HONumber=AcomDC_0309_2016-->