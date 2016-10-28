<properties
	pageTitle="Prise en main d’Azure Active Directory Identity Protection et de Microsoft Graph | Microsoft Azure"
	description="Fournit une introduction à l’interrogation de Microsoft Graph pour obtenir une liste d’événements à risque et des informations associées à partir d’Azure Active Directory."
	services="active-directory"
	keywords="azure active directory identity protection, événement à risque, vulnérabilité, stratégie de sécurité, Microsoft Graph"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="markvi"/>

# Prise en main d’Azure Active Directory Identity Protection et de Microsoft Graph

Microsoft Graph est le point de terminaison d’API unifiée de Microsoft et accueille également les API [d’Azure Active Directory Identity Protection](active-directory-identityprotection.md). Notre première API, **identityRiskEvents**, vous permet d’interroger Microsoft Graph pour obtenir une liste [d’événements à risque](active-directory-identityprotection-risk-events-types.md) et des informations associées. Cet article vous permet de vous familiariser avec l’interrogation de cette API. Pour des informations détaillées ainsi qu’un accès à l’explorateur de Graph, consultez le [site de Microsoft Graph](https://graph.microsoft.io/).

L’accès aux données d’Identity Protection par le biais de Microsoft Graph se fait en trois étapes :

1. Ajouter une application avec une clé secrète client.

2. Utilisez cette clé secrète et d’autres éléments d’information pour vous authentifier auprès de Microsoft Graph ; ce dernier vous transmettra un jeton d’authentification.

3. Utilisez ce jeton pour faire des demandes auprès du point de terminaison d’API et récupérer des données d’Identity Protection.

Avant de commencer, vous aurez besoin des éléments suivants :

- Des privilèges d’administrateur pour créer l’application dans Azure AD
- Le nom de domaine de votre client, par exemple contoso.onmicrosoft.com.



## Ajouter une application avec une clé secrète client


1. [Connectez-vous](https://manage.windowsazure.com) en tant qu’administrateur sur le portail Azure Classic.

1. Dans le volet de navigation gauche, cliquez sur **Active Directory**.

	![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_01.png)

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Dans le menu supérieur, cliquez sur **Applications**.

	![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_02.png)

4. Cliquez sur **Ajouter** en bas de la page.

	![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_03.png)

5. Sur la boîte de dialogue **Que voulez-vous faire**, cliquez sur **Ajouter une application développée par mon organisation**.

	![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_04.png)


5. Dans la boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :

	![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_05.png)

	a. Dans la zone de texte **Nom**, tapez un nom pour votre application (par exemple : Application d’API d’événement à risque AADIP).

	b. Pour **Type**, sélectionnez **Application web et/ou API web**.

	c. Cliquez sur **Next**.


5. Dans la boîte de dialogue **Propriétés de l’application**, effectuez les opérations suivantes :

	![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_06.png)

	a. Dans la zone de texte **URL d’authentification**, tapez `http://localhost`.

	b. Dans la zone de texte **URL ID d’application**, tapez `http://localhost`.

	c. Cliquez sur **Terminé**.


Vous pouvez à présent configurer votre application.

![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_07.png)



## Autorisation d'utilisation de l'API pour votre application


1. Dans la page de votre application, dans le menu du haut, cliquez sur **Configurer**.

	![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_08.png)

2. Dans la section **Autorisations pour d’autres applications**, cliquez sur **Ajouter une application**.

	![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_09.png)


2. Dans la boîte de dialogue **Autorisations pour d’autres applications**, effectuez les opérations suivantes :

	![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_10.png)

	a. Sélectionnez **Microsoft Graph**.

	b. Cliquez sur **Terminé**.


1. Cliquez sur **Autorisations d’application : 0**, puis sélectionnez **Read all identity risk event information** (Lire toutes les informations sur les événements à risque concernant l’identité).

	![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_11.png)

1. Cliquez sur **Enregistrer** au bas de la page.

	![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)


## Obtenir une clé d’accès

1. Sur la page de votre application, dans la section **Clés**, définissez la durée sur 1 an.

	![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_13.png)

1. Cliquez sur **Enregistrer** au bas de la page.

	![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)

1. Dans la section Clés, copiez la valeur de la clé qui vient d’être créée, puis collez-la dans un emplacement sûr.

	![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_14.png)

	> [AZURE.NOTE] Si vous perdez cette clé, vous devrez revenir à cette section et créer une nouvelle clé. Gardez cette clé secrète : toute personne la possédant peut accéder à vos données.


1. Dans la section **Propriétés**, copiez **l’ID Client**, puis collez-le dans un emplacement sûr.


## Authentifiez-vous auprès de Microsoft Graph et interrogez l’API d’événements à risque concernant l’identité

À ce stade, vous devez avoir :

- l’ID client copié précédemment ;

- la clé copiée précédemment ;

- le nom de domaine de votre client ;


Pour l’authentification, envoyez une demande POST à `https://login.microsoft.com`, avec les paramètres suivants dans le corps :

- grant\_type : “**client\_informationsidentification**”

- resource : “**https://graph.microsoft.com**”

- client\_id : <votre ID client>

- client\_secret : <votre clé>


> [AZURE.NOTE] Vous devez fournir des valeurs pour les paramètres **client\_id** et **client\_secret**.

Si l’opération réussit, elle retourne un jeton d’authentification. Pour appeler l’API, créez un en-tête avec le paramètre suivant :

	`Authorization`=”<token_type> <access_token>"


Lors de l’authentification, le jeton retourné contient le type de jeton ainsi que le jeton d’accès.

Envoyez cet en-tête en tant que requête à l’URL de l’API suivante : `https://graph.microsoft.com/beta/identityRiskEvents`

La réponse, en cas de réussite, consiste en une collection d’événements à risque concernant l’identité ainsi que les données associées au format JSON OData, qui peuvent être analysés et gérés selon vos besoins.

Voici un exemple de code pour l’authentification et l’appel de l’API par le biais de Powershell. Il suffit d’ajouter l’ID client, la clé ainsi que le domaine client.

	$ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
	$ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
	$tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

	$loginURL       = "https://login.microsoft.com"
	$resource       = "https://graph.microsoft.com"

	$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
	$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

	Write-Output $oauth

	if ($oauth.access_token -ne $null) {
    	$headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    	$url = "https://graph.microsoft.com/beta/identityRiskEvents"
    	Write-Output $url

    	$myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

    	foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
        	Write-Output $event
    	}

	} else {
    	Write-Host "ERROR: No Access Token"
	} 


## Étapes suivantes

Félicitations, vous venez de créer votre premier appel à Microsoft Graph ! Vous pouvez à présent interroger les événements à risque concernant l’identité et utiliser les données comme bon vous semble.

Pour en savoir plus sur Microsoft Graph et comment créer des applications à l’aide de l’API Graph, consultez la [documentation](https://graph.microsoft.io/docs) afférente et bien plus sur le [site de Microsoft Graph](https://graph.microsoft.io/). Assurez-vous également de marquer la page [Azure AD Identity Protection API](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root) (API Azure AD Identity Protection) à l’aide d’un signet ; cette dernière répertorie toutes les API d’Identity Protection disponibles dans Graph. Sur cette page, vous pourrez consulter l’ensemble des nouvelles façons de travailler avec Identity Protection via API au fur et à mesure que nous les ajoutons.


## Ressources supplémentaires

- [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

- [Types d’événements à risque détectés par Azure Active Directory Identity Protection](active-directory-identityprotection-risk-events-types.md)

- [Microsoft Graph](https://graph.microsoft.io/)

- [Overview of Microsoft Graph (Vue d’ensemble de Microsoft Graph)](https://graph.microsoft.io/docs)

- [Azure AD Identity Protection Service Root (Racine de service d’Azure AD Identity Protection)](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)

<!---HONumber=AcomDC_0824_2016-->