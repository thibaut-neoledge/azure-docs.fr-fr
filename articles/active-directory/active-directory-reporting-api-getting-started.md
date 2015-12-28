<properties
   pageTitle="Prise en main de l'API de création de rapports Azure AD"
   description="Prise en main de l'API de création de rapports Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="kenhoff"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/07/2015"
   ms.author="kenhoff"/>


# Prise en main de l'API de création de rapports Azure AD

*Cette documentation fait partie du guide [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).*

Azure Active Directory fournit plusieurs rapports d'activité, de sécurité et d'audit. Ces données peuvent être consommées par le biais du portail Azure, mais peuvent également être très utiles dans de nombreuses autres applications, telles que les outils de systèmes SIEM, d'audit et d'analyse décisionnelle.

Les API de création de rapports Azure AD fournissent un accès par programme à ces données via un ensemble d'API REST qui peuvent être appelées à partir de divers outils et langages de programmation.

Cet article vous guidera à travers le processus d'appel de l'API de création de rapports Azure AD, à l'aide de PowerShell. Vous pouvez modifier l'exemple de script PowerShell pour accéder aux données à partir des rapports disponibles au format JSON, XML ou texte, en fonction des besoins de votre scénario.

Pour utiliser cet exemple, vous aurez besoin d'[Azure Active Directory](active-directory-whatis.md)

## Création d'une application Azure AD pour accéder à l'API

L'API de création de rapports utilise [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) pour autoriser l'accès à l'API web. Pour accéder aux informations de votre répertoire, vous devez créer une application dans Active Directory et lui accorder des autorisations appropriées pour accéder aux données AAD.


### Création d'une application
- Accédez au [Portail de gestion Azure](https://manage.windowsazure.com/).
- Accédez à votre répertoire.
- Accédez aux applications.
- Dans la barre inférieure, cliquez sur « Ajouter ».
	- Cliquez sur « Ajouter une application développée par mon organisation ».
	- **Nom** : n'importe quel nom est correct. Quelque chose comme « Application API Création de rapports » est recommandé.
	- **Type** : Sélectionnez « Application web et/ou API web ».
	- Cliquez sur la flèche pour passer à la page suivante.
	- **URL d'authentification** : ```http://localhost```.
	- **URI d'ID d'application** : ```http://localhost```.
	- Cliquez sur la coche pour terminer l'ajout de l'application.

### Autorisation d'utilisation de l'API pour votre application
- Accédez à l'onglet Applications.
- Accédez à votre application nouvellement créée.
- Cliquez sur l’onglet **Configurer**.
- Dans la section « Autorisations pour d'autres applications » :
	- Dans Microsoft Azure Active Directory > Autorisations d'application, sélectionnez **Lire les données de répertoire**.
- Cliquez sur **Enregistrer** au bas de la page.


### Obtention de votre ID de répertoire, d'un ID client et d'une clé client secrète

Les étapes ci-dessous vous guideront pour l'obtention de l'ID de client de votre application et d'une clé client secrète. Vous devez également connaître le nom de votre client, il peut s'agir de votre *.onmicrosoft.com ou d'un nom de domaine personnalisé. Copiez-les dans un emplacement distinct ; vous les utiliserez pour modifier le script.

#### ID client d'application
- Accédez à l'onglet Applications.
- Accédez à votre application nouvellement créée.
- Accédez à l'onglet **Configurer**.
- L'ID client de votre application est répertorié dans le champ **ID Client**.

#### Clé d'application client secrète
- Accédez à l'onglet Applications.
- Accédez à votre application nouvellement créée.
- Accédez à l'onglet Configurer.
- Générez une nouvelle clé secrète pour votre application en sélectionnant une durée dans la section « Clés ».
- La clé s'affichera lors de l'enregistrement. Veillez à la copier et la coller dans un emplacement sûr, car il n'existe aucun moyen de la récupérer ultérieurement.


## Modification du script
Modifiez l’un des scripts ci-dessous afin qu’il fonctionne avec votre répertoire, en remplaçant $ClientID, $ClientSecret et $tenantdomain avec les valeurs correctes de « Délégation de l'accès dans Azure AD ».

### Script PowerShell

    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID	  	= "your-application-client-id-here"				# Should be a ~35 character string insert your info here
    $ClientSecret  	= "your-application-client-secret-here"			# Should be a ~44 character string insert your info here
    $loginURL		= "https://login.windows.net"
    $tenantdomain	= "your-directory-name-here.onmicrosoft.com"			# For example, contoso.onmicrosoft.com

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body		= @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth		= Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    $7daysago = "{0:s}" -f (get-date).AddDays(-7) + "Z"
    # or, AddMinutes(-5)

    Write-Output $7daysago

    if ($oauth.access_token -ne $null) {
    	$headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.windows.net/$tenantdomain/reports/auditEvents?api-version=beta&`$filter=eventTime gt $7daysago"

    	$myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
    	foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
    		Write-Output ($event | ConvertTo-Json)
    	}
        $myReport.Content | Out-File -FilePath auditEvents.json -Force
    } else {
    	Write-Host "ERROR: No Access Token"
    }

### Script Bash

    #!/bin/bash

    # Author: Ken Hoff (kenhoff@microsoft.com)
    # Date: 2015.08.20
    # NOTE: This script requires jq (https://stedolan.github.io/jq/)

    CLIENT_ID="your-application-client-id-here"         # Should be a ~35 character string insert your info here
    CLIENT_SECRET="your-application-client-secret-here" # Should be a ~44 character string insert your info here
    LOGIN_URL="https://login.windows.net"
    TENANT_DOMAIN="your-directory-name-here.onmicrosoft.com"    # For example, contoso.onmicrosoft.com

    TOKEN_INFO=$(curl -s --data-urlencode "grant_type=client_credentials" --data-urlencode "client_id=$CLIENT_ID" --data-urlencode "client_secret=$CLIENT_SECRET" "$LOGIN_URL/$TENANT_DOMAIN/oauth2/token?api-version=1.0")

    TOKEN_TYPE=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.token_type')
    ACCESS_TOKEN=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.access_token')

    # get yesterday's date

    YESTERDAY=$(date --date='1 day ago' +'%Y-%m-%d')

    URL="https://graph.windows.net/$TENANT_DOMAIN/reports/auditEvents?api-version=beta&\$filter=eventTime%20gt%20$YESTERDAY"


    REPORT=$(curl -s --header "Authorization: $TOKEN_TYPE $ACCESS_TOKEN" $URL)

    echo $REPORT | ./jq-win64.exe -r '.value' | ./jq-win64.exe -r ".[]"





## Exécution du script
Une fois que vous avez terminé la modification du script, exécutez-le, puis vérifiez que les données attendues dans le rapport AuditEvents sont retournées.

Le script renvoie des listes qui répertorient tous les rapports disponibles et renvoie le contenu du rapport AccountProvisioningEvents dans la fenêtre PowerShell au format JSON. Il crée également des fichiers avec le même résultat dans JSON, texte et XML. Vous pouvez commenter l'expérience en modifiant le script pour renvoyer des données à partir d'autres rapports, et également commenter les formats de sortie dont vous n'avez pas besoin.

## Remarques

- Il n’existe aucune limite quant au nombre d’événements retournés par l’API de création de rapports Azure AD (à l’aide de la pagination OData).
	- Pour connaître les limites de rétention de données de rapports, consultez [Stratégies de rétention des rapports](active-directory-reporting-retention.md).


## Étapes suivantes
- Curieux de savoir quels rapports de sécurité, d'audit et d'activité sont disponibles ? Découvrez [Rapports de sécurité, d'audit et d'activité d'Azure AD](active-directory-view-access-usage-reports.md)
- Consultez [Événements de rapport d'audit d'Azure AD](active-directory-reporting-audit-events.md) pour plus d'informations sur le rapport d'audit
- Consultez [Rapports et événements Azure AD (aperçu)](https://msdn.microsoft.com/library/azure/mt126081.aspx) pour plus d'informations sur le service REST d'API Graph

<!---HONumber=AcomDC_1217_2015-->