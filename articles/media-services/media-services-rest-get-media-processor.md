<properties 
	pageTitle="Création d'un processeur multimédia | Microsoft Azure" 
	description="Apprenez à créer un composant processeur multimédia pour encoder, chiffrer ou déchiffrer un contenu multimédia, ou convertir son format pour Azure Media Services." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/26/2016" 
	ms.author="juliako"/>


#Obtention d’une instance de processeur multimédia


> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [REST](media-services-rest-get-media-processor.md)

##Vue d'ensemble

Dans Media Services, un processeur multimédia est un composant qui gère une tâche de traitement spécifique, telle que l’encodage, la conversion de format, le chiffrement ou le déchiffrement de contenu multimédia. Le plus souvent, vous devez créer un processeur multimédia lorsque vous créez une tâche visant à encoder, à chiffrer ou à convertir le format du contenu multimédia.

Le tableau suivant vous indique le nom et la description de chaque processeur multimédia disponible.

Nom du processeur multimédia|Description|Informations complémentaires
---|---|---
Media Encoder Standard|Fournit des fonctionnalités standard pour l’encodage à la demande. |[Vue d’ensemble et comparaison d’encodeurs multimédia à la demande Azure](media-services-encode-asset.md)
Media Encoder Premium Workflow|Permet d’exécuter des tâches d’encodage à l’aide de Media Encoder Premium Workflow.|[Vue d’ensemble et comparaison d’encodeurs multimédia à la demande Azure](media-services-encode-asset.md)
Azure Media Indexer| Vous permet de faire en sorte que les fichiers et le contenu multimédias puissent faire l'objet d'une recherche, et de générer des pistes de sous-titrage et des mots clés.|[Azure Media Indexer](media-services-index-content.md)
Azure Media Hyperlapse (version préliminaire)|Permet de lisser les « dégradations » dans votre vidéo avec une stabilisation vidéo. Permet également d’accélérer votre contenu dans un clip consommable.|[Azure Media Hyperlapse](media-services-hyperlapse-content.md)
Encodeur multimédia Azure|Amorti
Storage Decryption| Amorti|
Azure Media Packager|Amorti|
Azure Media Encryptor|Amorti|

##Obtention de MediaProcessor

>[AZURE.NOTE] Lorsque vous utilisez l’API REST de Media Services, les considérations suivantes s’appliquent :
>
>Lors de l’accès aux entités dans Media Services, vous devez définir les valeurs et les champs d’en-tête spécifiques dans vos requêtes HTTP. Pour plus d'informations, consultez [Installation pour le développement REST API de Media Services](media-services-rest-how-to-use.md).

>Après vous être connecté à https://media.windows.net, vous recevrez une redirection 301 spécifiant un autre URI Media Services. Vous devez effectuer les appels suivants au nouvel URI comme décrit dans [Connexion à Media Services à l'aide de l'API REST](media-services-rest-connect-programmatically.md).


L’appel REST suivant montre comment obtenir une instance de processeur multimédia par nom (dans ce cas, **Media Encoder Standard**).



	
Demande :

	GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer <token>
	x-ms-version: 2.11
	Host: media.windows.net
	
Réponse :
		
	. . .
	
	{  
	   "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
	   "value":[  
	      {  
	         "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
	         "Description":"Media Encoder Standard",
	         "Name":"Media Encoder Standard",
	         "Sku":"",
	         "Vendor":"Microsoft",
	         "Version":"1.1"
	      }
	   ]
	}


##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Étapes suivantes

Maintenant que vous savez comment obtenir une instance de processeur multimédia, consultez la rubrique [Encodage d’un élément multimédia](media-services-rest-get-started.md) pour savoir comment utiliser Media Encoder Standard afin d’encoder un élément multimédia.

<!---HONumber=AcomDC_0928_2016-->