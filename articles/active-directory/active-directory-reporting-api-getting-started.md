<properties
   pageTitle="Prise en main de l’API de création de rapports Azure AD | Microsoft Azure"
   description="Prise en main de l'API de création de rapports Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/28/2016"
   ms.author="dhanyahk"/>

# Prise en main de l’API de création de rapports Azure Active Directory

*Cette documentation fait partie du guide [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).*

Azure Active Directory (AD) fournit plusieurs rapports d’activité, de sécurité et d’audit. Ces données peuvent être consommées par le biais du portail Azure Classic, mais peuvent également être très utiles dans de nombreuses autres applications, telles que les outils de systèmes SIEM, d’audit et d’analyse décisionnelle.

Les [API de création de rapports Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) fournissent un accès par programme à ces données au moyen d’un ensemble d’API REST qui peuvent être appelées à partir de divers outils et langages de programmation.

Cet article vous guidera à travers le processus d'appel de l'API de création de rapports Azure AD, à l'aide de PowerShell. Vous pouvez modifier l'exemple de script PowerShell pour accéder aux données à partir des rapports disponibles au format JSON, XML ou texte, en fonction des besoins de votre scénario.

Pour utiliser cet exemple, vous avez besoin d’un client [Azure Active Directory](active-directory-whatis.md).

## Création d'une application Azure AD pour accéder à l'API

L'API de création de rapports utilise [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) pour autoriser l'accès à l'API web. Pour accéder aux informations de votre annuaire, vous devez créer une application dans votre client Azure AD et lui accorder des autorisations appropriées pour accéder aux données Azure AD.


### Inscrire une application Azure AD
Pour procéder à l’inscription de l’application Azure AD, vous devez vous connecter au portail Azure Classic avec un compte d’administrateur d’abonnements Azure, également membre du rôle de répertoire Administrateur général dans votre client Azure AD. Cela est nécessaire, car vous allez inscrire l’application Azure AD avec des autorisations qui nécessitent l’inscription/le consentement à l’aide d’un compte disposant de privilèges d’administrateur général.

> [AZURE.IMPORTANT] Les applications qui s’exécutent sous les informations d’identification pourvues de privilèges d’administrateur de ce type peuvent être très puissantes. Veillez donc à sécuriser les informations d’identification d’ID/du secret de l’application.


1. Accédez au [portail Azure Classic](https://manage.windowsazure.com/).
2. Accédez à votre client Azure AD, dans l’extension **Active Directory** le long du volet gauche.
3. Accédez à l’onglet **Applications**.
4. Dans la barre inférieure, cliquez sur **Ajouter**.
	- Cliquez sur « Ajouter une application développée par mon organisation ».
	- **Nom** : n'importe quel nom est correct. Quelque chose comme « Application API Création de rapports » est recommandé.
	- **Type** : Sélectionnez « Application web et/ou API web ».
	- Cliquez sur la flèche pour passer à la page suivante.
	- **URL d'authentification** : ```https://localhost```.
	- **URI d'ID d'application** : ```https://localhost/ReportingApiApp```.
	- Cliquez sur la coche pour terminer l’ajout de l’application.

### Autorisation d'utilisation de l'API pour votre application
1. Accédez à l’onglet **Applications**.
2. Accédez à votre application nouvellement créée.
3. Cliquez sur l’onglet **Configurer**.
4. Dans la section « Autorisations pour d'autres applications » :
	- Pour la ressource Microsoft Azure Active Directory (autorisations sur l’API Microsoft Azure AD Graph), cliquez sur la liste déroulante Autorisations d’application, puis sélectionnez **Lire les données du répertoire**.

        > [AZURE.IMPORTANT] Veillez à spécifier les autorisations appropriées. Appliquez Autorisations d’application et pas Autorisations déléguées, faute de quoi l’application n’obtiendra pas le niveau d’autorisation nécessaire pour accéder à l’API de création de rapports, et votre script recevra le message d’erreur *Unable to check Directory Read access for appId (Impossible de vérifier l’accès en lecture au répertoire pour addID)*.


5. Cliquez sur **Enregistrer** au bas de la page.

### Obtention de votre ID de répertoire, d'un ID client et d'une clé client secrète

Les étapes ci-dessous vous guideront pour l'obtention de l'ID de client de votre application et d'une clé client secrète. Vous devez également connaître le nom de votre client, il peut s'agir de votre *.onmicrosoft.com ou d'un nom de domaine personnalisé. Copiez ces valeurs dans un emplacement distinct ; vous les utiliserez pour modifier le script ultérieurement.

#### Obtenir le nom de domaine de votre client Azure AD
1. Accédez à votre client Azure AD, dans l’extension **Active Directory** le long du volet gauche.
2. Accédez à l’onglet **Domaines**.
4. Le nom de domaine <nom-client>.onmicrosoft.com de votre client s’affiche ainsi que les éventuels noms de domaine personnalisés que vous avez configurés.

#### Obtenir l’ID client de l’application
1. Accédez à l’onglet **Applications**.
2. Accédez à votre application nouvellement créée.
3. Accédez à l'onglet **Configurer**.
4. L'ID client de votre application est répertorié dans le champ **ID Client**.

#### Obtenir la clé secrète client de l’application
1. Accédez à l’onglet **Applications**.
2. Accédez à votre application nouvellement créée.
3. Accédez à l'onglet **Configurer**.
4. Générez une nouvelle clé secrète pour votre application en sélectionnant une durée dans la section « Clés ».
5. La clé s'affichera lors de l'enregistrement. Veillez à la copier et la coller dans un emplacement sûr, car il n'existe aucun moyen de la récupérer ultérieurement.

## Modification du script
Modifiez l’un des scripts ci-dessous afin qu’il fonctionne avec votre répertoire, en remplaçant $ClientID, $ClientSecret et $tenantdomain par les valeurs correctes indiquées dans les sections ci-dessus.

### Script PowerShell
    # This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

    # Constants
    $ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
    $ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
    $loginURL       = "https://login.microsoftonline.com"     # AAD Instance; for example https://login.microsoftonline.com
    $tenantdomain   = "your-tenant-domain.onmicrosoft.com"    # AAD Tenant; for example, contoso.onmicrosoft.com
    $resource       = "https://graph.windows.net"             # Azure AD Graph API resource URI
    $7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
    Write-Output "Searching for events starting $7daysago"

    # Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    # Parse auditEvents report items, save output to file(s): auditEventsX.json, where X = 0 thru n for number of nextLink pages
    if ($oauth.access_token -ne $null) {   
        $i=0
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
        $url = 'https://graph.windows.net/$tenantdomain/reports/auditEvents?api-version=beta&$filter=eventTime gt ' + $7daysago

        # loop through each query page (1 through n)
        Do{
            # display each event on the console window
            Write-Output "Fetching data using Uri: $url"
            $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
            foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
                Write-Output ($event | ConvertTo-Json)
            }
        
            # save the query page to an output file
            Write-Output "Save the output to a file auditEvents$i.json"
            $myReport.Content | Out-File -FilePath auditEvents$i.json -Force
            $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
            $i = $i+1
        } while($url -ne $null)
    } else {
        Write-Host "ERROR: No Access Token"
        }

    Write-Host "Press any key to continue ..."
    $x = $host.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown")

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

    URL="https://graph.windows.net/$TENANT_DOMAIN/reports/auditEvents?api-version=beta&$filter=eventTime%20gt%20$YESTERDAY"


    REPORT=$(curl -s --header "Authorization: $TOKEN_TYPE $ACCESS_TOKEN" $URL)

    echo $REPORT | ./jq-win64.exe -r '.value' | ./jq-win64.exe -r ".[]"

### Python
	# Author: Michael McLaughlin (michmcla@microsoft.com)
	# Date: January 20, 2016
	# This requires the Python Requests module: http://docs.python-requests.org

	import requests
	import datetime
	import sys

	client_id = 'your-application-client-id-here'
	client_secret = 'your-application-client-secret-here'
	login_url = 'https://login.windows.net/'
	tenant_domain = 'your-directory-name-here.onmicrosoft.com'

	# Get an OAuth access token
	bodyvals = {'client_id': client_id,
	            'client_secret': client_secret,
	            'grant_type': 'client_credentials'}

	request_url = login_url + tenant_domain + '/oauth2/token?api-version=1.0'
	token_response = requests.post(request_url, data=bodyvals)

	access_token = token_response.json().get('access_token')
	token_type = token_response.json().get('token_type')

	if access_token is None or token_type is None:
	    print "ERROR: Couldn't get access token"
	    sys.exit(1)

	# Use the access token to make the API request
	yesterday = datetime.date.strftime(datetime.date.today() - datetime.timedelta(days=1), '%Y-%m-%d')

	header_params = {'Authorization': token_type + ' ' + access_token}
	request_string = 'https://graph.windows.net/' + tenant_domain + '/reports/auditEvents?api-version=beta&$filter=eventTime%20gt%20' + yesterday   
	response = requests.get(request_string, headers = header_params)

	if response.status_code is 200:
	    print response.content
	else:
	    print 'ERROR: API request failed'


## Exécution du script
Une fois que vous avez terminé la modification du script, exécutez-le, puis vérifiez que les données attendues dans le rapport AuditEvents sont retournées.

Le script renvoie la sortie du rapport auditEvents au format JSON. Il crée également un fichier `auditEvents.json` avec la même sortie. Vous pouvez expérimenter en modifiant le script pour renvoyer des données à partir d’autres rapports, et également commenter les formats de sortie dont vous n’avez pas besoin.

## Remarques

- Il n’existe aucune limite quant au nombre d’événements retournés par l’API de création de rapports Azure AD (à l’aide de la pagination OData).
- Pour connaître les limites de rétention de données de rapports, consultez [Stratégies de rétention des rapports](active-directory-reporting-retention.md).


## Étapes suivantes
- Vous êtes curieux de savoir quels rapports de sécurité, d’audit et d’activité sont disponibles ? Découvrez [Rapports de sécurité, d’audit et d’activité d’Azure AD](active-directory-view-access-usage-reports.md). Vous pouvez également consulter l’ensemble des points de terminaison de l’API Microsoft Azure AD Graph disponibles en accédant à [https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta](https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta). Ces points de terminaison sont également documentés dans l’article [Azure AD Reports and Events (Preview)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-reports-and-events-preview) (Rapports et événements Azure AD [version préliminaire]).
- Consultez [Événements de rapport d'audit d'Azure AD](active-directory-reporting-audit-events.md) pour plus d'informations sur le rapport d'audit
- Consultez [Azure AD Reports and Events (Preview)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) (Rapports et événements Azure AD [version préliminaire]) pour plus d’informations sur le service REST de l’API Microsoft Azure AD Graph.

<!---HONumber=AcomDC_0629_2016-->