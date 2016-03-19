<properties
	pageTitle="Configurer une API pour se connecter à un système principal sur un domaine Azure Active Directory dans PowerApps | Microsoft Azure"
	description="Configurer une API pour se connecter à un système principal AAD protégé dans PowerApps"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="guayan"/>

# Configurer une API pour se connecter à une ressource de serveur principal sur un domaine Azure Active Directory
Comme plusieurs utilisateurs créent des domaines sur Azure Active Directory (AAD), les ressources principales sont également ajoutées à ces domaines AAD. Vous pouvez créer et configurer des API pour vous connecter à ces ressources principales.

#### Conditions préalables

- Inscrivez-vous à [PowerApps Enterprise](powerapps-get-started-azure-portal.md).
- Créez un [environnement App Service](powerapps-get-started-azure-portal.md).
- Installez [Azure PowerShell][11] 1.0 Preview ou version ultérieure.
- Inscrivez une API dans votre [environnement App Service](powerapps-register-api-hosted-in-app-service.md).

## Étape 1 : créez une application Active Directory et attribuez-lui les autorisations

Pour accéder au système principal sur un domaine AAD, créez une application AAD et attribuez-lui les autorisations appropriées pour votre serveur principal existant (qui est également une application AAD). Étapes :

1. Dans le [portail Azure Classic][13], accédez à votre Azure Active Directory, ouvrez votre locataire et cliquez sur l’onglet **APPLICATIONS** : ![][14]
2. Sélectionnez le bouton **AJOUTER** du bas. Puis :  

	(a) Choisissez **AJOUTER UNE APPLICATION DÉVELOPPÉE PAR MON ORGANISATION**. (b) Entrez le nom de votre application, puis sélectionnez **APPLICATION WEB ET/OU API WEB**. c) Dans **URL DE CONNEXION** et **URI ID D'APPLICATION**, entrez une URL unique au sein de votre AAD et les URL qui correspondent à votre organisation. Par exemple, vous pouvez entrer http://powerappssignon.contoso.com ou http://powerappsappid.contoso.com. Nous recommandons d'utiliser une URL du domaine AAD de votre organisation. Les URL servent d’identificateurs et il n'est pas nécessaire qu’elles existent. Personne ne parcourra les URL que vous entrez. Vous pouvez entrer HTTP ou HTTPS.

3. Dans la page d'application AAD créée, accédez à l’onglet **CONFIGURER** : ![][15]
4. Dans la section **CLÉS**, utilisez la liste déroulante pour sélectionner une durée. Notez que la clé s’affiche lorsque vous sélectionnez **ENREGISTRER** : ![][16]
5. Dans **AUTHENTIFICATION UNIQUE**, ajoutez ``https://<your App Service Environment name>.azure-apim.net:456/redirect`` comme **URL DE RÉPONSE**.
6. Dans **AUTORISATIONS POUR D'AUTRES APPLICATIONS** :  

	a) Sélectionnez **AJOUTER UNE APPLICATION**. Dans la fenêtre contextuelle, sélectionnez l'application AAD qui sécurise votre serveur principal : ![][17]

	(b) Utilisez la liste déroulante pour ajouter les autorisations : ![][18]

7. Sélectionnez **ENREGISTRER** en bas.
8. Copiez l’**ID CLIENT** et la **CLÉ**, puis conservez ces informations. La clé ne réapparaît pas lorsque vous fermez le portail Azure. 

Pour plus d’informations sur les applications AAD, consultez [Intégration d’applications dans Azure Active Directory](../active-directory-integrating-applications.md).

## Étape 2 : Configurer votre API à l'aide d'Azure PowerShell

À ce stade, il n'existe aucune prise en charge du portail Azure permettant d’initialiser la configuration nécessaire pour votre API. Pour configurer l'API dans le portail Azure, utilisez le script Auzre PowerShell suivant :

> [AZURE.TIP]Pour savoir comment installer, configurer et exécuter Azure PowerShell, consultez [Installation et configuration d’Azure PowerShell][11]. Le script suivant fonctionne avec Azure PowerShell 1.0 version préliminaire ou ultérieure.

```powershell
# get the API resource
$api = Get-AzureRmResource -ResourceType Microsoft.Web/apiManagementAccounts/apis -ResourceName <App Service Environment name>/<API name> -ResourceGroupName <resource group name>

# configure the API resource for AAD authentication
$connectionParameters = @{
    token = @{
        type = "oauthSetting";
        oAuthSettings = @{
            identityProvider = "aad";
            clientId = "<your AAD app client id>";
            clientSecret = "<your AAD app key>";
            customParameters = @{
                TenantId = @{ # this property is optional
                    value = "<your AAD tenant ID>"
                };
                ResourceUri = @{ # this property is required
                    value = "<the app ID URI of the AAD app protecting your backend>"
                }
            }
        }
    }
}
Add-Member -InputObject $api.Properties -MemberType NoteProperty -Name ConnectionParameters -Value $connectionParameters -Force

# update the API resource
New-AzureRmResource -Location $api.Location -ResourceId $api.ResourceId -Properties $api.Properties
```

**Notez** que le nom du paramètre de connexion **TOKEN** est important. Vous pouvez choisir votre propre nom à condition d’utiliser une casse mixte. Vous utiliserez ce nom ultérieurement dans votre code principal ou dans la stratégie de l'API.

Accédez ensuite au [portail][19], puis au panneau des paramètres **GÉNÉRAL** de votre API. Vous devriez voir les options de configuration supplémentaires : ![][21]


## Faites un essai

Ouvrez une application dans PowerApps. Votre nouvelle API apparaît dans **CONNEXIONS DISPONIBLES**. Lorsque vous sélectionnez **CONNECTER**, une fenêtre de connexion AAD apparaît. Entrez les informations du compte AAD de votre organisation pour créer votre connexion.

Désormais, lorsqu'un appel runtime est effectué de votre application vers l'API à l’aide de cette connexion, votre serveur principal reçoit le jeton de l’utilisateur AAD dans l’en-tête HTTP **x-ms-apim-tokens** avec le format [codage Base64][20] suivant :

```json
{
  "token": {
    "AccessToken": ""
    // ...
  }
}
```

**Notez** que le nom de la propriété **TOKEN** correspond au nom du paramètre de connexion utilisé lors de la configuration du paramètre.

Votre code principal peut ensuite obtenir le jeton AAD de la part de la propriété **ACCESSTOKEN** et l’utiliser si nécessaire. L'environnement App Service actualise automatiquement le jeton.

## Configurer la stratégie de l’API

Si vous le souhaitez, vous pouvez également utiliser la stratégie de l’API pour définir le jeton AAD dans l’en-tête HTTP standard **Authorization**. De cette façon, si votre code principal doit utiliser le jeton AAD, vous pouvez l'obtenir d’une manière standard au lieu d’utiliser un en-tête HTTP personnalisé et effectuer un décodage Base64. Pour cela, accédez au portail Azure, ouvrez le panneau **STRATÉGIE** de votre API, puis définissez la stratégie suivante :

```xml
<policies>
	<inbound>
		<base/>
		<choose>
			<when condition="@(context.Variables.ContainsKey(";tokens";) &amp;&amp; ((JObject)context.Variables[";tokens";])[";token";] != null &amp;&amp; !String.IsNullOrEmpty((string)((JObject)context.Variables[";tokens";])[";token";][";AccessToken";]))">
				<set-header exists-action="override" name="Authorization">
					<value>@("Bearer " + (string)((JObject)context.Variables["tokens"])[";token";]["AccessToken"])</value>
				</set-header>
			</when>
		</choose>
	</inbound>
	<backend>
		<base/>
	</backend>
	<outbound>
		<base/>
	</outbound>
</policies>
```

Cette stratégie vous permet essentiellement de référencer les valeurs dans l’en-tête **x-ms-apim-tokens** comme un élément JObject décodé à l'aide d’une variable **tokens**. Vous pouvez utiliser la stratégie **set-header** pour obtenir le jeton AAD réel et lui affecter la valeur de l’en-tête **Authorization**. Il s’agit de la même stratégie utilisée par [Gestion des API Azure](https://azure.microsoft.com/services/api-management/). Pour en savoir plus, consultez [Stratégies dans Gestion des API Azure](../api-management-howto-policies.md).

**Notez** que le nom de la propriété **TOKEN** correspond au nom du paramètre de connexion vous avez utilisé lors de la configuration du paramètre.

## Résumé et étapes suivantes

Dans cette rubrique, vous avez appris à configurer une API pour la connexion (et l'authentification) à une ressource de serveur principal sur un domaine Azure Active Directory. Pour en savoir plus sur PowerApps, consultez les rubriques et ressources suivantes :

- [Développer une API pour PowerApps](powerapps-develop-api.md)


<!--References-->
[11]: ../powershell-install-configure.md
[13]: https://manage.windowsazure.com
[14]: ./media/powerapps-configure-apis-aad/aad-applications-tab.png
[15]: ./media/powerapps-configure-apis-aad/aad-application-configure-tab.png
[16]: ./media/powerapps-configure-apis-aad/aad-application-configure-keys.png
[17]: ./media/powerapps-configure-apis-aad/aad-application-add-other-application.png
[18]: ./media/powerapps-configure-apis-aad/aad-application-add-permissions.png
[19]: https://portal.azure.com
[20]: https://tools.ietf.org/html/rfc4648
[21]: ./media/powerapps-configure-apis-aad/api-settings-aad.png

<!---HONumber=AcomDC_1203_2015-->