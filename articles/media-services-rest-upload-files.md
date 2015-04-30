<properties 
	pageTitle="Téléchargement de fichiers dans un compte Media Services à l'aide des API REST" 
	description="Apprenez à obtenir du contenu multimédia dans Media Services en créant et en chargeant des ressources." 
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
	ms.date="02/03/2015" 
	ms.author="juliako"/>



# Téléchargement de fichiers dans un compte Media Services à l'aide des API REST
[AZURE.INCLUDE [media-services-selector-upload-files](../includes/media-services-selector-upload-files.md)]

Cet article fait partie de la série [workflow de vidéo à la demande Media Services](media-services-video-on-demand-workflow.md). 

Dans Media Services, vous téléchargez vos fichiers numériques dans une ressource. L'entité [Asset](https://msdn.microsoft.com/library/azure/hh974277.aspx) peut contenir des fichiers vidéo et audio, des images, des collections de miniatures, des pistes textuelles et des légendes (et les métadonnées concernant ces fichiers).  Une fois les fichiers téléchargés dans la ressource, votre contenu est stocké en toute sécurité dans le cloud et peut faire l'objet d'un traitement et d'une diffusion en continu. 


>[AZURE.NOTE]Media Services utilise la valeur de la propriété IAssetFile.Name lors de la génération d'URL pour le contenu de diffusion en continu (par exemple, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters). Pour cette raison, l'encodage par pourcentage n'est pas autorisé. La valeur de la propriété **Name** ne peut pas comprendre un des [caractères réservés à l'encodage par pourcentage](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters) suivants : !*'();:@&=+$,/?%#[]". En outre, il ne peut exister qu'un '.' pour l'extension de nom de fichier.

Le flux de travail classique d'ingestion de ressources se divise en différentes parties, à savoir :

- Créer une ressource
- Chiffrer une ressource (facultatif)
- Télécharger un fichier vers le stockage d'objets blob


## Création d'une ressource

>[AZURE.NOTE] Lorsque vous utilisez l'API REST de Media Services, les considérations suivantes s'appliquent :
>
>Lors de l'accès aux entités dans Media Services, vous devez définir les valeurs et les champs d'en-tête spécifiques dans vos requêtes HTTP. Pour plus d'informations, consultez [Installation pour le développement REST API de Media Services](media-services-rest-how-to-use.md).

>Après vous être connecté à https://media.windows.net, vous recevrez une redirection 301 spécifiant un autre URI Media Services. Vous devez effectuer les appels suivants au nouvel URI comme décrit dans [Connexion à Media Services à l'aide de l'API REST](media-services-rest-connect_programmatically.md). 
 
Une ressource est un conteneur pour plusieurs types ou ensembles d'objets dans Media Services, y compris des fichiers vidéo, audio, des images, des collections de miniatures, des pistes textuelles et des légendes. Dans l'API REST, la création d'une ressource nécessite d'envoyer une demande POST vers Media Services et de placer les informations de propriété concernant votre ressource dans le corps de la demande.

L'une des propriétés que vous pouvez spécifier lors de la création d'une ressource est **Options**. **Options** est une valeur d'énumération qui décrit les options de chiffrement permettant de créer une ressource. Une valeur valide est une des valeurs de la liste ci-dessous, et non une combinaison de valeurs. 

- **None** = **0**: Aucun chiffrement ne sera utilisé. Il s'agit de la valeur par défaut. À noter que quand vous utilisez cette option, votre contenu n'est pas protégé pendant le transit ou le repos dans le stockage.
	Si vous prévoyez de fournir un MP4 sous forme de téléchargement progressif, utilisez cette option. 

- **StorageEncrypted** = **1**: Spécifie si vous souhaitez que vos fichiers soient chiffrés avec le chiffrement AES-256 bits pour le chargement et le stockage.

	Si votre ressource est stockée sous forme chiffrée, vous devez configurer une stratégie de remise de ressources. Pour plus d'informations, consultez la rubrique [Configuration de la stratégie de remise de ressources](media-services-rest-configure-asset-delivery-policy.md).

- **CommonEncryptionProtected** = **2**: Spécifie si vous téléchargez des fichiers protégés par une méthode de chiffrement commune (comme PlayReady). 

- **EnvelopeEncryptionProtected** = **4**: Spécifiez si vous téléchargez des fichiers TLS chiffrés avec AES. Notez que les fichiers doivent avoir été encodés et chiffrés par le gestionnaire de transformation Transform Manager.

>[AZURE.NOTE]Si votre ressource utilise le chiffrement, vous devez créer une **ContentKey** et la lier à votre ressource, comme décrit dans la rubrique suivante :[Création d'une ContentKey](media-services-rest-create-contentkey.md). Notez qu'après avoir téléchargé les fichiers dans la ressource, vous devez mettre à jour les propriétés de cryptage sur l'entité **AssetFile** avec les valeurs obtenues au cours du cryptage **Asset**. Pour ce faire, utilisez la demande HTTP **MERGE**. 


L'exemple suivant montre comment créer une ressource.

**Demande HTTP**

	POST https://media.windows.net/api/Assets HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.8
	Host: media.windows.net
	
	{"Name":"BigBuckBunny.mp4"}
	

**Réponse HTTP**

Si l'opération réussit, l'élément suivant est retourné :
	
	HTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 452
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
	request-id: e98be122-ae09-473a-8072-0ccd234a0657
	x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sun, 18 Jan 2015 22:06:40 GMT
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
	   "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "State":0,
	   "Created":"2015-01-18T22:06:40.6010903Z",
	   "LastModified":"2015-01-18T22:06:40.6010903Z",
	   "AlternateId":null,
	   "Name":"BigBuckBunny.mp4",
	   "Options":0,
	   "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "StorageAccountName":"storagetestaccount001"
	}
	
## Création d'un AssetFile

L'entité [AssetFile](http://msdn.microsoft.com/library/azure/hh974275.aspx) représente un fichier audio ou vidéo stocké dans un conteneur d'objets blob. Un fichier de ressources est toujours associé à une ressource et une ressource peut contenir un ou plusieurs fichiers de ressources. La tâche de Media Services Encoder échoue si un objet de fichier de ressources n'est pas associé à un fichier numérique dans un conteneur d'objets blob.

Notez que l'instance **AssetFile** et le fichier multimédia réel sont deux objets distincts. L'instance AssetFile contient des métadonnées concernant le fichier multimédia, tandis que le fichier multimédia contient le contenu multimédia réel.

Après avoir téléchargé le fichier multimédia numérique dans un conteneur d'objets blob, vous utiliserez la demande HTTP **MERGE** pour mettre à jour AssetFile avec des informations sur votre fichier multimédia (comme indiqué ultérieurement dans cette rubrique). 

**Demande HTTP**

	POST https://media.windows.net/api/Files HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.8
	Host: media.windows.net
	Content-Length: 164
	
	{  
	   "IsEncrypted":"false",
	   "IsPrimary":"false",
	   "MimeType":"video/mp4",
	   "Name":"BigBuckBunny.mp4",
	   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
	}


**Réponse HTTP**

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 535
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
	Server: Microsoft-IIS/8.5
	request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
	x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 00:34:07 GMT
	
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
	   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
	   "Name":"BigBuckBunny.mp4",
	   "ContentFileSize":"0",
	   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "EncryptionVersion":null,
	   "EncryptionScheme":null,
	   "IsEncrypted":false,
	   "EncryptionKeyId":null,
	   "InitializationVector":null,
	   "IsPrimary":false,
	   "LastModified":"2015-01-19T00:34:08.1934137Z",
	   "Created":"2015-01-19T00:34:08.1934137Z",
	   "MimeType":"video/mp4",
	   "ContentChecksum":null
	}


## Création d'AccessPolicy avec autorisation d'écriture. 

Avant de télécharger des fichiers dans le stockage blob, définissez les droits de la stratégie d'accès pour l'écriture sur une ressource. Pour ce faire, utilisez POST avec une demande HTTP sur le jeu d'entités AccessPolicies. N'oubliez pas de définir une valeur DurationInMinutes après la création ou vous recevrez en réponse un message d'erreur interne de serveur 500. Pour plus d'informations sur AccessPolicies, consultez [AccessPolicy](http://msdn.microsoft.com/library/azure/hh974297.aspx).

L'exemple suivant montre comment créer une stratégie AccessPolicy :
		
**Demande HTTP**

	POST https://media.windows.net/api/AccessPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.8
	Host: media.windows.net
	
	{"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"} 

**Demande HTTP**

	If successful, the following response is returned:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 312
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
	Server: Microsoft-IIS/8.5
	request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
	x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sun, 18 Jan 2015 22:18:06 GMT

	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
	   "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
	   "Created":"2015-01-18T22:18:06.6370575Z",
	   "LastModified":"2015-01-18T22:18:06.6370575Z",
	   "Name":"NewUploadPolicy",
	   "DurationInMinutes":440.0,
	   "Permissions":2
	}

## Obtention de l'URL de téléchargement

Pour recevoir l'URL de téléchargement réelle, créez un localisateur SAS. Les localisateurs définissent l'heure de début et le type de point de terminaison de connexion pour les clients qui souhaitent accéder aux fichiers d'une ressource. Vous pouvez créer plusieurs entités de localisateurs pour une paire AccessPolicy et Asset donnée, afin de gérer les différentes demandes et besoins des clients. Chacun de ces localisateurs utilise la valeur StartTime et la valeur DurationInMinutes d'AccessPolicy pour déterminer la durée pendant laquelle une URL peut être utilisée. Pour plus d'informations, consultez la rubrique [Localisateur](http://msdn.microsoft.com/library/azure/hh974308.aspx).


Une URL SAS a le format suivant :

	{https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Certaines considérations s'appliquent :

- Vous ne pouvez avoir plus de cinq localisateurs uniques associés à une ressource donnée. Pour plus d'informations, consultez la rubrique Localisateur.
- Si vous avez besoin de télécharger vos fichiers immédiatement, vous devez définir la valeur StartTime sur cinq minutes avant l'heure actuelle. Cela vient du fait qu'il peut exister un décalage horaire entre votre ordinateur client et Media Services. En outre, la valeur de StartTime doit être au format DateTime suivant : YYYY-MM-DDTHH:mm:ssZ (par exemple, " 2014-05-23T17:53:50Z ").	
- Il peut y avoir un délai de 30 à 40 secondes après la création d'un localisateur avant qu'il soit disponible. Ce problème s'applique aux localisateurs d'URL SAS et d'origine.

L'exemple suivant montre comment créer un localisateur d'URL SAS, tel que défini par la propriété Type dans le corps de la demande (" 1 " pour un localisateur SAS et " 2 " pour un localisateur d'origine à la demande). La propriété **chemin d'accès** retournée contient l'URL que vous devez utiliser pour télécharger votre fichier.
	
**Demande HTTP**
	
	POST https://media.windows.net/api/Locators HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.8
	Host: media.windows.net
	{  
	   "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
	   "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "StartTime":"2015-02-18T16:45:53",
	   "Type":1
	}


**Réponse HTTP**

Si l'opération réussit, la réponse suivante est retournée :
		
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 949
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
	Server: Microsoft-IIS/8.5
	request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
	x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 03:01:29 GMT
	
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
	   "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
	   "ExpirationDateTime":"2015-02-19T00:05:53",
	   "Type":1,
	   "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
	   "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
	   "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
	   "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
	   "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "StartTime":"2015-02-18T16:45:53",
	   "Name":null
	}

## Téléchargement d'un fichier dans un conteneur de stockage d'objets blob
	
Après avoir défini AccessPolicy et Locator, le fichier réel est téléchargé vers un conteneur de stockage d'objets blob Microsoft Azure à l'aide des API REST Azure Storage. Vous pouvez le télécharger dans des objets blob de page ou de blocs. 

>[AZURE.NOTE] Vous devez ajouter le nom du fichier à télécharger dans la valeur **Path** du localisateur reçue dans la section précédente. Par exemple, https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . . 

Pour plus d'informations sur l'utilisation d'objets blob de stockage Microsoft Azure, consultez [API REST du service BLOB](http://msdn.microsoft.com/library/azure/dd135733.aspx).


## Mise à jour d'AssetFile 

Maintenant que vous avez téléchargé votre fichier, mettez à jour les informations de taille FileAsset (et autres). Par exemple :
	
	MERGE https://media.windows.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.8
	Host: media.windows.net
	
	{  
	   "ContentFileSize":"1186540",
	   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
	   "MimeType":"video/mp4",
	   "Name":"BigBuckBunny.mp4",
	   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
	}


**Réponse HTTP**

Si l'opération réussit, l'élément suivant est retourné :
	HTTP/1.1 204 No Content

## Suppression du localisateur et d'AcessPolicy 

**Demande HTTP**


	DELETE https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.8
	Host: media.windows.net

	
**Réponse HTTP**

Si l'opération réussit, l'élément suivant est retourné :

	HTTP/1.1 204 No Content 
	...

**Demande HTTP**

	DELETE https://media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.8
	Host: media.windows.net

**Réponse HTTP**

Si l'opération réussit, l'élément suivant est retourné :

	HTTP/1.1 204 No Content 
	...

 
[Obtention d'un processeur multimédia]: media-services-get-media-processor.md

<!--HONumber=52-->