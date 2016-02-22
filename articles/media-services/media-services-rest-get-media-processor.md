<properties 
	pageTitle="Création d'un processeur multimédia | Microsoft Azure" 
	description="Apprenez à créer un composant processeur multimédia pour encoder, chiffrer ou déchiffrer un contenu multimédia, ou convertir son format pour Azure Media Services." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/05/2015" 
	ms.author="juliako"/>


#Obtention d’une instance de processeur multimédia


> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [REST](media-services-rest-get-media-processor.md)

##Vue d’ensemble

Dans Media Services, un processeur multimédia est un composant qui gère une tâche de traitement spécifique, telle que l’encodage, la conversion de format, le chiffrement ou le déchiffrement de contenu multimédia. Le plus souvent, vous devez créer un processeur multimédia lorsque vous créez une tâche visant à encoder, à chiffrer ou à convertir le format du contenu multimédia.

Le tableau suivant vous indique le nom et la description de chaque processeur multimédia disponible.

Nom du processeur multimédia|Description|Informations complémentaires
---|---|---
Encodeur multimédia Azure|Permet d’exécuter les tâches d’encodage à l’aide de l’Encodeur multimédia Azure.|[Encodeur multimédia Azure](media-services-encode-asset.md#azure_media_encoder)
Media Encoder Standard|Vous permet d'exécuter des tâches d'encodage avec l'Encodeur multimédia standard.|[Encodeur multimédia Azure](media-services-encode-asset.md#media_encoder_standard)
Media Encoder Premium Workflow|Permet d’exécuter des tâches d’encodage à l’aide de Media Encoder Premium Workflow.|[Media Encoder Premium Workflow](media-services-encode-asset.md#media_encoder_premium_wokrflow)
Azure Media Indexer| Permet d’effectuer des recherches dans les fichiers multimédias et le contenu et de générer des pistes et des mots clés de sous-titrage codé.|[Indexation de fichiers multimédias avec Azure Media Indexer](media-services-index-content.md).
Azure Media Hyperlapse (version préliminaire)|Permet de lisser les « dégradations » dans votre vidéo avec une stabilisation vidéo. Permet également d’accélérer votre contenu dans un clip consommable.|		[Azure Media Hyperlapse](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)</a>
Storage Decryption| Vous permet de déchiffrer les éléments multimédias qui ont été chiffrés par chiffrement de stockage.|N/A
Microsoft Azure Media Packager|Vous permet de convertir des éléments multimédias du format .mp4 vers un format de diffusion en continu. De même, il permet de convertir des éléments multimédias du format de diffusion en continu lisse au format HLS (Apple HTTP Live Streaming).|[Chaînes de présélection des tâches pour Azure Media Packager](http://msdn.microsoft.com/library/hh973635.aspx)
Microsoft Azure Media Encryptor|Vous permet de chiffrer des éléments multimédias avec PlayReady Protection.|[Chaînes de présélection des tâches pour Azure Media Packager](http://msdn.microsoft.com/library/hh973610.aspx)

##Obtention de MediaProcessor

>[AZURE.NOTE] Lorsque vous utilisez l’API REST de Media Services, les considérations suivantes s’appliquent :
>
>Lors de l’accès aux entités dans Media Services, vous devez définir les valeurs et les champs d’en-tête spécifiques dans vos requêtes HTTP. Pour plus d'informations, consultez [Installation pour le développement REST API de Media Services](media-services-rest-how-to-use.md).

>Après vous être connecté à https://media.windows.net, vous recevrez une redirection 301 spécifiant un autre URI Media Services. Vous devez effectuer les appels suivants au nouvel URI comme décrit dans [Connexion à Media Services à l'aide de l'API REST](media-services-rest-connect_programmatically.md).



L’appel REST suivant montre comment obtenir une instance de processeur multimédia par nom (dans ce cas, **Azure Media Encoder**).

	
Demande :

	GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20Encoder' HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-e769-477b-2233-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423635565&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=6zwXEn7YJzVJbVCNpqDUjBLuE5iUwsdJbWvJNvpY3%2b8%3d
	x-ms-version: 2.11
	Host: media.windows.net
	
Réponse :
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 273
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 8a291764-4ed7-405d-aa6e-d3ebabb0b3f6
	request-id: dceeb559-48b5-48e1-81d3-d324b6203d51
	x-ms-request-id: dceeb559-48b5-48e1-81d3-d324b6203d51
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Wed, 11 Feb 2015 00:19:56 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors","value":[{"Id":"nb:mpid:UUID:1b1da727-93ae-4e46-a8a1-268828765609","Description":"Azure Media Encoder","Name":"Azure Media Encoder","Sku":"","Vendor":"Microsoft","Version":"4.4"}]}




##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Étapes suivantes
Maintenant que vous savez comment obtenir une instance de processeur multimédia, consultez la rubrique [Encodage d'un élément multimédia][] pour savoir comment utiliser l'Encodeur multimédia Azure afin d'encoder un élément multimédia.

[Encodage d'un élément multimédia]: media-services-rest-encode-asset.md
[Task Preset Strings for the Azure Media Encoder]: http://msdn.microsoft.com/library/jj129582.aspx
[How to: Connect to Media Services Programmatically]: ../media-services-rest-connect_programmatically/

<!---HONumber=AcomDC_0211_2016-->