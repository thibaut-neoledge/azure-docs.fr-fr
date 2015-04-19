<properties 
	pageTitle="Encodage d'une ressource à l'aide d'Azure Media Encoder" 
	description="Apprenez à utiliser Azure Media Encoder pour encoder un contenu multimédia sur Media Services. Les exemples de code utilisent l'API REST." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="juliako"/>


#Encodage d'une ressource à l'aide d'Azure Media Encoder

Cet article fait partie de la série [workflow de vidéo à la demande Media Services](media-services-video-on-demand-workflow.md) . 

##Vue d'ensemble
Pour fournir une vidéo numérique sur Internet, vous devez compresser le contenu multimédia. Les fichiers vidéo numériques sont volumineux et peuvent être trop gros pour être fournis sur Internet ou pour que les périphériques de vos clients les affichent correctement. L'encodage est le processus de compression audio et vidéo permettant à vos clients d'afficher votre contenu multimédia.

Les tâches d'encodage sont une des opérations de traitement les plus courantes dans Media Services. Vous créez des tâches d'encodage pour convertir des fichiers multimédias d'un encodage à un autre. Lorsque vous les encodez, vous pouvez utiliser l'encodeur multimédia intégré de Media Services. Vous pouvez aussi utiliser un encodeur fourni par un partenaire Media Services. Ces encodeurs tiers sont disponibles sur Azure Marketplace. Vous pouvez spécifier les détails des tâches d'encodage à l'aide de chaînes de présélection définies pour votre encodeur ou en utilisant des fichiers de configuration prédéfinis. Pour voir les types de présélections disponibles, consultez Présélections de tâches pour Azure Media Services. Si vous avez utilisé un encodeur tiers, [validez vos fichiers](https://msdn.microsoft.com/library/azure/dn750842.aspx).

Il est recommandé de toujours encoder vos fichiers mezzanine sous forme de jeu de fichiers MP4 à débit adaptatif, puis de convertir ce jeu au format souhaité au moyen de l'[empaquetage dynamique](https://msdn.microsoft.com/library/azure/jj889436.aspx).

##Création d'un travail avec une seule tâche d'encodage 

>[AZURE.NOTE] Lorsque vous utilisez l'API REST de Media Services, les considérations suivantes s'appliquent :
>
>Lors de l'accès aux entités dans Media Services, vous devez définir les valeurs et les champs d'en-tête spécifiques dans vos requêtes HTTP. Pour plus d'informations, consultez [Configuration du développement de l'API REST Media Services](media-services-rest-how-to-use.md).

>Après vous être connecté à https://media.windows.net, vous recevrez une redirection 301 spécifiant un autre URI Media Services. Vous devez adresser les appels suivants au nouvel URI comme décrit dans [Connexion à Media Services à l'aide de l'API REST](media-services-rest-connect_programmatically.md). 


L'exemple suivant montre comment créer et publier un projet avec une tâche visant à encoder une vidéo en une résolution et une qualité spécifiques. Quand vous encodez à l'aide de l'Encodeur multimédia Azure, vous pouvez utiliser les présélections de configuration de tâche spécifiés [ici](https://msdn.microsoft.com/library/azure/dn619389.aspx).
	
Demande :

	POST https://media.windows.net/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336802231&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=gR%2fNaIZgolFLxBOmfSECrp16Mp0Mti3KoePVjBUCzls%3d
	Host: media.windows.net
	Content-Length: 476
	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Broadband 720p", "MediaProcessorId" : "nb:mpid:UUID:70bdc2c3-ebf4-42a9-8542-5afc1e55d217",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Réponse :
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 1017
	Content-Type: application/json;odata=verbose;charset=utf-8
	Location: https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')
	Server: Microsoft-IIS/7.5
	x-ms-request-id: d2052a71-95b1-4a52-9420-ccca1202a5fb
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET
	Date: Fri, 11 May 2012 21:32:40 GMT
	
	{"d":{"__metadata":{"id":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')","uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')","type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"},"Tasks":{"__deferred":{"uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')/Tasks"}},"OutputMediaAssets":{"__deferred":{"uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')/OutputMediaAssets"}},"InputMediaAssets":{"__deferred":{"uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')/InputMediaAssets"}},"Id":"nb:jid:UUID:40dc7bef-6bd9-2247-9f3d-d80bc257d715","Name":"NewTestJob","Created":"\/Date(1336771959431)\/","LastModified":"\/Date(1336771959431)\/","EndTime":null,"Priority":0,"RunningDuration":0,"StartTime":null,"State":0,"TemplateId":null}}

##Création d'un travail avec des tâches chaînées

Dans de nombreux scénarios d'application, les développeurs souhaitent créer une série de tâches de traitement. Dans Media Services, vous pouvez créer une série de tâches chaînées. Chaque tâche effectue différentes étapes de traitement et peut utiliser différents processeurs multimédias. Les tâches chaînées peuvent transférer une ressource d'une tâche à une autre, en effectuant une séquence linéaire de tâches sur la ressource. Toutefois, les tâches effectuées dans un travail ne le sont pas obligatoirement dans une séquence. Lorsque vous créez une tâche chaînée, les objets chaînés **ITask** sont créés dans un seul objet **IJob**.

>[AZURE.NOTE] Il existe actuellement une limite de 30 tâches par travail. Si vous devez chaîner plus de 30 tâches, créez plusieurs travaux pour contenir les tâches.


	POST https://media.windows.net/api/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336802231&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=gR%2fNaIZgolFLxBOmfSECrp16Mp0Mti3KoePVjBUCzls%3d

	{  
	   "Name":"NewTestJob",
	   "InputMediaAssets":[  
	      {  
	         "__metadata":{  
	            "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
	         }
	      }
	   ],
	   "Tasks":[  
	      {  
	         "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
	      },
	      {  
	         "Configuration":"H264 Smooth Streaming 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
	      }
	   ]
	}


##Étapes suivantes
Maintenant que vous savez comment créer un travail d'encodage de ressource, accédez à la [procédure de vérification de la progression des tâches avec Media Services](media-services-rest-check-job-progress.md) .

[Azure Marketplace]: https://datamarket.azure.com/
[Présélection d'encodeur]: http://msdn.microsoft.com/library/dn619392.aspx
[Procédure : obtention d'une instance de processeur multimédia]:http://go.microsoft.com/fwlink/?LinkId=301732
[Procédure : téléchargement d'une ressource chiffrée]:http://go.microsoft.com/fwlink/?LinkId=301733
[Procédure : remise d'une ressource par téléchargement].:http://go.microsoft.com/fwlink/?LinkId=301734
[Vérification de la progression des tâches]:http://go.microsoft.com/fwlink/?LinkId=301737
[Présélection de tâches pour Azure Media Packager]:http://msdn.microsoft.com/library/windowsazure/hh973635.aspx

<!--HONumber=47-->
