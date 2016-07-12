<properties 
	pageTitle="Connexion à un compte Media Services à l’aide de l’API REST" 
	description="Cette rubrique montre comment se connecter à Media Services avec l’API REST." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/22/2016"  
	ms.author="juliako"/>


# Connexion à un compte Media Services à l’aide de l’API REST

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-connect-programmatically.md)
- [REST](media-services-rest-connect-programmatically.md)

Cette rubrique décrit comment obtenir une connexion à Microsoft Azure Media Services par programme lorsque vous programmez avec l’API REST Media Services.

Vous avez besoin de deux choses pour accéder à Microsoft Azure Media Services : un jeton d’accès fourni par Azure Access Control Service (ACS) et l’URI de Media Services. Vous pouvez utiliser la méthode de votre choix lors de la création de ces demandes, tant que vous spécifiez les valeurs d’en-têtes appropriées et transmettez le jeton d’accès correctement lors de l’appel dans Media Services.

Les étapes suivantes décrivent le flux de travail habituel lors de l’utilisation de l’API REST Media Services pour se connecter à Media Services :

1. Obtention d’un jeton d’accès
2. Connexion à l’URI Media Services

	>[AZURE.NOTE] Après vous être connecté à https://media.windows.net, vous recevrez une redirection 301 spécifiant un autre URI Media Services. Vous devez faire d’autres appels au nouvel URI. Vous pouvez également recevoir une réponse HTTP/1.1 200 qui contient la description des métadonnées de l’API ODATA.

3. Envoyez vos appels d’API suivants vers la nouvelle URL.

	Par exemple, si après avoir essayé de vous connecter, vous avez les éléments suivants :

		HTTP/1.1 301 Moved Permanently
		Location: https://wamsbayclus001rest-hs.cloudapp.net/api/

	Vous devez envoyer vos appels d’API suivants à https://wamsbayclus001rest-hs.cloudapp.net/api/.

##Adresse de contrôle d’accès

L’adresse de contrôle d’accès Media Services est https://wamsprodglobal001acs.accesscontrol.windows.net, sauf pour la Chine du Nord, où elle est https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn.

##Obtention d’un jeton d’accès

Pour accéder à Media Services directement par le biais de l’API REST, obtenez un jeton d’accès ACS et utilisez-le lors de chaque demande HTTP adressée au service. Ce jeton est semblable aux autres jetons fournis par ACS basés sur les revendications d’accès fournies dans l’en-tête d’une demande HTTP et à l’aide du protocole OAuth v2. Il n’existe pas d’autre condition préalable pour vous connecter directement à Media Services.

L’exemple suivant montre l’en-tête et le corps de demande HTTP qui permet de récupérer un jeton.

**En-tête** :

	POST https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13 HTTP/1.1
	Content-Type: application/x-www-form-urlencoded
	Host: wamsprodglobal001acs.accesscontrol.windows.net
	Content-Length: 120
	Expect: 100-continue
	Connection: Keep-Alive
	Accept: application/json

	
**Corps** :

Il convient de vérifier les valeurs client\_id et client\_secret dans le corps de cette demande ; client\_id et client\_secret correspondent aux valeurs AccountName et AccountKey, respectivement. Ces valeurs sont fournies par Media Services pour vous lorsque vous configurez votre compte.

Notez que la valeur AccountKey de votre compte Media Services doit être encodée dans l’URL (voir l’[encodage par pourcentage](http://tools.ietf.org/html/rfc3986#section-2.1) quand vous l’utilisez comme valeur client\_secret dans votre demande de jeton d’accès.

	grant_type=client_credentials&client_id=ams_account_name&client_secret=URL_encoded_ams_account_key&scope=urn%3aWindowsAzureMediaServices


Par exemple :

	grant_type=client_credentials&client_id=amstestaccount001&client_secret=wUNbKhNj07oqjqU3Ah9R9f4kqTJ9avPpfe6Pk3YZ7ng%3d&scope=urn%3aWindowsAzureMediaServices


L’exemple suivant montre la réponse HTTP qui contient le jeton d’accès dans le corps de réponse.

	HTTP/1.1 200 OK
	Cache-Control: no-cache, no-store
	Pragma: no-cache
	Content-Type: application/json; charset=utf-8
	Expires: -1
	request-id: a65150f5-2784-4a01-a4b7-33456187ad83
	X-Content-Type-Options: nosniff
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Thu, 15 Jan 2015 08:07:20 GMT
	Content-Length: 670
	
	{  
	   "token_type":"http://schemas.xmlsoap.org/ws/2009/11/swt-token-profile-1.0",
	   "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
	   "expires_in":"21600",
	   "scope":"urn:WindowsAzureMediaServices"
	}
	

>[AZURE.NOTE]
Il est recommandé de mettre en cache les valeurs « access\_token » et « expires\_in » sur un stockage externe. Les données du jeton peuvent être récupérées ultérieurement à partir du stockage et réutilisées dans vos appels d’API REST Media Services. Ceci est particulièrement utile pour les scénarios où le jeton peut être partagé en toute sécurité entre plusieurs processus ou ordinateurs.

Veillez à analyser la valeur « expires\_in » du jeton d’accès et à mettre à jour vos appels d’API REST avec de nouveaux jetons le cas échéant.

###Connexion à l’URI Media Services

L’URI racine de Media Services est https://media.windows.net/. Pour commencer, connectez-vous à cet URI. Si vous obtenez une redirection 301 en réponse, adressez les appels suivants au nouvel URI. En outre, n’utilisez pas de logique de redirection automatique/de suivi dans vos demandes. Les verbes HTTP et les corps de demande ne seront pas transférés au nouvel URI.

Notez que l’URI racine pour le téléchargement de fichiers de ressources est https://yourstorageaccount.blob.core.windows.net/, le nom du compte de stockage étant le même que celui que vous avez utilisé à l’étape de configuration de votre compte Media Services.

L’exemple suivant montre la demande HTTP vers l’URI racine de Media Services (https://media.windows.net/). La demande obtient une redirection 301 en réponse. La demande suivante utilise le nouvel URI (https://wamsbayclus001rest-hs.cloudapp.net/api/).

**Demande HTTP** :
	
	GET https://media.windows.net/ HTTP/1.1
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
	x-ms-version: 2.11
	Accept: application/json
	Host: media.windows.net


**Réponse HTTP** :
	
	HTTP/1.1 301 Moved Permanently
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/
	Server: Microsoft-IIS/8.5
	request-id: 987d7652-497a-44e5-b815-f492e02aef97
	x-ms-request-id: 987d7652-497a-44e5-b815-f492e02aef97
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sat, 17 Jan 2015 07:44:53 GMT
	Content-Length: 164
	
	<html><head><title>Object moved</title></head><body>
	<h2>Object moved to <a href="https://wamsbayclus001rest-hs.cloudapp.net/api/">here</a>.</h2>
	</body></html>


**Demande HTTP** (à l’aide du nouvel URI) :
			
	GET https://wamsbayclus001rest-hs.cloudapp.net/api/ HTTP/1.1
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
	x-ms-version: 2.11
	Accept: application/json
	Host: wamsbayclus001rest-hs.cloudapp.net


**Réponse HTTP** :
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 1250
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
	x-ms-request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sat, 17 Jan 2015 07:44:52 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata","value":[{"name":"AccessPolicies","url":"AccessPolicies"},{"name":"Locators","url":"Locators"},{"name":"ContentKeys","url":"ContentKeys"},{"name":"ContentKeyAuthorizationPolicyOptions","url":"ContentKeyAuthorizationPolicyOptions"},{"name":"ContentKeyAuthorizationPolicies","url":"ContentKeyAuthorizationPolicies"},{"name":"Files","url":"Files"},{"name":"Assets","url":"Assets"},{"name":"AssetDeliveryPolicies","url":"AssetDeliveryPolicies"},{"name":"IngestManifestFiles","url":"IngestManifestFiles"},{"name":"IngestManifestAssets","url":"IngestManifestAssets"},{"name":"IngestManifests","url":"IngestManifests"},{"name":"StorageAccounts","url":"StorageAccounts"},{"name":"Tasks","url":"Tasks"},{"name":"NotificationEndPoints","url":"NotificationEndPoints"},{"name":"Jobs","url":"Jobs"},{"name":"TaskTemplates","url":"TaskTemplates"},{"name":"JobTemplates","url":"JobTemplates"},{"name":"MediaProcessors","url":"MediaProcessors"},{"name":"EncodingReservedUnitTypes","url":"EncodingReservedUnitTypes"},{"name":"Operations","url":"Operations"},{"name":"StreamingEndpoints","url":"StreamingEndpoints"},{"name":"Channels","url":"Channels"},{"name":"Programs","url":"Programs"}]}
	 


>[AZURE.NOTE] Après l’obtention du nouvel URI, il convient de l’utiliser pour communiquer avec Media Services.


##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0629_2016-->