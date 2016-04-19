<properties 
	pageTitle="Configuration d’une chaîne de connexion dans Azure Storage | Microsoft Azure"
	description="Création d’une chaîne de connexion à un compte de stockage Azure. Une chaîne de connexion inclut les informations nécessaires pour authentifier l’accès aux ressources dans un compte de stockage à partir de votre application."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/01/2016"
	ms.author="tamram"/>

# Configuration des chaînes de connexion Azure Storage

## Vue d'ensemble

Une chaîne de connexion comprend les informations nécessaires pour accéder aux ressources Azure Storage par programme. Votre application utilise la chaîne de connexion pour fournir à Azure Storage les informations nécessaires à l’authentification de l'accès.

Vous pouvez configurer une chaîne de connexion des manières suivantes :

- en vous connectant à l’émulateur de stockage Azure lors du test local de votre service ou application ;
- en vous connectant à un compte de stockage dans Azure à l’aide des points de terminaison par défaut des services de stockage ou des points de terminaison explicites que vous avez définis ;
- en accédant aux ressources d’un compte de stockage via une signature d'accès partagé (SAS).

## Récupération de votre chaîne de connexion

Votre application devra stocker la chaîne de connexion pour authentifier l'accès à Azure Storage lors de son exécution. Plusieurs options vous permettant de stocker votre chaîne de connexion s’offrent à vous :

- Pour une application s’exécutant sur le bureau ou sur un périphérique, vous pouvez stocker la chaîne de connexion dans un fichier app.config ou un autre fichier de configuration. Si vous utilisez un fichier app.config, ajoutez la chaîne de connexion à la section **AppSettings**.
- Pour une application s’exécutant dans un service cloud Azure, vous pouvez stocker votre chaîne de connexion dans le [schéma de configuration du service Azure (fichier .cscfg)](https://msdn.microsoft.com/library/ee758710.aspx). Ajoutez la chaîne de connexion à la section **ConfigurationSettings** du fichier de configuration du service.

Le stockage de votre chaîne de connexion dans un fichier de configuration facilite la mise à jour de la chaîne de connexion qui vous permet de basculer entre l'émulateur de stockage et un compte de stockage Azure dans le cloud. Vous ne devez modifier que la chaîne de connexion pour que celle-ci pointe vers votre compte de stockage.

Vous pouvez utiliser la classe [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) pour accéder à la chaîne de connexion au moment de l’exécution, quel que soit l’environnement d’exécution de l’application.

## Création d’une chaîne de connexion à l’émulateur de stockage

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Pour plus d’informations sur l’émulateur de stockage, consultez la rubrique [Utilisation de l'émulateur de stockage Azure pour le développement et le test](storage-use-emulator.md).

## Création d’une chaîne de connexion à un compte de stockage Azure

Pour créer une chaîne de connexion à votre compte de stockage Azure, utilisez le format de chaîne de connexion suivant. Indiquez si vous souhaitez vous connecter au compte de stockage via HTTP ou HTTPS (recommandé), remplacez `myAccountName` par le nom de votre compte de stockage et remplacez `myAccountKey` par votre clé d’accès au compte :

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

Par exemple, votre chaîne de connexion ressemble à l’exemple de chaîne de connexion suivant :

	DefaultEndpointsProtocol=https;
	AccountName=storagesample;
	AccountKey=<account-key>

> [AZURE.NOTE] Azure Storage prend en charge HTTP et HTTPS au sein d’une chaîne de connexion, mais nous vous conseillons vivement d’utiliser HTTPS.

## Création d’une chaîne de connexion vers un point de terminaison de stockage explicite

Vous pouvez spécifier explicitement les points de terminaison de service dans votre chaîne de connexion si :

- Vous avez mappé un nom de domaine personnalisé pour votre compte de stockage avec le service BLOB.
- Vous possédez une signature d’accès partagé pour l’accès aux ressources de stockage dans un compte de stockage.

Pour créer une chaîne de connexion qui spécifie un point de terminaison d’objet blob explicite, spécifiez le point de terminaison complet pour chaque service, y compris la spécification de protocole (HTTP ou HTTPS) au format suivant :

	BlobEndpoint=myBlobEndpoint;
	QueueEndpoint=myQueueEndpoint;
	TableEndpoint=myTableEndpoint;
	FileEndpoint=myFileEndpoint;
	[credentials]


Vous devez spécifier au moins un point de terminaison de service, mais vous n’avez pas besoin de tous les spécifier. Par exemple, si vous créez une chaîne de connexion pour un point de terminaison personnalisé d’objet blob, la spécification des points de terminaison de file d’attente et de table est facultative. Notez que si vous choisissez de ne pas indiquer les points de terminaison de file d’attente et de table à partir de la chaîne de connexion, vous ne serez pas en mesure d’accéder aux services de Table et de File d’attente à partir de votre code à l’aide de cette chaîne de connexion.

Lorsque vous spécifiez explicitement les points de terminaison de service dans la chaîne de connexion, vous disposez de deux possibilités pour la spécification de `credentials` dans la chaîne ci-dessus :

- Vous pouvez spécifier le nom et la clé de compte : `AccountName=myAccountName;AccountKey=myAccountKey`
- Vous pouvez spécifier une signature d’accès partagé : `SharedAccessSignature=base64Signature`

### Spécification d’un point de terminaison d’objet blob avec un nom de domaine personnalisé

Si vous avez enregistré un nom de domaine personnalisé utilisable avec le service BLOB, vous souhaiterez peut-être configurer explicitement le point de terminaison d’objet blob dans votre chaîne de connexion. La valeur du point de terminaison indiquée dans la chaîne de connexion est utilisée pour construire les URI de demande au service BLOB. Elle indique la forme de tous les URI renvoyés à votre code.

Par exemple, une chaîne de connexion à un point de terminaison d’objet blob sur un domaine personnalisé peut être semblable à :

	DefaultEndpointsProtocol=https;
	BlobEndpoint=www.mydomain.com;
	AccountName=storagesample;
	AccountKey=<account-key>


### Spécification d’un point de terminaison d’objet blob avec une signature d’accès partagé

Vous pouvez créer une chaîne de connexion avec des points de terminaison explicites pour accéder aux ressources de stockage via une signature d’accès partagé. Dans ce cas, vous pouvez spécifier la signature d’accès partagé dans le cadre de la chaîne de connexion, au lieu des informations d’identification de nom et de clé de compte. Le jeton de signature d'accès partagé encapsule des informations sur la ressource à laquelle vous devez accéder, la durée pendant laquelle elle est disponible et les autorisations accordées. Pour plus d’informations sur les signatures d’accès partagé, consultez la page [Signatures d’accès partagé : présentation du modèle SAP](storage-dotnet-shared-access-signature-part-1.md)

Pour créer une chaîne de connexion incluant une signature d’accès partagé, spécifiez la chaîne au format suivant :

    BlobEndpoint=myBlobEndpoint; QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;SharedAccessSignature=sasToken

Le point de terminaison peut être le point de terminaison de service par défaut ou un point de terminaison personnalisé. Le `sasToken` est la chaîne de requête qui suit le point d’interrogation (?) sur l’URL SAP.

### Création d’une chaîne de connexion avec un suffixe de point de terminaison

Pour créer une chaîne de connexion pour le service de stockage dans les régions ou les instances avec des suffixes de point de terminaison différents, comme pour Azure China ou Azure Governance, utilisez le format de chaîne de connexion suivant. Indiquez si vous souhaitez vous connecter au compte de stockage via HTTP ou HTTPS, remplacez `myAccountName` par le nom de votre compte de stockage, remplacez `myAccountKey` par votre clé d’accès au compte et remplacez `mySuffix` par le suffixe d’URI :


	DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=mySuffix;


Par exemple, votre chaîne de connexion doit ressembler à l’exemple de chaîne de connexion suivant :

	DefaultEndpointsProtocol=https;
	AccountName=storagesample;
	AccountKey=<account-key>;
	EndpointSuffix=core.chinacloudapi.cn;

## Étapes suivantes

- [Utilisation de l'émulateur de stockage Azure pour le développement et le test](storage-use-emulator.md)
- [Explorateurs du stockage Azure](storage-explorers.md)

<!---HONumber=AcomDC_0406_2016-->